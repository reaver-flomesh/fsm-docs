---
title: "Installation"
description: "Enable FSM Gateway in cluster."
type: docs
weight: 1
---

To utilize the FSM Gateway, initial activation within the FSM is requisite. Analogous to the FSM Ingress, two distinct methodologies exist for its enablement.

***Note: It is imperative to acknowledge that the minimum required version of Kubernetes to facilitate the FSM Gateway activation is {{< param min_k8s_version_gateway_api >}}.***

Let's start.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- FSM version >= v1.1.0.
- FSM CLI to install FSM and enable FSM Gateway.

## Installation

One methodology for enabling FSM Gateway is enable it during FSM installation. Remember that it's diabled by defaulty.

```bash
fsm install \
    --set=fsm.fsmGateway.enabled=true
```

Another approach is installing it individually if you already have FSM mesh installed.

```bash
fsm gateway enable
```

Once done, we can check the [`GatewayClass`](https://gateway-api.sigs.k8s.io/api-types/gatewayclass/) resource in cluster.

```bash
kubectl get GatewayClass
NAME              CONTROLLER                      ACCEPTED   AGE
fsm-gateway-cls   flomesh.io/gateway-controller   True       113s
```

Yes, the `fsm-gateway-cls` is just the one we are expecting. We can also get the controller name above.

Different from Ingress controller, there is no explicit Deployment or Pod unless create a [`Gateway`](https://gateway-api.sigs.k8s.io/api-types/gateway/) manually.

Let's try with below to create a simple FSM gateway.

## Quickstart

To create a FSM gateway, we need to create `Gateway` resource. This manifest will setup a gateway which will listen on port `8000` and accept the `xRoute` resources from same namespace.

> `xRoute` stands for `HTTPRoute`, `HTTPRoute`, `TLSRoute`, `TCPRoute`, `UDPRoute` and `GRPCRoute`.


```bash
kubectl apply -n fsm-system -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm
  listeners:
    - protocol: HTTP
      port: 8000
      name: http
      allowedRoutes:
        namespaces:
          from: Same
EOF
```

Then we can check the resoureces:

```bash
kubectl get po,svc -n fsm-system -l app=fsm-gateway
NAME                                          READY   STATUS    RESTARTS   AGE
pod/fsm-gateway-fsm-system-745ddc856b-v64ql   1/1     Running   0          12m

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/fsm-gateway-fsm-system   LoadBalancer   10.43.20.139   10.0.2.4      8000:32328/TCP   12m
```

At this time, you will get result below if trying to access the gateway port:

```bash
curl -i 10.0.2.4:8000/
HTTP/1.1 404 Not Found
content-length: 13
connection: keep-alive

Not found
```

That's why we have not configure any route. Let's create a `HTTRoute` for the `Service` `fsm-controller`(The FSM controller has a Pipy repo running).

```bash
kubectl apply -n fsm-system -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: repo
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  rules:
  - backendRefs:
    - name: fsm-controller
      port: 6060
EOF
```

Trigger the request again, it responds 200 this time.

```bash
curl -i 10.0.2.4:8000/
HTTP/1.1 200 OK
content-type: text/html
content-length: 0
connection: keep-alive
```
