---
title: "UDP Routing"
description: "This document outlines setting up a UDPRoute in Kubernetes to route UDP traffic through an FSM Gateway, using Fortio server as a sample application."
type: docs
weight: 19
draft: false
---

The [UDPRoute](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1alpha2.UDPRoute) provides a method to route UDP traffic. When combined with a gateway listener, it can be used to forward traffic on a port specified by the listener to a set of backends defined in the UDPRoute.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

## Prerequisites

- Kubernetes cluster
- kubectl tool

## Environment Setup

### Deploying Sample Application

Use [fortio server](https://github.com/fortio/fortio) as a sample application, which provides a UDP service listening on port `8078` and echoes back the content sent by the client.

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
  - port: 8078
    name: udp-8078
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
        - containerPort: 8078
          name: http
EOF
```

### Creating UDP Gateway

Next, create a [Gateway](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1alpha2.Gateway) for the UDP service, setting the protocol of the listening port to `UDP`.

```shell
kubectl apply -n server -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  namespace: server
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm
  listeners:
    - protocol: UDP
      port: 8000
      name: udp
EOF
```

### Creating UDP Route

Similar to the HTTP protocol, to access backend services through the gateway, a [UDPRoute](https://gateway-api.sigs.k8s.io/reference/spec/#gateway.networking.k8s.io/v1alpha2.UDPRoute) needs to be created.

```shell
kubectl -n server apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: UDPRoute
metadata:
  name: udp-route-sample
spec:
  parentRefs:
    - name: simple-fsm-gateway
      namespace: server
      port: 8000
  rules:
  - backendRefs:
    - name: fortio
      port: 8078
EOF
```

Test accessing the UDP service. After sending the word 'fsm', the same word will be received back.

```shell
export GATEWAY_IP=$(kubectl get svc -n server -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')

echo 'fsm' | nc -4u -w1 $GATEWAY_IP 8000
fsm
```