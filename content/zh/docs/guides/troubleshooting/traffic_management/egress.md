---
title: "出口故障排查"
description: "出口故障排查指南"
type: docs
---

## 当出口未按预期工作时

### 1. 确认出口已启用

通过验证 `fsm-mesh-config` `MeshConfig` 自定义资源中的 `enableEgress` 的值来确认已启用出口。 `fsm-mesh-config` 位于命名空间 FSM 控制平面命名空间中（默认情况下为 `fsm-system`）。

```console
# Returns true if egress is enabled
$ kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.enableEgress}{"\n"}'
true
```

上述命令必须返回一个布尔字符串（`true`或`false`），表示是否启用了出口。

### 2. 检查 FSM 控制器日志是否有错误

```bash
# When fsm-controller is deployed in the fsm-system namespace
kubectl logs -n fsm-system $(kubectl get pod -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
```

日志消息中的 `level` 设置为 `error` 来记录错误：
```console
{"level":"error","component":"...","time":"...","file":"...","message":"..."}
```

### 3. 确认 Pipy 配置

检查 Pod 的 sidecar 使用的配置中是否开启了 egress：

```json
{
  "Spec": {
    "SidecarLogLevel": "error",
    "Traffic": {
      "EnableEgress": true
    }
  }
}
```