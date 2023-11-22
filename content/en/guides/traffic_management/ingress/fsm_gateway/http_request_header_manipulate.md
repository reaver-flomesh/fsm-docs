---
title: "HTTP Request Header Manipulate"
description: ""
type: docs
weight: 7
draft: false
---

The HTTP header manipulation feature allows you to fine-tune incoming and outgoing request and response headers. 

In Gateway API, the HTTPRoute resource utilities two [`HTTPHeaderFilter` filter](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1.HTTPHeaderFilter) for request and response header manipulation.

The both filters supports `add`, `set` and `remove` operation. The combination of them is also available.

This document will introduce the HTTP request header manipulation function of FSM Gateway. The introduction of HTTP response header manipulation is located in doc [HTTP Response Header Manipulate](/guides/traffic_management/ingress/fsm_gateway/http_response_header_manipulate).

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

We will follow the sample in [HTTP Routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/#deploy-example).

In backend service, there is a path `/headers` which will respond all request headers.

```shell
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "10.42.0.15:80",
    "User-Agent": "curl/8.1.2"
  }
}
```

### Add HTTP Request header

With header adding feature, let's try to add a new header to request by add `HTTPHeaderFilter` filter.

Modifying the `HTTPRoute` `http-route-foo` and add `RequestHeaderModifier` filter.

```yaml
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: http-route-foo
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  hostnames:
  - foo.example.com
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: httpbin
      port: 8080
    filters:
    - type: RequestHeaderModifier
      requestHeaderModifier:
        add: 
        - name: "header-2-add"
          value: "foo"
EOF
```

Now request the path `/headers` again and you will get the new header injected by gateway.

> **Thought HTTP header name is case insensitive but it will be converted to capital mode.**

```shel
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Header-2-Add": "foo",
    "Host": "10.42.0.15:80",
    "User-Agent": "curl/8.1.2"
  }
}
```

### Set HTTP Request header

`set` operation is used to update the value of specified header. If the header not exist, it will do as `add` operation.

Let's update the `HTTPRoute` resource again and set two headers with new value. One does not exist and another does.

```yaml
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: http-route-foo
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  hostnames:
  - foo.example.com
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: httpbin
      port: 8080
    filters:
    - type: RequestHeaderModifier
      requestHeaderModifier:
        set: 
        - name: "header-2-set"
          value: "foo"
        - name: "user-agent"
          value: "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.0 Safari/605.1.15"
EOF
```

In the response, we can get the two headers updated.

```shell
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Header-2-Set": "foo",
    "Host": "10.42.0.15:80",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/17.0 Safari/605.1.15"
  }
}
```

### Remove HTTP Request header

The last operation is `remove`, which can remove the header of client sending.

Let's update the `HTTPRoute` resource to remove `user-agent` header directly to hide client type from backend service.

```yaml
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: http-route-foo
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  hostnames:
  - foo.example.com
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: httpbin
      port: 8080
    filters:
    - type: RequestHeaderModifier
      requestHeaderModifier:
        remove:
        - "user-agent"
EOF
```

With resource udpated, the user agent is invisible on backend service side.

```shell
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "10.42.0.15:80"
  }
}
```