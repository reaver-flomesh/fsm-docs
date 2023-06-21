---
title: "Uninstall the FSM Control Plane and Components"
description: "Uninstall"
type: docs
weight: 4
---

This guide describes how to uninstall FSM from a Kubernetes cluster. This guide assumes there is a single FSM control plane (mesh) running. If there are multiple meshes in a cluster, repeat the process described for each control plane in the cluster before uninstalling any cluster wide resources at the end of the guide. Taking into consideration both the control plane and dataplane, this guide aims to walk through uninstalling all remnants of FSM with minimal downtime.

## Prerequisites

- Kubernetes cluster with FSM installed
- The `kubectl` CLI
- The [`fsm` CLI](/install/#set-up-the-fsm-cli) or the Helm 3 CLI

## Remove Pipy Sidecars from Application Pods and Pipy Secrets

The first step to uninstalling FSM is to remove the Pipy sidecar containers from application pods. The sidecar containers enforce traffic policies. Without them, traffic will flow to and from Pods according in accordance with default Kubernetes networking unless there are [Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) applied.

FSM Pipy sidecars and related secrets will be removed in the following steps:

1. [Disable automatic sidecar injection](#disable-automatic-sidecar-injection)
1. [Restart pods](#restart-pods)

### Disable Automatic Sidecar Injection

FSM Automatic Sidecar Injection is most commonly enabled by adding namespaces to the mesh via the `fsm` CLI. Use the `fsm` CLI to see which
namespaces have sidecar injection enabled. If there are multiple control planes installed, be sure to specify the `--mesh-name` flag.

View namespaces in a mesh:

```console
$ fsm namespace list --mesh-name=<mesh-name>
NAMESPACE          MESH           SIDECAR-INJECTION
<namespace1>       <mesh-name>    enabled
<namespace2>       <mesh-name>    enabled
```

Remove each namespace from the mesh:

```console
$ fsm namespace remove <namespace> --mesh-name=<mesh-name>
Namespace [<namespace>] successfully removed from mesh [<mesh-name>]
```

This will remove the `openservicemesh.io/sidecar-injection: enabled` annotation and `openservicemesh.io/monitored-by: <mesh name>` label from the namespace. 

Alternatively, if sidecar injection is enabled via annotations on pods instead of per namespace, please modify the pod or deployment spec to remove the sidecar injection annotation.

### Restart Pods

Restart all pods running with a sidecar:

```console
# If pods are running as part of a Kubernetes deployment
# Can use this strategy for daemonset as well
$ kubectl rollout restart deployment <deployment-name> -n <namespace>

# If pod is running standalone (not part of a deployment or replica set)
$ kubectl delete pod <pod-name> -n namespace
$ k apply -f <pod-spec> # if pod is not restarted as part of replicaset
```

Now, there should be no FSM Pipy sidecar containers running as part of the applications that were once part of the mesh. Traffic is no
longer managed by the FSM control plane with the `mesh-name` used above. During this process, your applications may experience some downtime
as all the Pods are restarting.

## Uninstall FSM Control Plane and Remove User Provided Resources

The FSM control plane and related components will be uninstalled in the following steps:

- [Prerequisites](#prerequisites)
- [Remove Pipy Sidecars from Application Pods and Pipy Secrets](#remove-pipy-sidecars-from-application-pods-and-pipy-secrets)
  - [Disable Automatic Sidecar Injection](#disable-automatic-sidecar-injection)
  - [Restart Pods](#restart-pods)
- [Uninstall FSM Control Plane and Remove User Provided Resources](#uninstall-fsm-control-plane-and-remove-user-provided-resources)
  - [Uninstall the FSM control plane](#uninstall-the-fsm-control-plane)
  - [Remove User Provided Resources](#remove-user-provided-resources)
  - [Delete FSM Namespace](#delete-fsm-namespace)
  - [Removal of FSM Cluster Wide Resources](#removal-of-fsm-cluster-wide-resources)

### Uninstall the FSM control plane

Use the `fsm` CLI to uninstall the FSM control plane from a Kubernetes cluster. The following step will remove:

1. FSM controller resources (deployment, service, mesh config, and RBAC)
1. Prometheus, Grafana, Jaeger, and Fluent Bit resources installed by FSM
1. Mutating webhook and validating webhook
1. The conversion webhook fields patched by FSM to the CRDs installed/required by FSM: [CRDs for FSM](https://github.com/flomesh-io/fsm/tree/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds) will be unpatched. To delete cluster wide resources refer to [Removal of FSM Cluster Wide Resources](#removal-of-fsm-cluster-wide-resources) for more details.

Run `fsm uninstall mesh`:

```console
# Uninstall fsm control plane components
$ fsm uninstall mesh --mesh-name=<mesh-name>
Uninstall FSM [mesh name: <mesh-name>] ? [y/n]: y
FSM [mesh name: <mesh-name>] uninstalled
```

Run `fsm uninstall mesh --help` for more options.

Alternatively, if you used Helm to install the control plane, run the following `helm uninstall` command:

```console
$ helm uninstall <mesh name> --namespace <fsm namespace>
```

Run `helm uninstall --help` for more options.

### Remove User Provided Resources

If any resources were provided or created for FSM at install time, they can be deleted at this point.

For example, if [Hashicorp Vault](/guides/certificates/#installing-hashi-vault) was deployed for the sole purpose of managing certificates for FSM, all related resources can be deleted.

### Delete FSM Namespace

When installing a mesh, the `fsm` CLI creates the namespace the control plane is installed into if it does not already exist. However, when uninstalling the same mesh, the namespace it lives in does not automatically get deleted by the `fsm` CLI. This behavior occurs because
there may be resources a user created in the namespace that they may not want automatically deleted.

If the namespace was only used for FSM and there is nothing that needs to be kept around, the namespace can be deleted at the time of uninstall or later using the following command.

```console
$ fsm uninstall mesh --delete-namespace
```

> Warning: Only delete the namespace if resources in the namespace are no longer needed. For example, if fsm was installed in `kube-system`, deleting the namespace may delete important cluster resources and may have unintended consequences.


### Removal of FSM Cluster Wide Resources

On installation FSM ensures that all the CRDs mentioned [here](https://github.com/flomesh-io/fsm/tree/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds) exist in the cluster at install time. During installation, if they are not already installed, the `fsm-bootstrap` pod will install them before the rest of the control plane components are running. This is the same behavior when using the Helm charts to install FSM as well. 

Uninstalling the mesh in both unmanaged and managed environments:
1. removes FSM control plane components, including control plane pods
2. removes/un-patches the conversion webhook fields from all the CRDs (which FSM adds to support multiple CR versions)

leaving behind certain FSM resources to prevent unintended consequences for the cluster after uninstalling FSM.The resources that are left behind will depend on whether FSM was uninstalled from a managed or unmanaged cluster environment.

When uninstalling FSM, both the `fsm uninstall mesh` command and Helm uninstallation will not delete any FSM or SMI CRD in any cluster environment (managed and unmanaged) for primarily two reasons:
1. CRDs are cluster-wide resources and may be used by other service meshes or resources running in the same cluster
2. deletion of a CRD will cause all custom resources corresponding to that CRD to also be deleted

To remove cluster wide resources that FSM installs (i.e. the meshconfig, secrets, FSM CRDs, SMI CRDs, and webhook configurations), the following command can be run during or after FSM's uninstillation.

```bash
fsm uninstall mesh --delete-cluster-wide-resources
```

> Warning: Deletion of a CRD will cause all custom resources corresponding to that CRD to also be deleted.

To troubleshoot FSM uninstallation, refer to the [uninstall troubleshooting section](/guides/troubleshooting/uninstall/)
