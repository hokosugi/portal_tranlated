# クイックソート

## 概要

このサンプルはクイックソートアルゴリズムを実装しています。

これはMotoko のサンプルで、現在のところ Rust バリアントはありません。

## 前提条件

このサンプルには以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/quicksort
    dfx start --background

### ステップ 2:canister をデプロイします：

    dfx deploy

### ステップ 3: 整数の配列をソートします。

    dfx canister call quicksort sort '(vec { 5; 3; 0; 9; 8; 2; 1; 4; 7; 6 })'

出力は以下のようになります：

    (
      vec {
        0 : int;
        1 : int;
        2 : int;
        3 : int;
        4 : int;
        5 : int;
        6 : int;
        7 : int;
        8 : int;
        9 : int;
      },
    )

<!---
# Quick sort

## Overview
This example implements the quick sort algorithm.

This is a Motoko example that does not currently have a Rust variant. 

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/quicksort
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

### Step 3: Sort an array of integers.

```
dfx canister call quicksort sort '(vec { 5; 3; 0; 9; 8; 2; 1; 4; 7; 6 })'
```

The output will resemble the following:

```
(
  vec {
    0 : int;
    1 : int;
    2 : int;
    3 : int;
    4 : int;
    5 : int;
    6 : int;
    7 : int;
    8 : int;
    9 : int;
  },
)
```
-->
