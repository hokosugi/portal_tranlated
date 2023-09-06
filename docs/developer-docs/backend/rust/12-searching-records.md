# 12: 単純なレコードの追加と検索

## 概要

このガイドでは、名前、説明、およびキーワードの配列で構成される単純なプロファイルレコードを追加および検索するための、いくつかの基本的な関数を提供するdapp を記述します。

このプログラムは以下の関数をサポートしています：

- `update` 関数を使用すると、`name` 、`description` 、`keywords` で構成されるプロファイルを追加できます。

- `getSelf` 関数は、関数呼び出し元に関連付けられたプリンシパルのプロファイルを返します。

- `get` 関数は、単純なクエリーを実行して、渡された`name` 値に一致するプロファイルを返します。この関数でレコードを返すには、指定された名前が`name` フィールドと正確に一致する必要があります。

- `search` 関数は、より複雑なクエリを実行して、任意のプロファイル・フィールドで指定されたテキストのすべて または一部に一致するプロファイルを返します。たとえば、`search` 関数は、特定のキーワードを含むプロフ ァイルを返したり、名前や説明の一部にのみ一致するプロファイルを返したりできます。

このガイドでは、Rust CDK インターフェースとマクロを使用して、Internet Computer ブロックチェーン用に Rust でdapps を記述する方法を簡単に説明します。

このガイドでは、次のことを示します：

- Candidインターフェース記述言語を使用して、`record` およびキーワードの`array` としてプロファイルの形で少し複雑なデータを表現する方法。
- 部分的な文字列マッチングによる簡単な検索機能の書き方。
- プロファイルを特定のプリンシパルに関連付ける方法。

## 前提条件

始める前に、[開発者環境ガイドの](./3-dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新しいプロジェクトの作成

まだ開いていなければ、ローカル・コンピュータでターミナル・ウィンドウを開いてください。そして、以下のコマンドを実行して新しいプロジェクトを作成します：

``` bash
dfx new --type=rust rust_profile
cd rust_profile
```

これで Rustdapp 用のファイルが揃ったので、`lib.rs` dapp のテンプレートをInternet Computer ブロックチェーンにデプロイしたい Rustdapp に置き換えることができます。

デフォルトのプログラムを置き換えるには、テキストエディタで`src/rust_profile_backend/Cargo.toml` ファイルを開き、依存関係に`serde` を追加します。

``` toml
[dependencies]
candid = "0.8.2"
ic-cdk = "0.6.0"
serde = "1.0"
```

次に、テンプレート`src/rust_profile_backend/src/lib.rs` ファイルをテキストエディタで開き、既存のコンテンツを削除します。

次のステップでは、`getSelf` 、`update` 、`get` 、`search` 関数を実装する Rust プログラムを追加します。これを行うには、このコードをコピーして`src/rust_profile_backend/src/lib.rs` ファイルに貼り付けます。

``` rust
use ic_cdk::{
    export::{
        candid::{CandidType, Deserialize},
        Principal,
    },
    query, update,
};
use std::cell::RefCell;
use std::collections::BTreeMap;

type IdStore = BTreeMap<String, Principal>;
type ProfileStore = BTreeMap<Principal, Profile>;

#[derive(Clone, Debug, Default, CandidType, Deserialize)]
struct Profile {
    pub name: String,
    pub description: String,
    pub keywords: Vec<String>,
}

thread_local! {
    static PROFILE_STORE: RefCell<ProfileStore> = RefCell::default();
    static ID_STORE: RefCell<IdStore> = RefCell::default();
}

#[query(name = "getSelf")]
fn get_self() -> Profile {
    let id = ic_cdk::api::caller();
    PROFILE_STORE.with(|profile_store| {
        profile_store
            .borrow()
            .get(&id)
            .cloned().unwrap_or_default()
    })
}

#[query]
fn get(name: String) -> Profile {
    ID_STORE.with(|id_store| {
        PROFILE_STORE.with(|profile_store| {
            id_store
                .borrow()
                .get(&name)
                .and_then(|id| profile_store.borrow().get(id).cloned()).unwrap_or_default()
        })
    })
}

#[update]
fn update(profile: Profile) {
    let principal_id = ic_cdk::api::caller();
    ID_STORE.with(|id_store| {
        id_store
            .borrow_mut()
            .insert(profile.name.clone(), principal_id);
    });
    PROFILE_STORE.with(|profile_store| {
        profile_store.borrow_mut().insert(principal_id, profile);
    });
}
```

変更を保存し、ファイルを閉じて続行します。

## インターフェース記述ファイルの更新

Candid は、Internet Computer 上で実行されているcanisters と対話するためのインターフェイス記述言語 (IDL) です。 Candid ファイルは、canisterのインターフェイスの言語に依存しない記述で、canister が定義する各関数の名前、パラメータ、結果の形式とデータ型を含みます。

Candid ファイルをプロジェクトに追加することで、Internet Computer ブロックチェーン上で安全に実行するために、Rust での定義からデータが適切に変換されることを保証できます。

Candidインターフェース記述言語構文の詳細については、[Candidガイド](./../candid/index.md)または[Candidクレートのドキュメントを](https://docs.rs/candid/)参照してください。

このガイドのCandidファイルを更新するには、テキストエディタで`src/rust_profile_backend/rust_profile_backend.did` ファイルを開きます。

以下の`Profile` 型宣言と`getSelf`,`update`,`get`,`search` 関数の`service` 定義をコピーして貼り付けます。

``` did
type Profile = record {
    "name": text;
    "description": text;
    "keywords": vec text;
};

service : {
    "getSelf": () -> (Profile) query;
    "get": (text) -> (Profile) query;
    "update": (Profile) -> ();
    "search": (text) -> (opt Profile) query;
}
```

変更を保存し、ファイルを閉じて続行します。

## ローカル実行環境の開始

`rust_profile` プロジェクトをビルドする前に、開発環境または分散型Internet Computer ブロックチェーンメインネットで実行されているローカル実行環境に接続する必要があります。

以下のコマンドを実行して、バックグラウンドでコンピュータ上のローカル実行環境を起動します：

``` bash
dfx start --background --clean
```

プラットフォームとローカルのセキュリティ設定によっては、警告が表示される場合があります。受信ネットワーク接続の許可または拒否を求めるプロンプトが表示されたら、\[**許可\]** をクリックします。

## プロジェクトの登録、ビルド、およびデプロイ

開発環境で実行されているローカル実行環境に接続したら、プロジェクトをローカルに登録、ビルド、デプロイできます。

これを行うには、以下のコマンドを実行します：

``` bash
dfx deploy
```

## デプロイされた関数を呼び出します。canister

canister のデプロイに成功したら、canister が提供する関数を呼び出してテストできます。

このガイドでは

- `update` 関数を呼び出して、プロファイルを追加します。

- `getSelf` 関数を呼び出して、プリンシパル ID のプロファイルを表示します。

- `search` 関数を呼び出して、キーワードを使用してプロファイルを検索します。

### レコードの更新

`update` 関数を呼び出して、次のコマンドを実行してプロファイル・レコードを作成します：

``` bash
dfx canister call rust_profile_backend update '(record {name = "Luxi"; description = "mountain dog"; keywords = vec {"scars"; "toast"}})'
```

現在の形式では、dapp は 1 つのプロファイルのみを保存して返します。`update` 関数を使用して 2 番目のプロファイルを追加するために次のコマンドを実行すると、このコマンドは`Luxi` プロファイルを`Dupree` プロファイルで置き換えます：

``` bash
dfx canister call rust_profile_backend update '(record {name = "Dupree"; description = "black dog"; keywords = vec {"funny tail"; "white nose"}})'
```

`get` 、`getSelf` 、`search` 関数を使用することもできますが、これらの関数は`Dupree` プロファイルの結果のみを返します。

### レコードの取得

次のコマンドを実行してプロファイル・レコードを取得するには、`getSelf` 関数を呼び出します：

``` bash
dfx canister call rust_profile_backend getSelf
```

このコマンドは、`update` 関数を使用して追加したプロファイルを返します。コマンドは、 関数を使用して追加したプロファイルを返します：

    (
        record {
            name = "Luxi";
            description = "mountain dog";
            keywords = vec { "scars"; "toast" };
        },
    )

### レコードの検索

次のコマンドを実行して、`search` 関数を呼び出します：

``` bash
dfx canister call rust_profile_backend search '("black")'
```

このコマンドは、`description` を使用して一致するプロフ ァイルを検索し、そのプロファイルを返します：

    (
        opt record {
            name = "Dupree";
            description = "black dog";
            keywords = vec { "funny tail"; "white nose" };
        },
    )

## 新しい ID 用のプロファイルの追加

現在の形式では、dapp は 1 つのプロファイル（コマンドを呼び出したプリンシパルに関連付けられて いるプロファイル）だけを保存します。`get` 、`getSelf` 、`search` の各関数が思い通りに動作することをテストするには、異なるプロファイルを持つことができる新しい ID をいくつか追加する必要があります。

テスト用の ID を追加するには、まず以下のコマンドを実行して新しいユーザ ID を作成し、プロンプトが表示されたら ID を保護するためのパスフレーズを入力します：

``` bash
dfx identity new Miles
```

以下の出力が返されます：

    Your seed phrase for identity 'Miles': recycle  ...
    This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
    Created identity: "Miles".

`update` 関数を呼び出して、新しい ID にプロファイルを追加します。プロンプトが表示されたら、パスフレーズを入力します。

``` bash
dfx --identity Miles canister call rust_profile_backend update '(record {name = "Miles"; description = "Great Dane"; keywords = vec {"Boston"; "mantle"; "three-legged"}})'
```

`default` ユーザ ID に関連付けられたプロファイルを表示するには、`getSelf` 関数を呼び出します。

``` bash
dfx canister call rust_profile_backend getSelf
```

このコマンドは、デフォルトの ID に現在関連付けられているプロファイル（この例では Dupree プロファイル）を表示します：

    (
        record {
            name = "Dupree";
            description = "black dog";
            keywords = vec { "funny tail"; "white nose" };
        },
    )

次のコマンドを実行して、`Miles` ユーザー ID を使用して`getSelf` 関数を呼び出します：

``` bash
dfx --identity Miles canister call rust_profile_backend getSelf
```

この例では、Miles ID に現在関連付けられているプロファイルが表示されます：

    (
        record {
            name = "Miles";
            description = "Great Dane";
            keywords = vec { "Boston"; "mantle"; "three-legged" };
        },
    )

説明の一部またはキーワードを使用して`search` 関数を呼び出し、正しいプロファイルが返されるかどうか をさらにテストします。

例えば、`Miles` プロファイルが返されることを確認するには、以下のコマンドを実行します：

``` bash
dfx canister call rust_profile_backend search '("Great")'
```

このコマンドは`Miles` プロファイルを返します：

    (
        opt record {
        name = "Miles";
        description = "Great Dane";
        keywords = vec { "Boston"; "mantle"; "three-legged" };
        },
    )

正しいプロファイルが返されるかどうかをさらにテストするには、`search` 関数を呼び出します。

たとえば、`Dupree` プロファイルが返されることを確認するには、次のコマンドを実行します：

``` bash
dfx canister call rust_profile_backend search '("black")'
```

このコマンドは`Dupree` プロファイルを返します：

    (
        opt record {
            name = "Dupree";
            description = "black dog";
            keywords = vec { "funny tail"; "white nose" };
        },
    )

## 次のステップ

次は、Rustcanisters の[アクセス制御について](./13-access-control.md)説明します。

<!---
# 12: Adding and searching simple records

## Overview
In this guide, you are going to write a dapp that provides a few basic functions to add and retrieve simple profile records that consist of a name, description, and an array of keywords.

This program supports the following functions:

-   The `update` function enables you to add a profile that consists of a `name`, a `description`, and `keywords`.

-   The `getSelf` function returns the profile for the principal associated with the function caller.

-   The `get` function performs a simple query to return the profile matching the `name` value passed to it. For this function, the name specified must match the `name` field exactly to return the record.

-   The `search` function performs a more complex query to return the profile matching all or part of the text specified in any profile field. For example, the `search` function can return a profile containing a specific keyword or that matches only part of a name or description.

This guide provides a simple example of how you can use the Rust CDK interfaces and macros to simplify writing dapps in Rust for the Internet Computer blockchain.

This guide demonstrates: 
-   How to represent slightly more complex data—in the form of a profile as a `record` and an `array` of keywords—using the Candid interface description language. 
-   How to write a simple search function with partial string matching. 
-   How profiles are associated with a specific principal.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./3-dev-env.md).

## Creating a new project

Open a terminal window on your local computer, if you don’t already have one open. Then, create a new project by running the following command:

``` bash
dfx new --type=rust rust_profile
cd rust_profile
```

Now that you have the files in place for your Rust dapp, we can replace the template `lib.rs` dapp with the Rust dapp we want to deploy on the Internet Computer blockchain.

To replace the default program, open the `src/rust_profile_backend/Cargo.toml` file in a text editor and add `serde` to dependencies.

``` toml
[dependencies]
candid = "0.8.2"
ic-cdk = "0.6.0"
serde = "1.0"
```

Then, open the template `src/rust_profile_backend/src/lib.rs` file in a text editor and delete the existing content.

The next step is to add a Rust program that implements the `getSelf`, `update`, `get`, and `search` functions. To do this, copy and paste this code into the `src/rust_profile_backend/src/lib.rs` file.

```rust
use ic_cdk::{
    export::{
        candid::{CandidType, Deserialize},
        Principal,
    },
    query, update,
};
use std::cell::RefCell;
use std::collections::BTreeMap;

type IdStore = BTreeMap<String, Principal>;
type ProfileStore = BTreeMap<Principal, Profile>;

#[derive(Clone, Debug, Default, CandidType, Deserialize)]
struct Profile {
    pub name: String,
    pub description: String,
    pub keywords: Vec<String>,
}

thread_local! {
    static PROFILE_STORE: RefCell<ProfileStore> = RefCell::default();
    static ID_STORE: RefCell<IdStore> = RefCell::default();
}

#[query(name = "getSelf")]
fn get_self() -> Profile {
    let id = ic_cdk::api::caller();
    PROFILE_STORE.with(|profile_store| {
        profile_store
            .borrow()
            .get(&id)
            .cloned().unwrap_or_default()
    })
}

#[query]
fn get(name: String) -> Profile {
    ID_STORE.with(|id_store| {
        PROFILE_STORE.with(|profile_store| {
            id_store
                .borrow()
                .get(&name)
                .and_then(|id| profile_store.borrow().get(id).cloned()).unwrap_or_default()
        })
    })
}

#[update]
fn update(profile: Profile) {
    let principal_id = ic_cdk::api::caller();
    ID_STORE.with(|id_store| {
        id_store
            .borrow_mut()
            .insert(profile.name.clone(), principal_id);
    });
    PROFILE_STORE.with(|profile_store| {
        profile_store.borrow_mut().insert(principal_id, profile);
    });
}
```

Save your changes and close the file to continue.

## Update the interface description file

Candid is an interface description language (IDL) for interacting with canisters running on the Internet Computer. Candid files provide a language-independent description of a canister’s interfaces including the names, parameters, and result formats and data types for each function a canister defines.

By adding Candid files to your project, you can ensure that data is properly converted from its definition in Rust to run safely on the Internet Computer blockchain.

To see details about the Candid interface description language syntax, see the [Candid Guide](./../candid/index.md) or the [Candid crate documentation](https://docs.rs/candid/).

To update Candid file for this guide open the `src/rust_profile_backend/rust_profile_backend.did` file in a text editor.

Copy and paste the following `Profile` type declaration and `service` definition for the `getSelf`, `update`, `get`, and `search` functions.

```did
type Profile = record {
    "name": text;
    "description": text;
    "keywords": vec text;
};

service : {
    "getSelf": () -> (Profile) query;
    "get": (text) -> (Profile) query;
    "update": (Profile) -> ();
    "search": (text) -> (opt Profile) query;
}
```

Save your changes and close the file to continue.

## Start the local execution environment

Before you can build the `rust_profile` project, you need to connect to the local execution environment running in your development environment or the decentralized Internet Computer blockchain mainnet.

Start the local execution environment on your computer in the background by running the following command:

``` bash
dfx start --background --clean
```

Depending on your platform and local security settings, you might see a warning displayed. If you are prompted to allow or deny incoming network connections, click **Allow**.

## Register, build, and deploy your project

After you connect to the local execution environment running in your development environment, you can register, build, and deploy your project locally.

To do this, run the following command:

``` bash
dfx deploy
```

## Call functions on the deployed canister

After successfully deploying the canister, you can test the canister by calling the functions it provides.

For this guide:

-   Call the `update` function to add a profile.

-   Call the `getSelf` function to display the profile for the principal identity.

-   Call the `search` function to look up the profile using a keyword.

### Updating records

Call the `update` function to create a profile record by running the following command:

``` bash
dfx canister call rust_profile_backend update '(record {name = "Luxi"; description = "mountain dog"; keywords = vec {"scars"; "toast"}})'
```

In its current form, the dapp only stores and returns one profile. If you run the following command to add a second profile using the `update` function, the command replaces the `Luxi` profile with the `Dupree` profile:

``` bash
dfx canister call rust_profile_backend update '(record {name = "Dupree"; description = "black dog"; keywords = vec {"funny tail"; "white nose"}})'
```

You can use the `get`, `getSelf`, and `search` functions, but they will only return results for the `Dupree` profile.

### Retrieving records

Call the `getSelf` function to retrieve a profile record by running the following command:

``` bash
dfx canister call rust_profile_backend getSelf
```

The command returns the profile you used the `update` function to add. For example:

```
(
    record {
        name = "Luxi";
        description = "mountain dog";
        keywords = vec { "scars"; "toast" };
    },
)
```

### Searching records
Run the following command to call the `search` function:

``` bash
dfx canister call rust_profile_backend search '("black")'
```

This command finds the matching profile using the `description` and returns the profile:

```
(
    opt record {
        name = "Dupree";
        description = "black dog";
        keywords = vec { "funny tail"; "white nose" };
    },
)
```

## Adding profiles for new identities

In its current form, the dapp only stores one profile—the one associated with the principal invoking the commands. To test that the `get`, `getSelf`, and `search` functions do what we want them to, we need to add some new identities that can have different profiles.

To add identities for testing, first create a new user identity by running the following command, enter a passphrase to secure the identity when prompted:

``` bash
dfx identity new Miles
```

The following output will be returned:
    
```
Your seed phrase for identity 'Miles': recycle  ...
This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
Created identity: "Miles".
```

Call the `update` function to add a profile for the new identity. Enter your passphrase when prompted.

``` bash
dfx --identity Miles canister call rust_profile_backend update '(record {name = "Miles"; description = "Great Dane"; keywords = vec {"Boston"; "mantle"; "three-legged"}})'
```

Call the `getSelf` function to view the profile associated with the `default` user identity.

``` bash
dfx canister call rust_profile_backend getSelf
```

The command displays the profile currently associated with the default identity, in this example, the Dupree profile:

```
(
    record {
        name = "Dupree";
        description = "black dog";
        keywords = vec { "funny tail"; "white nose" };
    },
)
```

Call the `getSelf` function using the `Miles` user identity by running the following command:

``` bash
dfx --identity Miles canister call rust_profile_backend getSelf
```

The command displays the profile currently associated with the Miles identity, in this example:

```
(
    record {
        name = "Miles";
        description = "Great Dane";
        keywords = vec { "Boston"; "mantle"; "three-legged" };
    },
)
```

Call the `search` function using part of the description or a keyword to further test the whether the correct profile is returned.

For example, to verify the `Miles` profile is returned, you might run the following command:

``` bash
dfx canister call rust_profile_backend search '("Great")'
```

The command returns the `Miles` profile:

```
(
    opt record {
    name = "Miles";
    description = "Great Dane";
    keywords = vec { "Boston"; "mantle"; "three-legged" };
    },
)
```

Call the `search` function to further test the whether the correct profile is returned.

For example, to verify the `Dupree` profile is returned, you might run the following command:

``` bash
dfx canister call rust_profile_backend search '("black")'
```

The command returns the `Dupree` profile:

```
(
    opt record {
        name = "Dupree";
        description = "black dog";
        keywords = vec { "funny tail"; "white nose" };
    },
)
```
## Next steps

Next, we'll cover [access control](./13-access-control.md) in Rust canisters.

-->
