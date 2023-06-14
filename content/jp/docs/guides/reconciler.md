---
title: 「調停者ガイド」
description: 「調停者ガイド」
aliases: ["/docs/reconciler_guide"]
type: docs
weight: 8
---

# 調停者ガイド

このガイドでは、FSM でリコンサイラーを有効にする方法について説明します。

## 調停者の仕組み

FSM でリコンサイラーを構築する目的は、FSM のコントロール プレーンの正しい操作に必要なリソースが常に望ましい状態になるようにすることです。 FSM インストールの一部としてインストールされ、「openservicemesh.io/reconcile: true」および「app.kubernetes.io/name: openservicemesh.io」というラベルを持つリソースは、調整ツールによって調整されます。

**注**: 調整可能なリソースでラベル `openservicemesh.io/reconcile: true` および `app.kubernetes.io/name: openservicemesh.io` が変更または削除された場合、調整ツールは期待どおりに動作しません。

調整可能なリソースの更新または削除イベントは、調整ツールをトリガーし、リソースを目的の状態に調整します。 調整可能なリソースでは、メタデータの変更 (名前の変更を除く) のみが許可されます。

### 調整されたリソース

FSM が調整するリソースは次のとおりです。

- CRD : FSM によってインストール/要求される CRD [CRDs for FSM ](https://github.com/flomesh-io/FSM /tree/{{< param fsm_branch >}}/cmd/fsm -bootstrap/crds) が調整されます。 FSM は必要な CRD のインストールとアップグレードを管理するため、FSM はそれらを調整して、それらの仕様、保存および提供されたバージョンが常に FSM によって必要とされる状態であることを保証します。

- MutatingWebhookConfiguration : MutatingWebhookConfiguration は、FSM のコントロール プレーンの一部としてデプロイされ、自動サイドカー インジェクションを有効にします。 これはポッドがメッシュに参加するための非常に重要なコンポーネントであるため、FSM はこのリソースを調整します。

- ValidatingWebhookConfiguration : ValidatingWebhookConfiguration は、さまざまなメッシュ構成を検証するために、FSM のコントロール プレーンの一部としてデプロイされます。 このリソースはメッシュに適用される構成を検証するため、FSM はこのリソースを調整します。


## reconciler で FSM をインストールする方法

reconciler を使用して FSM をインストールするには、次のコマンドを使用します。

```console
$ fsm install --set fsm.enableReconciler=true
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

