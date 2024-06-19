---
title: "Health Check"
description: "Gateway health checks in Kubernetes ensure traffic is directed only to healthy services, enhancing system availability and resilience by isolating unhealthy endpoints in microservices."
type: docs
weight: 32
draft: false
---

Gateway health check is an automated monitoring mechanism that regularly checks and verifies the health of backend services, ensuring traffic is only forwarded to those services that are healthy and can handle requests properly. This feature is crucial in microservices or distributed systems, as it maintains high availability and resilience by promptly identifying and isolating faulty or underperforming services.

Health checks enable gateways to ensure that request loads are effectively distributed to well-functioning services, thereby improving the overall system stability and response speed.

## Prerequisites

- Kubernetes cluster
- kubectl tool
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploying a Sample Application

To test the health check functionality, create two endpoints with different health statuses. This is achieved by creating the Service `pipy`, with two endpoints simulated using the programmable proxy [Pipy](https://github.com/flomesh-io/pipy).

```shell
kubectl create namespace server
kubectl apply -n server -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: pipy
spec:
  selector:
    app: pipy
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
---
apiVersion: v1
kind: Pod
metadata:
  name: pipy-1
  labels:
    app: pipy
spec:
  containers:
  - name: pipy
    image: flomesh/pipy:0.99.0-2
    command: ["pipy", "-e", "pipy().listen(8080).serveHTTP(new Message({status: 200},'Hello, world'))"]
---
apiVersion: v1
kind: Pod
metadata:
  name: pipy-2
  labels:
    app: pipy
spec:
  containers:
  - name: pipy
    image: flomesh/pipy:0.99.0-2
    command: ["pipy", "-e", "pipy().listen(8080).serveHTTP(new Message({status: 503},'Service unavailable'))"]
EOF
```

### Creating Gateway and Routes

Next, create a gateway and set up routes for the Service pipy.

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm
  listeners:
  - protocol: HTTP
    port: 8000
    name: http
    allowedRoutes:
      namespaces:
        from: Same
---
apiVersion: gateway.networking.k8s.io/v1
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
    - name: pipy
      port: 8080
EOF
```

Check if the application is accessible. The results show that the gateway has balanced the load between a healthy endpoint and an unhealthy one.

```shell
export GATEWAY_IP=$(kubectl get svc -n server -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
curl -o /dev/null -s -w '%{http_code}' http://$GATEWAY_IP:8000/
200
curl -o /dev/null -s -w '%{http_code}' http://$GATEWAY_IP:8000/
503
```

## Testing Health Check Functionality

Next, configure the health check policy.

- `targetRef` specifies the target resource for the policy, which can only be a K8s `core` `Service`. Here, the `pipy` in the `server` namespace is specified.
- `ports` is a list of service ports, as a service may expose multiple ports, allowing different ports to set retry strategies.
	- `port` is the service port, set to `8080` for the `pipy` service in this example.
	- `config` is the core configuration of the policy.

		* `interval`: Health check interval, indicating the time interval at which the system performs health checks on backend services.
		* `maxFails`: Maximum failure count, defining the consecutive health check failures allowed before marking an upstream service as unavailable. This is a key parameter as it determines the system's tolerance before deciding a service is unhealthy.
		* `failTimeout`: Failure timeout, defining the length of time an upstream service will be temporarily disabled after being marked unhealthy. This means that even if the service becomes available again, it will be considered unavailable by the proxy during this period.
		* `path`: Health check path, used for the path in HTTP health checks.
		* `matches`: Matching conditions, used to determine the success or failure of HTTP health checks. This field can contain multiple conditions, such as expected HTTP status codes, response body content, etc.
			* `statusCodes`: A list of HTTP response status codes to match, such as `[200,201,204]`.
			* `body`: The body content of the HTTP response to match.
			* `headers`: The header information of the HTTP response to match. This field is optional.
				* `name`: It defines the specific field name you want to match in the HTTP response header. For example, to check the value of the `Content-Type` header, you would set `name` to `Content-Type`. This field is only valid when `Type` is set to `headers` and should not be set in other cases. This field is optional.
				* `value`: The expected matching value. Defines the expected match value. For example,

For detailed policy configuration, refer to the official documentation [HealthCheckPolicy](/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.HealthCheckPolicy).

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: HealthCheckPolicy
metadata:
  name: health-check-policy-sample
spec:
  targetRef:
    group: ""
    kind: Service
    name: pipy
    namespace: server
  ports:
  - port: 8080
    config: 
      interval: 10
      maxFails: 3
      failTimeout: 1
      path: /healthz
      matches:
      - statusCodes: 
        - 200
        - 201
EOF
```

After this configuration, multiple requests consistently return a 200 response, indicating the unhealthy endpoint has been isolated by the gateway.

```shell
curl -o /dev/null -s -w '%{http_code}' http://$GATEWAY_IP:8000/
200
curl -o /dev/null -s -w '%{http_code}' http://$GATEWAY_IP:8000/
200
curl -o /dev/null -s -w '%{http_code}' http://$GATEWAY_IP:8000/
200
```
