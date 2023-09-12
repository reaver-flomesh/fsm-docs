---
title: "HTTP Redirect"
description: "Redirect HTTP Traffic to another location."
type: docs
weight: 6

---

Request redirection is a common network application function that allows the server to tell the client: "The resource you requested has been moved to another location, please go to the new location to obtain it."

The [HTTPRoute resource](https://gateway-api.sigs.k8s.io/api-types/httproute) utilizes [HTTPRequestRedirectFilter](https://gateway-api.sigs.k8s.io/references/spec/#gateway.networking.k8s.io/v1beta1.HTTPRequestRedirectFilter) to redirect client to the specified new location.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

We will follow the sample in [HTTP Routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/#deploy-example).

In our backend server, there are two paths `/headers` and `/get`. The previous one responds all request headers as body, and the latter one responds more information of client than `/headers`.

```bash
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "foo.example.com",
    "User-Agent": "curl/7.68.0"
  }
}
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/get
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "foo.example.com",
    "User-Agent": "curl/7.68.0"
  },
  "origin": "10.42.0.87",
  "url": "http://foo.example.com/get"
}
```

### Host Name Redirect

```bash
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
    filters:
    - type: RequestRedirect
      requestRedirect:
        hostname: bar.example.com
        port: 8000
        statusCode: 301
    backendRefs:
    - name: httpbin
      port: 8080
```