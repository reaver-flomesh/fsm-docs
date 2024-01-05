---
title: "Access Control"
description: "This doc will demonstrate how to control the access to backend services with blakclist and whitelist."
type: docs
weight: 24
draft: false
---

Blacklist and whitelist functionality is an effective network security mechanism used to control and manage network traffic. This feature relies on a predefined list of rules to determine which entities (IP addresses or IP ranges) are allowed or denied passage through the gateway. The gateway uses blacklists and whitelists to filter incoming network traffic. This method provides simple and direct access control, easy to manage, and effectively prevents known security threats.

As the entry point for cluster traffic, the FSM Gateway manages all traffic entering the cluster. By setting blacklist and whitelist access control policies, it can filter traffic entering the cluster.

FSM Gateway provides two granularities of access control, both targeting L7 HTTP protocol:

  1. **Domain-level access control**: A network traffic management strategy based on domain names. It involves implementing access rules for traffic that meets specific domain name conditions, such as allowing or blocking communication with certain domain names.
  2. **Route-level access control**: A management strategy based on routes (request headers, methods, paths, parameters), where access control policies are applied to specific routes to manage traffic accessing those routes.

Next, we will demonstrate the use of blacklist and whitelist access control.

## Prerequisites

  * Kubernetes cluster version v1.21.0 or higher.
  * kubectl CLI
  * FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploying a Sample Application

Next, deploy a sample application using the commonly used httpbin service, and create [Gateway](https://gateway-api.sigs.k8s.io/api-types/gateway/) and [HTTP Route (HttpRoute)](https://gateway-api.sigs.k8s.io/api-types/httproute/).

```shell
kubectl create namespace httpbin
kubectl apply -n httpbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/http-routing.yaml
```

Check the gateway and HTTP routes; you should see two routes with different domain names created.

```shell
kubectl get gateway,httproute -n httpbin
NAME                                                   CLASS             ADDRESS   PROGRAMMED   AGE
gateway.gateway.networking.k8s.io/simple-fsm-gateway   fsm-gateway-cls             Unknown      3s

NAME                                                 HOSTNAMES             AGE
httproute.gateway.networking.k8s.io/http-route-foo   ["foo.example.com"]   2s
httproute.gateway.networking.k8s.io/http-route-bar   ["bar.example.com"]   2s
```

Verify if the HTTP routing is effective by accessing the application.

```shell
export GATEWAY_IP=$(kubectl get svc -n httpbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')

curl http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "10.42.0.15:80",
    "User-Agent": "curl/7.81.0"
  }
}
```

### Domain-Based Access Control

With domain-based access control, we can set one or more domain names in the policy and add a blacklist or whitelist for these domains.

For example, in the policy below:

- `targetRef` is a reference to the target resource for which the policy is applied, which is the `HTTPRoute` resource for HTTP requests.
- Through the `hostname` field, we add a blacklist or whitelist policy for `foo.example.com` among the two domains.
- With the prevalence of cloud services and distributed network architectures, the direct connection to the gateway is no longer the client but an intermediate proxy. In such cases, we usually use the HTTP header `X-Forwarded-For` to identify the client's IP address. In FSM Gateway's policy, the `enableXFF` field controls whether to obtain the client's IP address from the `X-Forwarded-For` header.
- For denied communications, customize the response with `statusCode` and `message`.

For detailed configuration, please refer to [AccessControlPolicy API Reference](/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.AccessControlPolicy).

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: AccessControlPolicy
metadata:
  name: access-control-sample
spec:
  targetRef:
    group: gateway.networking.k8s.io
    kind: HTTPRoute
    name: http-route-foo
    namespace: httpbin
  hostnames:
    - hostname: foo.example.com
      config: 
        blacklist:
          - 192.168.0.0/24
        whitelist:
          - 112.94.5.242
        enableXFF:

 true
        statusCode: 403
        message: "Forbidden"
EOF
```

After the policy is effective, we send requests for testing, remembering to add `X-Forwarded-For` to specify the client IP.

```shell
curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'  -H 'x-forwarded-for:112.94.5.242'
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Fri, 29 Dec 2023 02:29:08 GMT
content-type: application/json
content-length: 139
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive

curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'  -H 'x-forwarded-for: 10.42.0.1'
HTTP/1.1 403 Forbidden
content-length: 9
connection: keep-alive
```

From the results, **when both a whitelist and a blacklist are present, the blacklist configuration will be ignored**.

### Route-Based Access Control

Route-based access control allows us to set access control policies for specific routes (path, request headers, method, parameters) to restrict access to these particular routes.

Before setting up the access control policy, we add a route with the path prefix `/headers` under the HTTP route `foo.example.com` to facilitate the configuration of access control for it.

```shell
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
        value: /headers
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
EOF
```

In the following policy:

- The `match` is used to configure the routes to be matched, here we use path matching.
- Other configurations continue to use the settings from above.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: AccessControlPolicy
metadata:
  name: access-control-sample
spec:
  targetRef:
    group: gateway.networking.k8s.io
    kind: HTTPRoute
    name: http-route-foo
    namespace: httpbin
  http:
    - match:
        path:
          type: PathPrefix
          value: /headers
      config: 
        blacklist:
          - 192.168.0.0/24
        whitelist:
          - 112.94.5.242
        enableXFF: true
        statusCode: 403
        message: "Forbidden"
EOF
```

After updating the policy, we send requests to test. For the path `/headers`, the results are as before.

```shell
curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'  -H 'x-forwarded-for:112.94.5.242'
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Fri, 29 Dec 2023 02:39:02 GMT
content-type: application/json
content-length: 139
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive

curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'  -H 'x-forwarded-for: 10.42.0.1'
HTTP/1.1 403 Forbidden
content-length: 9
connection: keep-alive
```

However, if the path `/get` is accessed, there are no restrictions.

```shell
curl -I http://$GATEWAY_IP:8000/get -H 'host:foo.example.com'  -H 'x-forwarded-for: 10.42.0.1'
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Fri, 29 Dec 2023 02:40:18 GMT
content-type: application/json
content-length: 230
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```

This demonstrates the effectiveness and specificity of route-based access control in managing access to different routes within a network infrastructure.