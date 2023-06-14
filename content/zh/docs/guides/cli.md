---
title: "安装 FSM CLI"
description: "这个章节描述了安装和使用 `FSM ` CLI."
type: docs
weight: 1
---

## 先决条件

- Kubernetes 集群，运行 Kubernetes {{< param min_k8s_version >}} 或者更高

## 搭建 FSM CLI

### 从二进制发布版开始

从[发布页](https://github.com/flomesh-io/FSM /releases)下载平台指定的压缩包
解压 `FSM ` 二进制文件，然后添加它到 `$PATH` 来开启。

#### Linux 和 macOS

在 Linux/macOS 上的基于 bash 的 shell 环境里，使用 `curl` 来下载 FSM 发布版，然后按照如下方式解压 `tar` 包。

```console
# Specify the FSM version that will be leveraged throughout these instructions
fsm_VERSION={{< param fsm_version >}}

# Linux curl command only
curl -sL "https://github.com/flomesh-io/FSM /releases/download/$fsm_VERSION/FSM -$fsm_VERSION-linux-amd64.tar.gz" | tar -vxzf -

# macOS curl command only
curl -sL "https://github.com/flomesh-io/FSM /releases/download/$fsm_VERSION/FSM -$fsm_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`fsm` 客户端二进制程序运行在客户端机器上，并且允许在 Kubernetes 集群里管理 FSM 。使用下面的命令在 Linux 或者 [Windows Linux 子系统 (WSL)](https://docs.microsoft.com/windows/wsl/about) 上基于 bash 的 shell 里面来安装 FSM `fsm` 客户端二进制程序。这些命令复制 `fsm` 客户端二进制程序到操作系统 `PATH` 下面的标准用户程序位置里。


```console
sudo mv ./linux-amd64/fsm /usr/local/bin/fsm
```

对于 **macOS**，使用下面的命令：

```console
sudo mv ./darwin-amd64/fsm /usr/local/bin/fsm
```

可以通过下面的命令，来验证已经被正确添加到环境里的 `fsm` 客户端库和它们的版本号。

```console
fsm version
```

### 从源码 (Linux, macOS)

从源码来构建 FSM 需要更多的步骤，但是这是最好的方式用来在一个开发环境里测试最近的变更和有用的东西。

必须有一个工作的 [Go](https://golang.org/doc/install) 环境。

```console
$ git clone git@github.com:flomesh-io/FSM .git
$ cd fsm
$ make build-fsm
```

`make build-fsm` 将拉取任何被需要的依赖，编译 `fsm`，然后把它放置于 `bin/fsm`。添加 `bin/fsm` 到 `$PATH`，这样就可以轻松地使用 `fsm` 了。

## 安装 FSM 

### FSM 配置

默认的，控制平面组件被安装到一个 Kubernetes 命名空间，被称之为 `fsm-system`，然后这个控制平面被给与一个唯一的标识符属性 `mesh-name`，默认是 `fsm`。
安装期间，这个命名空间和 mesh-name 能够被配置，当使用 `fsm` CLI 时，通过标志来进行，或者当使用 `helm` CLI 时，通过编辑值文件来进行。

在安装到标识并且管理一个网格实例期间，`mesh-name` 是一个唯一的标识符，其被指派给一个 FSM 控制器实例。

`mesh-name` 应该遵循 [RFC 1123](https://tools.ietf.org/html/rfc1123) DNS 标签限定。其必须：

- 至多包含 63 个字符
- 只能包含小写字母数字字符或者 '-'
- 以一个字母数字字符开始
- 以一个字母数字字符结束

### 使用 FSM CLI

使用 `fsm` CLI 来安装 FSM 控制平面到一个 Kubernetes 集群。

运行 `fsm install`。

```console
# Install fsm control plane components
$ fsm install
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

运行 `fsm install --help` 来了解更多选项。
