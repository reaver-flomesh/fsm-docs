---
title: "HTTP Header Modifier"
description: "Modify HTTP Request or Response header via Plugins"
type: docs
weight: 2
---

In daily network interactions, HTTP headers play a very important role. They can pass various information about the request or response, such as authentication, cache control, content type, etc. This allows users to precisely control incoming and outgoing request and response headers to meet various security, performance and business needs.

FSM does not provide HTTP header control functionality out of box, but we can provide with [Plugin Extending feature](/guides/operating/plugins/) easily.

### Enable plugin policy mode

To utilize plugin to extend mesh, we should enable the plugin policy mode first, because it's disabled by default.

Execute the command below to enable it.

```bash
kubectl patch meshconfig fsm-mesh-config -n "$fsm_namespace" -p '{"spec":{"featureFlags":{"enablePluginPolicy":true}}}' --type=merge
```

### Deploy sample application

Deploy client application:

```bash
# Create the curl namespace
kubectl create namespace curl
# Add the namespace to the mesh
fsm namespace add curl
# Deploy curl client in the curl namespace
kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/plugins/curl.yaml -n curl
```

Deploy service application:

```bash
# Create a namespace
kubectl create ns httpbin
# Add the namespace to the mesh
fsm namespace add httpbin
# Deploy the application
kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/httpbin/httpbin.yaml -n httpbin
```

Check the communication between apps.

```shell
curl_client="$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items[0].metadata.name}')"
kubectl exec ${curl_client} -n curl -c curl -- curl -s http://httpbin.httpbin:14001/headers
```

### Declaring a plugin

For header modification, we provide a script which can be applied with command bellow.

```bash
kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/plugins/header-modifier.yaml
```

Check the plugin declared or not.

```bash
kubectl get plugin
NAME              AGE
header-modifier   5s
```

### Setting up plugin-chain

Following the [PluginChain API doc](/api_reference/plugin/v1alpha1/#plugin.flomesh.io/v1alpha1.PluginChain), the new plugin works in chain `inbound-http` and available on resources which is labelled by `app=httpbin` or `app=curl` in namespace monitored by mesh.

```shell
kubectl apply -f - <<EOF
kind: PluginChain
apiVersion: plugin.flomesh.io/v1alpha1
metadata:
  name: header-modifier-chain
  namespace: pipy
spec:
  chains:
    - name: inbound-http
      plugins:
        - header-modifier
  selectors:
    podSelector:
      matchExpressions:
        - key: app
          operator: In
          values:
            - httpbin
            - curl
    namespaceSelector:
      matchExpressions:
        - key: flomesh.io/monitored-by
          operator: In
          values: ["fsm"]
EOF
```

### Apply plugin configuration

Once applied `Plugin` and `PluginChain`, we need to configure it. In the command bellow, we make Service `httpbin` to modify HTTP headers for request owning header `version=v2`. When route matched, the plugin will remove header `version` and add a new header `x-canary-tag=v2 ` from/in request, and add a new header `x-carary=true` in response.

```shell
kubectl apply -f - <<EOF
kind: PluginConfig
apiVersion: plugin.flomesh.io/v1alpha1
metadata:
  name: header-modifier-config
  namespace: httpbin
spec:
  config:
    Matches:
    - Headers:
        Exact:
          version: 'v2'
      Filters: 
      - Type: RequestHeaderModifier
        RequestHeaderModifier:
          Remove:
          - version
          Add:
          - Name: x-canary-tag
            Value: v2
      - Type: ResponseHeaderModifier
        ResponseHeaderModifier:
          Add:
          - Name: x-canary
            Value: 'true'
  plugin: header-modifier
  destinationRefs:
    - kind: Service
      name: httpbin
      namespace: httpbin
EOF
```

### Testing

Let's send a request again, but attach a header this time.

```shell
kubectl exec ${curl_client} -n curl -c curl -- curl -ksi http://httpbin.httpbin:14001/headers -H "version:v2"
```

In the request which `httpbin` received, the header `version` is removed and new header `X-Canary-Tag` appeared.

In response, we can get the new header `x-canary`.

```
HTTP/1.1 200 OK
server: gunicorn
date: Fri, 01 Dec 2023 10:37:14 GMT
content-type: application/json
access-control-allow-origin: *
access-control-allow-credentials: true
x-canary: true
content-length: 211
connection: keep-alive

{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "httpbin.httpbin:14001",
    "Serviceidentity": "curl.curl",
    "User-Agent": "curl/8.4.0",
    "X-Canary-Tag": "v2"
  }
}
```