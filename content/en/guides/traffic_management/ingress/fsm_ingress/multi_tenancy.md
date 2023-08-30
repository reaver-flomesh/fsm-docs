---
title: "Multi-tenancy Ingress"
description: "How to use the FSM Ingress controller to create physical isolation of Ingress controllers when hosting multiple tenants in your Kubernetes cluster"
type: docs
weight: 3
draft: true
---

It's very rare for organizations to have or provide a dedicated Kubernetes cluster to each tenant, as it's generally more cost-effective to share a cluster. Sharing clusters reduces expenses and streamlines management. Sharing clusters, however, also comes with difficulties like managing noisy neighbors and ensuring security.

Clusters can be shared in many ways. In some cases, different applications may run in the same cluster. In other cases, multiple instances of the same application may run in the same cluster, one for each end user. All these types of sharing are frequently described using the umbrella term multi-tenancy.

While Kubernetes does not have first-class concepts of end users or tenants, it provides several features to help manage different tenancy requirements. These are discussed below.

* A common form of multi-tenancy is to share a cluster between multiple teams within an organization, each of whom may operate one or more workloads.  In this scenario, members of the teams often have direct access to Kubernetes resources via tools such as kubectl, or indirect access through GitOps controllers or other types of release automation tools. There is often some level of trust between members of different teams, but Kubernetes policies such as RBAC, quotas, and network policies are essential to safely and fairly share clusters.

* The other major form of multi-tenancy frequently involves a Software-as-a-Service (SaaS) vendor running multiple instances of a workload for customers. This business model is so strongly associated with this deployment style that many people call it "SaaS tenancy." In this scenario, the customers do not have access to the cluster; Kubernetes is invisible from their perspective and is only used by the vendor to manage the workloads. Cost optimization is frequently a critical concern, and Kubernetes policies are used to ensure that the workloads are strongly isolated from each other.

### Tenant Isolation

There are several ways to design and build multi-tenant solutions with Kubernetes at its control plane, data plane, or at both levels. Each of these methods comes with its own set of tradeoffs that impact the isolation level, implementation effort, operational complexity, and cost of service.

_Kubernetes Control plane isolation ensures that different tenants cannot access or affect each other's Kubernetes API resources, and FSM Ingress controller provides isolated Ingress controllers for Kubernetes Namespaces._

In Kubernetes, a [Namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces) provides a mechanism for isolating groups of API resources within a single cluster. This isolation has two key dimensions:

* Object names within a namespace can overlap with names in other namespaces, similar to files in folders. This allows tenants to name their resources without having to consider what other tenants are doing.

* Many Kubernetes security policies are scoped to namespaces. For example, RBAC Roles and Network Policies are namespace-scoped resources. Using RBAC, Users and Service Accounts can be restricted to a namespace.

In a multi-tenant environment, a Namespace helps segment a tenant's workload into a logical and distinct management unit. A common practice is to isolate every workload in its namespace, even if multiple workloads are operated by the same tenant. This ensures that each workload has its own identity and can be configured with an appropriate security policy.


## FSM Ingress Controller

FSM Ingress Controller supports a multi-tenancy model via its concept of `NamespacedIngress` CRD where it deploys a physically isolated **Ingress Controller** for requested **Namespace**.

For example, the YAML below defines an Ingress Controller that monitors on port 100 and also creates a LoadBalancer type service for it that listens on port 100.

```yaml
apiVersion: flomesh.io/v1alpha1
kind: NamespacedIngress
metadata:
  name: namespaced-ingress-100
  namespace: test-100
spec:
  serviceType: LoadBalancer
  ports:
  - name: http
    port: 100
    protocol: TCP
```

![](/docs/images/ingress/multitenant/multi-tenant.png)

## Demo

- [Achieving multi-tenancy with FSM Ingress Controller](/demos/ingress/ingress_multitenant)