---
title: 「FSM CLI をインストールする」
description: 「このセクションでは、`fsm` CLI のインストールと使用について説明します。」
type: docs
weight: 1
---

## 前提条件

- Kubernetes {{< param min_k8s_version >}} 以上を実行している Kubernetes クラスター

## FSM CLI をセットアップする

### バイナリリリースから

[リリースページ](https://github.com/flomesh-io/FSM /releases)からプラットフォーム固有の圧縮パッケージをダウンロードします。
`fsm` バイナリを解凍し、それを `$PATH` に追加して開始します。

#### Linux と macOS

Linux/macOS 上の bash ベースのシェルまたは [Linux 用 Windows サブシステム](https://docs.microsoft.com/windows/wsl/about) では、`curl` を使用して FSM リリースをダウンロードし、 「tar」は次のとおりです。

```console
# Specify the FSM version that will be leveraged throughout these instructions
fsm_VERSION={{< param fsm_version >}}

# Linux curl command only
curl -sL "https://github.com/flomesh-io/FSM /releases/download/$fsm_VERSION/fsm-$fsm_VERSION-linux-amd64.tar.gz" | tar -vxzf -

# macOS curl command only
curl -sL "https://github.com/flomesh-io/FSM /releases/download/$fsm_VERSION/fsm-$fsm_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

「fsm」クライアント バイナリはクライアント マシンで実行され、Kubernetes クラスターで FSM を管理できます。 次のコマンドを使用して、Linux または [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about) の bash ベースのシェルに FSM `fsm` クライアント バイナリをインストールします。 これらのコマンドは、`fsm` クライアント バイナリを `PATH` の標準的なユーザー プログラムの場所にコピーします。

```console
sudo mv ./linux-amd64/fsm /usr/local/bin/fsm
```

**macOS** の場合、次のコマンドを使用します。

```console
sudo mv ./darwin-amd64/fsm /usr/local/bin/fsm
```

次のコマンドを使用して、「fsm」クライアント ライブラリがパスとそのバージョン番号に正しく追加されたことを確認できます。

```console
fsm version
```
### ソースから (Linux、MacOS)

ソースから FSM をビルドするには、より多くの手順が必要ですが、最新の変更をテストする最良の方法であり、開発環境で役立ちます。

[Go](https://golang.org/doc/install) 環境が動作している必要があります。

```console
$ git clone git@github.com:flomesh-io/FSM .git
$ cd fsm
$ make build-fsm
```

`make build-fsm` は必要な依存関係を取得し、`fsm` をコンパイルして `bin/fsm` に配置します。 `$PATH` に `bin/fsm` を追加して、`fsm` を簡単に使用できるようにします。

## FSM をインストールする

### FSM 構成

デフォルトでは、コントロール プレーン コンポーネントは「fsm-system」と呼ばれる Kubernetes 名前空間にインストールされ、コントロール プレーンには、デフォルトで「fsm」に設定された一意の識別子属性「mesh-name」が与えられます。
インストール中に、`fsm` CLI を使用する場合はフラグを介して、または `helm` CLI を使用する場合は値ファイルを編集することで、ネームスペースとメッシュ名を設定できます。

`mesh-name` は、メッシュ インスタンスを識別および管理するために、インストール中に fsm-controller インスタンスに割り当てられる一意の識別子です。

`mesh-name` は、[RFC 1123](https://tools.ietf.org/html/rfc1123) DNS ラベルの制約に従う必要があります。 `mesh-name` は次の条件を満たさなければなりません:

- 最大 63 文字を含む
- 小文字の英数字または「-」のみを含む
- 英数字で始まる
- 英数字で終わる

### FSM CLI を使用する

「fsm」CLI を使用して、FSM コントロール プレーンを Kubernetes クラスターにインストールします。

`fsm install` を実行します。

```console
# Install fsm control plane components
$ fsm install
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

その他のオプションについては、「fsm install --help」を実行してください。
