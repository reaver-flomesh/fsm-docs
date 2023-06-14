---
title: 「Grafana のトラブルシューティング」
description: 「FSM の Grafana 統合に関する一般的な問題を修正する方法」
aliases: "/docs/troubleshooting/observability/grafana"
type: docs
---

## Grafana に到達できません

FSM でインストールされた Grafana インスタンスに到達できない場合は、次の手順を実行して問題を特定して解決します。

1. Grafana Pod が存在することを確認します。

     「fsm install --set=fsm.deployGrafana=true」でインストールすると、「fsm-grafana-7c88b9687d-tlzld」のような名前の Grafana Pod が、「fsm」という名前の他の FSM コントロール プレーン コンポーネントの名前空間に存在する必要があります。 -system` デフォルトで。

     そのような Pod が見つからない場合は、FSM Helm チャートが、`helm` で `fsm.deployGrafana` パラメータを `true` に設定してインストールされたことを確認します。

    ```console
    $ helm get values -a <mesh name> -n <FSM namespace>
    ```

    パラメータが「true」以外に設定されている場合は、「fsm install」で「--set=fsm.deployGrafana=true」フラグを使用して FSM を再インストールします。

1. Grafana Pod が正常であることを確認します。

     上記の Grafana Pod は、「kubectl get」の出力に示されているように、実行中の状態であり、すべてのコンテナーの準備が整っている必要があります。

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl get pods -n fsm-system -l app=fsm-grafana
    NAME                           READY   STATUS    RESTARTS   AGE
    fsm-grafana-7c88b9687d-tlzld   1/1     Running   0          58s
    ```

    Pod が Running またはそのコンテナーの準備ができていると表示されない場合は、「kubectl describe」を使用して他の潜在的な問題を探します。

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl describe pods -n fsm-system -l app=fsm-grafana
    ```

    Grafana Pod が正常であることが判明したら、Grafana に到達できるはずです。

## ダッシュボードに Grafana のデータが表示されない

Grafana ダッシュボードにデータが表示されない場合は、次の手順を実行して問題を特定して解決します。

1. Prometheus がインストールされ、正常であることを確認します。

     Grafana は Prometheus にデータを照会するため、Prometheus が期待どおりに動作していることを確認します。 詳細については、[Prometheus troubleshooting guide](/troubleshooting/observability/prometheus/) を参照してください。

1. Grafana が Prometheus と通信できることを確認します。

     まず、ブラウザーで Grafana UI を開きます。

    ```console
    $ fsm dashboard
    [+] Starting Dashboard forwarding
    [+] Issuing open browser http://localhost:3000
    ```

    ログインし (デフォルトのユーザー名/パスワードは admin/admin)、[data source settings](http://localhost:3000/datasources) に移動します。 動作していない可能性のあるデータ ソースごとに、クリックしてその構成を表示します。 ページの下部には、設定を確認するための [保存してテスト] ボタンがあります。

     エラーが発生した場合は、Grafana 構成を確認して、目的の Prometheus インスタンスを正しく指していることを確認してください。 「保存 & テスト」チェックでエラーが表示されなくなるまで、必要に応じて Grafana 設定を変更します。

    ![Successful verification](https://user-images.githubusercontent.com/5503924/112394171-7e419e00-8cb9-11eb-99fc-3343c6b9fbbd.png)

   データ ソースの構成の詳細については、[Grafana's docs](https://grafana.com/docs/grafana/latest/administration/provisioning/#data-sources) を参照してください。.

その他の考えられる問題については、[Grafana's troubleshooting documentation](https://grafana.com/docs/grafana/latest/troubleshooting/) を参照してください。.
