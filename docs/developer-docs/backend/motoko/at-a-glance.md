# 4:Motoko クイックスタート

## 概要

[1.3: 最初のデプロイdapp](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md)開発者の旅 チュートリアルは、シンプルなデフォルトアプリケーションをデプロイするための素早い道筋を提供します。このセクションの個々のチュートリアルは、各ステップで行っていることの詳細を指摘しながら、特定のシナリオを歩んでいきます。

クイックスタートやチュートリアルがあなたのスタイルに合わない場合、この一目でわかるチートシートは、素早く参照できるように従うべきステップを要約しています。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていない場合は、ローカル・コンピューターでターミナル・ウィンドウを開きます。

新しいプロジェクトを作成し、プロジェクト・ディレクトリに移動します。

    dfx new <project_name> && cd <project_name>>

## デフォルトファイルの編集

`src/<project_name>_backend` ファイルを編集して、サービスやアプリケーションを定義します。

サービスまたはアプリケーションのフロントエンドを提供する HTML、JavaScript、および CSS を含む`src/<project_name>_frontend` ファイルを編集します。

## デプロイ環境の起動

ローカル開発用にInternet Computer を起動するか、ネットワーク デプロイメント用にInternet Computer への接続を確認します：

- [ローカル](../../setup/deploy-locally.md) デプロイメント
- [メインネットデプロイメント](../../setup/deploy-mainnet.md)。

## ローカルまたはメインネットへの登録、ビルド、デプロイ

メインネットには、`--network ic` を使用します。

    dfx deploy --network <network>

## `dfx deploy` コマンドの出力の URLS を使用して、サービスまたはアプリケーションをブラウザで表示します：

    ...
    Committing batch.
    Committing batch with 18 operations.
    Deployed canisters.
    URLs:
    Frontend canister via browser
            access_hello_frontend: http://127.0.0.1:8080/?canisterId=cuj6u-c4aaa-aaaaa-qaajq-cai
    Backend canister via Candid interface:
            access_hello_backend: http://127.0.0.1:8080/?canisterId=cbopz-duaaa-aaaaa-qaaka-cai&id=ctiya-peaaa-aaaaa-qaaja-cai

## 次のステップ

canisters の作成とデプロイに関する詳細については、[ canisters の作成とデプロイのページを](deploying.md)参照してください。

<!---
# 4: Motoko quick start

## Overview

The [1.3: Deploying your first dapp](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md) developer journey tutorial provides a fast path to deploying a simple default application without stopping to admire the scenery along the way. Individual tutorials in this section walk through specific scenarios, pointing out details about what you’re doing in each step.

If the quick start and tutorials are not quite your style, this at-a-glance cheat sheet summarizes the steps to follow for quick reference.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Creating a new project

Open a terminal window on your local computer, if you don’t already have one open.

Create a new project and change to the project directory.

```
dfx new <project_name> && cd <project_name>>
```

## Editing the default files

Edit the `src/<project_name>_backend` files to define your service or application.

Edit the `src/<project_name>_frontend` files with HTML, JavaScript, and CSS that provides the frontend for your service or application.

## Starting the deployment environment

Start the Internet Computer for local development or check your connection to the Internet Computer for network deployment:
- [Local deployment](../../setup/deploy-locally.md).
- [Mainnet deployment](../../setup/deploy-mainnet.md).

## Register, build, and deploy locally or on the mainnet 

For the mainnet, use: `--network ic`.

```
dfx deploy --network <network>
```

## View your service or application in a browser, using the URLS in the output of the `dfx deploy` command:

```
...
Committing batch.
Committing batch with 18 operations.
Deployed canisters.
URLs:
Frontend canister via browser
        access_hello_frontend: http://127.0.0.1:8080/?canisterId=cuj6u-c4aaa-aaaaa-qaajq-cai
Backend canister via Candid interface:
        access_hello_backend: http://127.0.0.1:8080/?canisterId=cbopz-duaaa-aaaaa-qaaka-cai&id=ctiya-peaaa-aaaaa-qaaja-cai
```

## Next steps

For a more detailed look at writing and deploying canisters, move onto the [writing and deploying canisters page](deploying.md).
-->
