---
title: 「アンインストールのトラブルシューティング」
description: 「FSM メッシュ アンインストール トラブルシューティング ガイド」
aliases: ["/docs/troubleshooting/uninstall"]
type: docs
---

# FSM メッシュ アンインストール トラブルシューティング ガイド

何らかの理由で「fsm uninstall mesh」([アンインストール ガイド](/docs/guides/uninstall/) に記載されている) が失敗した場合は、以下で説明するように FSM リソースを手動で削除できます。

メッシュの環境変数を設定します。
```console
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM is installed
export mesh_name=fsm # Replace fsm with the FSM mesh name
export fsm_version=<fsm version>
export fsm_ca_bundle=<fsm ca bundle>
```

Delete FSM control plane deployments:
```console
kubectl delete deployment -n $fsm_namespace fsm-bootstrap
kubectl delete deployment -n $fsm_namespace fsm-controller
kubectl delete deployment -n $fsm_namespace fsm-injector
```

Prometheus、Grafana、または Jaeger と一緒に FSM がインストールされている場合は、それらのデプロイメントを削除します。
```console
kubectl delete deployment -n $fsm_namespace fsm-prometheus
kubectl delete deployment -n $fsm_namespace fsm-grafana
kubectl delete deployment -n $fsm_namespace jaeger
```

FSM が FSM マルチクラスター ゲートウェイと共にインストールされている場合は、次のコマンドを実行して削除します。
```console
kubectl delete deployment -n $fsm_namespace fsm-multicluster-gateway
```

FSM シークレット、meshconfig、および webhook 構成を削除します。
> 警告: 続行する前に、クラスタ内のリソースが次のリソースに依存していないことを確認してください。
```console
kubectl delete secret -n $fsm_namespace $fsm_ca_bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
kubectl delete meshconfig -n $fsm_namespace fsm-mesh-config
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=$mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-injector
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-controller
```

クラスターから FSM および SMI CRD を削除するには、次を実行します。
> 警告: CRD を削除すると、その CRD に対応するすべてのカスタム リソースも削除されます。
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