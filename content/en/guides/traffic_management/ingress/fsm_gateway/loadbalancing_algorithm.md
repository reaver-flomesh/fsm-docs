---
title: "Loadbalancing Algorithm"
description: "FSM Gateway offers various load balancing algorithms like Round Robin, Hashing, and Least Connection in Kubernetes, ensuring efficient traffic distribution and optimal resource utilization."
type: docs
weight: 34
draft: false
---

In microservices and API gateway architectures, load balancing is critical for evenly distributing requests across each service instance and providing mechanisms for high availability and fault recovery. FSM Gateway offers various load balancing algorithms, allowing the selection of the most suitable method based on business needs and traffic patterns.

Multiple load balancing algorithms support efficient traffic distribution, maximizing resource utilization and improving service response times:

- **RoundRobinLoadBalancer**: A common load balancing algorithm where requests are sequentially assigned to each service instance. This is FSM Gateway's default algorithm unless otherwise specified.
- **HashingLoadBalancer**: Calculates a hash value based on certain request attributes (like source IP or headers), routing requests to specific service instances. This ensures the same requester or type of request is always routed to the same service instance.
- **LeastConnectionLoadBalancer**: Considers the current workload (number of connections) of each service instance, allocating new requests to the instance with the least load, ensuring more even resource utilization.

## Prerequisites

- Kubernetes cluster
- kubectl tool

## Environment Setup

### Installing FSM Gateway

Refer to the [Installation Documentation](https://fsm-docs.flomesh.io/guides/traffic_management/ingress/fsm_gateway/installation/#installation) for FSM Gateway. Installation is done using the CLI method.

Download FSM CLI.

```shell
system=$(uname -s | tr '[:upper:]' '[:lower:]')
arch=$(uname -m | sed -E 's/x86_/amd/' | sed -E 's/aarch/arm/')
release=v1.2.0
curl -L https://github.com/flomesh-io/fsm/releases/download/$release/fsm-$release-$system-$arch.tar.gz | tar -vxzf -
./$system-$arch/fsm version
sudo cp ./$system-$arch/fsm /usr/local/bin/fsm
```

Enable FSM Gateway during the FSM installation, which is not enabled by default.

```shell
fsm install \
    --set=fsm.fsmGateway.enabled=true
```

### Deploying a Sample Application

To test load balancing, create two endpoints with different response statuses (200, 201) and content. This is done by creating the Service `pipy`, with two endpoints simulated using the programmable proxy [Pipy](https://github.com/flomesh-io/pipy).

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
    command: ["pipy", "-e", "pipy().listen(8080).serveHTTP(new Message({status: 201},'Hi, world'))"]
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

Check application accessibility. The results show that the gateway balanced the load across the two endpoints using the default round-robin algorithm.

```shell
export GATEWAY_IP=$(kubectl get svc -n server -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
curl http://$GATEWAY_IP:8000/
Hi, world
curl http://$GATEWAY_IP:8000/
Hello, world
curl http://$GATEWAY_IP:8000/
Hi, world
```

## Load Balancing Algorithm Verification

For configuring load balancing strategies, refer to the [LoadBalancerPolicy](https://fsm-docs.flomesh.io/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.LoadBalancerPolicy) documentation.

### Round-Robin Load Balancing

Test with fortio load: Send 200 requests with 50 concurrent users. Responses of status codes 200 and 201 are evenly split, indicative of round-robin load balancing.

```shell
fortio load -quiet -c 50 -n 200 http://$GATEWAY_IP:8000/
Code 200 : 100 (50.0 %)
Code 201 : 100 (50.0 %)
```

### Hashing Load Balancer

Set the load balancing policy to `HashingLoadBalancer`.

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: LoadBalancerPolicy
metadata:
  name: lb-policy-sample
spec:
  targetRef:
    group: ""
    kind: Service
    name: pipy
    namespace: server
  ports:
    - port: 8080
      type: HashingLoadBalancer
EOF
```

Sending the same load, all 200 requests are routed to one endpoint, consistent with the hash-based load balancing.

```shell
fortio load -quiet -c 50 -n 200 http://$GATEWAY_IP:8000/
Code 201 : 200 (50.0 %)
```

### Least Connections Load Balancer

In Kubernetes, multiple endpoints of the same Service usually have the same specifications, so the effect of the least connections algorithm is similar to round-robin.

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: LoadBalancerPolicy
metadata:
  name: lb-policy-sample
spec:
  targetRef:
    group: ""
    kind: Service
    name: pipy
    namespace: server
  ports:
    - port: 8080
      type: LeastConnectionLoadBalancer
EOF
```

Sending the same load, the traffic is evenly distributed across the two endpoints, as expected.

```shell
fortio load -quiet -c 50 -n 200 http://$GATEWAY_IP:8000/
Code 200 : 100 (50.0 %)
Code 201 : 100 (50.0 %)
```
