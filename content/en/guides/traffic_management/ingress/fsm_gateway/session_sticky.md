---
title: "Session Sticky"
description: "Session sticky in gateways ensures user requests are directed to the same server, enhancing user experience and transaction integrity, typically implemented using cookies."
type: docs
weight: 30
draft: false
---

Session sticky in a gateway is a network technology designed to ensure that a user's consecutive requests are directed to the same backend server over a period of time. This functionality is particularly crucial in scenarios requiring user state maintenance or continuous interaction, such as maintaining online shopping carts, keeping users logged in, or handling multi-step transactions.

Session sticky plays a key role in enhancing website performance and user satisfaction by providing a consistent user experience and maintaining transaction integrity. It is typically implemented using client identification information like Cookies or server-side IP binding techniques, thereby ensuring request continuity and effective server load balancing.

## Prerequisites

- Kubernetes cluster
- kubectl tool
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploying a Sample Application

To verify the session sticky feature, create the Service `pipy`, and set up two endpoints with different responses. These endpoints are simulated using the programmable proxy [Pipy](https://github.com/flomesh-io/pipy).

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
    - name: pipy
      port: 8080
EOF
```

Check if the application is accessible. Results show that the gateway has balanced the load across two endpoints.

```shell
export GATEWAY_IP=$(kubectl get svc -n server -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')

curl http://$GATEWAY_IP:8000/
Service unavailable

curl http://$GATEWAY_IP:8000/
Hello, world
```

## Testing Session Sticky Strategy

Next, configure the session sticky strategy.

- `targetRef` specifies the target resource for the policy. In this policy, the target resource can only be a K8s `core` `Service`. Here, the `pipy` in the `server` namespace is specified.
- `ports` is a list of service ports, as a service may expose multiple ports, allowing different ports to set retry strategies.
	- `port` is the service port, set to `8080` for the `pipy` service in this example.
	- `config` is the core configuration of the strategy.
		- `cookieName` is the name of the cookie used for session sticky via cookie-based load balancing. This field is optional, but when cookie-based session sticky is enabled, it defines the name of the cookie storing backend server information, such as `_srv_id`. This means that when a user first visits the application, a cookie named `_srv_id` is set, typically corresponding to a backend server. When the user revisits, this cookie ensures their requests are routed to the same server as before.
		- `expires` is the lifespan of the cookie during session sticky. This defines how long the cookie will last, i.e., how long the user's consecutive requests will be directed to the same backend server.

For detailed configuration, refer to the official documentation [SessionStickyPolicy](/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.SessionStickyPolicy).

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: SessionStickyPolicy
metadata:
  name: session-sticky-policy-sample
spec:
  targetRef:
    group: ""
    kind: Service
    name: pipy
    namespace: server
  ports:
  - port: 8080
    config:
      cookieName: _srv_id
      expires: 600
EOF
```

After creating the policy, send requests again. By adding the option `-i`, you can see the cookie information added in the response header.

```shell
curl -i http://$GATEWAY_IP:8000/
HTTP/1.1 200 OK
set-cookie: _srv_id=7252425551334343; path=/; expires=Fri,  5 Jan 2024 19:15:23 GMT; max-age=600
content-length: 12
connection: keep-alive

Hello, world
```

Send 3 requests next, adding the cookie information from the above response with the `-b` parameter. All 3 requests receive the same response, indicating that the session sticky feature is effective.

```shell
curl -b _srv_id=7252425551334343 http://$GATEWAY_IP:8000/
Hello, world

curl -b _srv_id=7252425551334343 http://$GATEWAY_IP:8000/
Hello, world

curl -b _srv_id=7252425551334343 http://$GATEWAY_IP:8000/
Hello, world
```
