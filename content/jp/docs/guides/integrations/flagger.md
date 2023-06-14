---
title: "Flager を FSM と統合する"
description: 「FSM がメトリクスのために Prometheus とどのように統合されるかを示す簡単なデモ」
aliases: "/docs/guides/integrations/demo_flagger"
type: docs
weight: 4
---

# Flagger を FSM と統合する

FSM の [Service Mesh Interface](smi-spec.io) [Traffic Split](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha4/traffic-split.md) 機能の使用に関する自動化をさらに追加するために、FSM は [WeaveWorks](https://www.weave.works/)によって開発された [Flagger](https://www.weave.works/oss/flagger/) プロジェクトとの統合を提供しました。

「Flagger は、Kubernetes で実行されているアプリケーションのリリース プロセスを自動化するプログレッシブ デリバリー ツールです。メトリックを測定し、適合性テストを実行しながら、トラフィックを新しいバージョンに徐々に移行することで、本番環境に新しいソフトウェア バージョンを導入するリスクを軽減します。」 [[1]](#1)

## FSM のフラグガーによるプログレッシブ配信


Flagger プロジェクトとの協力により、Flager で FSM を使用する方法に関するドキュメントが Flagger サイトに置かれます。 インストール方法の詳細については、[Open Service Mesh Progressive Delivery Flagger documentation](https://docs.flagger.app/tutorials/fsm-progressive-delivery) を参照してください。 FSM /Flagger 統合コードは、[Flagger GitHub repository](https://github.com/fluxcd/flagger) にあります。 統合で問題が発生した場合は、[Flagger GitHub issues repository](https://github.com/fluxcd/flagger/issues) に問題を送信してください。

## 参考文献

<a id="1">[1]</a>
WeaveWorks/Flagger
What is Flagger?
