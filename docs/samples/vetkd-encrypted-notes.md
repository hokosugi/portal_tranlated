# VetKD暗号化ノートdapp

## 概要

この例dapp は、[暗号化されたノートdapp](./encrypted-notes.md)の新しいバージョンで、新しく[提案された vetKD 機能を](https://github.com/dfinity/interface-spec/pull/158)使用するように変更されています。

特に、dapp は、プリンシパル固有の AES キーを作成し、（デバイス固有の RSA キーによって）デバイス間で同期する代わりに、バックエンドcanister から取得されたプリンシパル固有の vetKey から（ブラウザで直接）導出される AES キーでノートが暗号化されるように変更されています。この鍵は、vetKD システム API から取得されるエフェメラル・トランスポート・キーを使用して、暗号化された形で取得されます。このように、dapp でデバイスを管理する必要はありません。

オリジナルの encrypted-notes-dapp とこの新しい vetKD の例の違いは https://github.com/dfinity/examples/pull/561 で見ることができます。

現在、このdapp を使用する唯一の方法は、以下に詳述するように、手動でローカルに配置することです。

詳細は[オリジナルのencrypted-notes-dapp のREADMEを](https://github.com/dfinity/examples/blob/master/motoko/encrypted-notes-dapp/README.md)参照してください。

## 免責事項

この例では、[vetkd\_system\_api.wasmを介して](https://github.com/dfinity/examples/blob/master/motoko/encrypted-notes-dapp-vetkd/vetkd_system_api.wasm)、コンパイル済みの形で[提案されているvetKDシステムAPIの](https://github.com/dfinity/interface-spec/pull/158) [**安全でない**実装を](https://github.com/dfinity/examples/tree/master/rust/vetkd/src/system_api)使用しています。本番**環境や機密性の高いデータには使用しないで**ください！このサンプルは、vetKDシステムAPIに関するフィードバックを収集するための**デモンストレーションのみを目的として**います。

## 前提条件

この例では以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install)インストールしてください。
- \[x\] ウェブフロントエンドを構築するために`node.js` をインストールしてください。
- \[x\] GitHubから以下のプロファイルファイルをダウンロードします: https://github.com/dfinity/examples/
- \[x\] 環境変数:Motoko の場合は`export BUILD_ENV=motoko` を、Rust の場合は`export BUILD_ENV=rust` を設定します。
- \[x\] Rustでデプロイする場合は、始める前に`rustup target add wasm32-unknown-unknown` 。

### ステップ 1: プロジェクトのファイルがあるフォルダに移動します：

    cd examples/motoko/encrypted-notes-dapp-vetkd

:::info
このプロジェクトフォルダには、Motoko と Rust コードの両方が含まれています。
::：

### ステップ 2:`$BUILD_ENV`-固有のファイル（つまり、Motoko または Rust）を生成するには、以下を実行します：

``` sh
sh ./pre_deploy.sh
```

### ステップ 3: プロジェクトルートから npm パッケージをインストールします：

``` sh
npm install
```

### ステップ4: dfxがすでに起動している場合は、以下を実行します：

``` sh
dfx stop
rm -rf .dfx
```

次に、コマンドでdfxを起動します：

``` sh
dfx start --clean --background
```

:::info
エラーが表示されたら、`Failed to set socket of tcp builder to 0.0.0.0:8000` 、ポート`8000` が、以前に実行したDockerコマンドなどで占有されていないことを確認します（このステップでは、Dockerデーモンを一切停止するとよいでしょう）。
::：

### ステップ5：ローカルの[インターネットID（II](https://wiki.internetcomputer.org/wiki/What_is_Internet_Identity)）をインストールするcanister ：

:::info
複数のdfx IDをセットアップしている場合は、`--identity` フラグで使用するIDを確認してください。
::：

canister ：

``` sh
dfx deploy internet_identity --argument '(null)'
```

次に、インターネット ID の URL を表示するには、以下を実行します：

``` sh
npm run print-dfx-ii
```

上記のコマンドの出力から URL にアクセスし、少なくとも 1 つのローカルインターネッ ト ID を作成します。

### ステップ 6: vetKD システム APIcanister をインストールします：

Canister SDK (dfx) がバックエンドcanister ソースコードにハードコードされているcanister ID を使用していることを確認します：

``` sh
dfx canister create vetkd_system_api --specified-id s55qq-oqaaa-aaaaa-aaakq-cai
```

canister をインストールし、デプロイします：

``` sh
dfx deploy vetkd_system_api
```

暗号化されたノートバックエンドcanister をデプロイします：

``` sh
dfx deploy "encrypted_notes_$BUILD_ENV"
```

生成されたcanister インターフェイスバインディングを更新します：

``` sh
dfx generate "encrypted_notes_$BUILD_ENV"
```

フロントエンドcanister をデプロイします：

``` sh
dfx deploy www
```

`npm run print-dfx-www` でURLを確認できます。

### ステップ7: フロントエンドを開きます：

ホットリロードにも対応したローカル開発サーバーを起動します：

``` sh
npm run dev
```

コンソール出力に表示されるURLを開いてください。通常、これは<http://localhost:3000/> です。

:::info
以前にこのページを開いたことがある場合は、ウェブブラウザからこのページのローカルストアデータをすべて削除し、ページをハードリロードしてください。例えばChromeの場合、Inspect → Application → Local Storage →`http://localhost:3000/` → Clear Allと進み、リロードしてください。
::：

<!---
# VetKD encrypted notes dapp

## Overview

This example dapp is a new version of the [encrypted notes dapp](./encrypted-notes.md) that has been altered to use the new [proposed vetKD feature](https://github.com/dfinity/interface-spec/pull/158).

Particularly, the dapp has been altered where instead of creating a principal-specific AES key and syncing it across devices (by means of device-specific RSA keys), the notes are encrypted with an AES key that is derived (directly in the browser) from a principal-specific vetKey obtained from the backend canister. This key is obtained in an encrypted form, using an ephemeral transport key, which obtains itself from the vetKD system API. This way, there is no need for any device management in the dapp.

The difference between the original encrypted-notes-dapp and the this new vetKD example can be seen in https://github.com/dfinity/examples/pull/561.

Currently, the only way to use this dapp is via manual local deployment, as detailed below.

Please see the [README of the original encrypted-notes-dapp](https://github.com/dfinity/examples/blob/master/motoko/encrypted-notes-dapp/README.md) for further details.

## Disclaimer

This example uses an [**insecure** implementation](https://github.com/dfinity/examples/tree/master/rust/vetkd/src/system_api) of [the proposed vetKD system API](https://github.com/dfinity/interface-spec/pull/158) in a pre-compiled form via the [vetkd_system_api.wasm](https://github.com/dfinity/examples/blob/master/motoko/encrypted-notes-dapp-vetkd/vetkd_system_api.wasm). **Do not use this in production or for sensitive data**! This example is solely provided **for demonstration purposes** to collect feedback on the mentioned vetKD system API.

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install).
- [x] Install `node.js` to build the web frontend.
- [x] Download the following profile files from GitHub: https://github.com/dfinity/examples/
- [x] Set environmental variable: `export BUILD_ENV=motoko` for Motoko or `export BUILD_ENV=rust` for Rust. 
- [x] If deploying in Rust, run `rustup target add wasm32-unknown-unknown` before getting started.

### Step 1: Navigate into the folder containing the project's files:

```
cd examples/motoko/encrypted-notes-dapp-vetkd
```

:::info 
This project folder contains both Motoko and Rust code.
:::

### Step 2: To generate `$BUILD_ENV`-specific files (i.e., Motoko or Rust) run:

```sh
sh ./pre_deploy.sh
```

### Step 3: Install npm packages from the project root:

```sh
npm install
```

### Step 4: If dfx was already started before, run the following:

```sh
dfx stop
rm -rf .dfx
```

Then, start dfx with the command:

```sh
dfx start --clean --background
```

:::info
If you see an error `Failed to set socket of tcp builder to 0.0.0.0:8000`, make sure that the port `8000` is not occupied, e.g., by the previously run Docker command (you might want to stop the Docker daemon whatsoever for this step).
:::

### Step 5: Install a local [Internet Identity (II)](https://wiki.internetcomputer.org/wiki/What_is_Internet_Identity) canister:

:::info
If you have multiple dfx identities set up, ensure you are using the identity you intend to use with the `--identity` flag.
:::

To install and deploy a canister run:

```sh
dfx deploy internet_identity --argument '(null)'
```

Then, to print the Internet Identity URL, run:

```sh
npm run print-dfx-ii
```

Visit the URL from the output of the command above and create at least one local Internet Identity.

### Step 6: Install the vetKD system API canister:

Ensure the Canister SDK (dfx) uses the canister ID that is hard-coded in the backend canister source code:

```sh
dfx canister create vetkd_system_api --specified-id s55qq-oqaaa-aaaaa-aaakq-cai
```

Install and deploy the canister:

```sh
dfx deploy vetkd_system_api
```

Deploy the encrypted notes backend canister:

```sh
dfx deploy "encrypted_notes_$BUILD_ENV"
```

Update the generated canister interface bindings: 

```sh
dfx generate "encrypted_notes_$BUILD_ENV"
```

Deploy the frontend canister:

```sh
dfx deploy www
```

You can check its URL with `npm run print-dfx-www`.

### Step 7: Open the frontend:

Start the local development server, which also supports hot-reloading:

```sh
npm run dev
```

Open the URL that is printed in the console output. Usually, this is [http://localhost:3000/](http://localhost:3000/).

:::info
If you have opened this page previously, please remove all local store data for this page from your web browser, and hard-reload the page. For example in Chrome, go to Inspect → Application → Local Storage → `http://localhost:3000/` → Clear All, and then reload.
:::

-->
