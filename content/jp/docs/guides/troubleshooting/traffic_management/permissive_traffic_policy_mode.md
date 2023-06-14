---
title: 「許容トラフィック ポリシー モードのトラブルシューティング」
description: 「許可トラフィック ポリシー モードのトラブルシューティング ガイド」
type: docs
---

## permissive トラフィック ポリシー モードが期待どおりに機能しない場合

### 1. permissive トラフィック ポリシー モードが有効になっていることを確認する

「fsm-mesh-config」カスタム リソースの「enablePermissiveTrafficPolicyMode」キーの値を確認して、寛容なトラフィック ポリシー モードが有効になっていることを確認します。 `fsm-mesh-config` MeshConfig は名前空間 FSM コントロール プレーンの名前空間 (デフォルトでは `fsm-system`) にあります。

```console
# Returns true if permissive traffic policy mode is enabled
$ kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

上記のコマンドは、許容トラフィック ポリシー モードが有効かどうかを示すブール文字列 (「true」または「false」) を返す必要があります。

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

クライアントの Pipy プロキシ構成とサーバー Pod で、クライアントがサーバーにアクセスできることを確認します。 