---
title: "HTTP Redirect"
description: "This document discusses FSM Gateway's request redirection, covering host name, prefix path, and full path redirects, with examples of each method."
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

To facilitate testing, it's better to add records to local hosts.

```bash
echo $GATEWAY_IP foo.example.com bar.example.com >> /etc/hosts
-bash: /etc/hosts: Permission denied
```

```bash
curl foo.example.com/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "foo.example.com",
    "User-Agent": "curl/7.68.0"
  }
}
curl bar.example.com/get
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "bar.example.com",
    "User-Agent": "curl/7.68.0"
  },
  "origin": "10.42.0.87",
  "url": "http://bar.example.com/get"
}
```

### Host Name Redirect

The HTTP status code `3XX` are used to redirect client to another address. We can redirect all requests to `foo.example.com` to `bar.example.com` by responding `301` status and new hostname.

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

Now, it will return the `301` code and `bar.example.com:8000` when requesting `foo.example.com`.

```bash
curl -i http://foo.example.com:8000/get
HTTP/1.1 301 Moved Permanently
Location: http://bar.example.com:8000/get
content-length: 0
connection: keep-alive
```

By default, curl does not follow location redirecting unless enable it by assign opiton `-L`.

```bash
curl -L http://foo.example.com:8000/get
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "bar.example.com:8000",
    "User-Agent": "curl/7.68.0"
  },
  "origin": "10.42.0.161",
  "url": "http://bar.example.com:8000/get"
}
```

### Prefix Path Redirect

With path redirection, we can implement what we did with [URL Rewriting](/guides/traffic_management/ingress/fsm_gateway/http_url_rewrite/#replace-url-prefix-path): redirect the request to `/status/{n}` to `/stream/{n}`.

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
        value: /status
    filters:
    - type: RequestRedirect
      requestRedirect:
        path:
          type: ReplacePrefixMatch
          replacePrefixMatch: /stream
        statusCode: 301
    backendRefs:
    - name: httpbin
      port: 8080
  - matches:
    backendRefs:
    - name: httpbin
      port: 8080
```

After update rull, we will get.

```shell
curl -i http://foo.example.com:8000/status/204
HTTP/1.1 301 Moved Permanently
Location: http://foo.example.com:8000/stream/204
content-length: 0
connection: keep-alive
```

### Full Path Redirect

We can also change full path during redirecting, such as redirect all `/status/xxx` to `/status/200`.

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
        value: /status
    filters:
    - type: RequestRedirect
      requestRedirect:
        path:
          type: ReplaceFullPath
          replaceFullPath: /status/200
        statusCode: 301
    backendRefs:
    - name: httpbin
      port: 8080
  - matches:
    backendRefs:
    - name: httpbin
      port: 8080      
```

Now, the status of requests to `/status/xxx` will be redirected to `/status/200`.

```bash
curl -i http://foo.example.com:8000/status/204
HTTP/1.1 301 Moved Permanently
Location: http://foo.example.com:8000/status/200
content-length: 0
connection: keep-alive
```