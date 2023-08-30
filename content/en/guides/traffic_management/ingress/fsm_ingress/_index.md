---
title: FSM Ingress Controller
description: "Kubernetes Ingress Controller implementation provided by FSM"
type: docs
weight: 8
---

The Kubernetes [Ingress API](https://kubernetes.io/docs/concepts/services-networking/ingress/) is designed with a separation of concerns, where the Ingress implementation provides an entry feature infrastructure managed by operations staff; it also allows application owners to control the routing of requests to the backend through rules.

Ingress is an API object for managing external access to services in a cluster, with typical access through HTTP. It provides load balancing, SSL termination, and name-based virtual hosting. For the Ingress resource to work, the cluster must have a running Ingress controller.

[Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) configures the HTTP load balancer by monitoring Ingress resources in the cluster.

![](/images/ingress/basics/fsm-demo.png)

## Installation

### Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- FSM version >= v1.1.0.

There are two options to install FSM Ingress Controller. One is installing it along with FSM during FSM installation. It won't be enabled by default so we need to enable it explicitly:

```bash
fsm install \
    --set=fsm.fsmIngress.enabled=true
```

Another is installing it separately if you already have FSM mesh installed.

Using the `fsm` command line tool to enable FSM Ingress Controller.

```bash
fsm ingress enable
```

Check the resource.

```bash
kubectl get pod,svc -n fsm-system -l app=fsm-ingress                                                                            
NAME                               READY   STATUS    RESTARTS   AGE
pod/fsm-ingress-574465b678-xj8l6   1/1     Running   0          14h

NAME                  TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/fsm-ingress   LoadBalancer   10.43.243.124   10.0.2.4      80:30508/TCP   14h
```

Once all done, we can start to play with FSM Ingress Controller.