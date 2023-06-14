---
title: "调节器指南"
description: "调节器指南"
aliases: ["/docs/reconciler_guide"]
type: docs
weight: 8
---

# 调节器指南

这篇指南描述了如何在开放边缘服务网格 (FSM ) 里面启用调节器。

## 调节器如何工作

在 FSM 里创建一个调节器的目标就是：确保对 FSM 控制平面的正确操作所需要的资源在任何时刻都处于其预期的状态。那些在 FSM 安装时作为一部分被安装，且带有标签 `openservicemesh.io/reconcile: true` 和 `app.kubernetes.io/name: openservicemesh.io` 的资源将被调节器协调。

**注意**：如果标签 `openservicemesh.io/reconcile: true` 和 `app.kubernetes.io/name: openservicemesh.io` 在调节器资源上被修改了或者被删除了，那么调节器将不会按照要求的来操作。

可调节资源上的更新或删除事件将触发调节器，从而将调节资源回到它被期望的状态。可调节资源上只允许做元数据 (不包含名字的修改) 的变更。

### 资源调节

FSM 调节的资源是：

- CRD：调节 FSM 安装/需要的 CRD [FSM 的 CRD](https://github.com/flomesh-io/FSM /tree/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds) 。既然 FSM 管理着它所需要的 CRD 的安装和升级，FSM 也将调节它们以确保它们的规范、存储和服务的版本一直在 FSM 做需要的状态。

- MutatingWebhookConfiguration：部署一个 MutatingWebhookConfiguration，以作为 FSM 控制平面的一部分来开启自动 Sidecar 注入。因为对于 Pod 接入网格来说这是非常关键的组件，FSM 会调节该资源。

- ValidatingWebhookConfiguration：部署一个 ValidatingWebhookConfiguration ，作为 FSM 控制平面的一部分来验证多种网格配置。这个资源验证被应用到网格的配置，因此 FSM 将调节这个资源。


## 如何安装带调节器的 FSM 

要安装带调节器的 FSM ，使用下面的命令：

```console
$ fsm install --set fsm.enableReconciler=true
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

