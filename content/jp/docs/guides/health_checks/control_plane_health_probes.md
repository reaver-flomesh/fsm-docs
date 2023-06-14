---
title: 「FSM コントロール プレーンのヘルス プローブ」
description: "FSM のヘルス プローブの動作と失敗した場合の対処方法"
Alias: "/docs/control_plane_health_probes"
type: docs
---

# FSM コントロール プレーンのヘルス プローブ

FSM コントロール プレーン コンポーネントは、ヘルス プローブを活用して全体的なステータスを伝達します。 正常性プローブは、成功または失敗を示す HTTP ステータス コードで要求に応答する HTTP エンドポイントとして実装されます。

Kubernetes はこれらのプローブを使用して、コントロール プレーン Pod のステータスを伝達し、いくつかのアクションを自動的に実行して可用性を向上させます。 Kubernetes プローブの詳細については、[こちら](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) を参照してください。

## プローブ付きの FSM コンポーネント

次の FSM コントロール プレーン コンポーネントにはヘルス プローブがあります。

#### fsm コントローラー

ポート 9091 の fsm-controller では、次の HTTP エンドポイントを使用できます。

- `/health/alive`: HTTP 200 応答コードは、FSM の Aggregated Discovery Service (ADS) が実行中であることを示します。 サービスがまだ実行されていない場合、応答は送信されません。

- `/health/ready`: HTTP 200 応答コードは、ADS がプロキシからの gRPC 接続を受け入れる準備ができていることを示します。 HTTP 503 または応答がない場合は、プロキシからの gRPC 接続が成功しないことを示します。

#### fsmインジェクター

ポート 9090 の fsm-injector では、次の HTTP エンドポイントを使用できます。

- `/healthz`: HTTP 200 応答コードは、インジェクターがプロキシ サイドカー コンテナーを使用して新しい Pod を注入する準備ができていることを示します。 それ以外の場合、応答は送信されません。

## FSM の正常性を確認する方法

FSM の Kubernetes リソースは liveness および readiness プローブで構成されているため、Kubernetes は fsm-controller および fsm-injector Pod のヘルス エンドポイントを自動的にポーリングします。

liveness プローブが失敗すると、Kubernetes はイベントを生成し (「kubectl describe pod <pod name>」で表示)、Pod を再起動します。 `kubectl describe` の出力は次のようになります。

```console
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  24s               default-scheduler  Successfully assigned fsm-system/fsm-controller-85fcb445b-fpv8l to fsm-control-plane
  Normal   Pulling    23s               kubelet            Pulling image "flomesh/fsm-controller:v0.8.0"
  Normal   Pulled     23s               kubelet            Successfully pulled image "flomesh/fsm-controller:v0.8.0" in 562.2444ms
  Normal   Created    1s (x2 over 23s)  kubelet            Created container fsm-controller
  Normal   Started    1s (x2 over 23s)  kubelet            Started container fsm-controller
  Warning  Unhealthy  1s (x3 over 21s)  kubelet            Liveness probe failed: HTTP probe failed with statuscode: 503
  Normal   Killing    1s                kubelet            Container fsm-controller failed liveness probe, will be restarted
```

readiness プローブが失敗すると、Kubernetes はイベント (「kubectl describe pod <pod name>」で表示) を生成し、Pod がサポートしている可能性のあるサービス宛てのトラフィックが異常な Pod にルーティングされないようにします。 readiness プローブが失敗した Pod の「kubectl describe」出力は、次のようになります。

```console
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  36s               default-scheduler  Successfully assigned fsm-system/fsm-controller-5494bcffb6-tn5jv to fsm-control-plane
  Normal   Pulling    36s               kubelet            Pulling image "flomesh/fsm-controller:latest"
  Normal   Pulled     35s               kubelet            Successfully pulled image "flomesh/fsm-controller:v0.8.0" in 746.4323ms
  Normal   Created    35s               kubelet            Created container fsm-controller
  Normal   Started    35s               kubelet            Started container fsm-controller
  Warning  Unhealthy  4s (x3 over 24s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 503
```

Pod の `status` は、`kubectl get pod` 出力に表示される準備ができていないことも示します。 例えば: 

```console
NAME                              READY   STATUS    RESTARTS   AGE
fsm-controller-5494bcffb6-tn5jv   0/1     Running   0          26s
```

Pod のヘルス プローブは、Pod の必要なポートを転送し、「curl」またはその他の HTTP クライアントを使用してリクエストを発行することにより、手動で呼び出すこともできます。 たとえば、fsm-controller の liveness プローブを確認するには、Pod の名前を取得し、ポート 9091 を転送します。

```bash
# Assuming FSM is installed in the fsm-system namespace
kubectl port-forward -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') 9091
```

次に、別の端末インスタンスで、「curl」を使用してエンドポイントを確認できます。 次の例は、健全な fsm-controller を示しています。

```console
$ curl -i localhost:9091/health/alive
HTTP/1.1 200 OK
Date: Thu, 18 Mar 2021 20:15:29 GMT
Content-Length: 16
Content-Type: text/plain; charset=utf-8

Service is alive
```

## トラブルシューティング

いずれかの正常性プローブが一貫して失敗する場合は、次の手順を実行して根本原因を特定します。

1. 異常な fsm-controller または fsm-injector Pod が Pipy サイドカー コンテナーを実行していないことを確認します。

     fsm-controller Pod が Pipy サイドカー コンテナーを実行していないことを確認するには、Pod のコンテナーのイメージがいずれも Pipy イメージでないことを確認します。 Pipy イメージには、名前に「flomesh/pipy」が含まれています。

     たとえば、Pipy コンテナーを含む fsm-controller Pod:
    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh/fsm-controller:v0.8.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    fsm-injector Pod が Pipy サイドカー コンテナーを実行していないことを確認するには、Pod のコンテナーのイメージが Pipy イメージでないことを確認します。 Pipy イメージには、名前に「flomesh/pipy」が含まれています。

     たとえば、Pipy コンテナーを含む fsm-injector Pod:
    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh/fsm-injector:v0.8.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    いずれかの Pod が Pipy コンテナーを実行している場合、これまたは別の FSM インスタンスによって誤って注入された可能性があります。 「fsm mesh list」コマンドで見つかったメッシュごとに、異常な Pod の FSM 名前空間が、見つかったすべての FSM インスタンスに対して「SIDECAR-INJECTION」が「有効」になっている「fsm 名前空間リスト」出力にリストされていないことを確認します。 `fsm mesh list` コマンドで。

     たとえば、次のすべてのメッシュの場合:

    ```console
    $ fsm mesh list

    MESH NAME   NAMESPACE      CONTROLLER PODS                  VERSION     SMI SUPPORTED
    fsm         fsm-system     fsm-controller-5494bcffb6-qpjdv  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    fsm2        fsm-system-2   fsm-controller-48fd3c810d-sornc  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    ```

    「fsm-system」(メッシュ コントロール プレーンの名前空間) が次の名前空間のリストにどのように存在するかに注意してください。

    ```console
    $ fsm namespace list --mesh-name fsm --fsm-namespace fsm-system
    NAMESPACE    MESH    SIDECAR-INJECTION
    fsm-system   fsm2    enabled
    bookbuyer    fsm2    enabled
    bookstore    fsm2    enabled
    ```

    `SIDECAR-INJECTION` が有効になっている `fsm namespace list` コマンドで FSM 名前空間が見つかった場合は、サイドカーを注入するメッシュから名前空間を削除します。 上記の例では:

    ```console
    $ fsm namespace remove fsm-system --mesh-name fsm2 --fsm-namespace fsm-system2
    ```

1. Pod のスケジューリングまたは開始中に Kubernetes でエラーが発生したかどうかを確認します。

     異常な Pod の「kubectl describe」で最近発生した可能性のあるエラーを探します。

     fsm コントローラーの場合:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    fsm インジェクターの場合:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    エラーを解決し、FSM の正常性を再度確認します。

1. Pod でランタイム エラーが発生したかどうかを確認します。

     ログを調べて、コンテナの起動後に発生した可能性のあるエラーを探します。 具体的には、文字列 `"level":"error"` を含むログを探します。

     fsm コントローラーの場合:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    fsm インジェクターの場合:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    エラーを解決し、FSM の正常性を再度確認します。
