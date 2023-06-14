---
title: 「ログ」
description: 「FSM コントロール プレーンからの診断ログ」
type: docs
---

# ログ
FSM コントロール プレーン コンポーネントは、診断メッセージを stdout に記録して、メッシュの管理を支援します。

ログでは、ユーザーは次の種類の情報を確認できます。
メッセージと一緒に: 
- 名前や名前空間などの Kubernetes リソース メタデータ
- mTLS 証明書の共通名

FSM は次のような機密情報のログを**記録しません**:
- Kubernetes シークレット データ
- Kubernetes リソース全体

## 冗長性

ログの冗長性は、特定のログ メッセージが書き込まれるタイミングを制御します。
デバッグ用により多くのメッセージを含めるか、またはポイントのみを示すメッセージを少なくする
重大なエラーに。

FSM は、詳細度の高い順に次のログ レベルを定義します。

| ログレベル | 目的                                                                                |
| --------- | -------------------------------------------------------------------------------------- |
| 無効   | ロギングを完全に無効にします。                                                              |
| パニック     | *現在使用されていません*                         |
| 致命的     | 終了に至る回復不可能なエラーの場合、通常は起動時に |
| エラー     | 解決するためにユーザーの操作が必要になる可能性があるエラーの場合 |
| 警告      | 回復されたエラーまたはエラーにつながる可能性のある予期しない状況について                  |
| 情報      | ユーザーアクションの確認など、通常の動作を示すメッセージの場合        |
| デバッグ     | メッシュが期待どおりに機能しない理由を理解するのに役立つ追加情報については |
| トレース     | 主に開発用に使用される余分な詳細メッセージ用 |

上記の各ログ レベルは、次の MeshConfig で設定できます。
`spec.observability.fsmLogLevel` またはインストール時に
`fsm.controllerLogLevel`チャート値。

## 流暢なビット
有効にすると、Fluent Bit はこれらのログを収集して処理し、ユーザーが選択した Elasticsearch、Azure Log Analytics、BigQuery などの出力に送信できます。

[Fluent Bit](https://fluentbit.io/) は、データ/ログを収集して複数の宛先に送信できるオープン ソースのログ プロセッサおよびフォワーダです。 出力プラグインを使用して、FSM コントローラー ログをさまざまな出力/ログ コンシューマーに転送するために、FSM と共に使用できます。

FSM は、インストール時に `--set=fsm.enableFluentbit=true` フラグを使用してオプションで Fluent Bit サイドカーを FSM コントローラーにデプロイすることにより、ログ転送を提供します。 ユーザーは、利用可能な [Fluent Bit output plugins](https://docs.fluentbit.io/manual/pipeline/outputs) のいずれかを使用して、FSM ログの転送先を定義できます。

### Fluent Bit を使用したログ転送の構成
デフォルトでは、Fluent Bit サイドカーは単にログを Fluent Bit コンテナーの stdout に送信するように構成されています。 Fluent Bit を有効にして FSM をインストールした場合は、「kubectl logs -n <fsm-namespace> <fsm-controller-name> -c fluentbit-logger」を使用してこれらのログにアクセスできます。 このコマンドは、パーサーとフィルターを変更する必要がある場合に、ログがどのようにフォーマットされているかを見つけるのにも役立ちます。

> 注: `<fsm-namespace>` は、fsm コントロール プレーンがインストールされている名前空間を指します。

Fluent Bit をデフォルト値ですばやく起動するには、`--set=fsm.enableFluentbit` オプションを使用します。
```console
fsm install --set=fsm.enableFluentbit=true
```
fsm.controllerLogLevel=<desired log level>` を使用して、インストール中にログ レベルを「debug」、「warn」、「fatal」、「panic」、「disabled」、または「trace」に変更できます。 _all_ ログを取得するには、ログ レベルを trace に設定します。

この基本的なセットアップを試したら、より有益な結果を得るために、好みの出力へのログ転送を構成することをお勧めします。

出力へのログ転送をカスタマイズするには、次の手順に従い、Fluent Bit を有効にして FSM を再インストールします。

1. [Fluent Bit documentation](https://docs.fluentbit.io/manual/pipeline/outputs) で、ログを転送する出力プラグインを見つけます。 [`fluentbit-configmap.yaml`](https://github.com/openservicemesh/fsm/blob/{{< param fsm_branch >}}/charts/fsm/templates/fluentbit- configmap.yaml) を適切な値に設定します。

1. デフォルトの構成では、CRI ログ形式の解析が使用されます。 ログのフォーマットが異なる kubernetes ディストリビューションを使用している場合は、[PARSER] セクションに新しいパーサーを追加し、[INPUT] セクションのパーサー名を次のいずれかに変更する必要がある場合があります。 [ここ](https://github.com/fluent/fluent-bit/blob/master/conf/parsers.conf)で定義されたパーサー。

1. 利用可能な [Fluent Bit Filters](https://docs.fluentbit.io/manual/pipeline/filters) を調べて、必要な数の [FILTER] セクションを追加します。
    * `[INPUT]` セクションは、取り込まれたログに `kube.*` のタグを付けるため、各カスタム フィルターに `Match kube.*` キー/値ペアを必ず含めてください。
     * デフォルトの構成では、変更フィルターを使用して `controller_pod_name` キー/値のペアを追加し、ポッド名で結果を絞り込むことで、出力のログを照会できるようにします (以下の使用例を参照)。

1. これらの変更を有効にするには、次を実行します。
    ```console
    make build-fsm
    ```

1. Fluent Bit ConfigMap テンプレートを更新したら、FSM のインストール中に以下を使用して Fluent Bit をデプロイできます。
    ```console
    fsm install --set=fsm.enableFluentbit=true [--set fsm.controllerLogLevel=<desired log level>]
    ```
    エラーログが生成されると、選択した出力でエラーログを操作できるようになります。


### 例: Fluent Bit を使用してログを Azure Monitor に送信する
Fluent Bit には、次のように Azure Log Analytics ワークスペースにログを送信するために使用できる Azure 出力プラグインがあります。
1. [Create a Log Analytics workspace](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/quick-create-workspace)

2. Azure Portal で新しいワークスペースに移動します。 エージェント管理の下のワークスペースで、ワークスペース ID とプライマリ キーを見つけます。 「values.yaml」の「fluentBit」の下で、「outputPlugin」を「azure」に更新し、キー「workspaceId」と「primaryKey」を Azure Portal からの対応する値 (引用符なし) で更新します。 または、他の出力プラグインの場合と同様に、「fluentbit-configmap.yaml」の出力セクション全体を置き換えることもできます。

3. 上記の手順 2 ～ 5 を実行します。

4. Fluent Bit を有効にして FSM を実行すると、Log Analytics ワークスペースの [ログ] > [カスタム ログ] セクションにログが入力されます。 そこで、次のクエリを実行して、最新のログを最初に表示できます。
    ```console
    fluentbit_CL
    | order by TimeGenerated desc
    ```
5. FSM コントローラー ポッドの特定のデプロイに関するログ結果を絞り込みます。
    ```console
    | where controller_pod_name_s == "<desired fsm controller pod name>"
    ```

ログが Log Analytics に送信されると、次のように Application Insights で使用することもできます。
1. [Create a Workspace-based Application Insights instance](https://docs.microsoft.com/en-us/azure/azure-monitor/app/create-workspace-resource).

2. Azure Portal でインスタンスに移動します。 ログセクションに移動します。 次のクエリを実行して、Log Analytics からログが取得されていることを確認します。
    ```console
    workspace("<your-log-analytics-workspace-name>").fluentbit_CL
    ```

これらのインスタンスのいずれかでログを操作できるようになりました。

*注: 現在、Fluent Bit は OpenShift ではサポートされていません。*

### Fluent Bit のアウトバウンド プロキシ サポートの構成
エグレス トラフィックがプロキシ サーバーを通過するように構成されている場合は、アウトバウンド プロキシのサポートが必要になる場合があります。 これを有効にする方法は 2 つあります。

上記の MeshConfig の変更を使用して FSM を既にビルドしている場合は、FSM CLI を使用してプロキシ サポートを有効にし、以下のコマンドの値を置き換えます。
```bash
fsm install --set=fsm.enableFluentbit=true,fsm.fluentBit.enableProxySupport=true,fsm.fluentBit.httpProxy=<http-proxy-host:port>,fsm.fluentBit.httpsProxy=<https-proxy-host:port>
```

1.「enableProxySupport」を「true」に変更します

1. httpProxy および httpsProxy の値を「http://<host>:<port>」に更新します。 プロキシ サーバーに基本認証が必要な場合は、そのユーザー名とパスワードを次のように含めることができます: `http://<username>:<password>@<host>:<port>`

1. これらの変更を有効にするには、次を実行します。
    ```console
    make build-fsm
    ```

1. Fluent Bit を有効にして FSM をインストールします。
    ```console
    fsm install --set=fsm.enableFluentbit=true
    ```
> 注: [Fluent Bit image tag](https://github.com/openservicemesh/fsm/blob/{{< param fsm_branch >}}/charts/fsm/values.yaml) が「1.6.4」または この機能に必要なため、より大きくなります。