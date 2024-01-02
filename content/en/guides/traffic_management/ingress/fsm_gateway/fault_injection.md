---
title: "Fault Injection"
description: "This document will introduce how to inject specific faults at the gateway level to test the behavior and stability of the system."
type: docs
weight: 22
draft: false
---

The fault injection feature is a powerful testing mechanism used to enhance the robustness and reliability of microservice architectures. This capability tests a system's fault tolerance and recovery mechanisms by simulating network-level failures such as delays and error responses. Fault injection mainly includes two types: delayed injection and error injection.

- **Delay injection** simulates network delays or slow service processing by artificially introducing delays during the gateway's processing of requests. This helps test whether the timeout handling and retry strategies of downstream services are effective, ensuring that the entire system can maintain stable operation when actual delays occur.

- **Error injection** simulates a backend service failure by having the gateway return an error response (such as HTTP 5xx errors). This method can verify the service consumer's handling of failures, such as whether error handling logic and fault tolerance mechanisms, such as circuit breaker mode, are correctly executed.

FSM Gateway supports these two types of fault injection and provides two types of granular fault injection: domain and routing. Next, we will show you the fault injection of FSM Gateway through a demonstration.

## Prerequisites

  * Kubernetes cluster version v1.21.0 or higher.
  * kubectl CLI
  * FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration
  
### Deploy Sample Application

Next, deploy the sample application, use the commonly used httpbin service, and create [Gateway](https://gateway-api.sigs.k8s.io/api-types/gateway/) and [HTTP Route (HttpRoute)] (https://gateway-api.sigs.k8s.io/api-types/httproute/).

```shell
kubectl create namespace httpbin
kubectl apply -n httpbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/http-routing.yaml
```

Confirm Gateway and HTTPRoute created. You will get two HTTP routes with different domain.

```shell
kubectl get gateway,httproute -n httpbin
NAME                                                   CLASS             ADDRESS   PROGRAMMED   AGE
gateway.gateway.networking.k8s.io/simple-fsm-gateway   fsm-gateway-cls             Unknown      3s

NAME                                                 HOSTNAMES             AGE
httproute.gateway.networking.k8s.io/http-route-foo   ["foo.example.com"]   2s
httproute.gateway.networking.k8s.io/http-route-bar   ["bar.example.com"]   2s
```

Check if you can reach service via gateway.

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

## Fault Injection Testing

### Route-Level Fault Injection

We add a route under the HTTP route `foo.example.com` with a path prefix `/headers` to facilitate setting fault injection.

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

When we request the `/headers` and `/get` paths, we can get the correct response.

Next, we inject a `404` fault with a `100%` probability on the `/headers` route.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: FaultInjectionPolicy
metadata:
  name: fault-injection
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
      abort:
        percent: 100
        statusCode: 404
EOF
```

Now, requesting `/headers` results in a `404` response.

```shell
curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'
HTTP/1.1 404 Not Found
content-length: 0
connection: keep-alive
```

Requesting `/get` will not be affected.

```shell
curl -I http://$GATEWAY_IP:8000/get -H 'host:foo.example.com'
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Thu, 14 Dec 2023 14:11:36 GMT
content-type: application/json
content-length: 220
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```

### Domain-Level Fault Injection

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: FaultInjectionPolicy
metadata:
  name: fault-injection
spec:
  targetRef:
    group: gateway.networking.k8s.io
    kind: HTTPRoute
    name: http-route-foo
    namespace: httpbin
  hostnames:
    - hostname: foo.example.com
      config: 
        abort:
          percent: 100
          statusCode: 404
EOF
```

Requesting `foo.example.com` returns a `404` response.

```shell
curl -I http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com'
HTTP/1.1 404 Not Found
content-length: 0
connection: keep-alive
```

However, requesting `bar.example.com`, which is not listed in the fault injection, responds normally.

```
curl -I http://$GATEWAY_IP:8000/headers -H 'host:bar.example.com'
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Thu, 14 Dec 2023 13:55:07 GMT
content-type: application/json
content-length: 140
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```

Modify the fault injection policy to change the error fault to a delay fault: introducing a random delay of 500 to 1000 ms.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: FaultInjectionPolicy
metadata:
  name: fault-injection
spec:
  targetRef:
    group: gateway.networking.k8s.io
    kind: HTTPRoute
    name: http-route-foo
    namespace: httpbin
  hostnames:
    - hostname: foo.example.com
      config: 
        delay:
          percent: 100
          range: 
            min: 500
            max: 1000
          unit: ms
EOF
```

Check the response time of the requests to see the introduced random delay.

```shell
time curl -s http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com' > /dev/null

real	0m0.904s
user	0m0.000s
sys	0m0.010s

time curl -s http://$GATEWAY_IP:8000/headers -H 'host:foo.example.com' > /dev/null

real	0m0.572s
user	0m0.005s
sys	0m0.005s
```