# 4: Rustクイックスタート

## 概要

Internet Computer ブロックチェーン用のアプリケーションは、**プロジェクトとして**開始します。IC SDK を使用して、Internet Computer ブロックチェーン用の新しいプロジェクトを作成できます。このプロジェクトはInternet Computer ブロックチェーンにデプロイするために構築するため、このガイドでは`dfx` 親コマンドとそのサブコマンドを使用して Rust プログラムを作成、ビルド、デプロイする方法を中心に説明します。

## 前提条件

始める前に、[開発者環境ガイドの](./3-dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新しい Rust プロジェクトの作成

IC SDK を使用して新しい Rust プロジェクトを作成するには、ターミナルウィンドウを開き、以下のコマンドを実行します：

    dfx new --type=rust <project_name>

ここで、`<project name>` はプロジェクト名を表します。たとえば、`rust_hello` という名前のプロジェクトを作成するには、次のコマンドを実行します：

    dfx new --type=rust rust_hello

`dfx new --type=rust rust_hello` コマンドを実行すると、`rust_hello` プロジェクトディレクトリ、テンプレートファイル、`rust_hello` Git リポジトリが作成されます。

次に、以下のコマンドを実行してプロジェクト・ディレクトリに移動します：

    cd rust_hello

## ローカル実行環境の開始

次に、プロジェクトをビルドする前に、開発環境で稼働しているローカル実行環境か、分散型のInternet Computer メインネットのどちらかに接続する必要があります。このガイドでは、開発環境のローカルにデプロイすることにします。メインネットへのデプロイの詳細については、次のステップの[デプロイcanisters](./5-deploying.md) を参照してください。

ローカル実行環境を起動するには、まずプロジェクトのルートディレクトリにいることを確認します。

次に、以下のコマンドを実行して、バックグラウンドでローカル実行環境を起動します：

    dfx start --background

:::info
プラットフォームとローカルのセキュリティ設定によっては、警告が表示されることがあります。受信ネットワーク接続を許可または拒否するようプロンプトが表示された場合は、\[**許可\]** をクリックします。
::：

## プロジェクトの登録、ビルド、デプロイ

開発環境で実行されているローカル実行環境に接続したら、プロジェクトをローカルに登録、ビルド、デプロイできます。

以下のコマンドを実行して、`dfx.json` ファイルで指定したcanisters を登録、ビルド、デプロイします：

    dfx deploy

`dfx deploy` コマンドの出力には、以下の抜粋のような各操作に関する情報が表示されます：

    Creating a wallet canister on the local network.
    The wallet canister on the "local" network for user "default" is "rwlgt-iiaaa-aaaaa-aaaaa-cai"
    Deploying all canisters.
    Creating canisters...
    Creating canister rust_hello_backend...
    rust_hello_backend canister created with canister id: rrkah-fqaaa-aaaaa-aaaaq-cai
    Creating canister rust_hello_frontend...
    rust_hello_frontend canister created with canister id: ryjl3-tyaaa-aaaaa-aaaba-cai
    Building canisters...
    
    ...
    
    Deployed canisters.
    URLs:
    Frontend canister via browser
        rust_hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
    Backend canister via Candid interface:
        rust_hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai

## のテストdapp

canisters とやりとりする方法はいくつかあります。まず、ブラウザからのアクセスについて説明しましょう。

フロントエンドcanister (`rust_hello_frontend`) にアクセスするには、前のステップでターミナルに表示されたリンクをたどるだけです。

```
    Frontend canister via browser
        rust_hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
```

フロントエンドcanisters は**アセットcanisters** とも呼ばれ、スマートコントラクトから直接ウェブコンテンツにアクセスすることができます。これにより、バックエンドだけでなくdapp 全体をInternet Computer にデプロイすることができます。

バックエンドcanister (`rust_hello_backend`) にアクセスするには、前のステップで端末に出力された URL を再度開きます。

    Backend canister via Candid interface:
            rust_hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai

この方法を使って [`Candid UI` canister](https://github.com/dfinity/candid/tree/master/tools/ui)`dfx deploy` を最初に実行したときに、自動的にローカルレプリカにデプロイされます。`Candid UI` canister はバックエンドcanister からインタフェースをフェッチし、ビジュアルなウェブインタフェースでcanister の API をテストしたりブラウズしたりできます。

dfxを使用してコマンドラインからローカルにデプロイされた`rust_hello_backend` をテストするには、次のコマンドを実行して`greet` 関数を実行します：

    dfx canister call rust_hello_backend greet world

次に、`rust_hello_backend` canister `greet` 関数の呼び出しがテキスト・メッセージ`("Hello, world!")` を返すことを確認します。

## ローカル実行環境の停止

アプリケーションをテストした後、ローカル実行環境を停止して、バックグラウンドで実行し続けないようにすることができます。

コンピュータ上で実行されているローカル実行環境を停止するには、次のコマンドを実行します：

    dfx stop

今後dfxを再度起動する場合は、`dfx start --clean` コマンドで起動し、以前のステート状態を消去する必要があります。

## 次のステップ

では、次のステップで、dfxの記述と[デプロイについて](5-deploying.md)詳しく見ていきましょう。[ canisters](5-deploying.md)

<!---
# 4: Rust quick start

## Overview

Applications for the Internet Computer blockchain start as **projects**. You can create new projects for the Internet Computer blockchain using the IC SDK. Because you are building this project to be deployed on the Internet Computer blockchain, this guide focuses on how to create, build, and deploy a Rust program by using the `dfx` parent command and its subcommands.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./3-dev-env.md).

## Creating a new Rust project

To create a new Rust project using the IC SDK, open a terminal window and run the following command:

```
dfx new --type=rust <project_name>
```

where `<project name>` represents the name of your project. For example, to create a project with the name `rust_hello`, use the command:

```
dfx new --type=rust rust_hello
```

The `dfx new --type=rust rust_hello` command creates a new `rust_hello` project directory, template files, and a new `rust_hello` Git repository for your project.

Next, navigate into your project directory by running the following command:

```
cd rust_hello
```

## Starting the local execution environment

Next, before you can build your project, you need to connect to either the local execution environment running in your development environment or the decentralized Internet Computer mainnet. For this guide, we'll deploy locally in our developer environment. For more information on deploying on the mainnet, check out the next step [deploying canisters](./5-deploying.md).

To start the local execution environment, first check that you are still in the root directory for your project, if needed.

Then, start the local execution environment on your computer in the background by running the following command:

```
dfx start --background
```

:::info
Depending on your platform and local security settings, you might see a warning displayed. If you are prompted to allow or deny incoming network connections, click **Allow**.
:::

## Register, build, and deploy your project

After you connect to the local execution environment running in your development environment, you can register, build, and deploy your project locally.

Register, build, and deploy the canisters specified in the `dfx.json` file by running the following command:

```
dfx deploy
```

The `dfx deploy` command output displays information about each of the operations it performs similar to the following excerpt:

```
Creating a wallet canister on the local network.
The wallet canister on the "local" network for user "default" is "rwlgt-iiaaa-aaaaa-aaaaa-cai"
Deploying all canisters.
Creating canisters...
Creating canister rust_hello_backend...
rust_hello_backend canister created with canister id: rrkah-fqaaa-aaaaa-aaaaq-cai
Creating canister rust_hello_frontend...
rust_hello_frontend canister created with canister id: ryjl3-tyaaa-aaaaa-aaaba-cai
Building canisters...

...

Deployed canisters.
URLs:
Frontend canister via browser
    rust_hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
Backend canister via Candid interface:
    rust_hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai
```

## Testing the dapp

There are several ways to interact with your canisters. Let's talk about access from the browser first. 

To access the frontend canister (`rust_hello_frontend`) you can simply follow the link that was printed in the terminal in the previous step.
```
    Frontend canister via browser
        rust_hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
```

Frontend canisters, also called **asset canisters**, allow access to web content directly from a smart contract. This enables you to deploy your entire dapp, not just the backend, on the Internet Computer.

To access the backend canister (`rust_hello_backend`) you can again open the URL printed to your terminal in the previous step.
```
Backend canister via Candid interface:
        rust_hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai
```
Using this method, we open the so called [`Candid UI` canister](https://github.com/dfinity/candid/tree/master/tools/ui) that is automatically deployed to our local replica when you first run `dfx deploy`. The `Candid UI` canister fetches the interface from your backend canister and allows you to test and browse your canister's API with a visual web interface.

To test the deployed `rust_hello_backend` locally from your command line using dfx, you can run the `greet` function by running the following command:

```
dfx canister call rust_hello_backend greet world
```

Then, verify that the call to the `rust_hello_backend` canister `greet` function returns a text message `("Hello, world!")`.

## Stopping the local execution environment

After testing the application, you can stop the local execution environment so that it doesn’t continue running in the background.

To stop the local execution environment running on your computer, run the following command:

```
dfx stop
```

When dfx is started again in the future, it should be started with the `dfx start --clean` command to wipe the previous state. 

## Next steps

Now, let's take a closer look into writing and [deploying canisters](5-deploying.md)

-->
