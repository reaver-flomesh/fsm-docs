---
title: 「イングレスのトラブルシューティング」
description: 「イングレストラブルシューティングガイド」
type: docs
---

## Ingress が期待どおりに動作しない場合

### 1. グローバル イングレス構成が期待どおりに設定されていることを確認します。

```console
# Returns true if HTTPS ingress is enabled
$ kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.useHTTPSIngress}{"\n"}'
false
```

このコマンドの出力が「false」の場合、これは HTTP イングレスが有効であり、HTTPS イングレスが無効であることを意味します。 HTTP イングレスを無効にして HTTPS イングレスを有効にするには、次のコマンドを使用します。

```bash
# Replace fsm-system with fsm-controller's namespace if using a non default namespace
kubectl patch meshconfig fsm-mesh-config -n fsm-system -p '{"spec":{"traffic":{"useHTTPSIngress":true}}}'  --type=merge
```

同様に、HTTP イングレスを有効にして HTTPS イングレスを無効にするには、次を実行します。

```bash
# Replace fsm-system with fsm-controller's namespace if using a non default namespace
kubectl patch meshconfig fsm-mesh-config -n fsm-system -p '{"spec":{"traffic":{"useHTTPSIngress":false}}}'  --type=merge
```

### 2. FSM コントローラーのログでエラーを調べます

```bash
# When fsm-controller is deployed in the fsm-system namespace
kubectl logs -n fsm-system $(kubectl get pod -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
```

エラーは、ログ メッセージの「レベル」キーを「エラー」に設定してログに記録されます。
```console
{"level":"error","component":"...","time":"...","file":"...","message":"..."}
```

### 3. イングレス リソースが正常にデプロイされたことを確認する

```bash
kubectl get ingress <ingress-name> -n <ingress-namespace>
```
