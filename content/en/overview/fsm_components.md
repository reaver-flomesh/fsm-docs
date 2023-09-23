---
title: "FSM Components"
description: "Components"
type: docs
weight: 2
---

## Inspect FSM Components

Some FSM components will be installed by default in the chosen namespace, which defaults to `fsm-system`. Inspect them by using the following `kubectl` command:

```console
# Replace fsm-system with the namespace where FSM is installed
kubectl get pods,svc,secrets,meshconfigs,serviceaccount --namespace fsm-system
```

Some cluster-wide (non-namespaced) FSM components will also be installed. Inspect them using the following `kubectl` command:

```console
kubectl get clusterrolebinding,clusterrole,mutatingwebhookconfiguration
```

Under the hood, `fsm` is using [Helm](https://helm.sh) libraries to create a Helm `release` object in the control plane Namespace. The Helm `release` name is the mesh-name. The `helm` CLI can also be used to inspect Kubernetes manifests installed in more detail. See the Helm docs for how to [install Helm](https://helm.sh/docs/intro/install/).

```console
helm get manifest fsm --namespace <fsm-namespace>
```

## Components

![FSM Architecture](/images/fsm-architecture.png)

Let's take a look at each component:

### (1) FSM Controller - Control Plane

The Control Plane plays a key part in operating the [service mesh](https://www.bing.com/search?q=What%27s+a+service+mesh%3F). All proxies are installed as [sidecars](https://docs.microsoft.com/en-us/azure/architecture/patterns/sidecar) and establish an mTLS gRPC connection to the Control Plane. The proxies continuously receive configuration updates. This component implements the interfaces required by the specific reverse proxy chosen. FSM implements [Pipy Repo](https://flomesh.io/pipy/docs/en/operating/repo/0-intro).

Besides providing configuration to sidecar proxied, it also distributes configuration to other components, such as FSM Ingress, FSM Gateway and Egress Gateway. 

### (2) Pipy Sidecar - Sidecar Proxy

FSM uses [Pipy](https://github.com/flomesh-io/pipy) as a sidecar proxy

Pipy is a programmable proxy for the cloud, edge and IoT. It's written in C++, which makes it extremely lightweight and fast. It's also fully programmable by using PipyJS, a tailored version from the standard JavaScript language.

The sidecar is injected into pod by injector during pod starting. All traffic of pod is intercepted to sidecar proxy. Then sidecar proxy will redirect traffic to local application in same pod or other applications base on the configuration distributed from the Control Plane.

This means that all sidecar proxies handle the traffic of Data Plane and connect to Control Plane for configuration.

### (3) FSM Ingress - Ingress Controller

FSM Ingress is an implementation of [Kubernegtes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) which will handle the traffic from outside the Kubernetes cluster. 

It focuses on L7 traffic management and it uses Pipy to handle traffic.

### (4) FSM Gateway - Gateway API

[Kubernetes Gateway API](https://gateway-api.sigs.k8s.io) is another API of Ingress traffic provided by Kubernetes community. It provides much flexibility and extendibility than Ingress API.

FSM Gateway is just the implementation of Gateway API. It also uses Pipy to handle traffic.

### (5) Egress Gateway - Egress Traffic Control

Egress Gateway is another standalone component of FSM, but is optional. The traffic sending outside the cluster can be redirected to Egress Gateway first. So the Egress Gateway runs as unified outlet of traffic sent outside cluster.

The underlying traffic processing is completed by Pipy.

### (6) Connectors - Discovery Integration

By default, service mesh supports Kubernetes service discovery, but Kubernetes service discovery is not the default implementation for users, or it is not the only one.

Connectors is the componets to support other discovery services, such as HashiCorp Consul, Netflix Eureka, Nacos and so on. Currently, it supports Consul and Eureka only.