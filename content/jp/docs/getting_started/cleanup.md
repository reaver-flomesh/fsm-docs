---
title: "FSM をアンインストールする"
description: "FSM とbookstoreアプリケーションをアンインストールする"
type: docs
weight: 6
---

# FSM をアンインストールする
入門セクションの記事では、FSM とサンプル アプリケーションのインストールについて概説する。これらのリソースをすべてアンインストールするには、サンプルアプリケーションと関連するSMIリソースを削除し、FSM コントロール プレーンとクラスター全体のFSM リソースをアンインストールする。

## サンプルアプリケーションを削除する

サンプルアプリケーションと関連する SMI リソースをクリーンアップするには、それらの名前空間を削除する。例えば、

```bash
kubectl delete ns bookbuyer bookthief bookstore bookwarehouse
```

## FSM コントロールプレーンのアンインストール

FSM コントロールプレーンをアンインストールするには、「fsm uninstall mesh」を使用する。

```bash
fsm uninstall mesh
```

## sFSM クラスター全体のリソースをアンインストールする

FSM クラスター全体のリソースをアンインストールするには、「fsm uninstall mesh --delete-cluster-wide-resources」を使用する。

```bash
fsm uninstall mesh --delete-cluster-wide-resources
```

FSM のアンインストールの詳細については、[uninstallation guide](/docs/guides/uninstall/)を参照してください。
