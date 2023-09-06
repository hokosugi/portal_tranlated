# 9: カウンターのインクリメント

## 概要

このガイドでは、カウンタをインクリメントするためのいくつかの基本的な関数を提供し、値の永続性を示すdapp を記述します。

このガイドでは、dapp は、カウンタの現在値を表す自然数を格納するミュータブル変数として`COUNTER` を宣言します。このdapp は以下の関数をサポートしています：

- `increment` 関数は現在値を更新し、戻り値なしで 1 ずつ増分します。

- `get` 関数は、カウンタの現在値を返す単純なクエリです。

- `set` 関数は、現在値を引数として指定した数値に更新します。

このガイドでは、配置されたcanister で関数を呼び出してカウンタをインクリメントする簡単な例を示します。値をインクリメントする関数を複数回呼び出すことで、変数のステート、つまり呼び出しの間の変数の値が存続していることを確認できます。

## 前提条件

始める前に、[開発者環境ガイドの](./3-dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## counterdapp プロジェクトを作成します。

まだ開いていなければ、ローカル・コンピューターでターミナル・ウィンドウを開いてください。

まず、以下のコマンドを実行して新しいプロジェクトを作成します：

``` bash
dfx new --type=rust rust_counter
```

次に、次のコマンドを実行して、プロジェクト・ディレクトリに移動します：

``` bash
cd rust_counter
```

:::info
Note: 以下の手順は、ターミナルが開いていて、カレントディレクトリが`rust_counter` であると仮定しています .
::：

これで、Rustdapp のファイルが揃ったので、`lib.rs` dapp のテンプレートを、dapp のカウンターを作成するコードに置き換えることができます。

デフォルトのdapp を置き換えるには、テキストエディタでテンプレート`src/rust_counter_backend/src/lib.rs` ファイルを開き、既存の内容を削除します。

次に、このコードをコピーして`src/rust_counter_backend/src/lib.rs` ファイルに貼り付けます。

``` rust
use std::cell::RefCell;
use candid::types::number::Nat;

thread_local! {
    static COUNTER: RefCell<Nat> = RefCell::new(Nat::from(0));
}

/// Get the value of the counter.
#[ic_cdk_macros::query]
fn get() -> Nat {
    COUNTER.with(|counter| (*counter.borrow()).clone())
}

/// Set the value of the counter.
#[ic_cdk_macros::update]
fn set(n: Nat) {
    // COUNTER.replace(n);  // requires #![feature(local_key_cell_methods)]
    COUNTER.with(|count| *count.borrow_mut() = n);
}

/// Increment the value of the counter.
#[ic_cdk_macros::update]
fn inc() {
    COUNTER.with(|counter| *counter.borrow_mut() += 1);
}



#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_get_set() {
        let expected = Nat::from(42);
        set(expected.clone());
        assert_eq!(get(), expected);
    }

    #[test]
    fn test_init() {
        assert_eq!(get(), Nat::from(0));
    }

    #[test]
    fn test_inc() {
        for i in 1..10 {
            inc();
            assert_eq!(get(), Nat::from(i));
        }
    }
}
```

変更を保存し、`lib.rs` ファイルを閉じて続行します。

### インターフェース記述ファイルの更新

Candid は、Internet Computer 上で動作するcanisters と対話するためのインターフェイス記述言語 (IDL) です。 Candid ファイルは、canisterのインターフェイスの言語に依存しない記述で、canister が定義する各関数の名前、パラメータ、結果の形式とデータ型を含みます。

Candid ファイルをプロジェクトに追加することで、Internet Computer ブロックチェーン上で安全に実行するために、Rust での定義からデータが適切に変換されることを保証できます。

Candidインターフェース記述言語の構文の詳細については、[**Candidガイド**](./../candid/index.md)または[Candidクレートのドキュメントを](https://docs.rs/candid/)参照してください。

Candidファイルを更新するには、テキストエディタで`src/rust_counter_backend/rust_counter_backend.did` ファイルを開き、`increment` 、`get` 、`set` 関数の次の`service` 定義をコピーして貼り付けます：

``` did
service : {
    "increment": () -> ();
    "get": () -> (nat) query;
    "set": (nat) -> ();
}
```

変更を保存し、`rust_counter_backend.did` ファイルを閉じて続行します。

## Cargo.tomlファイルの作成

他の規格のRustクレートと同様に、Rustクレート構築の詳細を設定する`Cargo.toml` ファイルがあります。

`src/rust_counter_backend/Cargo.toml` ファイルを開き、既存の内容を以下の内容に置き換えます：

``` toml
[package]
name = "rust_counter_backend"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[lib]
crate-type = ["cdylib"]

[dependencies]
candid = "0.8.2"
ic-cdk = "0.7.0"
serde = { version = "1.0", features = ["derive"] }
```

ファイルを保存します。

## Cargo.tomlの依存関係を更新します。

Cargo.tomlファイルを変更したので、以下のコマンドを実行してプロジェクトの依存関係を更新します：

    cargo update

### ローカルのcanister 実行環境を起動します。

`rust_counter` プロジェクトをビルドする前に、開発環境または分散型Internet Computer ブロックチェーンメインネットで実行されているローカルcanister 実行環境に接続する必要があります。

以下のコマンドを実行して、バックグラウンドでコンピューター上のローカルcanister 実行環境を起動します：

``` bash
dfx start --clean --background 
```

プラットフォームとローカルのセキュリティ設定によっては、警告が表示される場合があります。受信ネットワーク接続の許可または拒否を求められた場合は、\[**許可\]** をクリックします。

### プロジェクトの登録、ビルド、およびデプロイ

開発環境で実行されているローカルのcanister 実行環境に接続したら、プロジェクトをローカルに登録、ビルド、およびデプロイできます。

次のコマンドを実行して、`dfx.json` ファイルで指定したcanisters を登録、ビルド、デプロイします：

``` bash
dfx deploy
```

## canister の関数を呼び出して、その関数をテストします。dapp

canister のデプロイに成功したら、canister が提供する関数を呼び出してテストできます。このガイドでは

- `get` 関数を呼び出してカウンタの値をクエリーします。

- `increment` 関数を呼び出して、呼び出されるたびにカウンタをインクリメントします。

- `set` 関数を呼び出して引数を渡し、カウンタを指定した任意の値にアップデートします。

`get` 関数を呼び出して、次のコマンドを実行して`COUNTER` 変数の現在値を読み取ります：

``` bash
dfx canister call rust_counter_backend get
```

このコマンドは、`COUNTER` 変数の現在値をゼロとして返します：

    (0 : nat)

`increment` 関数を呼び出して、`COUNTER` 変数の値を 1 つインクリメントします：

``` bash
dfx canister call rust_counter_backend increment
```

このコマンドは変数の値をインクリメントし、ステート を変更しますが、結果は返しません。

コマンドを再実行して`get` 関数を呼び出し、`COUNTER` 変数の現在の値を確認します：

``` bash
dfx canister call rust_counter_backend get
```

コマンドは更新された`COUNTER` 変数の値を1として返します：

    (1 : nat)

追加のコマンドを実行して、関数の呼び出しや異なる値の使用を試してみてください。

例えば、以下のようなコマンドを実行して、カウンターの値を設定したり返したりしてみてください：

``` bash
dfx canister call rust_counter_backend set '(987)'
dfx canister call rust_counter_backend get
```

現在の値 987 を返します。

``` bash
dfx canister call rust_counter_backend increment
dfx canister call rust_counter_backend get
```

インクリメントされた値 988 を返します。

## 次のステップ

次に、[周期タイマーの](10-timers.md)使い方を調べてみましょう。

<!---
# 9: Incrementing a counter

## Overview

In this guide, you are going to write a dapp that provides a few basic functions to increment a counter and illustrates the persistence of a value.

For this guide, the dapp declares a `COUNTER` as a mutable variable to contain a natural number that represents the current value of the counter. This dapp supports the following functions:

-   The `increment` function updates the current value, incrementing by 1 with no return value.

-   The `get` function is a simple query that returns the current value of the counter.

-   The `set` function updates the current value to the numeric value you specify as an argument.

This guide provides a simple example of how you can increment a counter by calling functions on a deployed canister. By calling the function to increment a value multiple times, you can verify that the variable state—that is, the value of the variable between calls—persists.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./3-dev-env.md).

## Create the counter dapp project

Open a terminal window on your local computer, if you don’t already have one open.

Start by creating a new project by running the following command:

``` bash
dfx new --type=rust rust_counter
```

Then, navigate into your project directory by running the command:

``` bash
cd rust_counter
```

:::info
Note: the following steps assume the terminal is still open and the current directory is `rust_counter`.
:::

Now that you have the files in place for your Rust dapp, we can replace the template `lib.rs` dapp code with some code that creates a counter dapp instead. 

To replace the default dapp, open the template `src/rust_counter_backend/src/lib.rs` file in a text editor and delete the existing content.

Then, copy and paste this code into the `src/rust_counter_backend/src/lib.rs` file.

```rust
use std::cell::RefCell;
use candid::types::number::Nat;

thread_local! {
    static COUNTER: RefCell<Nat> = RefCell::new(Nat::from(0));
}

/// Get the value of the counter.
#[ic_cdk_macros::query]
fn get() -> Nat {
    COUNTER.with(|counter| (*counter.borrow()).clone())
}

/// Set the value of the counter.
#[ic_cdk_macros::update]
fn set(n: Nat) {
    // COUNTER.replace(n);  // requires #![feature(local_key_cell_methods)]
    COUNTER.with(|count| *count.borrow_mut() = n);
}

/// Increment the value of the counter.
#[ic_cdk_macros::update]
fn inc() {
    COUNTER.with(|counter| *counter.borrow_mut() += 1);
}



#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_get_set() {
        let expected = Nat::from(42);
        set(expected.clone());
        assert_eq!(get(), expected);
    }

    #[test]
    fn test_init() {
        assert_eq!(get(), Nat::from(0));
    }

    #[test]
    fn test_inc() {
        for i in 1..10 {
            inc();
            assert_eq!(get(), Nat::from(i));
        }
    }
}
```

Save your changes and close the `lib.rs` file to continue.

### Update interface description file

Candid is an interface description language (IDL) for interacting with canisters running on the Internet Computer. Candid files provide a language-independent description of a canister’s interfaces including the names, parameters, and result formats and data types for each function a canister defines.

By adding Candid files to your project, you can ensure that data is properly converted from its definition in Rust to run safely on the Internet Computer blockchain.

To see details about the Candid interface description language syntax, see the [**Candid guide**](./../candid/index.md) or the [Candid crate documentation](https://docs.rs/candid/).

To update the Candid file, open the `src/rust_counter_backend/rust_counter_backend.did` file in a text editor, then copy and paste the following `service` definition for the `increment`, `get`, and `set` functions:

``` did
service : {
    "increment": () -> ();
    "get": () -> (nat) query;
    "set": (nat) -> ();
}
```

Save your changes and close the `rust_counter_backend.did` file to continue.

## Writing the Cargo.toml file

As with any standard Rust crate, it has a `Cargo.toml` file which configures the details to build the Rust crate.

Open the `src/rust_counter_backend/Cargo.toml` file and replace the existing content with the following:

``` toml
[package]
name = "rust_counter_backend"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[lib]
crate-type = ["cdylib"]

[dependencies]
candid = "0.8.2"
ic-cdk = "0.7.0"
serde = { version = "1.0", features = ["derive"] }
```

Save the file.

## Update the Cargo.toml dependencies

Since we made changes to the Cargo.toml file, run the following command to update the project's dependencies:

```
cargo update
```

### Start the local canister execution environment

Before you can build the `rust_counter` project, you need to connect to the local canister execution environment running in your development environment or the decentralized Internet Computer blockchain mainnet.

Start the local canister execution environment on your computer in the background by running the following command:

``` bash
dfx start --clean --background 
```

Depending on your platform and local security settings, you might see a warning displayed. If you are prompted to allow or deny incoming network connections, click **Allow**.

### Register, build, and deploy your project

After you connect to the local canister execution environment running in your development environment, you can register, build, and deploy your project locally.

Register, build, and deploy the canisters specified in the `dfx.json` file by running the following command:

``` bash
dfx deploy
```

## Call the canister's functions and test the dapp

After successfully deploying the canister, you can test the canister by invoking the functions it provides. For this guide:

-   Call the `get` function to query the value of the counter.

-   Call the `increment` function to increment the counter each time it is called.

-   Call the `set` function to pass an argument to update the counter to an arbitrary value you specify.

Call the `get` function to read the current value of the `COUNTER` variable by running the following command:

``` bash
dfx canister call rust_counter_backend get
```

The command returns the current value of the `COUNTER` variable as zero:

    (0 : nat)

Call the `increment` function to increment the value of the `COUNTER` variable by one:

``` bash
dfx canister call rust_counter_backend increment
```

This command increments the value of the variable—changing its state—but does not return the result.

Rerun the command to call the `get` function to see the current value of the `COUNTER` variable:

``` bash
dfx canister call rust_counter_backend get
```

The command returns the updated value of the `COUNTER` variable as one:

    (1 : nat)

Run additional commands to experiment with call the functions and using different values.

For example, try commands similar to the following to set and return the counter value:

``` bash
dfx canister call rust_counter_backend set '(987)'
dfx canister call rust_counter_backend get
```

Returns the current value of 987.

``` bash
dfx canister call rust_counter_backend increment
dfx canister call rust_counter_backend get
```

Returns the incremented value of 988.

## Next steps

Next, let's explore how to use [periodic timers](10-timers.md).

-->
