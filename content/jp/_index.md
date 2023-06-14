---
title: "FSM ドキュメント"
description: ""
type: docs

---

シンプルで整ったスタンドアロンサービスメッシュ。

FSM は[Kubernetes](https://kubernetes.io/)上で実行される。FSM コントロールプレーンは [Pipy Repo](https://flomesh.io/docs/en/operating/repo/0-intro)を実装し、[SMI](https://smi-spec.io/)  API で設定される。FSM は、アプリケーションの各インスタンスの横にサイドカーコンテナーとして Pipy プロキシを挿入する。

FSM について詳しく知るには:
*  [getting started articles](/docs/getting_started/)を一通り読んで、FSM をインストールし、サンプルアプリケーションを実行してください。
* [overview of FSM ](/docs/overview/about/)と、 [the design of its components](/docs/overview/fsm_components/)の詳細をお読みください。
*　[デモ](/docs/demos/) セクションから追加のサンプル シナリオを実行する。