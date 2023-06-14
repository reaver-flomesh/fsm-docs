---
title: 「メッシュ構成」
description: 「FSM MeshConfig」
type: docs
aliases: ["/docs/fsm_mesh_config/"]
weight: 7
---

# FSM MeshConfig
FSM は MeshConfig リソース `fsm-mesh-config` をそのコントロール プレーン (fsm-controller pod と同じ名前空間内) の一部としてデプロイします。これは、メッシュの所有者/オペレーターがいつでも更新できます。 この MeshConfig の目的は、メッシュの所有者/オペレーターが、ニーズに基づいてメッシュ構成の一部を更新できるようにすることです。

インストール時に、FSM MeshConfig は、[charts/fsm/templates](https://github.com/flomesh-io) の下にあるプリセット MeshConfig (`preset-mesh-config`) から展開されます。 /FSM /blob/{{< param fsm_branch >}}/charts/fsm/templates/preset-mesh-config.yaml)。

まず、fsm がインストールされた名前空間を参照するように環境変数を設定します。
```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM is installed
```

CLI で「fsm-mesh-config」を表示するには、「kubectl get」コマンドを使用します。

```bash
kubectl get meshconfig fsm-mesh-config -n "$fsm_namespace" -o yaml
```

*注: MeshConfig `fsm-mesh-config` の値は、アップグレード後も保持されます。*

## FSM MeshConfig を構成する

### Kubectl パッチ コマンド

「fsm-mesh-config」への変更は、「kubectl patch」コマンドを使用して行うことができます。
```bash
kubectl patch meshconfig fsm-mesh-config -n "$fsm_namespace" -p '{"spec":{"traffic":{"enableEgress":true}}}'  --type=merge
```
詳細については、[Config API reference](/docs/apidocs/config/v1alpha1) を参照してください。

誤った値が使用された場合、[MeshConfig CRD](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/crds/meshconfig. yaml) は、値が無効である理由を説明するエラー メッセージで変更を防ぎます。
たとえば、以下のコマンドは、「enableEgress」をブール値以外の値にパッチするとどうなるかを示しています。
```bash
kubectl patch meshconfig fsm-mesh-config -n "$fsm_namespace" -p '{"spec":{"traffic":{"enableEgress":"no"}}}'  --type=merge
# Validations on the CRD will deny this change
The MeshConfig "fsm-mesh-config" is invalid: spec.traffic.enableEgress: Invalid value: "string": spec.traffic.enableEgress in body must be of type boolean: "string"
```
#### キーの種類ごとの kubectl パッチ コマンド

> 注: `<fsm-namespace>` は、fsm コントロール プレーンがインストールされている名前空間を指します。 デフォルトでは、fsm 名前空間は「fsm-system」です.

| キー                                            | タイプ   | デフォルト値                                | kubectl パッチ コマンドの例                                                                                                     |
| ---------------------------------------------- | ------ | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
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
