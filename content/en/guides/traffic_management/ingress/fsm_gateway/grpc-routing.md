---
title: "gRPC Routing"
description: "This doc will show how to route traffic to gRPC backends."
type: docs
weight: 18
draft: false
---

The [GRPCRoute](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1alpha2.GRPCRoute) is used to route gRPC request to backend service. It can match requests by hostname, gRPC service, gRPC method, or HTTP/2 header.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploy sample

```shell
kubectl create namespace grpcbin
kubectl apply -n grpcbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/gprc-routing.yaml
```

In gRPC case, the listener configuration is similar with [HTTP routing](/guides/traffic_management/ingress/fsm_gateway/http_routing/).

### gRPC Route

We configure the match rule using `service: hello.HelloService` and `method: SayHello` to direct traffic to the target service. 

```yaml
rules:
- matches:
  - method:
      service: hello.HelloService
      method: SayHello
  backendRefs:
  - name: grpcbin
    port: 9000
```

Let's test our configuration now.

### Test

To test gRPC service, we will test with help of the tool [grpcurl](https://github.com/fullstorydev/grpcurl).

Let's record the IP address of gateway first.

```bash
export GATEWAY_IP=$(kubectl get svc -n grpcbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

Issue a request using the grpcurl command, specifying the service name and method. Doing so will yield the correct response.

```shell
grpcurl -plaintext -d '{"greeting":"Flomesh"}' $GATEWAY_IP:8000 hello.HelloService/SayHello
{
  "reply": "hello Flomesh"
}
```