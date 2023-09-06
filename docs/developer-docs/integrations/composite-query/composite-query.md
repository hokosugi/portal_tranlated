# 複合クエリー

## 概要

Internet Computer (IC) は、更新とクエリという 2 種類のメッセージをサポートしています。更新メッセージはすべてのノードで実行され、canister ステートの変更を永続化します。クエリ・メッセージはステート変更を破棄し、通常は 1 つのノードで実行されます。クエリ・メッセージを更新として実行することもできます。この場合、クエリはステート変更を破棄しますが、実行はすべてのノードで行われ、実行結果はコンセンサスを通過します。この「更新としてのクエリ」実行モードは、レプリケート・クエリとしても知られています。

アップデートは他のアップデートやクエリーをコールすることができます。しかし、クエリーコールは呼び出すことができないため、スケーラブルな分散型アプリケーション(dapps)、特に複数のcanisters にまたがってデータをシャードするようなアプリケーションの開発を妨げる可能性があります。

この問題を解決するのが複合クエリです。コンポジットクエリは、以下のアノテーションを使用してcanister に追加できる新しいタイプのクエリです：

- Candid：`composite_query`
- Azle:`$query`;と組み合わせて使用します。`async`
- Motoko:`composite query`
- Rust：`#[query(composite = true)]`

ユーザーとクライアント側の JavaScript コードは、既存の通常のクエリと同じクエリ URL を使用して、canister の複合クエリのエンドポイントを呼び出すことができます。通常のクエリとは対照的に、コンポジットクエリは他のコンポジットクエリや通常のクエリを呼び出すことができます。現在の実装の制限により、複合クエリには2つの制限があります：

- 複合クエリーは、別のサブネット上のcanisters を呼び出すことはできません。
- 複合クエリは更新として実行できません。その結果、アップデートは複合クエリーをコールできません。

これらの制限は、将来の実装で解除されることを期待しています。

複合クエリは以下のリリースで有効になっています：

| プラットフォーム / 言語 | バージョン |
| --- | --- |
| Internet computer メインネット | Release[7742d96ddd30aa6b607c9d2d4093a7b714f5b25b](https://nns.ic0.app/proposal/?u=qoctq-giaaa-aaaaa-aaaea-cai&proposal=123311) |
| 候補 | [2023-06-30 (Rust 0.9.0)](https://github.com/dfinity/candid/blob/master/Changelog.md#2023-06-30-rust-090) |
| Motoko | 0.[9.4](https://github.com/dfinity/motoko/releases/tag/0.9.4), リビジョン:[2d9902f](https://github.com/dfinity/motoko/commit/2d9902fb75bb04e377c28913c311aa2be373e159) |
| さび | [0.6.8](https://github.com/dfinity/cdk-rs/blob/219ae179b9c5ef0ebfff20b926a90f6624ebe704/src/ic-cdk/CHANGELOG.md#068---2022-11-28) |
| アズール | [0.11.0](https://github.com/demergent-labs/azle/releases/tag/0.11.0) |

## コード例

例として、パーティショニングされたキーバリューストアを考えてみましょう。`put` と`get` の呼び出しに対して、単一のフロントエンドが次のことを行います：

- まず、与えられたキーの値を保持するデータパーティションcanister のIDを決定します。
- 次に、canister の`get` または`put` 関数を呼び出し、結果を解析します。

以下はフロントエンドのコードの簡略化した例です。新しい複合クエリ機能を活用するために使用されている`#[query(composite = true)]` 行に注目してください：

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

バックエンドは、単純にキーと値のペアを`BTreeMap` 、安定したメモリに保存します：

``` rust
#[query]
fn get(key: u128) -> Option<u128> {
	STORE.with(|store| store.borrow().get(&key))
}
```

## 複合クエリの使用

### 前提条件

- \[x\][IC SDKをダウンロードしてインストールして](https://internetcomputer.org/docs/current/developer-docs/setup/install/)ください。
- \[x\][gitをダウンロードしてインストールして](https://git-scm.com/downloads)ください。

### クエリのセットアップcanisters

### ステップ 1: ターミナルウィンドウを開き、DFINITY examples repo をコマンドでクローンします：

``` bash
git clone https://github.com/dfinity/examples.git
```

### ステップ2:`rust/composite_query/src` ディレクトリに移動し、ローカルレプリカを起動し、コマンドでフロントエンドcanister をビルドします：

``` bash
cd rust/composite_query/src
dfx start
dfx canister create kv_frontend
dfx build kv_frontend
During compilation of the fronted canister, the backend canister's wasm code will be compiled and inlined in the frontend canister's wasm code.

### Step 3: Then, install the frontend canister with the command:
dfx canister install kv_frontend
```

### コマンドでビルドします。canisters

### ステップ1: フロントエンドcanister でキーと値のペアを追加するには、次のコマンドを実行します：

``` bash
dfx canister call kv_frontend put '(1, 1337)'
```

:::note
データパーティションcanisters を最初に作成する必要があるため、putの最初の呼び出しはレスポンスが遅くなるかもしれません。
::：

出力は以下のようになり、このキーに値が登録されていないことがわかります：
`(null)`

### ステップ2：コマンドを使用した複合クエリを使用して、キーに関連付けられた値を取得します：

``` bash
dfx canister call kv_frontend get '(1)'
```

出力は以下のようになります：
`(opt (1337 : nat))`

このワークフローでは、コンポジットクエリを使用して非常に低いレイテンシで値を取得できることがわかります。

### コンポジットクエリとアップデート関数のコールの比較

それでは、コンポジットクエリーコールとアップデート関数からのコールを利用した同等の実装のパフォーマンスを比較してみましょう。これには、`get_update` メソッドを使用します。このメソッドにはまったく同じロジックが含まれていますが、アップデートコールに基づいて実装されています。ターミナルウィンドウで以下のコマンドを実行します：

``` bash
dfx canister call kv_frontend get_update '(1)'
```

出力は以下のようになります：
`(opt (1_337 : nat))`

出力は以下のようになります。アップデートコールを使用すると、まったく同じ結果が得られますが、複合クエリーコールと比較して、少なくとも1桁遅くなることがわかります。

:::note
examplesリポジトリには、同等の[Motoko exampleも](https://github.com/dfinity/examples/tree/master/motoko/composite_query)あります。
::：

## リソース

以下の例canisters は複合クエリの使い方を示しています：

- [Azleの例](https://github.com/demergent-labs/azle/tree/main/examples/composite_queries)
- [Motoko 例](https://github.com/dfinity/examples/tree/master/motoko/composite_query)
- [Rustの例](https://github.com/dfinity/examples/tree/master/rust/composite_query)

フィードバックや提案はこちらのフォーラムに投稿[できます: https://forum.dfinity.org/t/proposal-composite-queries/15979](https://forum.dfinity.org/t/proposal-composite-queries/15979)

<!---
# Composite queries
## Overview
The Internet Computer (IC) supports two types of messages: updates and queries. An update message is executed on all nodes and persists canister state changes. A query message discards state changes and typically executes on a single node. It is possible to execute a query message as an update. In such a case, the query still discards the state changes, but the execution happens on all nodes and the result of execution goes through consensus. This “query-as-update” execution mode is also known as replicated query.

An update can call other updates and queries. However a query cannot make any calls, which can hinder development of scalable decentralized applications (dapps), especially those that shard data across multiple canisters.

Composite queries solve this problem. A composite query is a new type of query that you can add to your canister using the following annotations:

 * Candid: `composite_query`
 * Azle: `$query`; in combination with `async`
 * Motoko: `composite query`
 * Rust: `#[query(composite = true)]`

Users and the client-side JavaScript code can invoke a composite query endpoint of a canister using the same query URL as for existing regular queries. In contrast to regular queries, a composite query can call other composite and regular queries. Due to limitations of the current implementation, composite queries have two restrictions:

 * A composite query cannot call canisters on another subnet.
 * A composite query cannot be executed as an update. As a result, updates cannot call composite queries.

These restrictions will be hopefully lifted in future implementations.

Composite queries are enabled in the following releases:

| Platform / Language        | Version |
| -------------------------- | ------- |
| Internet computer mainnet  | Release [7742d96ddd30aa6b607c9d2d4093a7b714f5b25b](https://nns.ic0.app/proposal/?u=qoctq-giaaa-aaaaa-aaaea-cai&proposal=123311)     |
| Candid                     | [2023-06-30 (Rust 0.9.0)](https://github.com/dfinity/candid/blob/master/Changelog.md#2023-06-30-rust-090)     |
| Motoko                     | [0.9.4](https://github.com/dfinity/motoko/releases/tag/0.9.4), revision: [2d9902f](https://github.com/dfinity/motoko/commit/2d9902fb75bb04e377c28913c311aa2be373e159)    |
| Rust                       | [0.6.8](https://github.com/dfinity/cdk-rs/blob/219ae179b9c5ef0ebfff20b926a90f6624ebe704/src/ic-cdk/CHANGELOG.md#068---2022-11-28)    |
| Azle                       | [0.11.0](https://github.com/demergent-labs/azle/releases/tag/0.11.0)    |


## Code example
As an example, consider a partitioned key-value store, where a single frontend does the following for a `put` and `get` call:

- First, determines the ID of the data partition canister that holds the value with the given key.
- Then, makes a call into the `get` or `put` function of that canister and parses the result.

Below is a simplified example of the frontend code. Take note of the line `#[query(composite = true)]` which is used to leverage the new composite query feature:

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

The backend simply stores the key value pairs in a `BTreeMap` in stable memory:

```rust
#[query]
fn get(key: u128) -> Option<u128> {
	STORE.with(|store| store.borrow().get(&key))
}
```

## Using composite queries
### Prerequisites
 - [x] [Download and install the IC SDK.](https://internetcomputer.org/docs/current/developer-docs/setup/install/)
 - [x] [Download and install git.](https://git-scm.com/downloads)

### Setting up the canisters
### Step 1: Open a terminal window and clone the DFINITY examples repo with the command:

```bash
git clone https://github.com/dfinity/examples.git
```

### Step 2: Navigate into the `rust/composite_query/src` directory, start a local replica, and build the frontend canister with the commands:

```bash
cd rust/composite_query/src
dfx start
dfx canister create kv_frontend
dfx build kv_frontend
During compilation of the fronted canister, the backend canister's wasm code will be compiled and inlined in the frontend canister's wasm code.

### Step 3: Then, install the frontend canister with the command:
dfx canister install kv_frontend
```

### Interacting with the canisters
### Step 1: To add a key-value pair via the frontend canister, run the following command:

```bash
dfx canister call kv_frontend put '(1, 1337)'
```

:::note
The first call to put might be slow to respond because the data partition canisters have to be created first.
:::

The output should resemble the following indicating that no value has previously been registered for this key:
```(null)```

### Step 2: Retrieve the value associated with a key using composite queries with the command:

```bash
dfx canister call kv_frontend get '(1)'
```

The output should resemble the following:
```(opt (1337 : nat))```

This workflow displays the ability to fetch the value using composite queries with very low latency.

### Comparing composite queries to calls from update functions
Let’s now compare the performance of composite query calls with those of an equivalent implementation that leverages calls from update functions. To do this, we will use the `get_update` method, which contains the exact same logic, but is implemented based on update calls. Run the following command in your terminal window:

```bash
dfx canister call kv_frontend get_update '(1)'
```

The output will resemble the following:
```(opt (1_337 : nat))```

We can observe that with update calls we receive the very same result, but the call is at least one order of magnitude slower compared to composite query calls.

:::note
The examples repository also contains an equivalent [Motoko example](https://github.com/dfinity/examples/tree/master/motoko/composite_query).
:::

## Resources
The following example canisters demonstrate how to use composite queries:

 * [Azle example](https://github.com/demergent-labs/azle/tree/main/examples/composite_queries)
 * [Motoko example](https://github.com/dfinity/examples/tree/master/motoko/composite_query)
 * [Rust example](https://github.com/dfinity/examples/tree/master/rust/composite_query)

Feedback and suggestions can be contributed on the forum here: [https://forum.dfinity.org/t/proposal-composite-queries/15979](https://forum.dfinity.org/t/proposal-composite-queries/15979)

-->
