# vetKeys APIデモ

## 概要

このデモは、https://github.com/dfinity/interface-spec/pull/158 で提案されている vetKD システム API を提供するcanister (`src/system_api`) を、デモ用に安全でない方法で実装したものです。

このデモでは、[このリポジトリに](https://github.com/dfinity/examples/tree/master/rust/vetkd)あるファイルを使用します。

## 前提条件

- \[x\] IC SDKの[インストール](./../../setup/install/)ページで説明されているように、IC SDKパッケージをダウンロードしてインストールしてください。
- \[x\][npmを](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)ダウンロードしてインストールしてください。
- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールします。

### ステップ1: このプロジェクトを含むGithubリポジトリのクローンを作成します：

    git clone https://github.com/dfinity/examples/

次に、このプロジェクト専用のディレクトリに移動します：

Motoko ：

    cd examples/motoko/vetkd

Rustデプロイの場合：

    cd examples/rust/vetkd

### ステップ2: 次に、Internet Computer のローカルインスタンスを起動します：

``` sh
dfx start --clean --background
```

### ステップ3:Canister SDK (dfx)がRustソースコードにハードコードされているcanister IDを使用していることを確認します：

``` sh
dfx canister create system_api --specified-id s55qq-oqaaa-aaaaa-aaakq-cai
```

これを行わないと、Canister SDK (dfx)は、`system_api` と`app_backend` canisters に対して、ローカル環境で異なるcanister ID を使用する可能性があります。

### ステップ 4: 必要であれば、以下のコマンドを実行して、必要なノードモジュールがプロジェクトディレクトリで利用可能であることを確認します：

``` sh
npm install
```

### ステップ 5: プロジェクトを登録、ビルド、デプロイします：

``` sh
dfx deploy
```

このコマンドは、次のような出力で正常に終了するはずです：

``` sh
Deployed canisters.
URLs:
Frontend canister via browser
 app_frontend_js: http://127.0.0.1:4943/?canisterId=by6od-j4aaa-aaaaa-qaadq-cai
Backend canister via Candid interface:
 app_backend: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=tcvdh-niaaa-aaaaa-aaaoa-cai
 app_frontend: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=b77ix-eeaaa-aaaaa-qaada-cai
 system_api: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=s55qq-oqaaa-aaaaa-aaakq-cai
```

### ステップ 6:`app_frontend_js` の印刷された URL をブラウザで開きます。

<!---
# vetKeys API demo

## Overview

This demo provides a canister (`src/system_api`) that offers the vetKD system API proposed in https://github.com/dfinity/interface-spec/pull/158, implemented in an unsafe manner for demonstration purposes.

This demo uses files found in [this repository](https://github.com/dfinity/examples/tree/master/rust/vetkd).

## Prerequisites

- [x] Download and install the IC SDK package as described in the [installing the IC SDK](./../../setup/install/) page.
- [x] Download and install [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- [x] Download and install [git](https://git-scm.com/downloads).


### Step 1: Begin by cloning the Github repository containing this project:

```
git clone https://github.com/dfinity/examples/
```

Then navigate into the directory specifically for this project:

For Motoko deployment:

```
cd examples/motoko/vetkd
```

For Rust deployment:

```
cd examples/rust/vetkd
```

### Step 2: Then, start a local instance of the Internet Computer:

```sh
dfx start --clean --background
```

### Step 3: Ensure the Canister SDK (dfx) uses the canister IDs that are hard-coded in the Rust source code:

```sh
dfx canister create system_api --specified-id s55qq-oqaaa-aaaaa-aaakq-cai
```

Without this, the Canister SDK (dfx) may use different canister IDs for the `system_api` and `app_backend` canisters in your local environment.

### Step 4: Ensure that the required node modules are available in your project directory, if needed, by running the following command:

```sh
npm install
```

### Step 5: Register, build and deploy the project:

```sh
dfx deploy
```

This command should finish successfully with output similar to the following one:

```sh
Deployed canisters.
URLs:
Frontend canister via browser
 app_frontend_js: http://127.0.0.1:4943/?canisterId=by6od-j4aaa-aaaaa-qaadq-cai
Backend canister via Candid interface:
 app_backend: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=tcvdh-niaaa-aaaaa-aaaoa-cai
 app_frontend: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=b77ix-eeaaa-aaaaa-qaada-cai
 system_api: http://127.0.0.1:4943/?canisterId=avqkn-guaaa-aaaaa-qaaea-cai&id=s55qq-oqaaa-aaaaa-aaakq-cai
```

### Step 6: Open the printed URL for the `app_frontend_js` in your browser.

-->
