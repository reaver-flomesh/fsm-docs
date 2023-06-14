---
title: "安装 FSM "
description: "使用 FSM 命令行工具安装 FSM 控制平面"
type: docs
weight: 1
---

# 安装 FSM 

## 先决条件

这个 FSM {{< param fsm_version >}} 的演示需要：
  - 运行 Kubernetes {{< param min_k8s_version >}} 或更高版本的集群（使用选择的云提供商、[k3s](https://k3s.io)、[k8e](https://getk8e.com) 或类似的发行版）
  - 能够执行 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 脚本的工作站
  - [Kubernetes 命令行工具](https://kubernetes.io/docs/tasks/tools/#kubectl) - `kubectl`
  - [FSM 代码仓库](https://github.com/flomesh-io/FSM ) 在本地可用
  
> 注意：本文档假设你已经在 `~/.kube/config` 中安装了 Kubernetes 集群的凭据，并且 `kubectl cluster-info` 成功执行。

## 下载并安装 FSM 命令行工具

`fsm` 命令行工具包含安装和配置 Open Service Mesh 所需的一切。
该二进制文件可在 [FSM GitHub 发布页面](https://github.com/flomesh-io/FSM /releases) 上找到。

### GNU/Linux

下载 FSM {{< param fsm_version >}} 的 64 位 GNU/Linux 二进制文件：

```bash
system=$(uname -s | tr "[:upper:]" "[:lower:]")
arch=$(dpkg --print-architecture)
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/FSM /releases/download/${release}/FSM -${release}-${system}-${arch}.tar.gz | tar -vxzf -
./$system-$arch/fsm version
```

### macOS

下载 FSM {{< param fsm_version >}} 的 64 位 macOS 二进制文件：

```bash
system=$(uname -s | tr [:upper:] [:lower:])
arch=$(uname -m)
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/FSM /releases/download/${release}/FSM -${release}-${system}-${arch}.tar.gz | tar -vxzf -
./${system}-${arch}/fsm version
```

`fsm` CLI 可以根据 [本指南](/guides/cli) 从源代码编译。

## 在 Kubernetes 上安装 FSM 

下载并解压 `fsm` 二进制文件后，我们就可以在 Kubernetes 集群上安装 FSM ：

下面的命令显示了如何在 Kubernetes 集群上安装 FSM 。

此命令启用 [Prometheus](https://github.com/prometheus/prometheus)、[Grafana](https://github.com/grafana/grafana) 和 [Jaeger](https://github.com/jaegertracing/jaeger) 集成。

`values.yaml` 文件中的 `fsm.enablePermissiveTrafficPolicy` chart 参数指示 FSM 忽略任何策略并让流量在 pod 之间自由流动。启用宽松流量策略模式后，新的 Pod 将注入 Pipy，但流量将流经代理，不会被访问控制策略阻止。

> 注意：宽松流量策略模式是 brownfield 部署的一项重要功能，其中可能需要一些时间来制定 SMI 策略。在运维设计 SMI 策略的同时，现有服务将继续按照安装 FSM 之前的方式运行。

```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM will be installed
export fsm_mesh_name=fsm # Replace fsm with the desired FSM mesh name

fsm install \
    --mesh-name "$fsm_mesh_name" \
    --fsm-namespace "$fsm_namespace" \
    --set=fsm.enablePermissiveTrafficPolicy=true \
    --set=fsm.deployPrometheus=true \
    --set=fsm.deployGrafana=true \
    --set=fsm.deployJaeger=true \
    --set=fsm.tracing.enable=true
```

在 [可观察性文档](/guides/observability/) 中阅读有关 FSM 与 Prometheus、Grafana 和 Jaeger 集成的更多信息。

## 下一步

现在 FSM 控制平面已启动并运行，[添加应用程序](/getting_started/install_apps/) 到网格。