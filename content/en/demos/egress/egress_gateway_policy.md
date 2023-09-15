---
title: "Egress Gateway Policy"
description: "Accessing external services via Egress Gateway using Egress policies"
type: docs
weight: 4
---

This guide demonstrates a client within the service mesh accessing destinations external to the mesh via egress gateway using FSM's Egress policy API.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/kubernetes_ingress/#installation)

## Egress Gateway passthrough demo

1. Deploy egress gateway during FSM installation.

    ```bash
    fsm install --set=fsm.egressGateway.enabled=true
    ```

    Or, enable egress gateway with FSM CLI.

    ```bash
    fsm egressgateway enable
    ```
   
   > There are more options supported by `fsm egressgateway enable`.

2. Disable global egress passthrough to enable egress policy if not disabled:
    
    ```bash
    export FSM_NAMESPACE=fsm-system # Replace fsm-system with the namespace where FSM is installed
    kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}'  --type=merge
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

4. Confirm the `curl` client is unable make the HTTP request `http://httpbin.org:80/get` to the `httpbin.org` website on port `80`.
    
    ```console
    kubectl exec $(kubectl get pod -n curl -l app=curl -o jsonpath='{.items..metadata.name}') -n curl -c curl -- curl -sI http://httpbin.org:80/get
    command terminated with exit code 7
    ```

5. Apply an Egress policy to allow the `curl` client's ServiceAccount to access the `httpbin.org` website on port `80` serving the `http` protocol.
    
    ```bash
    kubectl apply -f - <<EOF
    kind: Egress
    apiVersion: policy.flomesh.io/v1alpha1
    metadata:
      name: httpbin-80
      namespace: curl
    spec:
      sources:
      - kind: ServiceAccount
        name: curl
        namespace: curl
      hosts:
      - httpbin.org
      ports:
      - number: 80
        protocol: http
    EOF

6. Confirm the `curl` client is able to make successful HTTP requests to `http://httpbin.org:80/get`.

    ```bash
    kubectl exec $(kubectl get pod -n curl -l app=curl -o jsonpath='{.items..metadata.name}') -n curl -c curl -- curl -sI http://httpbin.org:80/get
    HTTP/1.1 200 OK
    date: Fri, 27 Jan 2023 22:31:46 GMT
    content-type: application/json
    content-length: 314
    server: gunicorn/19.9.0
    access-control-allow-origin: *
    access-control-allow-credentials: true
    connection: keep-alive
    ```