---
title: "TLS Passthrough"
description: ""
type: docs
weight: 15
draft: false
---

TLS passthrough means that the gateway does not decrypt TLS traffic, but directly transmits the encrypted data to the back-end server, which decrypts and processes it.

This doc will guide how to use the TLS Passthrought feature.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

We will utilize https://httpbin.org for TLS passthrough testing, functioning similarly to the sample app deployed in other documentation sections.

### Create Gateway

First of all, we need to create a gateway to accept incoming request. Different from [TLS Termination](/guides/traffic_management/ingress/fsm_gateway/tls_termination/), the mode is set to `Passthrough` for the listener.

Let's create it in namespace `httpbin` which accepts route resources in same namespace.

```shell
kubectl create ns httpbin
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm-gateway-cls
  listeners:
  - protocol: TLS
    port: 8000
    name: foo
    tls:
      mode: Passthrough
    allowedRoutes:
      namespaces:
        from: Same
EOF
```

Let's record the IP address of gateway.

```bash
export GATEWAY_IP=$(kubectl get svc -n httpbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

### Create TCP Route

To route encrypted traffic to a backend service without decryption, the use of [TLSRoute](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1alpha2.TLSRoute) is necessary in this context.

In the `rules.backendRefs` configuration, we specify an external service using its host and port. For example, for https://httpbin.org, these would be set as `name: httpbin.org` and `port: 443`.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: TLSRoute
metadata:
  name: tcp-route
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  rules:
  - backendRefs:
    - name: httpbin.org
      port: 443
EOF
```

### Test

We issue requests to the URL `https://httpbin.org`, but in reality, these are routed through the gateway. 

```shell
curl https://httpbin.org/headers --connect-to httpbin.org:443:$GATEWAY_IP:8000
{
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.1.2",
    "X-Amzn-Trace-Id": "Root=1-655dd2be-583e963f5022e1004257d331"
  }
}
```