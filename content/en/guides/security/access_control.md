---
title: "Access Control Management"
description: "Using access control policies to access services with the service mesh."
type: docs
weight: 10
---

# Access Control Management

Deploying a service mesh in a complex brownfield environment is a lengthy and gradual process requiring upfront planning, or there may exist use cases where you have a specific set of services that either aren't yet ready for migration or for some reason can not be migrated to service mesh.

This guide will talk about the approaches which can be used to enable services outside of the service mesh to communicate with services within the `FSM` service mesh.

![](/images/access_control/arch.png)

FSM offers two ways to allow accessing services within the service mesh:

* via Ingress
  * [FSM](https://github.com/flomesh-io/fsm) Ingress controller
  * Nginx Ingress controller
* Access Control
  * Service
  * IPRange


The first method to access the services in the service mesh is via Ingress controller, and treat the services outside the mesh as the services inside the cluster. The advantage of this approach is that the setup is simple and straightforward and the disadvantages are also apparent, as you cannot achieve fine-grained access control, and all services outside the mesh can access services within the mesh.

This guide will focus on the second approach, which allows support for fine-grained access control on who can access services within the service mesh. This feature was added and to release [FSM v1.0.0](https://github.com/flomesh-io/FSM/releases/tag/v1.0.0).

Access Control can be configured via two resource types: Service and IP range. In terms of data transmission, it supports plaintext transmission and mTLS-encrypted traffic.


## Demo

To learn more about access control, refer to following demo guides:

- [Service-based access control](/demos/security/service_based_access_control)
- [IP range-based access control](/demos/security/ip_range_based_access_control)