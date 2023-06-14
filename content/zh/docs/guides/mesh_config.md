---
title: "网格配置"
description: "FSM MeshConfig"
type: docs
aliases: ["/docs/fsm_mesh_config/"]
weight: 7
---

# FSM MeshConfig
FSM 部署一个 MeshConfig 资源 `fsm-mesh-config` 作为它的控制平面(同 FSM 控制器 Pod 的在同一命名空间) 的一部分，其能够被网格所有者/操作员在任意时刻更新。这个 MeshConfig 的目的是提供一种能够更新所需的网格配置的能力给网格所有者/运维人员。

在安装的时候，FSM MeshConfig 从一个现成的 MeshConfig (`preset-mesh-config`) 来部署，其能够在 [charts/fsm/templates](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds/config_meshconfig.yaml) 里面找到。

首先，设置一个环境变量来引用 FSM 被安装所在的命名空间。
```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM is installed
```

要在 CLI 里面查阅 `fsm-mesh-config`，请使用 `kubectl get` 命令。

```bash
kubectl get meshconfig fsm-mesh-config -n "$fsm_namespace" -o yaml
```

*注意：在 MeshConfig `fsm-mesh-config` 里面的值被持续更新。*

## 配置 FSM MeshConfig

### Kubectl 补丁命令

修改 `fsm-mesh-config`，可以使用 `kubectl patch` 命令。
```bash
kubectl patch meshconfig fsm-mesh-config -n "$fsm_namespace" -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge
```
参考 [Config API reference](/docs/api_reference/config/v1alpha1) 以获取更多信息。

如果一个不正确的值被使用了，在 [MeshConfig CRD](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/crds/meshconfig.yaml) 上的验证将阻止修改并给出一个错误信息来解释为什么这个值是无效的。
例如，下面的命令显示了如果我们用 `enableEgress` 给一个非布尔值打补丁会发生什么。
```bash
kubectl patch meshconfig fsm-mesh-config -n "$fsm_namespace" -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge
# Validations on the CRD will deny this change
The MeshConfig "fsm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```
#### 给每一个键类型的 kubectl 补丁命令 

> 注意：`<fsm-namespace>` 引用了 FSM 控制平面被安装所在的命名空间。默认的，FSM 的命名空间是 `fsm-system`。

| 键                                             | 类型   | 默认值                                       | Kubectl 补丁命令例子                                                                                                                                                           |
|------------------------------------------------|--------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| spec.traffic.enableEgress                      | bool   | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge`                                                     |
| spec.traffic.enablePermissiveTrafficPolicyMode | bool   | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"traffic":{"enablePermissiveTrafficPolicyMode":true}}}'  --type=merge`                                |
| spec.traffic.useHTTPSIngress                   | bool   | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge`                                                  |
| spec.traffic.outboundPortExclusionList         | array  | `[]`                                         | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"traffic":{"outboundPortExclusionList":6379,8080}}}'  --type=merge`                                   |
| spec.traffic.outboundIPRangeExclusionList      | array  | `[]`                                         | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"traffic":{"outboundIPRangeExclusionList":"10.0.0.0/32,1.1.1.1/24"}}}'  --type=merge`                 |
| spec.certificate.serviceCertValidityDuration   | string | `"24h"`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"certificate":{"serviceCertValidityDuration":"24h"}}}'  --type=merge`                                 |
| spec.observability.enableDebugServer           | bool   | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"observability":{"serviceCertValidityDuration":true}}}'  --type=merge`                                |
| spec.observability.tracing.enable              | bool   | `"jaeger.<fsm-namespace>.svc.cluster.local"` | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"observability":{"tracing":{"address": "jaeger.<fsm-namespace>.svc.cluster.local"}}}}'  --type=merge` |
| spec.observability.tracing.address             | string | `"/api/v2/spans"`                            | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"observability":{"tracing":{"endpoint":"/api/v2/spans"}}}}'  --type=merge' --type=merge`              |
| spec.observability.tracing.endpoint            | string | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"observability":{"tracing":{"enable":true}}}}'  --type=merge`                                         |
| spec.observability.tracing.port                | int    | `9411`                                       | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"observability":{"tracing":{"port":9411}}}}'  --type=merge`                                           |
| spec.sidecar.enablePrivilegedInitContainer     | bool   | `false`                                      | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"sidecar":{"enablePrivilegedInitContainer":true}}}'  --type=merge`                                    |
| spec.sidecar.logLevel                          | string | `"error"`                                    | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"sidecar":{"logLevel":"error"}}}'  --type=merge`                                                      |
| spec.sidecar.maxDataPlaneConnections           | int    | `0`                                          | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"sidecar":{"maxDataPlaneConnections":"error"}}}'  --type=merge`                                       |
| spec.sidecar.configResyncInterval              | string | `"0s"`                                       | `kubectl patch meshconfig fsm-mesh-config -n $fsm_namespace -p '{"spec":{"sidecar":{"configResyncInterval":"30s"}}}'  --type=merge`                                            |
