---
title: "FSM Control Plane Health Probes"
description: "How FSM's health probes work and what to do if they fail"
aliases: "/docs/control_plane_health_probes"
type: "docs"
---

FSM control plane components leverage health probes to communicate their overall status. Health probes are implemented as HTTP endpoints which respond to requests with HTTP status codes indicating success or failure.

Kubernetes uses these probes to communicate the status of the control plane Pods' statuses and perform some actions automatically to improve availability. More details about Kubernetes probes can be found [here](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

## FSM Components with Probes

The following FSM control plane components have health probes:

#### fsm-controller

The following HTTP endpoints are available on fsm-controller on port 9091:

- `/health/alive`: HTTP 200 response code indicates FSM's Aggregated Discovery Service (ADS) is running. No response is sent when the service is not yet running.

- `/health/ready`: HTTP 200 response code indicates ADS is ready to accept gRPC connections from proxies. HTTP 503 or no response indicates gRPC connections from proxies will not be successful.

#### fsm-injector

The following HTTP endpoints are available on fsm-injector on port 9090:

- `/healthz`: HTTP 200 response code indicates the injector is ready to inject new Pods with proxy sidecar containers. No response is sent otherwise.

## How to Verify FSM Health

Because FSM's Kubernetes resources are configured with liveness and readiness probes, Kubernetes will automatically poll the health endpoints on the fsm-controller and fsm-injector Pods.

When a liveness probe fails, Kubernetes will generate an Event (visible by `kubectl describe pod <pod name>`) and restart the Pod. The `kubectl describe` output may look like this:

```console
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  24s               default-scheduler  Successfully assigned fsm-system/fsm-controller-85fcb445b-fpv8l to fsm-control-plane
  Normal   Pulling    23s               kubelet            Pulling image "flomesh/fsm-controller:v0.8.0"
  Normal   Pulled     23s               kubelet            Successfully pulled image "flomesh/fsm-controller:v0.8.0" in 562.2444ms
  Normal   Created    1s (x2 over 23s)  kubelet            Created container fsm-controller
  Normal   Started    1s (x2 over 23s)  kubelet            Started container fsm-controller
  Warning  Unhealthy  1s (x3 over 21s)  kubelet            Liveness probe failed: HTTP probe failed with statuscode: 503
  Normal   Killing    1s                kubelet            Container fsm-controller failed liveness probe, will be restarted
```

When a readiness probe fails, Kubernetes will generate an Event (visible with `kubectl describe pod <pod name>`) and ensure no traffic destined for Services the Pod may be backing is routed to the unhealthy Pod. The `kubectl describe` output for a Pod with a failing readiness probe may look like this:

```console
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  36s               default-scheduler  Successfully assigned fsm-system/fsm-controller-5494bcffb6-tn5jv to fsm-control-plane
  Normal   Pulling    36s               kubelet            Pulling image "flomesh/fsm-controller:latest"
  Normal   Pulled     35s               kubelet            Successfully pulled image "flomesh/fsm-controller:v0.8.0" in 746.4323ms
  Normal   Created    35s               kubelet            Created container fsm-controller
  Normal   Started    35s               kubelet            Started container fsm-controller
  Warning  Unhealthy  4s (x3 over 24s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 503
```

The Pod's `status` will also indicate that it is not ready which is shown in its `kubectl get pod` output. For example:

```console
NAME                              READY   STATUS    RESTARTS   AGE
fsm-controller-5494bcffb6-tn5jv   0/1     Running   0          26s
```

The Pods' health probes may also be invoked manually by forwarding the Pod's necessary port and using `curl` or any other HTTP client to issue requests. For example, to verify the liveness probe for fsm-controller, get the Pod's name and forward port 9091:

```bash
# Assuming FSM is installed in the fsm-system namespace
kubectl port-forward -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') 9091
```

Then, in a separate terminal instance, `curl` may be used to check the endpoint. The following example shows a healthy fsm-controller:

```console
$ curl -i localhost:9091/health/alive
HTTP/1.1 200 OK
Date: Thu, 18 Mar 2021 20:15:29 GMT
Content-Length: 16
Content-Type: text/plain; charset=utf-8

Service is alive
```

## Troubleshooting

If any health probes are consistently failing, perform the following steps to identify the root cause:

1. Ensure the unhealthy fsm-controller or fsm-injector Pod is not running an Pipy sidecar container.

    To verify The fsm-controller Pod is not running an Pipy sidecar container, verify none of the Pod's containers' images is an Pipy image. Pipy images have "flomesh/pipy" in their name.

    For example, an fsm-controller Pod that includes an Pipy container:
    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh/fsm-controller:v0.8.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    To verify The fsm-injector Pod is not running an Pipy sidecar container, verify none of the Pod's containers' images is an Pipy image. Pipy images have "flomesh/pipy" in their name.

    For example, an fsm-injector Pod that includes an Pipy container:
    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh/fsm-injector:v0.8.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    If either Pod is running an Pipy container, it may have been injected erroneously by this or another another instance of FSM. For each mesh found with the `fsm mesh list` command, verify the FSM namespace of the unhealthy Pod is not listed in the `fsm namespace list` output with `SIDECAR-INJECTION` "enabled" for any FSM instance found with the `fsm mesh list` command.

    For example, for all of the following meshes:

    ```console
    $ fsm mesh list

    MESH NAME   NAMESPACE      CONTROLLER PODS                  VERSION     SMI SUPPORTED
    fsm         fsm-system     fsm-controller-5494bcffb6-qpjdv  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    fsm2        fsm-system-2   fsm-controller-48fd3c810d-sornc  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    ```

    Note how `fsm-system` (the mesh control plane namespace) is present in the following list of namespaces:

    ```console
    $ fsm namespace list --mesh-name fsm --fsm-namespace fsm-system
    NAMESPACE    MESH    SIDECAR-INJECTION
    fsm-system   fsm2    enabled
    bookbuyer    fsm2    enabled
    bookstore    fsm2    enabled
    ```

    If the FSM namespace is found in any `fsm namespace list` command with `SIDECAR-INJECTION` enabled, remove the namespace from the mesh injecting the sidecars. For the example above:

    ```console
    $ fsm namespace remove fsm-system --mesh-name fsm2 --fsm-namespace fsm-system2
    ```

1. Determine if Kubernetes encountered any errors while scheduling or starting the Pod.

    Look for any errors that may have recently occurred with `kubectl describe` of the unhealthy Pod.

    For fsm-controller:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    For fsm-injector:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    Resolve any errors and verify FSM's health again.

1. Determine if the Pod encountered a runtime error.

    Look for any errors that may have occurred after the container started by inspecting its logs. Specifically, look for any logs containing the string `"level":"error"`.

    For fsm-controller:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    For fsm-injector:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    Resolve any errors and verify FSM's health again.
