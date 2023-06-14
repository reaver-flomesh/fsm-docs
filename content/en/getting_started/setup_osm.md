---
title: "Setup FSM"
description: "Install the FSM control plane using the FSM CLI"
type: docs
weight: 1
---

## Prerequisites
This demo of FSM {{< param fsm_version >}} requires:
  - a cluster running Kubernetes {{< param min_k8s_version >}} or greater (using a cloud provider of choice, [minikube](https://minikube.sigs.k8s.io/docs/start/), or similar)
  - a workstation capable of executing [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) scripts
  - [The Kubernetes command-line tool](https://kubernetes.io/docs/tasks/tools/#kubectl) - `kubectl`
  - the [FSM code repo](https://github.com/flomesh-io/fsm/) available locally

> Note: This document assumes you have already installed credentials for a Kubernetes cluster in ~/.kube/config and `kubectl cluster-info` executes successfully.



## Download and install the FSM command-line tool

The `fsm` command-line tool contains everything needed to install and configure Open Service Mesh.
The binary is available on the [FSM GitHub releases page](https://github.com/flomesh-io/fsm/releases/).

### GNU/Linux

Download the 64-bit GNU/Linux or macOS binary of FSM {{< param fsm_version >}}:

```bash
system=$(uname -s | tr '[:upper:]' '[:lower:]')
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/fsm/releases/download/${release}/fsm-${release}-${system}-amd64.tar.gz | tar -vxzf -
./${system}-amd64/fsm version
```

### macOS

Download the 64-bit macOS binaries for FSM {{< param fsm_version >}}

```bash
system=$(uname -s | tr "[:upper:]" "[:lower:]")
arch=$(uname -m)
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/fsm/releases/download/$release/fsm-$release-$system-$arch.tar.gz | tar -vxzf -
./$system-$arch/fsm version
```

The `fsm` CLI can be compiled from source according to [this guide](/guides/cli).

## Installing FSM on Kubernetes

With the `fsm` binary downloaded, unzipped, and placed into `$PATH`, we are ready to install Open Service Mesh on a Kubernetes cluster:

The command below shows how to install FSM on your Kubernetes cluster.
This command enables
[Prometheus](https://github.com/prometheus/prometheus),
[Grafana](https://github.com/grafana/grafana), and
[Jaeger](https://github.com/jaegertracing/jaeger) integrations.
The `fsm.enablePermissiveTrafficPolicy` chart parameter in the `values.yaml` file instructs FSM to ignore any policies and
let traffic flow freely between the pods. With Permissive Traffic Policy mode enabled, new pods
will be injected with Envoy, but traffic will flow through the proxy and will not be blocked by access control policies.

> Note: Permissive Traffic Policy mode is an important feature for brownfield deployments, where it may take some time to craft SMI policies. While operators design the SMI policies, existing services will continue to operate as they have been before FSM was installed.

```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM will be installed
export fsm_mesh_name=fsm # Replace fsm with the desired FSM mesh name

fsm install \
    --mesh-name "$fsm_mesh_name" \
    --fsm-namespace "$fsm_namespace" \
    --set=fsm.enablePermissiveTrafficPolicy=true \
    --set=fsm.deployPrometheus=true \
    --set=fsm.deployGrafana=true \
    --set=fsm.deployJaeger=true
```

Read more on FSM's integrations with Prometheus, Grafana, and Jaeger in the [observability documentation](/guides/observability/).

## Next Steps

Now that the FSM control plane is up and running, [add applications](/getting_started/install_apps/) to the mesh.
