---
title: 「FSM コントロール プレーンをインストールする」
description: 「このセクションでは、Kubernetes クラスターに FSM をインストール/アンインストールする方法について説明します」
type: docs
weight: 2
---

## 前提条件

- Kubernetes {{< param min_k8s_version >}} 以上を実行している Kubernetes クラスター
- [FSM CLI](/guides/cli) または [helm 3 CLI](https://helm.sh/docs/intro/install/) または OpenShift `oc` CLI。

### Kubernetes のサポート

FSM は、FSM リリース時にサポートされている Kubernetes バージョンで実行できます。 現在のサポート マトリックスは次のとおりです。

| FSM          | Kubernetes  |
| ----------------- | ----------- |
| 1.1               | 1.19 - 1.24 |

### FSM CLI の使用

「fsm」CLI を使用して、FSM コントロール プレーンを Kubernetes クラスターにインストールします。

#### FSM CLI とチャートの互換性

FSM CLI の各バージョンは、一致するバージョンの FSM Helm チャートでのみ動作するように設計されています。 バージョンの偏りが存在する場合でも、多くの操作は引き続き機能する可能性がありますが、これらのシナリオはテストされておらず、異なる CLI およびチャート バージョンを使用するときに発生する問題は、報告されても修正されない場合があります。

#### CLI の実行

「fsm install」を実行して、FSM コントロール プレーンをインストールします。

```console
$ fsm install
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

その他のオプションについては、「fsm install --help」を実行してください。

_注: CLI を使用して FSM をインストールすると、クラスター内にメッシュを 1 つだけ展開することが強制されます。 FSM は、CRD を FSM の特定のインスタンスに結び付ける複数の API バージョンをサポートするために、変換 Webhook フィールドをすべての CRD に追加することにより、CRD をインストールおよび管理します。 したがって、FSM の正しい操作のために、クラスタごとに 1 つの FSM メッシュのみを持つことが **強く推奨されます**。_

### Helm CLI の使用

[FSM チャート](https://github.com/flomesh-io/FSM /tree/{{< param fsm_branch >}}/charts/fsm) は [Helm CLI]( https://helm.sh/docs/intro/install/)。

#### 値ファイルの編集

値ファイルをオーバーライドすることで、FSM インストールを構成できます。

1. [values file](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/values.yaml) のコピーを作成します (必ず使用してください インストールするチャートのバージョン)。
1. カスタマイズしたい値を変更します。 その他の値はすべて省略できます。

    - MeshConfig 設定に対応する値を確認するには、[FSM MeshConfig documentation](/guides/mesh_config) を参照してください。

    - たとえば、MeshConfig の「logLevel」フィールドを「info」に設定するには、以下を「override.yaml」として保存します。
     ```console
     fsm:
       sidecarLogLevel: info
     ```

#### ヘルムのインストール

次に、次の「helm install」コマンドを実行します。 チャートのバージョンは、[こちら](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/Chart) にインストールしたい Helm チャートにあります。 .yaml#L17)。

```console
$ helm install <mesh name> fsm --repo https://flomesh-io.github.io/FSM --version <chart version> --namespace <fsm namespace> --values override.yaml
```

デフォルト設定を使用する場合は、`--values` フラグを省略します。

その他のオプションについては、`helm install --help` を実行してください。

### OpenShift

OpenShift に FSM をインストールするには:

1. iptables を適切にプログラミングできるように、特権付きの init コンテナーを有効にします。 NET_ADMIN 機能は、OpenShift では十分ではありません。
   ```bash
   fsm install --set="fsm.enablePrivilegedInitContainer=true"
   ```
   - 特権 init コンテナーを有効にせずに FSM を既にインストールしている場合は、[FSM MeshConfig](/guides/mesh_config) で「enablePrivilegedInitContainer」を「true」に設定し、メッシュ内のすべてのポッドを再起動します。
1. `privileged` [security context constraint](https://docs.openshift.com/container-platform/4.7/authentication/managing-security-context-constraints.html) をメッシュ内の各サービス アカウントに追加します。
    - [oc CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html) をインストールします。
    - セキュリティ コンテキストの制約をサービス アカウントに追加する
     ```bash
      oc adm policy add-scc-to-user privileged -z <service account name> -n <service account namespace>
     ```

### ポッド セキュリティ ポリシー

**非推奨: PSP サポートは v0.10.0 以降、FSM で非推奨になっています。**

**PSP サポートは FSM 1.0.0 で削除されます**

PSP が有効なクラスターで FSM を実行している場合は、`--set fsm.pspEnabled=true` を `fsm install` または `helm install` CLI コマンドに渡します。

### FSM で Reconciler を有効にする

FSM でリコンサイラーを有効にしたい場合は、`--set fsm.enableReconciler=true` を `fsm install` または `helm install` CLI コマンドに渡します。 Reconciler の詳細については、[Reconciler Guide](/guides/reconciler) を参照してください。

## FSM コンポーネントの検査

デフォルトでいくつかのコンポーネントがインストールされます。 次の「kubectl」コマンドを使用して検査します。

```console
# Replace fsm-system with the namespace where FSM is installed
$ kubectl get pods,svc,secrets,meshconfigs,serviceaccount --namespace fsm-system
```

いくつかのクラスター全体 (ネームスペース化されていないコンポーネント) もインストールされます。 次の「kubectl」コマンドを使用して検査します。

```console
kubectl get clusterrolebinding,clusterrole,mutatingwebhookconfiguration,validatingwebhookconfigurations -l app.kubernetes.io/name=openservicemesh.io
```

内部では、`fsm` は [Helm](https://helm.sh) ライブラリを使用して、コントロール プレーンの名前空間に Helm の `release` オブジェクトを作成しています。 Helm の「リリース」名はメッシュ名です。 `helm` CLI を使用して、インストールされた Kubernetes マニフェストをより詳細に検査することもできます。 Helm のインストール手順については、https://helm.sh にアクセスしてください。

```console
# Replace fsm-system with the namespace where FSM is installed
$ helm get manifest fsm --namespace fsm-system
```

## 次のステップ

FSM コントロール プレーンが起動して実行されるようになったので、メッシュに [add services](/guides/app_onboarding/) します。
