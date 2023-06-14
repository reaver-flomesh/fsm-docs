---
title: "Prometheus と Grafana でobservabilityを設定する"
description: "Prometheus および GrafanaとのFSM のobservability統合を使用して、書店アプリケーション間のトラフィックを検査する"
type: docs
weight: 5
---

# Prometheus と Grafana を使用してobservabilityを設定する

次の記事では、オブザーバビリティとモニタリングのために Prometheus と Grafana スタックの自動プロビジョニングを使用して FSM をインストールする方法を示す。FSM を使用してクラスターで独自の (BYO) Prometheus および Grafana スタックを使用する例については、 [Integrate FSM with Prometheus and Grafana](/docs/demos/prometheus_grafana/)デモを参照してください。

この記事で作成した設定は、本番環境では使用しないでください。本番レベルのデプロイについては、[Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md)  および [Deploy Grafana in Kubernetes](https://grafana.com/docs/grafana/latest/installation/kubernetes/) を参照してください。


## Prometheus と Grafana で FSM をインストールする

fsm のインストール時に、Prometheus および/または Grafana インスタンスをデフォルトの FSM 設定で自動的にプロビジョニングできる。
```bash
 fsm install --set=fsm.deployPrometheus=true \
             --set=fsm.deployGrafana=true
```
observabilityの詳細については、[Observability Guide](/docs/guides/observability)を参照してください。

## Prometheus

--set=fsm.deployPrometheus=true フラグで設定すると、FSM インストールは Prometheus インスタンスをデプロイして、サイドカーと FSM コントロール プレーンのメトリクス エンドポイントを取得する。 [scraping configuration file](https://github.com/openservicemesh/fsm/blob/{{< param fsm_branch >}}/charts/fsm/templates/prometheus-configmap.yaml)は、デフォルトの Prometheus の動作とFSM によって収集されたメトリクスのセットを定義する。

## Grafana

FSM は、fsm install で --set=fsm.deployGrafana=true フラグを使用して [Grafana](https://grafana.com/grafana/) インスタンスをデプロイするように設定できる。FSM は、オブザーバビリティ ガイドの  [FSM Grafana dashboards](/docs/guides/observability/metrics/#fsm-grafana-dashboards) セクションに記載されている事前設定済みのダッシュボードを提供する。

## メトリクス スクレイピングを有効にする

`fsm metrics` コマンドを使用して、名前空間スコープでメトリックを有効にすることができる。デフォルトでは、FSM はメッシュ内のポッドのメトリック スクレイピングを**設定しない**。
```bash
fsm metrics enable --namespace test
fsm metrics enable --namespace "test1, test2"

```
> 注意: メトリクス スクレイピングを有効にする名前空間は、既にメッシュの一部である必要がある。

## ダッシュボードを検査する

FSM Grafana ダッシュボードは、次のコマンドで表示できる。

```bash
fsm dashboard
```

> 注意:追加のターミナルがまだ ./scripts/port-forward-all.sh スクリプトを実行している場合は、CTRL+C を押してポート フォワーディングを終了する。「fsm ダッシュボード」のポートリダイレクトは、ポートフォワーディングスクリプトが実行されていると同時には機能しない。

http://localhost:3000 に移動して、Grafana ダッシュボードにアクセスする。デフォルトのユーザー名は admin で、デフォルトのパスワードは admin だ。Grafana ホームページで**Home**アイコンをクリックすると、FSM コントロール プレーンと FSM データ プレーンの両方のダッシュボードを含むフォルダーが見える。

## 次のステップ

[Cleanup sample applications and uninstall FSM ](/docs/getting_started/cleanup/).