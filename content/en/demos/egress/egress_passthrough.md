---
title: "Egress Passthrough to Unknown Destinations"
description: "Accessing external services without Egress policies"
type: docs
weight: 1
---

This guide demonstrates a client within the service mesh accessing destinations external to the mesh using FSM's Egress capability to passthrough traffic to unknown destinations without an Egress policy.


## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/fsm_ingress/installation/#installation)


## HTTP(S) mesh-wide Egress passthrough demo

1. Enable global egress passthrough if not enabled:
    ```bash
    export FSM_NAMESPACE=fsm-system # Replace fsm-system with the namespace where FSM is installed
    kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge
    ```

1. Deploy the `curl` client into the `curl` namespace after enrolling its namespace to the mesh.
    ```bash
    # Create the curl namespace
    kubectl create namespace curl

    # Add the namespace to the mesh
    fsm namespace add curl

    # Deploy curl client in the curl namespace
    kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/curl/curl.yaml -n curl
    ```

    Confirm the `curl` client pod is up and running.

    ```console
    kubectl get pods -n curl

    NAME                    READY   STATUS    RESTARTS   AGE
    curl-54ccc6954c-9rlvp   2/2     Running   0          20s
    ```

1. Confirm the `curl` client is able to make successful HTTPS requests to the `httpbin.org` website on port `443`.
    ```console
    kubectl exec -n curl -ti "$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items[0].metadata.name}')" -c curl -- curl -I https://httpbin.org:443

    HTTP/2 200
    date: Tue, 16 Mar 2021 22:19:00 GMT
    content-type: text/html; charset=utf-8
    content-length: 9593
    server: gunicorn/19.9.0
    access-control-allow-origin: *
    access-control-allow-credentials: true
    ```

    A `200 OK` response indicates the HTTPS request from the `curl` client to the `httpbin.org` website was successful.

1. Confirm the HTTPS requests fail when mesh-wide egress is disabled.
    ```bash
    kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}'  --type=merge
    ```

    Let's trigger the request again, and you will find it failed this time.

    ```console
    kubectl exec -n curl -ti "$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items[0].metadata.name}')" -c curl -- curl -I https://httpbin.org:443

    curl: (7) Failed to connect to httpbin.org port 443 after 114 ms: Couldn't connect to server
command terminated with exit code 7
    ```
