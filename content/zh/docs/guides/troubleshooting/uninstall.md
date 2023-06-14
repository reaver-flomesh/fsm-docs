---
title: "卸载"
description: "卸载 FSM 的故障排查"
aliases: ["/docs/troubleshooting/uninstall"]
type: docs
weight: 20
---

# 卸载 FSM 的故障排查

不论何种原因导致的 `fsm uninstall mesh`（如 [卸载指南](/guides/uninstall/) 中所述）失败，可以按照下面的操作手动删除 FSM 资源。

为网格设置环境变量：

```console
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM is installed
export mesh_name=fsm # Replace fsm with the FSM mesh name
export fsm_version=<fsm version>
export fsm_ca_bundle=<fsm ca bundle>
```

删除 FSM 控制平面部署：

```console
kubectl delete deployment -n $fsm_namespace fsm-bootstrap
kubectl delete deployment -n $fsm_namespace fsm-controller
kubectl delete deployment -n $fsm_namespace fsm-injector
```

如果 FSM 与 Prometheus、Grafana 或 Jaeger 一起安装，请删除这些部署：

```console
kubectl delete deployment -n $fsm_namespace fsm-prometheus
kubectl delete deployment -n $fsm_namespace fsm-grafana
kubectl delete deployment -n $fsm_namespace jaeger
```

如果 FSM 与 FSM 多集群网关一起安装，请运行以下命令将其删除：

```console
kubectl delete deployment -n $fsm_namespace fsm-multicluster-gateway
```

删除 FSM secrets、meshconfig 和 webhook 配置：
> 警告：确保集群中没有资源依赖于以下资源，然后再继续。

```console
kubectl delete secret -n $fsm_namespace $fsm_ca_bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
kubectl delete meshconfig -n $fsm_namespace fsm-mesh-config
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=$mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-injector
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-controller
```

要从集群中删除 FSM 和 SMI CRD，请运行以下命令。
> 警告：删除 CRD 将导致与该 CRD 对应的所有自定义资源也被删除。
```console
kubectl delete crd meshconfigs.config.openservicemesh.io
kubectl delete crd multiclusterservices.config.openservicemesh.io
kubectl delete crd egresses.policy.openservicemesh.io
kubectl delete crd ingressbackends.policy.openservicemesh.io
kubectl delete crd httproutegroups.specs.smi-spec.io
kubectl delete crd tcproutes.specs.smi-spec.io
kubectl delete crd traffictargets.access.smi-spec.io
kubectl delete crd trafficsplits.split.smi-spec.io
```