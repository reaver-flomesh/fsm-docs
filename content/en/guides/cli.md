---
title: "Install the FSM CLI"
description: "This section describes installing and using the `fsm` CLI."
type: docs
weight: 1
---

## Prerequisites

- Kubernetes cluster running Kubernetes {{< param min_k8s_version >}} or greater

## Set up the FSM CLI

### From the Binary Releases

Download platform specific compressed package from the [Releases page](https://github.com/flomesh-io/fsm/releases).
Unpack the `fsm` binary and add it to `$PATH` to get started.

#### Linux and macOS

In a bash-based shell on Linux/macOS or [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about), use `curl` to download the FSM release and then extract with `tar` as follows:

```console
# Specify the FSM version that will be leveraged throughout these instructions
fsm_VERSION={{< param fsm_version >}}

# Linux curl command only
curl -sL "https://github.com/flomesh-io/fsm/releases/download/$fsm_VERSION/fsm-$fsm_VERSION-linux-amd64.tar.gz" | tar -vxzf -

# macOS curl command only
curl -sL "https://github.com/flomesh-io/fsm/releases/download/$fsm_VERSION/fsm-$fsm_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

The `fsm` client binary runs on your client machine and allows you to manage FSM in your Kubernetes cluster. Use the following commands to install the FSM `fsm` client binary in a bash-based shell on Linux or [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about). These commands copy the `fsm` client binary to the standard user program location in your `PATH`.

```console
sudo mv ./linux-amd64/fsm /usr/local/bin/fsm
```

For **macOS** use the following commands:

```console
sudo mv ./darwin-amd64/fsm /usr/local/bin/fsm
```

You can verify the `fsm` client library has been correctly added to your path and its version number with the following command.

```console
fsm version
```
### From Source (Linux, MacOS)

Building FSM from source requires more steps but is the best way to test the latest changes and useful in a development environment.

You must have a working [Go](https://golang.org/doc/install) environment.

```console
$ git clone git@github.com:flomesh-io/FSM.git
$ cd fsm
$ make build-fsm
```

`make build-fsm` will fetch any required dependencies, compile `fsm` and place it in `bin/fsm`. Add `bin/fsm` to `$PATH` so you can easily use `fsm`.

## Install FSM

### FSM Configuration

By default, the control plane components are installed into a Kubernetes Namespace called `fsm-system` and the control plane is given a unique identifier attribute `mesh-name` defaulted to `fsm`.
During installation, the Namespace and mesh-name can be configured through flags when using the `fsm` CLI or by editing the values file when using the `helm` CLI.

The `mesh-name` is a unique identifier assigned to an fsm-controller instance during install to identify and manage a mesh instance.

The `mesh-name` should follow [RFC 1123](https://tools.ietf.org/html/rfc1123) DNS Label constraints. The `mesh-name` must:

- contain at most 63 characters
- contain only lowercase alphanumeric characters or '-'
- start with an alphanumeric character
- end with an alphanumeric character

### Using the FSM CLI

Use the `fsm` CLI to install the FSM control plane on to a Kubernetes cluster.

Run `fsm install`.

```console
# Install fsm control plane components
$ fsm install
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

Run `fsm install --help` for more options.
