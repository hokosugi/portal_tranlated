# 0.3: 開発環境のセットアップ

## 概要

開発者の旅を始める前に、開発者環境をセットアップする必要があります。開発者環境は、コードプロジェクトを開発するために必要なツールやパッケージで構成されます。通常、開発者環境はローカルコンピュータに保存されホストされますが、仮想的なウェブベースの開発環境が存在する状況もあります。

この例として、[Motoko Playground](https://m7sm4-2iaaa-aaaab-qabra-cai.ic0.app/) があります。これはウェブベースの仮想開発者環境で、開発者はローカル環境をセットアップすることなく使用できます。しかし、Motoko Playground にはいくつかの制限があり、単純で小規模なテスト以外のワークフローに使用することは推奨されません。

## 開発者環境のセットアップ

### インターネットに接続していることを確認します。

開発者の旅に沿ってInternet Computer で開発するには、インターネットへの接続が必要です。

#### なぜこれが重要なのでしょうか？

この文書でさらに説明するように、いくつかの異なるツールやパッケージをダウンロードするためにインターネット接続が必要です。また、canister をメインネットにデプロイする場合は、必ずインターネット接続が必要です。canister をローカルの実行環境にデプロイする場合は、インターネット接続は必要ありません。

### ローカルのmacOSまたはLinuxコンピュータでコマンドラインインターフェイス（CLI）にアクセスできることを確認します。

コマンドラインインターフェイス（CLI）ウィンドウを開きます。これは、お使いのコンピュータのオペレーティング・システムによって「Terminal」または「Shell」と呼ばれる場合があります。このドキュメントでは、これを「ターミナル・ウィンドウ」と呼ぶことがあります。

#### なぜこれが重要なのでしょうか？

この開発者の旅では、主に`dfx` や`git` といった CLI ベースのツールを使用します。

#### Windowsユーザーのためのオプション

`dfx` は Windows ではネイティブにサポートされていません。Windows で をダウンロードするには、Windows Subsystem for Linux をダウンロードする必要があります。詳しくは`dfx` [こちらを](/docs/developer-docs/setup/install/index.mdx)ご覧ください。

### IC SDKのダウンロードとインストール

IC SDKをダウンロードしてインストールするには、まずターミナルウィンドウを開きます。次に、そのウィンドウで次のコマンドを実行します：

    sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"

このコマンドは、インストールを開始する前に使用許諾契約書を読み、同意するよう促します。`y` と入力し、`Return` を押して同意書に同意し、インストールを開始します。

:::info
Apple Silicon を使用している場合は、Rosetta をインストールする必要があります。ターミナルで`softwareupdate --install-rosetta` を実行することで、[Rosetta を](https://support.apple.com/en-us/HT211861)インストールできます。
::：

次に、IC SDKが使用可能であることを確認するために、以下のコマンドを実行してください：

    dfx --version

このコマンドは、`dfx` の最新バージョンに関する情報を出力するはずです。この出力は、インストールが成功し、`dfx` が使用可能であることを示しています。

#### なぜこれが重要なのでしょうか？

IC SDKは、Internet Computer で開発するために必要ないくつかのコンポーネントで構成されています：

- `dfx`:canisters Motoko はdfxのインストールに含まれています。
- `moc`:Motoko ランタイムコンパイラ。
- `replica`:Internet Computer のローカルネットワークバイナリ。

### コードエディタのダウンロードとインストール

コードを書いたり編集したりするには、コードエディタが必要です。macOS や Linux システムには、`vi` や`nano` などの基本的なエディタが付属していますが、これらは機能が非常に限られており、使いにくいことがあります。

[Visual Studio Codeは](https://code.visualstudio.com/download)よく使われており、Motoko 開発のための追加ツールを提供する[Motoko 拡張](https://github.com/dfinity/vscode-motoko)機能があります。

#### なぜこれが重要なのでしょうか？

コードエディタは、コードを書いたり開発したりするための中心的な要素です。

### gitのダウンロードとインストール

[gitを](https://git-scm.com/downloads)ダウンロードしてインストールしてください。

#### なぜこれが重要なのでしょうか？

`examples` リポジトリのように、DFINITY の公開リポジトリの多くは Github でホストされています。開発者の旅の後半でこのリポジトリからのコードを使用することになるので、`git` をインストールして、サンプルコードをダウンロードし、後のチュートリアルに沿って進めるようにすることが重要です。

### Node.js のダウンロードとインストール

[node.](https://nodejs.org/en)js をダウンロードしてインストールします。

#### なぜこれが重要なのでしょうか？

Node.jsは`dfx` 、フロントエンドのコードと依存関係を生成するために使用されます。フロントエンドインターフェースを含まないdapps では必要ありませんが、この開発者の旅シリーズに従うには必要です。後のチュートリアルでフロントエンドcanisters を探求するからです。

### すべてのパッケージとツールが最新のリリースバージョンに更新されていることを確認してください。

このガイドに従って各ツールを初めてインストールした場合、最新のリリースバージョンがインストールされているはずです。

以前にこれらのツールのいずれかをインストールしていた場合は、必ず最新のリリースバージョンを確認し、必要に応じてアップデートしてください。

#### なぜこれが重要なのでしょうか？

最新のリリースバージョンを使用することで、各ツールの最新機能とバグ修正がすべて反映され、最もシームレスな開発体験が保証されます。

### 作業ディレクトリの作成

開発者環境のセットアップの最後のステップは、ビルドするための新しいディレクトリを作成することです。コマンドで新しいディレクトリを作成できます：

    mkdir developer_journey

今後のモジュールでは、このディレクトリを作業**ディレクトリと**呼ぶことにします。私たちが作成する各プロジェクトは、この作業ディレクトリの*サブディレクトリに*なります。

#### なぜこれが重要なのでしょうか？

この作業ディレクトリには、開発者としての旅を通して構築したプロジェクトを格納します。こうすることで、ローカルのファイル構造を整理しやすくなります。

## 次のステップ

- 0\.[4:canisters](04-intro-canisters.md) の[紹介](04-intro-canisters.md).

<!---
# 0.3: Developer environment setup

## Overview

Before we can begin our developer journey, we need to set up our developer environment. A developer environment consists of tools and packages that are required to develop code projects. Usually, developer environments are stored and hosted on your local computer, but there are some situations where a virtual, web-based development environment exists. 

An example of this is the [Motoko Playground](https://m7sm4-2iaaa-aaaab-qabra-cai.ic0.app/), which is a web-based, virtual developer environment that can be used by developers without having to set up a local environment. The Motoko Playground has several restrictions, however, and it isn't recommended to be used for workflows other than simple, small-scale testing. 

## Setting up your developer environment

### Confirm you have a connection to the internet

To follow along with the developer journey and develop on the Internet Computer, you will need a connection to the internet. 

#### Why does this matter?

You will need an internet connection to download a few different tools and packages, as described further in this document. You will also need an internet connection whenever you plan to deploy your canister to the mainnet. You do not need an internet connection to deploy your canister to your local execution environment.

### Confirm you have access to a command line interface (CLI) on your local macOS or Linux computer

Open a command line interface (CLI) window. This may be referred to as 'Terminal' or 'Shell' depending on your computer's operating system. In this documentation, this is often referred to as the 'terminal window'. 

#### Why does this matter?

We will primarily be using CLI-based tools, such as `dfx` and `git`, in this developer journey. 

#### Options for Windows users

`dfx` is not natively supported on Windows. To download `dfx` on Windows, you will need to download the Windows Subsystem for Linux. You can learn more [here](/docs/developer-docs/setup/install/index.mdx).

### Download and install the IC SDK

To download and install the IC SDK, first open a terminal window. Then, run the following command in that window:

```
sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
```

This command will prompt you to read and accept the license agreement before the install begins. Type `y`, then press `Return` to accept the agreement and begin the installation. 

:::info
If you are using a machine running Apple silicon, you will need to have Rosetta installed. You can install [Rosetta](https://support.apple.com/en-us/HT211861) by running `softwareupdate --install-rosetta` in your terminal.
:::

Then, to verify that the IC SDK is ready to use, run the following command:

```
dfx --version
```

This command should output information about the latest version of `dfx`. This output indicates that the installation has been successful, and that `dfx` is ready to use. 

#### Why does this matter?

The IC SDK is composed of several components that are required for developing on the Internet Computer. These components are:

- `dfx`: The CLI tool used to interact with and develop canisters on the IC. Motoko is included in the installation of dfx. 
- `moc`: The Motoko runtime compiler. 
- `replica`: The Internet Computer's local network binary. 

### Download and install a code editor

To write and edit code, you will need a code editor. macOS and Linux systems come with some basic editors, such as `vi` or `nano`, but these have very limited functionality and can be hard to use. 

We recommend you use [Visual Studio Code](https://code.visualstudio.com/download), as it is a popular choice and there is a [Motoko extension](https://github.com/dfinity/vscode-motoko) that provides additional tools for Motoko development. 

#### Why does this matter?

Code editors are a core component to writing and developing code. 

### Download and install git

Download and install [git](https://git-scm.com/downloads). 

#### Why does this matter?

Many of the DFINITY public repositories are hosted on Github, such as our `examples` repository. We will be using code from this repository later in the developer journey, so it is important to install `git` to assure that you can download the sample code pieces and follow along with the later tutorials. 

### Download and install Node.js

Download and install [node.js](https://nodejs.org/en).

#### Why does this matter?

Node.js is used by `dfx` to generate frontend code and dependencies. It is not required for dapps that do not contain a frontend interface, though it is required for you to follow along with this developer journey series, since we will explore frontend canisters in a later tutorial. 

### Assure all packages and tools are updated to the latest release versions

If you have followed this guide and installed each of these tools for the first time, you will have the most recent release versions installed.

If you previously had installed any of these tools, be sure to check the most recent release version and update them if needed. 
#### Why does this matter?

Having the latest release version assures that you have all of the newest features and bug fixes for each tool to assure for the most seamless developer experience. 

### Create a working directory

The last step in setting up our developer environment is to create a new directory for us to build in. You can create a new directory with the command:

```
mkdir developer_journey
```

In future modules, we'll refer to this directory as the **working directory**. Each project that we create will be a *sub-directory* of this working directory.

#### Why does this matter?

We'll use this working directory to contain the projects that we build throughout our developer journey. This will help keep things organized in our local file structure. 
## Next steps

- [0.4: Introduction to canisters](04-intro-canisters.md).

-->
