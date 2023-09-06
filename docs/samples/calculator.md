# 電卓

## 概要

このサンプルは基本的な電卓dapp を示します。直交永続セル変数を使用して、最新の計算結果を表す任意の精度の整数を格納します。

dapp は以下のメソッドを公開するインターフェースを提供します：

- `add`入力を受け付け、加算を実行します。
- `sub`入力を受け付け、減算を行います。
- `mul`入力を受け付け、乗算を行います。
- `div`入力を受け付け、除算を行い、ゼロによる除算を防ぐためにオプションの型を返します。
- `clearall`セル変数の値をゼロにしてクリアします。

これはMotoko の例で、現在のところ Rust のバリアントはありません。

## 前提条件

この例には、以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/calc
    dfx start --background

### ステップ 2: コマンドでcanister をデプロイします：

    dfx deploy

### ステップ 3: 電卓関数を実行します。例えば、2を3で倍します：

    dfx canister call calc add '(2)'
    dfx canister call calc mul '(3)'

出力します：

    (2 : int)
    (6 : int)

<!---
# Calculator 

## Overview

This example demonstrates a basic calculator dapp. It uses an orthogonally persistent cell variable to store an arbitrary precision integer that represents the result of the most recent calculation.

The dapp provides an interface that exposes the following methods:

- `add`: accepts input and performs addition.
- `sub`: accepts input and performs subtraction.
- `mul`: accepts input and performs multiplication.
- `div`: accepts input, performs division, and returns an optional type to guard against division by zero.
- `clearall`: clears the cell variable by setting its value to zero.

This is a Motoko example that does not currently have a Rust variant. 


## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/calc
dfx start --background
```

### Step 2: Deploy the canister with the command:

```
dfx deploy
```

### Step 3: Run a calculator function. For example, to multiple 2 by 3:

```
dfx canister call calc add '(2)'
dfx canister call calc mul '(3)'
```

Output:

```
(2 : int)
(6 : int)
```


-->
