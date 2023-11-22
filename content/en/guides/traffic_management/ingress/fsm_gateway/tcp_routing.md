---
title: "TCP Routing"
description: ""
type: docs
weight: 13
draft: false
---

This document will describe how to configure FSM Gateway to load balance TCP traffic.

During the L4 load balancing process, FSM Gateway determines which backend server to distribute traffic to based mainly on network layer and transport layer information, such as IP address and port number. This approach allows the FSM Gateway to make decisions quickly and forward traffic to the appropriate server, thereby improving overall network performance.

If you want to load balance HTTP traffic, please refer to the document [HTTP Routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/).

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploy sample

First, let's install the example in namespace `httpbin` with commands below.

```bash
kubectl create namespace httpbin
kubectl apply -n httpbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/tcp-routing.yaml
```

The command above will create `Gateway` and `TCPRoute` resources except for sample app `ht tpbin`.

In `Gateway`, there are two `listener` defined listening on ports `8000` and `8001`.

```yaml
listeners:
- protocol: TCP
  port: 8000
  name: foo
  allowedRoutes:
    namespaces:
      from: Same
- protocol: TCP
  port: 8001
  name: bar
  allowedRoutes:
    namespaces:
      from: Same 
```

The `TCPRoute` mapping to backend service `httpbin` is bound to the two ports defined above.

```yaml
parentRefs:
- name: simple-fsm-gateway
  port: 8000
- name: simple-fsm-gateway
  port: 8001    
rules:
- backendRefs:
  - name: httpbin
    port: 8080
```

This means we should reach backend service via either of two ports.

### Testing

Let's record the IP address of Gateway first.

```bash
export GATEWAY_IP=$(kubectl get svc -n httpbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

Sending a request to port `8000` of gateway and it will forward the traffic to backend service.

```shell
curl http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Host": "20.24.88.85:8000",
    "User-Agent": "curl/8.1.2"
  }
```

With gatweay port 8081, it works fine too.

```shell
curl http://$GATEWAY_IP:8001/headers
{
  "headers": {
    "Accept": "*/*",
    "Host": "20.24.88.85:8001",
    "User-Agent": "curl/8.1.2"
  }
}
```

The path `/headers` responds all request header received. From the header `Host`, we can get the entrance.