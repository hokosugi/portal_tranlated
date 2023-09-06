# 2: プロジェクト組織

## 概要

コマンドで新しい Rust プロジェクトを作成すると、以下のようなプロジェクト構成になります：

    dfx new --type rust example

コマンドで新しい Rust プロジェクトを作成すると、次のようなプロジェクト構造が生成されます：

    Cargo.lock
    Cargo.toml
    dfx.json
    package.json
    src
    ├── example_backend
    │   ├── Cargo.toml
    │   ├── example_backend.did
    │   └── src
    │       └── lib.rs
    └── example_frontend
        ├── assets
        │   ├── favicon.ico
        │   ├── logo2.svg
        │   ├── main.css
        │   └── sample-asset.txt
        └── src
            ├── index.html
            └── index.js
    webpack.config.js

この構造の中で、バックエンドcanister を見ることができます。この場合、`example_backend` には以下のコンポーネントが含まれています：

src/example\_backend：

    │   ├── Cargo.toml //
    │   ├── example_backend.did // The backend canister's Candid file. 
    │   └── src 
    │       └── lib.rs // The file containing your Rust smart contract.

### `dfx.json`

src/example\_backend: プロジェクトディレクトリに含まれるテンプレートファイルの1つはデフォルトの`dfx.json` 設定ファイルです。このファイルには、`Cargo.toml` ファイルが Rust プログラムのビルドとパッケージ管理の設定詳細を提供するのと同じように、Internet Computer ブロックチェーン用のプロジェクトをビルドするために必要な設定が含まれています。

設定ファイルは以下のようになります：

``` json
{
  "canisters": {
    "example_backend": {
      "candid": "src/example_backend/example_backend.did",
      "package": "example_backend",
      "type": "rust"
    },
    "example_frontend": {
      "dependencies": [
        "example_backend"
      ],
      "frontend": {
        "entrypoint": "src/example_frontend/src/index.html"
      },
      "source": [
        "src/example_frontend/assets",
        "dist/example_frontend/"
      ],
      "type": "assets"
    }
  },
  "defaults": {
    "build": {
      "args": "",
      "packtool": ""
    }
  },
  "version": 1
}
```

`canisters` キーの下に、`example_backend` canister .

- `"type": "rust"` は、 が タイプであることを指定します .`example_backend` `rust` canister

- `"candid": "src/example_backend/example_backend.did""` で使用する Candid インターフェース記述ファイルの場所を指定します。 .canister

- `"package": "example_backend"` Rustクレートのパッケージ名を指定します。crate ファイルと同じでなければなりません。`Cargo.toml` 

### `Cargo.toml`

ルートディレクトリには`Cargo.toml` ファイルがあります。

このファイルは、各 Rust クレートへのパスを指定して Rust ワークスペースを定義します。Rust タイプcanister は、WebAssembly にコンパイルされた Rust クレートにすぎません。ここでは、`src/example_backend` にメンバが 1 つあり、これが唯一の Rustcanister です。

``` toml
[workspace]
members = [
    "src/example_backend",
]
```

### `src/example_backend/`

これで、Rustcanister に入りました。標準のRustクレートと同様に、`Cargo.toml` ファイルがあり、Rustクレート構築の詳細を設定します。

#### `src/example_backend/Cargo.toml`

``` toml
[package]
name = "example_backend"
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

:::info
この Rust プログラムをWebAssembly モジュールにコンパイルするために必要な`crate-type = ["cdylib"]` 行に注目してください。
::：

#### `src/example_backend/src/lib.rs`

デフォルトのプロジェクトには、Rust CDK`query` マクロを使用するシンプルな`greet` 関数があります。

``` rust
#[ic_cdk::query]
fn greet(name: String) -> String {
    format!("Hello, {}!", name)
}

```

#### `src/example_backend/example_backend.did`

Candid は、Internet Computer 上で実行されるcanisters と対話するためのインターフェイス記述言語 (IDL) です。 Candid ファイルは、canister が定義する各関数の名前、パラメータ、結果の形式とデータ型を含む、canister'のインターフェイスの言語に依存しない記述を提供します。

Candid ファイルをプロジェクトに追加することで、Internet Computer ブロックチェーン上で安全に実行するために、Rust での定義からデータが適切に変換されることを保証できます。

Candidインターフェース記述言語の構文の詳細については、[*Candidガイド*](./../candid/index.md)または[Candidクレートのドキュメントを](https://docs.rs/candid/)参照してください。

``` did
service : {
    "greet": (text) -> (text) query;
}

```

この定義では、`greet` 関数が入力として`text` データを受け取り、`text` データを返す`query` メソッドであることを指定しています。

## グローバル変数の宣言

### 安定変数と柔軟変数

安定**変数とは**、システムがアップグレード後も保持するグローバル変数のことです。例えば、ユーザーデータベースはおそらく安定であるべきでしょう。

柔軟**変数とは**、コードのアップグレード時にシステムが破棄するグローバル変数のことです。例えば、キャッシュをホットな状態に保つことが製品にとって重要でない場合、キャッシュをフレキシブルにすることは合理的です。

### すべてのグローバル変数を一箇所に

すべてのグローバル変数を、canister のメインファイルという、ひとつのファイルに個人的に保存するのがベストプラクティスです。この方法がベストプラクティスとされている理由は

- コードの大半はグローバル変数と直接やりとりしないので、コードのテストが簡単になります。
- canister でグローバル・ステート がどのように使用されているかを理解しやすくなります。

また、コード内に、どの変数が安定しているかを指定するコメントを追加することも推奨します：

``` rust
thread_local! {
    // static
    static USERS: RefCell<Users> = ... ;
    // flexible
    static LAST_ACTIVE: Cell<UserId> = ...;
}
```

## Canister インターフェース

コンパイラが対応する Candid ファイルを自動的に生成するMotoko と比較して、Rust 開発では異なるアプローチを推奨します。

### .did ファイルをcanister の真実のソースにします。

あなたの Candid ファイルは、フロントエンド部分で働く同僚を含め、あなたのcanister と対話したい人々のためのドキュメンテーションの主要なソースであるべきです。インターフェイスは安定し、見つけやすく、十分に文書化されていなければなりません。

以下は .did ファイルの例です：

    type TransferError = variant {
      // The debit account didn't have enough funds
      // for completing the transaction.
      InsufficientFunds : Balance;
      // ...
    };
    type TransferResult =
      variant { Ok : BlockHeight; Err : TransferError; };
    service {
      // Transfer funds between accounts.
      transfer : (TransferArgs) -> (TransferResult);
    }

Candidの詳細およびRustに相当するCandidの型については、[Candidのリファレンスドキュメントを](https://internetcomputer.org/docs/current/references/candid-ref)参照してください。

.didファイルと実装が同期していることを確認するには、Candidツールを使用してください。Rust CDKには、Canister メソッドにアノテーションを付け、.didファイルを抽出できるマクロがあります。

Candidパッケージの最新バージョンには、あるインターフェースが別のインターフェースのサブタイプであることをチェックする関数があります。

### エラーケースを示すためのバリアント型の使用

Rustエラー型はAPIコンシューマーがエラーから正しく回復するのを容易にする傾向があり、一方Candidバリアント型はクライアントがより優雅にエッジケースエラーを処理するのを助けることができます。バリアント型の使用は、Motoko でのエラー処理方法としても推奨されています。

以下は、エラーケースを示すためにバリアント型を使用する例です：

    type CreateEntityResult = variant {
      Ok  : record { entity_id : EntityId; };
      Err : opt variant {
        EntityAlreadyExists;
        NoSpaceLeftInThisShard;
      }
    };
    service : {
      create_entity : (EntityParams) -> (CreateEntityResult);
    }

:::info
サービスメソッドが結果型を返しても、呼び出しを拒否することができます。そのため、InvalidArgument や Unauthorized のようなエラーのバリアントを追加しても、 プログラムでそのようなエラーから回復する意味がないため、 あまりメリットがないかもしれません。ですから、不正な、無効な、あるいは認証されていないリクエストを 拒否することは、たいていの状況では正しいことでしょう。
::：

## 次のステップ

それではさっそく Rust の[開発環境を](./3-dev-env.md)セットアップしましょう。

<!---
# 2: Project organization

## Overview

When a new Rust project is created with the command:

```
dfx new --type rust example
```

the following project structure is generated: 

```
Cargo.lock
Cargo.toml
dfx.json
package.json
src
├── example_backend
│   ├── Cargo.toml
│   ├── example_backend.did
│   └── src
│       └── lib.rs
└── example_frontend
    ├── assets
    │   ├── favicon.ico
    │   ├── logo2.svg
    │   ├── main.css
    │   └── sample-asset.txt
    └── src
        ├── index.html
        └── index.js
webpack.config.js
```

In this structure, you can see the backend canister, in this case `example_backend` contains the following components:

src/example_backend:
```
│   ├── Cargo.toml //
│   ├── example_backend.did // The backend canister's Candid file. 
│   └── src 
│       └── lib.rs // The file containing your Rust smart contract.
```

### `dfx.json`

One of the template files included in your project directory is a default `dfx.json` configuration file. This file contains settings required to build a project for the Internet Computer blockchain much like the `Cargo.toml` file provides build and package management configuration details for Rust programs.

The configuration file should look like this:

```json
{
  "canisters": {
    "example_backend": {
      "candid": "src/example_backend/example_backend.did",
      "package": "example_backend",
      "type": "rust"
    },
    "example_frontend": {
      "dependencies": [
        "example_backend"
      ],
      "frontend": {
        "entrypoint": "src/example_frontend/src/index.html"
      },
      "source": [
        "src/example_frontend/assets",
        "dist/example_frontend/"
      ],
      "type": "assets"
    }
  },
  "defaults": {
    "build": {
      "args": "",
      "packtool": ""
    }
  },
  "version": 1
}
```

Notice that under the `canisters` key, you have some default settings for the `example_backend` canister.

-  `"type": "rust"` specifies that `example_backend` is a `rust` type canister.

-  `"candid": "src/example_backend/example_backend.did""` specifies the location of the Candid interface description file to use for the canister.

-  `"package": "example_backend"` specifies the package name of the Rust crate. It should be the same as in the crate `Cargo.toml` file.

### `Cargo.toml`

In the root directory, there is a `Cargo.toml` file.

It defines a Rust workspace by specifying paths to each Rust crate. A Rust type canister is just a Rust crate compiled to WebAssembly. Here we have one member at `src/example_backend` which is the only Rust canister.

``` toml
[workspace]
members = [
    "src/example_backend",
]
```

### `src/example_backend/`

Now we are in the Rust canister. As any standard Rust crate, it has a `Cargo.toml` file which configures the details to build the Rust crate.

#### `src/example_backend/Cargo.toml`

``` toml
[package]
name = "example_backend"
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

:::info
Notice the `crate-type = ["cdylib"]` line which is necessary to compile this Rust program into WebAssembly module.
:::

#### `src/example_backend/src/lib.rs`

The default project has a simple `greet` function that uses the Rust CDK `query` macro.

``` rust
#[ic_cdk::query]
fn greet(name: String) -> String {
    format!("Hello, {}!", name)
}

```

#### `src/example_backend/example_backend.did`

Candid is an interface description language (IDL) for interacting with canisters running on the Internet Computer. Candid files provide a language-independent description of a canister’s interfaces including the names, parameters, and result formats and data types for each function a canister defines.

By adding Candid files to your project, you can ensure that data is properly converted from its definition in Rust to run safely on the Internet Computer blockchain.

To see details about the Candid interface description language syntax, see the [*Candid Guide*](./../candid/index.md) or the [Candid crate documentation](https://docs.rs/candid/).

``` did
service : {
    "greet": (text) -> (text) query;
}

```

This definition specifies that the `greet` function is a `query` method which takes `text` data as input and returns `text` data.

## Declaring global variables

### Stable variables vs flexible variables

**Stable variables** are global variables that the system preserves across upgrades. For example, a user database should probably be stable.

**Flexible variables** are global variables that the system discards on code upgrade. For example, it is reasonable to make a cache flexible if keeping this cache hot is not critical for your product.

### Putting all global variables in one place

It is best practice to store all global variables privately in a single file; the canister main file. This approach is considered the best practice because:

- Testing your code is easier since the majority of your code won't interact with the global variables directly. 
- It is easier to understand how the global state is being used by the canister. 

It is also recommended that you add comments that within your code that specify which variables are stable, such as:

```rust
thread_local! {
    // static
    static USERS: RefCell<Users> = ... ;
    // flexible
    static LAST_ACTIVE: Cell<UserId> = ...;
}
```

## Canister interfaces
In comparison to Motoko, where the compiler automatically generates the corresponding Candid file, a different approach is recommended to Rust development. 

### Making the .did file the canister's source of truth
Your Candid file should be the main source of documentation for people who want to interact with your canister, including your colleagues who work on the front end portion. The interface should be stable, easy to find, and well documented, which is not something you can get automatically.

The following is an example of a .did file:

```
type TransferError = variant {
  // The debit account didn't have enough funds
  // for completing the transaction.
  InsufficientFunds : Balance;
  // ...
};
type TransferResult =
  variant { Ok : BlockHeight; Err : TransferError; };
service {
  // Transfer funds between accounts.
  transfer : (TransferArgs) -> (TransferResult);
}
```

For more information on Candid and to see the Rust equivalent of Candid types, please see the [Candid reference documentation](https://internetcomputer.org/docs/current/references/candid-ref). 

To make sure that your .did file and your implementation are in sync with one another, use the Candid tooling. There are macros in the Rust CDK that allow you to annotate your Canister methods and extract the .did file.

The latest versions of the Candid package have functions to check that one interface is a subtype of another interface, which is a Candid term for “backward compatible”.

### Using variant types to indicate error cases
Rust error types tend to make it easy to recover from errors correctly for API consumers, while Candid variants can help clients handle edge case errors more gracefully. Using variant types is also the preferred method of error handling in Motoko.

The following is an example of using variant types to indicate error cases:

```
type CreateEntityResult = variant {
  Ok  : record { entity_id : EntityId; };
  Err : opt variant {
    EntityAlreadyExists;
    NoSpaceLeftInThisShard;
  }
};
service : {
  create_entity : (EntityParams) -> (CreateEntityResult);
}
```

:::info
If a service method returns a result type, it can still reject the call. Therefore, there may not be much benefit from adding error variants like InvalidArgument or Unauthorized, as there is no meaningful way to recover from such errors programmatically. So rejecting malformed, invalid, or unauthorized requests is probably the right thing to do in most situations.
:::

## Next steps
Now let's get started setting up the Rust [developer environment](./3-dev-env.md).

-->
