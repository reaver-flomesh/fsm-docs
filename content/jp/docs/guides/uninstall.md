---
title: 「FSM コントロール プレーンとコンポーネントをアンインストールする」
description: "アンインストール"
type: docs
weight: 4
---

# FSM コントロール プレーンとコンポーネントのアンインストール

このガイドでは、Kubernetes クラスターから FSM をアンインストールする方法について説明します。 このガイドでは、単一の FSM コントロール プレーン (メッシュ) が実行されていることを前提としています。 クラスター内に複数のメッシュがある場合は、ガイドの最後にあるクラスター全体のリソースをアンインストールする前に、クラスター内の各コントロール プレーンに対して説明されているプロセスを繰り返します。 コントロール プレーンとデータ プレーンの両方を考慮して、このガイドは最小限のダウンタイムで FSM のすべての残りの部分をアンインストールすることを目的としています。

## 前提条件

- FSM がインストールされた Kubernetes クラスター
- `kubectl` CLI
- [`fsm` CLI](/docs/install/#set-up-the-fsm-cli) または Helm 3 CLI

## アプリケーション ポッドと Pipy シークレットから Pipy サイドカーを削除する

FSM をアンインストールする最初のステップは、アプリケーション ポッドから Pipy サイドカー コンテナーを削除することです。 サイドカー コンテナーは、トラフィック ポリシーを適用します。 それらがない場合、[Kubernetes Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) が適用されていない限り、トラフィックはデフォルトの Kubernetes ネットワーキングに従って Pod との間で流れます。

FSM Pipy サイドカーと関連するシークレットは、次の手順で削除されます。

1. [Disable automatic sidecar injection](#disable-automatic-sidecar-injection)
1. [Restart pods](#restart-pods)

### 自動サイドカー インジェクションを無効にする

FSM 自動サイドカー インジェクションは、「fsm」CLI を介してメッシュに名前空間を追加することによって最も一般的に有効になります。 `fsm` CLI を使用して、
名前空間ではサイドカー インジェクションが有効になっています。 複数のコントロール プレーンがインストールされている場合は、必ず「--mesh-name」フラグを指定してください。

メッシュ内の名前空間を表示:

```console
$ fsm namespace list --mesh-name=<mesh-name>
NAMESPACE          MESH           SIDECAR-INJECTION
<namespace1>       <mesh-name>    enabled
<namespace2>       <mesh-name>    enabled
```

メッシュから各名前空間を削除します。

```console
$ fsm namespace remove <namespace> --mesh-name=<mesh-name>
Namespace [<namespace>] successfully removed from mesh [<mesh-name>]
```

これにより、名前空間から「openservicemesh.io/sidecar-injection: enabled」アノテーションと「openservicemesh.io/monitored-by: <メッシュ名>」ラベルが削除されます。

または、名前空間ごとではなくポッドのアノテーションを介してサイドカー インジェクションが有効になっている場合は、ポッドまたはデプロイ仕様を変更して、サイドカー インジェクションのアノテーションを削除してください。

### Pod を再起動する

サイドカーで実行されているすべてのポッドを再起動します。

```console
# If pods are running as part of a Kubernetes deployment
# Can use this strategy for daemonset as well
$ kubectl rollout restart deployment <deployment-name> -n <namespace>

# If pod is running standalone (not part of a deployment or replica set)
$ kubectl delete pod <pod-name> -n namespace
$ k apply -f <pod-spec> # if pod is not restarted as part of replicaset
```

これで、以前はメッシュの一部であったアプリケーションの一部として実行されている FSM Pipy サイドカー コンテナーはなくなります。 トラフィックはありません
上記で使用された `mesh-name` を持つ FSM コントロール プレーンによって管理されなくなりました。 このプロセス中に、アプリケーションでダウンタイムが発生する場合があります
すべての Pod が再起動しているためです。

## FSM コントロール プレーンをアンインストールし、ユーザー提供のリソースを削除する

FSM コントロール プレーンと関連コンポーネントは、次の手順でアンインストールされます。

1. [Uninstall the FSM control plane](#uninstall-the-fsm-control-plane)
1. [Remove User Provided Resources](#remove-user-provided-resources)
1. [Delete FSM Namespace](#delete-fsm-namespace)
1. [Removal of FSM Cluster Wide Resources](#removal-of-fsm-cluster-wide-resources)

### FSM コントロール プレーンをアンインストールする

「fsm」CLI を使用して、Kubernetes クラスターから FSM コントロール プレーンをアンインストールします。 次の手順で削除します。

1. FSM コントローラー リソース (デプロイ、サービス、メッシュ構成、および RBAC)
1. FSM によってインストールされる Prometheus、Grafana、Jaeger、および Fluent Bit リソース
1. Webhook の変更と Webhook の検証
1. FSM によってインストール/要求される CRD に FSM によってパッチされた変換 Webhook フィールド: [FSM の CRD](https://github.com/flomesh-io/FSM /tree/{{ < param fsm_branch >}}/cmd/fsm-bootstrap/crds) にはパッチが適用されません。 クラスター全体のリソースを削除するには、詳細について [FSM クラスター全体のリソースの削除](#removal-of-fsm-cluster-wide-resources) を参照してください。

`fsm uninstall mesh` を実行します:

```console
# Uninstall fsm control plane components
$ fsm uninstall mesh --mesh-name=<mesh-name>
Uninstall FSM [mesh name: <mesh-name>] ? [y/n]: y
FSM [mesh name: <mesh-name>] uninstalled
```

その他のオプションについては、「fsm uninstall mesh --help」を実行してください。

または、Helm を使用してコントロール プレーンをインストールした場合は、次の「helm uninstall」コマンドを実行します。

```console
$ helm uninstall <mesh name> --namespace <fsm namespace>
```

その他のオプションについては、`helm uninstall --help` を実行してください。

### ユーザー提供のリソースを削除する

インストール時に FSM 用にリソースが提供または作成された場合は、この時点で削除できます。

たとえば、[Hashicorp Vault](/docs/guides/certificates/#installing-hashi-vault) が FSM の証明書を管理するためだけにデプロイされた場合、関連するすべてのリソースを削除できます。

### FSM 名前空間を削除

メッシュをインストールするとき、「fsm」CLI は、コントロール プレーンがインストールされる名前空間がまだ存在しない場合に作成します。 ただし、同じメッシュをアンインストールする場合、存在する名前空間は `fsm` CLI によって自動的に削除されません。 この動作は、次の理由で発生します。
ユーザーが名前空間に作成したリソースが、自動的に削除されたくない場合があります。

名前空間が FSM にのみ使用され、保持する必要があるものが何もない場合、アンインストール時または後で次のコマンドを使用して名前空間を削除できます。

```console
$ fsm uninstall mesh --delete-namespace
```

> 警告: 名前空間のリソースが不要になった場合にのみ、名前空間を削除してください。 たとえば、fsm が「kube-system」にインストールされている場合、名前空間を削除すると、重要なクラスター リソースが削除され、意図しない結果が生じる可能性があります。


### FSM クラスター全体のリソースの削除

インストール時に、FSM は　[ここ](https://github.com/flomesh-io/FSM /tree/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds) に記載されているすべての CRD を保証します。 インストール時にクラスターに存在します。 インストール中に、それらがまだインストールされていない場合、「fsm-bootstrap」ポッドは、残りのコントロール プレーン コンポーネントが実行される前にそれらをインストールします。 これは、Helm チャートを使用して FSM をインストールする場合も同じ動作です。 

管理されていない環境と管理されている環境の両方でのメッシュのアンインストール:
1. コントロール プレーン ポッドを含む、FSM コントロール プレーン コンポーネントを削除します。
2. すべての CRD (複数の CR バージョンをサポートするために FSM が追加) から変換 Webhook フィールドを削除/パッチ解除します。

FSM のアンインストール後にクラスターに意図しない結果が生じるのを防ぐために、特定の FSM リソースを残します。残されるリソースは、FSM が管理されたクラスター環境からアンインストールされたか、管理されていないクラスター環境からアンインストールされたかによって異なります。

FSM をアンインストールするとき、「fsm uninstall mesh」コマンドと Helm アンインストールの両方が、主に次の 2 つの理由から、どのクラスター環境 (管理対象および非管理対象) でも FSM または SMI CRD を削除しません。
1. CRD はクラスター全体のリソースであり、同じクラスターで実行されている他のサービス メッシュまたはリソースによって使用される場合があります。
2. CRD を削除すると、その CRD に対応するすべてのカスタム リソースも削除されます。

FSM がインストールするクラスター全体のリソース (つまり、meshconfig、secrets、FSM CRD、SMI CRD、および webhook 構成) を削除するには、FSM のインストール解除中またはインストール後に次のコマンドを実行できます。

```bash
fsm uninstall mesh --delete-cluster-wide-resources
```

> 警告: CRD を削除すると、その CRD に対応するすべてのカスタム リソースも削除されます。

FSM アンインストールのトラブルシューティングについては、[uninstall troubleshooting section](/docs/guides/troubleshooting/uninstall/) を参照してください。