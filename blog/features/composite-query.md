---

title: Building a partitioned key-value store with composite queries on the Internet Computer
description: "Today, we’ll dive into composite queries and walk through the process of building a sample app: a partitioned key-value store. Each partition is represented by a single canister. We'll leverage the power of the Internet Computer’s composite queries to efficiently retrieve data from those partition canisters."
tags: [New features]
image: /img/blog/dev-update-blog-composite-query.png
---
# 複合クエリによるパーティショニングされたキー・バリュー・ストアの構築Internet Computer

別の開発者ブログ記事へようこそ！本日は、コンポジット・クエリに飛び込み、dapp: パーティショニングされたキーバリューストアのサンプルを構築するプロセスを説明します。各パーティションは、単一のcanister で表されます。Internet Computerの複合クエリのパワーを活用して、これらのパーティションcanisters からデータを効率的に取得します。

要するに、パーティショニングされたキーバリュー・ストアは、複数のバックエンドを持つ単一のフロントエンドとして構造化されています。各バックエンドは、キーバリューストアの1つのパーティションを表します。

![Partitioned key-value store](/img/blog/dev-update-blog-composite-query.png)

## フロントエンドのコード

フロントエンドのコードは、プットおよびゲットの呼び出しに対して以下のことを行います：

- 与えられたキーを持つパーティションを保持するcanister のIDを決定します。
- そのcanister の`get` または`put` 関数を呼び出し、結果を解析します。

次のコードはフロントエンドのコードを簡略化したものです。新しい複合クエリ機能を活用するために使用されている`#[query(composite = true)]` 行に注意してください：

``` rust
#[query(composite = true)]
async fn frontend_get(key: u128) -> Option<u128> {
    let canister_id = get_partition_for_key(key);
    match call(canister_id, "get", (key, ), ).await {
        Ok(r) => {
            let (res,): (Option<u128>,) = r;
            res
        },
        Err(_) => None,
    }
}
```

完全を期すため、`put` コードは複合クエリーコールの恩恵を受けることができません。キーバリューストアに値を追加すると、canisterのステートが変更されるため、`update` コールとして実装する必要があります。

``` rust
#[update]
async fn put(key: u128, value: u128) -> Option<u128> {
    let canister_id = get_partition_for_key(key);
    match call(canister_id, "put", (key, value), ).await {
        Ok(r) => {
            let (res,): (Option<u128>,) = r;
            res
        },
        Err(_) => None,
    }
}
```

## バックエンドのコード

バックエンドは、キーと値のペアを`BTreeMap` 、安定したメモリに格納するだけです：

``` rust
#[update]
fn put(key: u128, value: u128) -> Option<u128> {
    STORE.with(|store| store.borrow_mut().insert(key, value))
}

#[query]
fn get(key: u128) -> Option<u128> {
    STORE.with(|store| store.borrow().get(&key))
}
```

それだけです！

完全なコードは[ここに](https://github.com/dfinity/examples/tree/master/rust/composite_query)あります。

Motoko の別の実装は[こちら](https://github.com/dfinity/examples/tree/master/motoko/composite_query)。

## 複合クエリの使用

はじめに、開発環境をセットアップしましょう。コンピュータに[dfxが](https://internetcomputer.org/docs/current/developer-docs/setup/install/)インストールされていることを確認してください。コンポジットクエリをサポートするためには、少なくともdfxのバージョン0.15.0が必要です。ターミナルを開き、以下のコマンドに従ってください：

``` bash
DFX_VERSION=0.15.0-beta.1 sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
```

次に、以下のようにICサンプルアプリをクローンします：

``` bash
git clone https://github.com/dfinity/examples.git
```

## サンプルのデプロイcanister

まず、dfxを介してローカルのICインスタンスを起動し、フロントエンドcanister を作成してビルドする必要があります：

``` bash
cd rust/composite_query/src
dfx start
dfx canister create kv_frontend
dfx build kv_frontend
```

フロントエンドcanister のコンパイル中に、バックエンドcanister のwasmコードがコンパイルされ、フロントエンドcanister のwasmコードにインライン化されます。
最後に、フロントエンドcanister をインストールしましょう：

``` bash
dfx canister install kv_frontend
```

素晴らしい！パーティショニングされたKey-Valueストアのセットアップが完了しました。では、その機能を探ってみましょう。

## キー・バリュー・ストアとの対話canister

フロントエンドcanister を使ってキーと値のペアを追加するには、ターミナルで次のコマンドを実行してください：

``` bash
$ dfx canister call kv_frontend put '(1, 1337)'
(null)
```

データパーティションcanisters を最初に作成する必要があるため、putへの最初の呼び出しのレスポンスが遅くなる可能性があることに留意してください。
では、複合クエリーの力を使って、キーに関連付けられた値を取得してみましょう：

``` bash
$ dfx canister call kv_frontend get '(1)'
(opt (42 : nat))
```

 `get_update` では、コンポジットクエリコールと、アップデート関数からのコールを活用した同等の実装のパフォーマンスを比較してみましょう：

``` bash
$ dfx canister call kv_frontend get_update '(1)'
(opt (1_337 : nat))
```

。さらに、2つのクエリーコールをオーケストレーションすることができます。最初はフロントエンドcanister 、次にデータパーティションcanister 。これは複合クエリーコールと同じような待ち時間ですが、クライアント側に余分なロジックが必要です。

``` bash
$ dfx canister call kv_frontend lookup '(1)'
(1 : nat, "dmalx-m4aaa-aaaaa-qaanq-cai")
$ dfx canister call dmalx-m4aaa-aaaaa-qaanq-cai get '(1: nat)' --query
(1_337 : nat)
```

まとめると、複合クエリーを使用することで、クライアント側をシンプルに保ちながら、低レイテンシーを実現します。これは、dapps 、複数のcanisters にデータを分割して垂直方向にスケーリングする場合に特に有効です。

おめでとうございます！Rust を使用してキーバリューストアを構築し、Internet Computer の強力な複合クエリ機能を活用することに成功しました。これにより、canisters から効率的にデータを取得できるようになります。

このブログ記事がお役に立ちましたら幸いです。コンポジットクエリ機能を使ってハッピーコーディング！
コンポジットクエリ機能に貢献してくれたDFINITY に感謝します：Adam Spofford、Claudio Russo、Martin Raszyk、Robin Künzler、Roel Storms、Stefan Kaestle、Ulan Degenbaev、Yan Chen。

<!---


# Building a partitioned key-value store with composite queries on the Internet Computer

Welcome to another developer blog post! Today, we’ll dive into composite queries and walk through the process of building a sample dapp: a partitioned key-value store. Each partition is represented by a single canister. We'll leverage the power of the Internet Computer’s composite queries to efficiently retrieve data from those partition canisters.

In essence, the partitioned key-value store is structured as a single frontend with multiple backends. Each backend represents one partition of the key-value store.

![Partitioned key-value store](/img/blog/dev-update-blog-composite-query.png)

## Frontend code
The frontend code does the following for a put and get call:

 * Determines the ID of the canister that holds the partition with the given key.
 * Makes a call into the `get` or `put` function of that canister and parsing of the result.

The following code shows a simplified version of the frontend code. Note the line `#[query(composite = true)]` which is used to leverage the new composite query feature:

```rust
#[query(composite = true)]
async fn frontend_get(key: u128) -> Option<u128> {
    let canister_id = get_partition_for_key(key);
    match call(canister_id, "get", (key, ), ).await {
        Ok(r) => {
            let (res,): (Option<u128>,) = r;
            res
        },
        Err(_) => None,
    }
}
```

For completeness, the `put` code cannot benefit from composite query calls, as adding values to the key value store modifies the canister’s state and therefore needs to be implemented as an `update` call.

```rust
#[update]
async fn put(key: u128, value: u128) -> Option<u128> {
    let canister_id = get_partition_for_key(key);
    match call(canister_id, "put", (key, value), ).await {
        Ok(r) => {
            let (res,): (Option<u128>,) = r;
            res
        },
        Err(_) => None,
    }
}
```

## Backend code
The backend simply stores the key value pairs in a `BTreeMap` in stable memory:

```rust
#[update]
fn put(key: u128, value: u128) -> Option<u128> {
    STORE.with(|store| store.borrow_mut().insert(key, value))
}

#[query]
fn get(key: u128) -> Option<u128> {
    STORE.with(|store| store.borrow().get(&key))
}
```

And that’s it!

The complete code can be found [here](https://github.com/dfinity/examples/tree/master/rust/composite_query).

An alternative implementation for Motoko can be found [here](https://github.com/dfinity/examples/tree/master/motoko/composite_query).

## Using composite queries
To start, let's set up our development environment. Make sure you have [dfx](https://internetcomputer.org/docs/current/developer-docs/setup/install/) installed on your computer. You will need at least version 0.15.0 of dfx for composite query support. Open your terminal and follow these commands:

```bash
DFX_VERSION=0.15.0-beta.1 sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
```

Then clone the IC sample apps as follows:

```bash
git clone https://github.com/dfinity/examples.git
```

## Deploy the example canister
We first need to start a local IC instance via dfx and then create and build our frontend canister:

```bash
cd rust/composite_query/src
dfx start
dfx canister create kv_frontend
dfx build kv_frontend
```

During compilation of the fronted canister, the backend canister's wasm code will be compiled and inlined in the frontend canister's wasm code.
Finally, let’s install the frontend canister:

```bash
dfx canister install kv_frontend
```

Excellent! We have our partitioned key-value store set up and ready to go. Now, let's explore its capabilities.

## Interacting with the canister
To add a key-value pair via the frontend canister, run the following command in your terminal:

```bash
$ dfx canister call kv_frontend put '(1, 1337)'
(null)
```

Keep in mind that the first call to put might be slow to respond because the data partition canisters have to be created first.
Now, let's retrieve the value associated with a key using the power of composite queries:

```bash
$ dfx canister call kv_frontend get '(1)'
(opt (42 : nat))
```

As you can see, we can effortlessly fetch the value using composite queries with very low latency.
Let’s now compare the performance of composite query calls with those of an equivalent implementation that leverages calls from update functions: for that, we use the `get_update` method, which contains the exact same logic, but is implemented based on update calls:

```bash
$ dfx canister call kv_frontend get_update '(1)'
(opt (1_337 : nat))
```

We can observe that with update calls we receive the very same result, but the call is at least one order of magnitude slower compared to composite query calls.
Furthermore, we can orchestrate two query calls: first into the frontend canister and then into the data partition canister. This has similar latency as the composite query call, but requires extra logic on the client side.
```bash
$ dfx canister call kv_frontend lookup '(1)'
(1 : nat, "dmalx-m4aaa-aaaaa-qaanq-cai")
$ dfx canister call dmalx-m4aaa-aaaaa-qaanq-cai get '(1: nat)' --query
(1_337 : nat)
```

In summary, by using composite queries, we achieve low latency while keeping the client side simple. This is especially useful for dapps that are scaling vertically by partitioning data across multiple canisters.

Congratulations! You have successfully built a key-value store using Rust and leveraged the powerful composite query feature of the Internet Computer. This allows for efficient retrieval of data from your canisters.

We hope you found this blog post helpful. Happy coding with the composite query feature!
Many thanks to the DFINITY for contributing to the composite query feature: Adam Spofford, Claudio Russo, Martin Raszyk, Robin Künzler, Roel Storms, Stefan Kaestle, Ulan Degenbaev, Yan Chen

-->
