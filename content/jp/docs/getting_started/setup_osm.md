---
title: "FSM をセットアップする"
description: "FSM CLI を使用して FSM コントロール プレーンをインストールする"
type: docs
weight: 1
---

# FSM をセットアップする

## 前提条件
この FSM {{< param fsm_version >}} のデモには以下が必要だ:
  - Kubernetes {{< param min_k8s_version >}} またはその以上を実行するクラスター (using a cloud provider of choice, [minikube](https://minikube.sigs.k8s.io/docs/start/), or similar)
  - [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell))スクリプトを実行できるワークステーション
  - [The Kubernetes command-line tool](https://kubernetes.io/docs/tasks/tools/#kubectl) - `kubectl`
  - ローカルで利用可能な[FSM code repo](https://github.com/flomesh-io/FSM /) 

> 注意: このドキュメントでは、~/.kube/config に Kubernetes クラスターの資格情報が既にインストールされており、kubectl cluster-info が正常に実行されることを前提としている。



## FSM コマンドライン ツールをダウンロードしてインストールする

「fsm」コマンドラインツールには、Open Service Mesh のインストールと設定に必要なものがすべて含まれている。バイナリは [FSM GitHub releases page](https://github.com/flomesh-io/FSM /releases/)で入手できる。
### GNU/Linux

FSM {{< param fsm_version >}}の64ビットGNU/LinuxまたはmacOSバイナリをダウンロードする。

```bash
system=$(uname -s)
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/FSM /releases/download/${release}/fsm-${release}-${system}-amd64.tar.gz | tar -vxzf -
./${system}-amd64/fsm version
```

### macOS

FSM {{< param fsm_version >}}用の64ビットmacOSバイナリをダウンロードする。

```bash
system=$(uname -s | tr [:upper:] [:lower:])
arch=$(uname -m)
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/FSM /releases/download/${release}/FSM -${release}-${system}-${arch}.tar.gz | tar -vxzf -
. /${system}-${arch}/fsm version
```

fsm CLI は、 [this guide](/guides/cli)に従ってソースからコンパイルできる。

## Kubernetes に FSM をインストールする

「fsm」バイナリをダウンロードして解凍したら、Open Service Mesh を Kubernetes クラスターにインストールする準備が整った。

以下のコマンドは、Kubernetes クラスターに FSM をインストールする方法を示す。このコマンドは、[Prometheus](https://github.com/prometheus/prometheus)、[Grafana](https://github.com/grafana/grafana)、および [Jaeger](https://github.com/jaegertracing/jaeger) の統合を有効にする。`value.yaml` ファイルにある `fsm.enablePermissiveTrafficPolicy` チャートパラメータは、FSM に対して、ポリシーを無視し、トラフィックがポッド間を自由に流れるように指示する。Permissive Traffic Policy モードを有効にすると、新しいポッドはEnvoyで注入されるが、トラフィックはプロキシを経由して流れ、アクセス制御ポリシーによってブロックされることはない。

> 注意: Permissive Traffic Policy モードは、SMI ポリシーの作成に時間がかかるブラウンフィールド展開にとって重要な機能だ。オペレーターが SMI ポリシーを設計している間、既存のサービスは FSM がインストールされる前と同様に動作し続ける。

```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM will be installed
export fsm_mesh_name=fsm # Replace fsm with the desired FSM mesh name

fsm install \
    --mesh-name "$fsm_mesh_name" \
    --fsm-namespace "$fsm_namespace" \
    --set=fsm.enablePermissiveTrafficPolicy=true \
    --set=fsm.deployPrometheus=true \
    --set=fsm.deployGrafana=true \
    --set=fsm.deployJaeger=true
```

FSM の Prometheus、Grafana、および Jaeger との統合の詳細については、[observability documentation](/guides/observability/)を参照してください。

## 次のステップ

FSM コントロールプレーンが起動して実行されるようになったので、 [add applications](/getting_started/install_apps/)をメッシュに追加する。