---
title: "Egress Gateway Passthrough to Unknown Destinations"
description: "Accessing external services via Egress Gateway without Egress policies"
type: docs
weight: 2
---

This guide demonstrates a client within the service mesh accessing destinations external to the mesh via egress gateway using FSM's Egress capability to passthrough traffic to unknown destinations without an Egress policy.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/fsm_ingress/installation/#installation)

## Egress Gateway passthrough demo

1. Deploy egress gateway via fsm.
    
    ```bash
    fsm install --set=fsm.egressGateway.enabled=true
    ```

    Or, enable egress gateway with FSM CLI.

    ```bash
    fsm egressgateway enable
    ```
   
   > There are more options supported by `fsm egressgateway enable`.

2. Enable global egress passthrough if not enabled:
    ```bash
    export FSM_NAMESPACE=fsm-system # Replace fsm-system with the namespace where FSM is installed
    kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":true}}}' --type=merge
    ```

3. Deploy the `curl` client into the `curl` namespace after enrolling its namespace to the mesh.

    ```bash
    # Create the curl namespace
    kubectl create namespace curl

    # Add the namespace to the mesh
    fsm namespace add curl

    # Deploy curl client in the curl namespace
    kubectl apply -n curl -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/curl/curl.yaml
    ```

    Confirm the `curl` client pod is up and running.

    ```bash
    kubectl get pods -n curl 
    NAME                    READY   STATUS    RESTARTS   AGE
    curl-7bb5845476-8s9kv   2/2     Running   0          29s
    ```

4. Confirm the `curl` client is able to make successful HTTP requests to the `httpbin.org` website on port `80`.

    ```bash
    kubectl exec "$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items..metadata.name}')" -n curl -c curl -- curl -sI http://httpbin.org:80/get
    HTTP/1.1 200 OK
    Date: Fri, 27 Jan 2023 22:27:53 GMT
    Content-Type: application/json
    Content-Length: 258
    Connection: keep-alive
    Server: gunicorn/19.9.0
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Credentials: true
    ```

    A `200 OK` response indicates the HTTP request from the `curl` client to the `httpbin.org` website was successful.

5. Confirm the HTTP requests fail when mesh-wide egress is disabled.

    ```bash
    kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}'  --type=merge
    ```

    ```bash
    kubectl exec "$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items..metadata.name}')" -n curl -c curl -- curl -sI http://httpbin.org:80/get
    command terminated with exit code 7
    ```