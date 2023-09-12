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