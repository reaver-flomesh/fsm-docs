---
title: "Install the FSM Control Plane"
description: "This section describes how to install/uninstall FSM on a Kubernetes cluster"
type: docs
weight: 2
---

## Prerequisites

- Kubernetes cluster running Kubernetes {{< param min_k8s_version >}} or greater
- The [FSM CLI](/guides/operating/cli) or the [helm 3 CLI](https://helm.sh/docs/intro/install/) or the OpenShift `oc` CLI.

### Kubernetes support

FSM can be run on Kubernetes versions that are supported at the time of the FSM release. The current support matrix is:

| FSM          | Kubernetes  |
| ----------------- | ----------- |
| 1.1               | 1.19 - 1.24 |

### Using the FSM CLI

Use the `fsm` CLI to install the FSM control plane on to a Kubernetes cluster.

#### FSM CLI and Chart Compatibility

Each version of the FSM CLI is designed to work only with the matching version of the FSM Helm chart. Many operations may still work when some version skew exists, but those scenarios are not tested and issues that arise when using different CLI and chart versions may not get fixed even if reported.

#### Running the CLI

Run `fsm install` to install the FSM control plane.

```console
fsm install
fsm-preinstall[fsm-preinstall-xsmz4] Done
fsm-bootstrap[fsm-bootstrap-7f59b7bf7-rs55z] Done
fsm-injector[fsm-injector-787bc867db-54gl6] Done
fsm-controller[fsm-controller-58d758b7fb-2zrr8] Done
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

Run `fsm install --help` for more options.

_Note: Installing FSM via the CLI enforces deploying only one mesh in the cluster. FSM installs and manages the CRDs by adding a conversion webhook field to all the CRDs to support multiple API versions, which ties the CRDs to a specific instance of FSM. Hence, for FSM's correct operation it is **strongly recommended** to have only one FSM mesh per cluster._

### Using the Helm CLI

The [FSM chart](https://github.com/flomesh-io/fsm/tree/{{< param fsm_branch >}}/charts/fsm) can be installed directly via the [Helm CLI](https://helm.sh/docs/intro/install/).

#### Editing the Values File

You can configure the FSM installation by overriding the values file.

1. Create a copy of the [values file](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/charts/fsm/values.yaml) (make sure to use the version for the chart you wish to install).
1. Change any values you wish to customize. You can omit all other values.

   - To see which values correspond to the MeshConfig settings, see the [FSM MeshConfig documentation](/guides/operating/mesh_config)
   - For example, to set the `logLevel` field in the MeshConfig to `info`, save the following as `override.yaml`:

     ```console
     fsm:
       sidecarLogLevel: info
     ```

#### Helm install

Then run the following `helm install` command. The chart version can be found in the Helm chart you wish to install [here](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/charts/fsm/Chart.yaml#L17).

```console
helm install <mesh name> fsm --repo https://flomesh-io.github.io/fsm --version <chart version> --namespace <fsm namespace> --create-namespace --values override.yaml
```

Omit the `--values` flag if you prefer to use the default settings.

Run `helm install --help` for more options.

### OpenShift

To install FSM on OpenShift:

1. Enable privileged init containers so that they can properly program iptables. The NET_ADMIN capability is not sufficient on OpenShift.

   ```bash
   fsm install --set="fsm.enablePrivilegedInitContainer=true"
   ```

   - If you have already installed FSM without enabling privileged init containers, set `enablePrivilegedInitContainer` to `true` in the [FSM MeshConfig](/guides/operating/mesh_config) and restart any pods in the mesh.
1. Add the `privileged` [security context constraint](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html) to each service account in the mesh.
   - Install the [oc CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html).
   - Add the security context constraint to the service account

     ```bash
      oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
     ```

### Pod Security Policy

**Deprecated: PSP support has been deprecated in FSM since v0.10.0**

**PSP support will be removed in FSM 1.0.0**

If you are running FSM in a cluster with PSPs enabled, pass in `--set fsm.pspEnabled=true` to your `fsm install` or `helm install` CLI command.

### Enable Reconciler in FSM

If you wish to enable a reconciler in FSM, pass in `--set fsm.enableReconciler=true` to your `fsm install` or `helm install` CLI command. More information on the reconciler can be found in the [Reconciler Guide](/guides/operating/reconciler).

## Inspect FSM Components

A few components will be installed by default. Inspect them by using the following `kubectl` command:

```console
# Replace fsm-system with the namespace where FSM is installed
kubectl get pods,svc,secrets,meshconfigs,serviceaccount --namespace fsm-system
```

A few cluster wide (non Namespaced components) will also be installed. Inspect them using the following `kubectl` command:

```console
kubectl get clusterrolebinding,clusterrole,mutatingwebhookconfiguration,validatingwebhookconfigurations -l app.kubernetes.io/name=flomesh.io
```

Under the hood, `fsm` is using [Helm](https://helm.sh) libraries to create a Helm `release` object in the control plane Namespace. The Helm `release` name is the mesh-name. The `helm` CLI can also be used to inspect Kubernetes manifests installed in more detail. Goto https://helm.sh for instructions to install Helm.

```console
# Replace fsm-system with the namespace where FSM is installed
helm get manifest fsm --namespace fsm-system
```

## Next Steps

Now that the FSM control plane is up and running, [add services](/guides/app_onboarding/) to the mesh.
