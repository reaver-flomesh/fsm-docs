---
title: "HTTP Response Header Manipulate"
description: ""
type: docs
weight: 7
draft: false
---

The HTTP header manipulation feature allows you to fine-tune incoming and outgoing request and response headers. 

In Gateway API, the HTTPRoute resource utilities two [`HTTPHeaderFilter` filter](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1.HTTPHeaderFilter) for request and response header manipulation.

The both filters supports `add`, `set` and `remove` operation. The combination of them is also available.

This document will introduce the HTTP response header manipulation function of FSM Gateway. The introduction of HTTP request header manipulation is located in doc [HTTP Request Header Manipulate](/guides/traffic_management/ingress/fsm_gateway/http_request_header_manipulate).

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

We will follow the sample in [HTTP Routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/#deploy-example).

In backend service responds the generated headers as below.=

```shell
curl -I -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Tue, 21 Nov 2023 08:54:43 GMT
content-type: application/json
content-length: 106
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```

### Add HTTP Response header

With header adding feature, let's try to add a new header to response by add `HTTPHeaderFilter` filter.

Modifying the `HTTPRoute` `http-route-foo` and add `ResponseHeaderModifier` filter.

```yaml
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
    - type: ResponseHeaderModifier
      responseHeaderModifier:
        add: 
        - name: "header-2-add"
          value: "foo"
```

Now request the path `/headers` again and you will get the new header in response injected by gateway.

```shel
curl -I -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Tue, 21 Nov 2023 08:56:58 GMT
content-type: application/json
content-length: 139
access-control-allow-origin: *
access-control-allow-credentials: true
header-2-add: foo
connection: keep-alive
```

### Set HTTP Response header

`set` operation is used to update the value of specified header. If the header not exist, it will do as `add` operation.

Let's update the `HTTPRoute` resource again and set two headers with new value. One does not exist and another does.

```yaml
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
    - type: ResponseHeaderModifier
      responseHeaderModifier:
        set: 
        - name: "header-2-set"
          value: "foo"
        - name: "server"
          value: "fsm/gateway"
```

In the response, we can get the two headers updated.

```shell
curl -I -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
HTTP/1.1 200 OK
server: fsm/gateway
date: Tue, 21 Nov 2023 08:58:56 GMT
content-type: application/json
content-length: 139
access-control-allow-origin: *
access-control-allow-credentials: true
header-2-set: foo
connection: keep-alive
```

### Remove HTTP Response header

The last operation is `remove`, which can remove the header of client sending.

Let's update the `HTTPRoute` resource to remove `server` header directly to hide backend implementation from client.

```yaml
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
    - type: ResponseHeaderModifier
      responseHeaderModifier:
        remove:
        - "server"
```

With resource udpated, the backend server implementation is invisible on client side.

```shell
curl -I -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
HTTP/1.1 200 OK
date: Tue, 21 Nov 2023 09:00:32 GMT
content-type: application/json
content-length: 139
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```