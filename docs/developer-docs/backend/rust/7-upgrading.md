# 7: アップグレードcanister

## 概要

canister 識別子は保持されますがステートは保持されないcanister 再インストールとは異なり、canister アップグレードでは、デプロイされたcanister のステートを保持し、コードを変更することができます。

たとえば、プロフェッショナルなプロフィールとソーシャルなつながりを管理するdapp があるとします。dapp に新機能を追加したい場合、canister のコードを、以前に保存されたデータを失うことなく更新できる必要があります。canister アップグレードを使用すると、プログラムのステート を失うことなく、プログラムの変更に合わせて既存のcanister 識別子を更新できます。

## Canister アップグレード

canister をアップグレードする必要がある場合、以下のワークフローが使用されます：

- canister フックが定義されている場合、システムは`pre_upgrade` フックを呼び出します。
- システムはcanister のメモリーを破棄し、新しいバージョンのWebAssembly モジュールをインスタンス化します。システムは安定したメモリを保持し、新しいバージョンで使用できるようにします。
- canister が定義されている場合、システムは新しく作成されたインスタンスで`post_upgrade` フックを呼び出します。`init` 関数は実行されません。
- 上記のいずれかのステップでcanister がトラップ (回復不能なエラーを投げる) した場合、システムはcanister をアップグレード前のステートに戻します。

### 安定したメモリーのバージョン管理

安定したメモリは、canister の古いバージョンと新しいバージョンの間の通信チャネルと見なすことができます。グッドプラクティスとして、通信プロトコルはバージョン管理されるべきです。場合によっては、開発者はcanister のシリアライズ形式や安定したデータレイアウトのようなものを根本的に変更したいと思うかもしれません。このプロセスを簡単にするために、安定メモリのバージョニングを計画すべきです。これは、canister の安定化メモリの最初のバイトをバージョン番号に使用すると宣言するのと同じくらい簡単なことです。

### アップグレードフックのテスト

アップグレードを適用する前にテストするのがベストプラクティスです。アップグレードをテストするには、シェルスクリプトや bash スクリプト、Rust テストスクリプトなど、いくつかの異なるワークフローやアプローチを使用できます。以下の擬似コードでは、アップグレードテストのステート検証を実行するステップを追加した、Rust によるアップグレードの例を示しています。

    let canister_id = install_canister(WASM);
    populate_data(canister_id);
    if should_upgrade { upgrade_canister(canister_id, WASM); }
    let data = query_canister(canister_id);
    assert_eq!(data, expected_value);

そして、テストを 2 つの異なるシナリオで 2 回実行します：

- アップグレードのないシナリオでは、アップグレードを実行しなくてもテストが正常に実行されることを確認します。
- アップグレードのないシナリオでは、アップグレードを実行せずにテストが正常に実行されることを確認します。アップグレードのあるシナリオでは、アップグレードを実行しながらテストが正常に実行されることを確認します。
  次に、異なるモードでテストを 2 回実行します：

これらのテストを両方実行することで、開発者は、アップグレードがcanister に適用されても、canister のステー トは保持されるという確信を得ることができます。

:::caution
 `pre_upgrade` フック内でトラップすることは推奨されません。`pre_upgrade` と`post_upgrade` のフックは対称のように見えますが、そうではないからです。

`pre_upgrade` フックが成功しても`post_upgrade` フックがトラップした場合、canister をデバッグして別のバージョンをビルドすることができます。しかし、`pre_upgrade` フックがトラップした場合、それについてできることはあまりありません。`pre_upgrade` フックが壊れていると、canister の動作を変更することができません。
::：

### 安定したメモリを主記憶として使用

canister がアップグレードされるとき、そのアップグレード中にcanister がバーンできるcycles の数には制限があります。canister がその制限を超えると、システムによってアップグレードはキャンセルされ、canister のステートも元に戻ります。つまり、canister'のステート全体を`pre_upgrade` フックの安定したメモリにシリアライズし、そのステートが非常に大きくなった場合、canister は再びアップグレードできなくなる可能性があります。

これを防ぐ1つの方法は、canister ステートを最初からシリアライズしないことです。安定したメモリをcanister の主記憶として使用し、各アップグレード呼び出しを格納できるようにします。この方法を使えば、`pre_upgrade` フックは必要なくなるかもしれませんし、`post_upgrade` フックは、cycles のバーンを少なくするでしょう。

:::caution
この方法は、ワークフローによっては便利かもしれませんが、いくつかの欠点があります：

- 特に、複数の相互接続されたデータ構造からなる複雑なステート（canister ）の場合、安定したストレージのフラットなアドレス空間をデータ構造に整理するのは困難です。[ic-stable-structures](https://crates.io/crates/ic-stable-structures)パッケージと[ic-stable-memory](https://crates.io/crates/ic-stable-memory)パッケージは、安定したメモリでデータを整理するのに役立つツールを提供します。
- canister のデータレイアウトを変更することは、非生産的で実行不可能かもしれません。
- canister のデータ構造に後方互換性を持たせる必要があるかもしれません。canister の新しいバージョンは、以前のバージョンで書かれたデータを読む必要があるかもしれません。
  ::：

全体として、canister ステートデータをギガバイト単位で保存し、コードをアップグレードする予定があるのであれば、アプローチの欠点はあるにせよ、主記憶に安定したメモリを使うことを検討する価値はあるかもしれません。

## アップグレードcanister

### 前提条件

作業を始める前に、[開発者環境ガイドの](./3-dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

このガイドでは、アップグレードしたい既存のcanister があることを前提にしています。既存のcanister をアップグレードするには、このセクションの前のドキュメントページをご覧ください。

Rust で書かれたcanister をアップグレードするには、`pre_upgrade` と`post_upgrade` 関数を使用して、canister のアップグレード後にデータが適切に保存されるようにする必要があります：

``` rust
use ic_cdk::{
    api::call::ManualReply, export::Principal, init, post_upgrade, pre_upgrade, query, storage,
    update,
};
use std::cell::RefCell;
use std::collections::{BTreeMap, BTreeSet};

type Users = BTreeSet<Principal>;
type Store = BTreeMap<String, Vec<u8>>;

thread_local! {
    static USERS: RefCell<Users> = RefCell::default();
    static STORE: RefCell<Store> = RefCell::default();
}

#[init]
fn init() {
    USERS.with(|users| users.borrow_mut().insert(ic_cdk::api::caller()));
}

fn is_user() -> Result<(), String> {
    if USERS.with(|users| users.borrow().contains(&ic_cdk::api::caller())) {
        Ok(())
    } else {
        Err("Store can only be set by the owner of the asset canister.".to_string())
    }
}

#[update(guard = "is_user")]
fn store(path: String, contents: Vec<u8>) {
    STORE.with(|store| store.borrow_mut().insert(path, contents));
}

#[query(manual_reply = true)]
fn retrieve(path: String) -> ManualReply<Vec<u8>> {
    STORE.with(|store| match store.borrow().get(&path) {
        Some(content) => ManualReply::one(content),
        None => panic!("Path {} not found.", path),
    })
}

#[update(guard = "is_user")]
fn add_user(principal: Principal) {
    USERS.with(|users| users.borrow_mut().insert(principal));
}

#[pre_upgrade]
fn pre_upgrade() {
    USERS.with(|users| storage::stable_save((users,)).unwrap());
}

#[post_upgrade]
fn post_upgrade() {
    let (old_users,): (BTreeSet<Principal>,) = storage::stable_restore().unwrap();
    USERS.with(|users| *users.borrow_mut() = old_users);
}
```

このコードは、参考のために[Rust CDK 資産保存の](https://github.com/dfinity/cdk-rs/blob/main/examples/asset_storage/src/asset_storage_rs/lib.rs)例に表示されています。

canister をアップグレードするには、必要に応じて新しいターミナルウィンドウを開き、プロジェクトのディレクトリに移動します。

次に、コマンドでローカルのcanister 環境を起動します：

    dfx start --clean --background

次に、アップグレードしたいcanister のcanister 識別子を取得します。そのためには、次のコマンドを使います：

    dfx canister id [canister-name]

次に、canister のコードに、アップデートに含めたい変更を加えます。これらの変更には、`pre_upgrade` と`post_upgrade` 関数の追加が含まれます。

すべてのcanisters をアップグレードするには、次のコマンドを使います：

    dfx canister install --all --mode upgrade

単一のcanister をアップグレードするには、次のコマンドを使います：

    dfx canister install [canister-id] --mode upgrade

## 次のステップ

次に、[ canisters を](./8-optimizing.md) [最適化して](./8-optimizing.md)みましょう。

<!---
# 7: Upgrading a canister

## Overview

Unlike a canister reinstall that preserves the canister identifier but no state, a canister upgrade enables you to preserve the state of a deployed canister, and change the code.

For example, assume you have a dapp that manages professional profiles and social connections. If you want to add a new feature to the dapp, you need to be able to update the canister code without losing any of the previously-stored data. A canister upgrade enables you to update existing canister identifiers with program changes without losing the program state.

## Canister upgrades
When a canister needs to be upgraded, the following workflow is used:
- The system calls a `pre_upgrade` hook if your canister defines it.
- The system discards canister memory and instantiates the new version of your WebAssembly module. The system does preserve the stable memory, which is now available to the new version.
- The system calls a `post_upgrade` hook on the newly created instance if your canister defines it. The `init` function is not executed.
- If the canister traps (throws an unrecoverable error) in any of the steps above, the system reverts the canister to the pre-upgrade state.

### Versioning stable memory

Stable memory can be viewed as the communication channel between old and new versions of a canister. As good practice, communication protocols should be versioned. In some cases, developers may want to radically change something such as the serialization format or the stable data layout of their canister. In radical changes like these, the stable memory decoding mechanism may need to guess the data's format, which can become messy and complicated. To make this process easier, stable memory versioning should be planned for. It can be as simple as declaring that the first byte of the canister's stable memory will be used to represent the version number. 

### Testing upgrade hooks

It is best practice to test upgrades before applying them in order to catch any potential errors that may result in losing data irrevocably. To test upgrades, several different workflows or approaches can be used, such as shell or bash scripts, or Rust test scripts. The following psuedo-code showcases a Rust upgrade example that adds an additional step to execute the state validation of your upgrade test. 

```
let canister_id = install_canister(WASM);
populate_data(canister_id);
if should_upgrade { upgrade_canister(canister_id, WASM); }
let data = query_canister(canister_id);
assert_eq!(data, expected_value);
```

Then, your tests should be run twice in two different scenarios:
- In a scenario without any upgrades, to assure that your tests run successfully without executing an upgrade.
- In a scenario with an upgrade, to assure that your tests run successfully while executing an upgrade. 
You then run your tests twice in different modes:

By running both of these tests, developers can gain confidence that when an upgrade is applied to a canister, the canister's state is preserved. 

:::caution
It is not recommended to trap within the `pre_upgrade` hook. This is because while the `pre_upgrade` and `post_upgrade` hooks appear to be symmetrical, they are not. 

If the `pre_upgrade` hook succeeds, but the `post_upgrade` hook traps, the canister can be debugged and another version can be built. However, if the `pre_upgrade` hook traps, there is not much you can do about it; a broken `pre_upgrade` hook prevents you from changing the canister's behavior. 
:::

### Using stable memory as primary storage

When a canister is upgraded, there is a limit regarding how many cycles a canister can burn during that upgrade. If the canister goes beyond that limit, the upgrade will be canceled by the system and the canister's state will be reverted. That means if you serialize the canister's whole state to stable memory in the `pre_upgrade` hook and the state becomes very large, the canister may not be able to be upgraded again. 

One way to prevent this is to avoid serializing the canister state to begin with. Stable memory can be used as the canister's primary storage, where it can be used to store each upgrade call. Using this method, the `pre_upgrade` hook may not be necessary, and the `post_upgrade` hook will burn fewer cycles. 

:::caution
While this approach might be useful for some workflows, there are a few drawbacks of this approach:
- It is a challenge to organize the flat address space of stable storage into a data structure, especially for complex canister states that consist of multiple interconnected data structures. The [ic-stable-structures](https://crates.io/crates/ic-stable-structures) package and the [ic-stable-memory](https://crates.io/crates/ic-stable-memory) package provide tools to help you organize data in stable memory.
- Altering your canister's data layout may be counterproductive and infeasible. 
- There may be a need for your canister to have backward compatibility of it's data structures; new versions of your canister may need to read data written by previous versions. 
:::

Overall, if your canister plans to store gigabytes of state data and upgrade the code, it may be worth considering using stable memory for the primary storage despite the drawbacks of the approach. 

## Upgrading a canister 

### Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./3-dev-env.md).

This guide assumes you have an existing canister that you'd like to upgrade. To get an existing canister, review the previous documentation pages in this section. 

To upgrade a canister written in Rust, you should use `pre_upgrade` and `post_upgrade` functions to ensure data is properly preserved after a canister upgrade as illustrated in the example below:

```rust
use ic_cdk::{
    api::call::ManualReply, export::Principal, init, post_upgrade, pre_upgrade, query, storage,
    update,
};
use std::cell::RefCell;
use std::collections::{BTreeMap, BTreeSet};

type Users = BTreeSet<Principal>;
type Store = BTreeMap<String, Vec<u8>>;

thread_local! {
    static USERS: RefCell<Users> = RefCell::default();
    static STORE: RefCell<Store> = RefCell::default();
}

#[init]
fn init() {
    USERS.with(|users| users.borrow_mut().insert(ic_cdk::api::caller()));
}

fn is_user() -> Result<(), String> {
    if USERS.with(|users| users.borrow().contains(&ic_cdk::api::caller())) {
        Ok(())
    } else {
        Err("Store can only be set by the owner of the asset canister.".to_string())
    }
}

#[update(guard = "is_user")]
fn store(path: String, contents: Vec<u8>) {
    STORE.with(|store| store.borrow_mut().insert(path, contents));
}

#[query(manual_reply = true)]
fn retrieve(path: String) -> ManualReply<Vec<u8>> {
    STORE.with(|store| match store.borrow().get(&path) {
        Some(content) => ManualReply::one(content),
        None => panic!("Path {} not found.", path),
    })
}

#[update(guard = "is_user")]
fn add_user(principal: Principal) {
    USERS.with(|users| users.borrow_mut().insert(principal));
}

#[pre_upgrade]
fn pre_upgrade() {
    USERS.with(|users| storage::stable_save((users,)).unwrap());
}

#[post_upgrade]
fn post_upgrade() {
    let (old_users,): (BTreeSet<Principal>,) = storage::stable_restore().unwrap();
    USERS.with(|users| *users.borrow_mut() = old_users);
}
```

This code is displayed in the [Rust CDK asset storage example](https://github.com/dfinity/cdk-rs/blob/main/examples/asset_storage/src/asset_storage_rs/lib.rs) for reference. 

To upgrade a canister, start by opening a new terminal window if necessary and navigating into your project's directory.

Then, start the local canister environment with the command:

```
dfx start --clean --background
```

Next, obtain the canister identifier of the canister(s) you'd like to upgrade. To do so, the following command can be used:

```
dfx canister id [canister-name]
```

Then, make any changes to the canister's code that you'd like to be included in the update. These changes should include adding the `pre_upgrade` and `post_upgrade` functions as displayed above. 

To upgrade all canisters, the following command can be used:

```
dfx canister install --all --mode upgrade
```

To upgrade a single canister, use the command:

```
dfx canister install [canister-id] --mode upgrade
```

## Next steps

Next, let's take a look at [optimizing canisters](./8-optimizing.md).

-->
