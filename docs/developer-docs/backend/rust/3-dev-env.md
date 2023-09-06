# 3: 開発環境

## 環境のセットアップ

プロジェクトを開始する前に、以下を確認してください：

- \[x\] インターネットに接続されており、ローカルの macOS または Linux コンピュータでシェル端末にアクセスできること。

- \[x\] コマンドラインインターフェイス（CLI）ウィンドウが開いていること。このウィンドウは「ターミナル」ウィンドウとも呼ばれます。

- \[x\] コンピュータ上で実行中のローカル実行環境プロセスを停止しています。

- \[x\][npmを](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)インストールしました。

- \[x\] Rustプログラミング言語とCargoを、お使いのオペレーティングシステムの[Rustインストール](https://doc.rust-lang.org/book/ch01-01-installation.html)手順に記載されているとおりにダウンロードしてインストールしました。
  
  ``` bash
  curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
  ```

- \[x\]`wasm32-unknown-unknown` ターゲットをインストールしました：
  
  ``` bash
  rustup target add wasm32-unknown-unknown
  ```

- \[x\][IC SDKのインストール](./../../setup/install/index.mdx)ページに記載されているように、IC SDKパッケージをダウンロードしてインストールしました。
  
  [IC SDK セットアップガイドには](../../setup/install/index.mdx)、Internet Computer メインネット上で実行する[Rust スマートコントラクト](../choosing-language.md)やdapps を作成するのに役立つツール、サンプルコード、ドキュメントが用意されています。セットアップガイドは、IC SDKを初めてインストールすることを前提としています。
  
  Rust開発をサポートするため、IC SDKには[Rustcanister 開発キット（Rust CDK](https://github.com/dfinity/cdk-rs)）が含まれています。
  
  **ほとんどの開発者はIC SDKを使用しますが、経験豊富なRust開発者はIC SDKを完全に回避し、[Rust CDKを](https://github.com/dfinity/cdk-rs)直接使用することもできます。**

- \[x\] コードエディタをインストールしていること。Rust では[VS Code IDE](https://code.visualstudio.com/download)がよく使われています。

- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールしていること。

- \[x\] 上記のすべてのパッケージとツールが最新のリリースバージョンにアップデートされていることを確認してください。

## 次のステップ

上記の手順を踏んだら、Rust を使ってバックエンドcanisters の開発を始める準備ができました！まずは[Rust クイックスタートガイド](./4-quickstart.md) をご覧ください。

<!---
# 3: Developer environment 

## Setting up your environment 

Before you start your project, verify the following:

- [x] You have an internet connection and access to a shell terminal on your local macOS or Linux computer.

- [x] You have a command line interface (CLI) window open. This window is also referred to as the 'terminal' window.

- [x] You have stopped any local execution environment processes running on your computer.

- [x] You have installed [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).

- [x] You have downloaded and installed the Rust programming language and Cargo as described in the [Rust installation instructions](https://doc.rust-lang.org/book/ch01-01-installation.html) for your operating system.

    ``` bash
    curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
    ```

- [x] You have installed the `wasm32-unknown-unknown` target:

    ``` bash
    rustup target add wasm32-unknown-unknown
    ```

- [x]  You have downloaded and installed the IC SDK package as described in the [installing the IC SDK](./../../setup/install/index.mdx) page.

    The [IC SDK setup guide](../../setup/install/index.mdx) provides tools, sample code, and documentation to help you create [Rust smart contracts](../choosing-language.md) and dapps to run on the Internet Computer mainnet. The setup guide assumes that you are installing the IC SDK for the first time.

    To support Rust development, the IC SDK includes the [Rust canister development kit (Rust CDK)](https://github.com/dfinity/cdk-rs). 

    **While using the IC SDK is the typical path for most developers, experienced Rust developers may choose to circumvent IC SDK entirely and use the [Rust CDK](https://github.com/dfinity/cdk-rs) directly.**

- [x] You have a code editor installed. The [VS Code IDE](https://code.visualstudio.com/download) is a popular choice for Rust.

- [x] You have downloaded and installed [git](https://git-scm.com/downloads).

- [x] Assure that all packages and tools above are updated to the latest release versions. 

## Next steps

After following the steps above, you're ready to get started developing backend canisters with Rust! To get started, check out the [Rust quick start guide](./4-quickstart.md).

-->
