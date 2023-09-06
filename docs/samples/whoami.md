# 私は誰？

## 概要

この例では、canister 、呼び出し元と自分自身を識別する方法を示します。

## 前提条件

この例には、以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクト・ファイルをダウンロードしてください。https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/whoami
    dfx start --background

### ステップ 2:canister をビルドしてデプロイします：

    dfx canister install whoami --argument='(principal "2mxjj-pyyts-rk2hl-2xyka-avylz-dfama-pqui5-pwrhx-wtq2x-xl5lj-qqe")'
    dfx build
    dfx deploy

### ステップ 3:`whoami` メソッドを呼び出します：

    dfx canister call whoami whoami

### ステップ 4: プリンシパル識別子を確認します。

### ステップ 5:`id` メソッドを呼び出します：

    dfx canister call whoami id

### ステップ 6:canister のプリンシパル識別子を確認します。

<!---
# Who am I?

## Overview

This example demonstrates how a canister can identify its caller and itself.

## Prerequisites

This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/whoami
dfx start --background
```

### Step 2: Build and deploy the canister:

```
dfx canister install whoami --argument='(principal "2mxjj-pyyts-rk2hl-2xyka-avylz-dfama-pqui5-pwrhx-wtq2x-xl5lj-qqe")'
dfx build
dfx deploy
```

### Step 3: Invoke the `whoami` method:

```
dfx canister call whoami whoami
```

### Step 4: Observe your principal identifier.

### Step 5: Invoke the `id` method:

```
dfx canister call whoami id
```

### Step 6: Observe the principal identifier of your canister.


-->
