---
title: "Retry"
description: "This document introduces the speed limiting function, including speed limiting based on ports, domain names, and routes."
type: docs
weight: 26
draft: false
---

The retry functionality of a gateway is a crucial network communication mechanism designed to enhance the reliability and fault tolerance of system service calls. This feature allows the gateway to automatically resend a request if the initial request fails, thereby reducing the impact of temporary issues (such as network fluctuations, momentary service overloads, etc.) on the end-user experience.

The working principle is, when the gateway sends a request to a downstream service and encounters specific types of failures (such as connection errors, timeouts, 5xx series errors, etc.), it attempts to resend the request based on pre-set policies instead of immediately returning the error to the client.

## Prerequisites

- Kubernetes cluster
- kubectl tool
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploying Example Application

We use the [fortio server](https://github.com/fortio/fortio) as the example application, which allows defining response status codes and their occurrence probabilities through the `status` request parameter.

```shell
kubectl create namespace server
kubectl apply -n server -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: fortio
  labels:
    app: fortio
    service: fortio
spec:
  ports:
  - port: 8080
    name: http-8080
  selector:
    app: fortio
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fortio
spec:
  replicas: 1
  selector:
    matchLabels:
      app: fortio
  template:
    metadata:
      labels:
        app: fortio
    spec:
      containers:
      - name: fortio
        image: fortio/fortio:latest_release
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
EOF
```

### Creating Gateway and Route

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm-gateway-cls
  listeners:
  - protocol: HTTP
    port: 8000
    name: http
    allowedRoutes:
      namespaces:
        from: Same
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: fortio-route
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: fortio
      port: 8080
EOF
```

Check if the application is accessible.

```shell
export GATEWAY_IP=$(kubectl get svc -n server -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')

curl -i http://$GATEWAY_IP:8000/echo
HTTP/1.1 200 OK
date: Fri, 05 Jan 2024 07:02:17 GMT
content-length: 0
connection: keep-alive
```

## Testing Retry Strategy

Before setting the retry strategy, add the parameter `status=503:10` to make the fortio server have a 10% chance of returning 503. Using `fortio load` to generate load, sending 100 requests will see nearly 10% are 503 responses.

```shell
fortio load -quiet -c 1 -n 100 http://$GATEWAY_IP:8000/echo\?status\=503:10

Code 200 : 89 (89.0 %)
Code 503 : 11 (11.0 %)
All done 100 calls (plus 0 warmup) 1

.054 ms avg, 8.0 qps
```

Then set the retry strategy.

- `targetRef` specifies the target resource for the policy, which in the retry policy can only be a `Service` in K8s `core` or `ServiceImport` in `flomesh.io` (the latter for multi-cluster). Here we specify the `fortio` in namespace `server`.
- `ports` is the list of service ports, as the service may expose multiple ports, different ports can have different retry strategies.
	- `port` is the service port, set to `8080` for the `fortio` service in this example.
	- `config` is the core configuration of the retry policy.
		- `retryOn` is the list of response codes that are retryable, e.g., 5xx matches 500-599, or 500 matches only 500.
		- `numRetries` is the number of retries.
		- `backoffBaseInterval` is the base interval for calculating backoff (in seconds), i.e., the waiting time between consecutive retry requests. It's mainly to avoid additional pressure on services that are experiencing problems.

For detailed retry policy configuration, refer to the official documentation [RetryPolicy](/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.RetryPolicy).

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: RetryPolicy
metadata:
  name: retry-policy-sample
spec:
  targetRef:
    kind: Service
    name: fortio
    namespace: server
  ports:
  - port: 8080
    config:
      retryOn:
      - 5xx
      numRetries: 5
      backoffBaseInterval: 2
EOF
```

After the policy takes effect, send the same 100 requests, and you can see all are 200 responses. Note that the average response time has increased due to the added time for retries.

```shell
fortio load -quiet -c 1 -n 100 http://$GATEWAY_IP:8000/echo\?status\=503:10

Code 200 : 100 (100.0 %)
All done 100 calls (plus 0 warmup) 160.820 ms avg, 5.8 qps
```