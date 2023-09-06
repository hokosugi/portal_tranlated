# カウンター

## 概要

このサンプルはカウンタ・アプリケーションを示します。直交永続カウンタ変数を使用して、カウンタの現在値を表す任意の精度の自然 数を格納します。

カウンタ変数を宣言するときにMotoko キーワード stable を使用すると、canister コードがアップグレードされるたびに、この変数の値が自動的に保存されます。stable キーワードがないと、変数は柔軟性があるとみなされ、canister アップグレードのたびに、つまり新しいコードがcanister にデプロイされるたびに、その値が再初期化されます。

アプリケーションは、以下のメソッドを公開するインターフェースを提供します：

- `set`カウンターの値を設定します。
- `inc`カウンターの値を設定します。
- `get`カウンタの値を取得します。

## Motoko バリアント

### 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

- #### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

<!-- end list -->

    cd examples/motoko/counter
    dfx start --background

- #### ステップ 2:canister をデプロイします：

<!-- end list -->

    dfx deploy

- #### ステップ 3: カウンターの値を設定します：

<!-- end list -->

    dfx canister call counter set '(7)'

- #### ステップ 4: カウンタの値をインクリメントします：

<!-- end list -->

    dfx canister call counter inc

- #### ステップ5：カウンターの値を取得します：

<!-- end list -->

    dfx canister call counter get

以下の出力が返されるはずです：

    (8 : nat)

### リソース

Motoko のこれらの機能の詳細については、以下を参照してください：

- [直交持続性](../motoko/main/motoko#orthogonal-persistence)。
- [安定値の宣言](../motoko/main/upgrades#declaring-stable-variables)。

## Rustの変種

### 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

- #### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

<!-- end list -->

    cd examples/rust/counter
    dfx start --background

- #### ステップ 2:canister をテストします：

<!-- end list -->

    cargo test

- #### ステップ 3:canister をデプロイします：

<!-- end list -->

    dfx deploy

- #### ステップ 4: カウンターの値を設定します：

<!-- end list -->

    dfx canister call counter set '(7)'

- #### ステップ5：カウンタの値をインクリメントします：

<!-- end list -->

    dfx canister call counter inc

- #### ステップ6：カウンターの値を取得します：

<!-- end list -->

    dfx canister call counter get

以下の出力が返されるはずです：

    (8 : nat)

<!---
# Counter

## Overview

This example demonstrates a counter application. It uses an orthogonally persistent counter variable to store an arbitrary precision natural number that represents the current value of the counter.

By using the Motoko keyword stable when declaring the counter variable, the value of this variable will automatically be preserved whenever your canister code is upgraded. Without the stable keyword, a variable is deemed flexible, and its value is reinitialized on every canister upgrade, i.e. whenever new code is deployed to the canister.

The application provides an interface that exposes the following methods:

- `set`: sets the value of the counter.
- `inc`: increments the value of the counter.
- `get`: gets the value of the counter.

## Motoko variant

### Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

- #### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/counter
dfx start --background
```

- #### Step 2: Deploy the canister:

```
dfx deploy
```

- #### Step 3: Set the value of the counter:

```
dfx canister call counter set '(7)'
```

- #### Step 4: Increment the value of the counter:

```
dfx canister call counter inc
```

- #### Step 5: Get the value of the counter:

```
dfx canister call counter get
```

The following output should be returned:

```
(8 : nat)
```

### Resources
To learn more about these features of Motoko, see:

- [Orthogonal persistence](../motoko/main/motoko#orthogonal-persistence).
- [Declaring stable values](../motoko/main/upgrades#declaring-stable-variables).

## Rust variant

### Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

- #### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/rust/counter
dfx start --background
```

- #### Step 2: Test the canister:

```
cargo test
```

- #### Step 3: Deploy the canister:

```
dfx deploy
```

- #### Step 4: Set the value of the counter:

```
dfx canister call counter set '(7)'
```

- #### Step 5: Increment the value of the counter:

```
dfx canister call counter inc
```

- #### Step 6: Get the value of the counter:

```
dfx canister call counter get
```

The following output should be returned:

```
(8 : nat)
```


-->
