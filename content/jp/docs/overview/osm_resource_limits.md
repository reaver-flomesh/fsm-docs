---
Title: 「FSM リソースのリクエストと制限」
Description: "FSM ポッドおよびデプロイメントのリソース要求と制限"
Type: docs
Weight: 3
---

| 鍵 | タイプ | デフォルト | 説明 |
|-----|------|---------|-------------|
| fsm.injector.resource| 物体 | `{"limits":{"cpu":"0.5","memory":"64M"},"requests":{"cpu":"0.3","memory":"64M"}}` | サイドカー インジェクターのコンテナー リソース パラメーター |
| fsm.fsmBootstrap.resource | 物体 | `{"limits":{"cpu":"0.5","memory":"128M"},"requests":{"cpu":"0.3","memory":"128M"}}` | FSM ブートストラップのコンテナ リソース パラメータ |
| fsm.fsmController.resource | 物体 | `{"limits":{"cpu":"1.5","memory":"1G"},"requests":{"cpu":"0.5","memory":"128M"}}` | FSM コントローラのコンテナ リソース パラメータ。 https://docs.openservicemesh.io/docs/guides/ha_scale/scale/ を参照してください。 |
| fsm.prometheus.resources | 物体 | `{"limits":{"cpu":"1","memory":"2G"},"requests":{"cpu":"0.5","memory":"512M"}}` | Prometheus のコンテナー リソース パラメーター |

> 注: これらはデフォルト値であり、[values.yaml]((https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/ values.yaml)