fo---
title: "Application onboarding"
description: "Onboard Services"
type: docs
weight: 5
---

The following guide describes how to onboard a Kubernetes microservice to an FSM instance.

1. Refer to the [application requirements](/guides/app_onboarding/prereqs) guide before onboarding applications.

2. Configure and Install [Service Mesh Interface (SMI) policies](https://github.com/servicemeshinterface/smi-spec)

    FSM conforms to the SMI specification. By default, FSM denies all traffic communications between Kubernetes services unless explicitly allowed by SMI policies. This behavior can be overridden with the `--set=fsm.enablePermissiveTrafficPolicy=true` flag on the `fsm install` command, allowing SMI policies not to be enforced while allowing traffic and services to still take advantage of features such as mTLS-encrypted traffic, metrics, and tracing.

    For example SMI policies, please see the following examples:
    - [demo/deploy-traffic-specs.sh](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/demo/deploy-traffic-specs.sh)
    - [demo/deploy-traffic-split.sh](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/demo/deploy-traffic-split.sh)
    - [demo/deploy-traffic-target.sh](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/demo/deploy-traffic-target.sh)

3. If an application in the mesh needs to communicate with the Kubernetes API server, the user needs to explicitly allow this either by using IP range exclusion or by creating an egress policy as outlined below.

   First get the Kubernetes API server cluster IP:
   ```console
   $ kubectl get svc -n default
   NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
   kubernetes   ClusterIP   10.0.0.1     <none>        443/TCP   1d
   ```

    **Option 1:** add the Kubernetes API server's address to the list of Global outbound IP ranges for exclusion. The IP address could be a cluster IP address or a public IP address and should be appropriately excluded for connectivity to the Kubernetes API server.

    Add this IP to the MeshConfig so that outbound traffic to it is excluded from interception by FSM's sidecar:
    ```console
    $ kubectl patch meshconfig fsm-mesh-config -n <fsm-namespace> -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":["10.0.0.1/32"]}}}'  --type=merge
    meshconfig.config.flomesh.io/fsm-mesh-config patched
    ```
    
    Restart the relevant pods in monitored namespaces for this change to take effect.

    **Option 2:** apply an Egress policy to allow access to the Kubernetes API server over HTTPS
   
   > _Note: when using an Egress policy, the Kubernetes API service must not be in a namespace that FSM manages_

    1. Enable egress policy if not enabled:
    ```console
    kubectl patch meshconfig fsm-mesh-config -n <fsm-namespace> -p '{"spec":{"featureFlags":{"enableEgressPolicy":true}}}'  --type=merge
    ```
   
    2. Apply an Egress policy to allow the application's ServiceAccount to access the Kubernetes API server cluster IP found above.
    For example:
    ```console
    kubectl apply -f - <<EOF
    kind: Egress
    apiVersion: policy.flomesh.io/v1alpha1
    metadata:
        name: k8s-server-egress
        namespace: test
    spec:
        sources:
        - kind: ServiceAccount
          name: <app pod's service account name>
          namespace: <app pod's service account namespace>
        ipAddresses:
        - 10.0.0.1/32
        ports:
        - number: 443
          protocol: https
    EOF
    ```  

4. Onboard Kubernetes Namespaces to FSM

    To onboard a namespace containing applications to be managed by FSM, run the `fsm namespace add` command:

    ```console
    $ fsm namespace add <namespace> --mesh-name <mesh-name>
    ```

    By default, the `fsm namespace add` command enables automatic sidecar injection for pods in the namespace.

    To disable automatic sidecar injection as a part of enrolling a namespace into the mesh, use `fsm namespace add <namespace> --disable-sidecar-injection`.
    Once a namespace has been onboarded, pods can be enrolled in the mesh by configuring automatic sidecar injection. See the [Sidecar Injection](/guides/app_onboarding/sidecar_injection) document for more details.

5.  Deploy new applications or redeploy existing applications

    By default, new deployments in onboarded namespaces are enabled for automatic sidecar injection. This means that when a new Pod is created in a managed namespace, FSM will automatically inject the sidecar proxy to the Pod.
    Existing deployments need to be restarted so that FSM can automatically inject the sidecar proxy upon Pod re-creation. Pods managed by a Deployment can be restarted using the `kubectl rollout restart deploy` command.

    In order to route protocol specific traffic correctly to service ports, configure the application protocol to use. Refer to the [application protocol selection guide](/guides/app_onboarding/app_protocol_selection) to learn more.

#### Note: Removing Namespaces
Namespaces can be removed from the FSM mesh with the `fsm namespace remove` command:

```console
fsm namespace remove <namespace>
```

> **Please Note:**
> The **`fsm namespace remove`** command only tells FSM to stop applying updates to the sidecar proxy configurations in the namespace. It **does not** remove the proxy sidecars. This means the existing proxy configuration will continue to be used, but it will not be updated by the FSM control plane. If you wish to remove the proxies from all pods, remove the pods' namespaces from the FSM mesh with the CLI and reinstall all the pod workloads.
