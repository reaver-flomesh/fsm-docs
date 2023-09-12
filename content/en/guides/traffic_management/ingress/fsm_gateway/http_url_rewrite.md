---
title: "HTTP URL Rewrite"
description: "Rewrite the HTTP URL path."
type: docs
weight: 4
---

The URL rewriting feature provides FSM Gateway users with a way to modify the request URL before the traffic enters the target service. This not only provides greater flexibility to adapt to changes in backend services, but also ensures smooth migration of applications and normalization of URLs.

The [HTTPRoute resource](https://gateway-api.sigs.k8s.io/api-types/httproute) utilizes [HTTPURLRewriteFilter](https://gateway-api.sigs.k8s.io/references/spec/#gateway.networking.k8s.io/v1beta1.HTTPURLRewriteFilter) to rewrite the path in request to another one before it gets forwarded to upstream.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

We will follow the sample in [HTTP Routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/#deploy-example).

In backend server, there is a path `/get` which will responds more information than path `/headers`.

```bash
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

### Replace URL Full Path

Example bellow will replace the `/get` path to `/headers` path.

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
        value: /get
    filters:
    - type: URLRewrite
      urlRewrite: 
        path: 
          type: ReplaceFullPath
          replaceFullPath: /headers
    backendRefs:
    - name: httpbin
      port: 8080          
  - matches:
    - path:
        type: PathPrefix
        value: /        
    backendRefs:
    - name: httpbin
      port: 8080
```

After updated the HTTP rule, we will get the same response as `/headers` when requesting `/get`.

```bash
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/get
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "foo.example.com",
    "User-Agent": "curl/7.68.0"
  }
}
```

### Replace URL Prefix Path

In backend server, there is another path `/status/{statusCode}` which will respond with specified status code.

```bash
curl -s -w "%{response_code}\n" -H 'host:foo.example.com' http://$GATEWAY_IP:8000/status/204
204
curl -s -w "%{response_code}\n" -H 'host:foo.example.com' http://$GATEWAY_IP:8000/status/20
0
200
```

If we hope to change all `20x` codes to `200`, the rule is required to update again.

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
        value: /status/20
    filters:
    - type: URLRewrite
      urlRewrite: 
        path: 
          type: ReplacePrefixMatch
          replacePrefixMatch: /status/20
    backendRefs:
    - name: httpbin
      port: 8080          
  - matches:
    - path:
        type: PathPrefix
        value: /        
    backendRefs:
    - name: httpbin
      port: 8080
```

Update the rule again, we will get `200` response even we specify `204`.

```bash
curl -s -w "%{response_code}\n" -H 'host:foo.example.com' http://$GATEWAY_IP:8000/status/204
200
```

### Replace Host Name

Let's follow the example rule below. It will replace host name from `foo.example.com` to `baz.example.com` for all traffic requesting `/get`.

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
        value: /get
    filters:
    - type: URLRewrite
      urlRewrite: 
        hostname: baz.example.com
    backendRefs:
    - name: httpbin
      port: 8080          
  - matches:
    - path:
        type: PathPrefix
        value: /        
    backendRefs:
    - name: httpbin
      port: 8080
```

Update rule and trigger request. We can see the client is requesting url `http://foo.example.com/get`, but the `Host` is replaced.

```bash
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/get
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "baz.example.com",
    "User-Agent": "curl/7.68.0"
  },
  "origin": "10.42.0.87",
  "url": "http://foo.example.com/get"
```