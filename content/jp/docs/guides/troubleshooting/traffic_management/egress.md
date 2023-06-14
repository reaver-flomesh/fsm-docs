---
title: 「出口のトラブルシューティング」
description: 「エグレストラブルシューティングガイド」
type: docs
---

## Egress が期待どおりに機能しない場合

### 1. エグレスが有効になっていることを確認する

「fsm-mesh-config」「MeshConfig」カスタム リソースの「enableEgress」キーの値を確認して、エグレスが有効になっていることを確認します。 `fsm-mesh-config` は名前空間 FSM コントロール プレーンの名前空間 (デフォルトでは `fsm-system`) にあります。

```console
# Returns true if egress is enabled
$ kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.enableEgress}{"\n"}'
true
```

上記のコマンドは、エグレスが有効かどうかを示すブール文字列 (`true` または `false`) を返す必要があります。

### 2. FSM コントローラーのログでエラーを調べます

```bash
# When fsm-controller is deployed in the fsm-system namespace
kubectl logs -n fsm-system $(kubectl get pod -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
```

エラーは、ログ メッセージの「レベル」キーを「エラー」に設定してログに記録されます。
```console
{"level":"error","component":"...","time":"...","file":"...","message":"..."}
```

### 3. Pipy の構成を確認する

Pod のサイドカーで使用される構成で egress が有効になっていることを確認します。

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
