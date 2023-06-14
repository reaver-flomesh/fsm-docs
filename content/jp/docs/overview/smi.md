---
title: サービス メッシュ インターフェイス (SMI) のサポート
description: "FSM での SMI 実装"
type: docs
---

## 概要

FSM は [Service Mesh Interface (SMI)](https://smi-spec.io/) リソースを実装します。 これにより、FSM ユーザーは、一般的なサービス メッシュ シナリオを柔軟に実装できます。

## サポートされているバージョン

メッシュでサポートされている SMI リソースのバージョンを確認するには、「fsm mesh list」を実行します。 サンプル出力からの抜粋を次に示します。

```
MESH NAME   MESH NAMESPACE   SMI SUPPORTED
fsm         fsm-system       HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha2,TrafficTarget:v1alpha3
```

現在サポートされている SMI リソースとそのバージョンは次のとおりです。

| SMI リソース | バージョン |
|--------------|---------|
| トラフィック ターゲット | access.smi-spec.io/v1alpha3 |
| トラフィックスプリット | split.smi-spec.io/v1alpha2 |
| HTTP ルート グループ | specs.smi-spec.io/v1alpha4 |
| TCP ルート | specs.smi-spec.io/v1alpha4 |
