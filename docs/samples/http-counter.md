# HTTPカウンタ

## 概要

このサンプルは、dapp カウンターと HTTP インターフェースのデモです。これは基本的に、ネイティブの HTTP インタフェースを追加したカウンタcanister の繰り返しです。

このサンプルdapp は、以下のメソッドを公開するインタフェースを提供します：

- `http_request`このサンプルは、以下のメソッドを公開するインタフェースを提供します：
  - `GET` `gzip` が受け入れられた場合、いくつかの静的な ed データ。`gzip`
  - `GET` そうでない場合は、カウンタ。
  - `POST`を参照して`http_request_update` を呼び出します。
  - `400` その他のすべてのリクエストを返します。
- `http_request_update`カウンタをインクリメントします：
  - `POST` カウンタをインクリメントします。
    - `gzip` が受け入れられた場合、静的な`gzip`ed データを返します。
    - そうでない場合は、新しいカウンタ値を返します。
  - `400` その他のすべてのリクエストを返します。

## 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールします。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/http_counter
    dfx start --background

### ステップ 2:canister をデプロイします：

    dfx deploy

### ステップ 3:`http_counter` にアクセスできる URL を作成するため、canister の ID をメモしておきます。

``` bash
CANISTER_ID=$(dfx canister id http_counter)

echo "http://localhost:4943/?canisterId=$CANISTER_ID"

echo "http://$CANISTER_ID.localhost:4943/"
```

### ステップ4：canister のすべての機能は、以下のコマンドで実行できます：

``` bash
CANISTER_ID=$(dfx canister id http_counter)

# Get the counter
curl "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Get the static gziped query content
curl --compressed "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Increment the counter
curl -X POST "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Increment the counter and get the static gziped update content
curl --compressed -X POST "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"
```

<!---
# HTTP counter

## Overview

The example demonstrates a counter dapp and an HTTP interface. It is essentially an iteration on the counter canister which adds native HTTP interfaces.

This sample dapp provides an interface that exposes the following methods:

*  `http_request`, which can:
    * `GET` some static `gzip`ed data if `gzip` is accepted.
    * `GET` the counter otherwise.
    * Refer `POST`s to call `http_request_update`.
    * Returns `400` all other requests.
* `http_request_update`, which can:
    * `POST` to increment the counter.
        * returning some static `gzip`ed data if `gzip` is accepted.
        * otherwise returning the new counter value.
    * Returns `400` all other requests.


## Prerequisites 

This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/http_counter
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

### Step 3: Take note of canister ID to form URLs at which the `http_counter` is accessible.

```bash
CANISTER_ID=$(dfx canister id http_counter)

echo "http://localhost:4943/?canisterId=$CANISTER_ID"

echo "http://$CANISTER_ID.localhost:4943/"
```

### Step 4: All functionality of the canister can be exercised with the following commands:

```bash
CANISTER_ID=$(dfx canister id http_counter)

# Get the counter
curl "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Get the static gziped query content
curl --compressed "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Increment the counter
curl -X POST "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"

# Increment the counter and get the static gziped update content
curl --compressed -X POST "$CANISTER_ID.localhost:4943/" --resolve "$CANISTER_ID.localhost:4943:127.0.0.1"
```


-->
