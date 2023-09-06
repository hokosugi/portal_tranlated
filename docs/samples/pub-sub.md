# パブサブ

## 概要

このサンプル・プロジェクトは、関数をcanister 間呼び出しの引数として渡し、コールバックとして使用する方法を示します。

分散システムでも非集中システムでもよくある問題は、別々のサービス（またはcanisters ）を互いに同期させておくことです。この問題に対する潜在的な解決策はたくさんありますが、よく使われるのはPublisher/Subscriberパターン、または「PubSub」です。PubSubは、Internet Computer 、その主な欠点であるメッセージ配信の失敗が適用されないため、特に価値のあるパターンです。

## 前提条件

この例では以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクト・ファイルをダウンロードしてください。https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/pub-sub
    dfx start --background

### ステップ 2:canister をデプロイします：

    dfx deploy

### ステップ 3: "Apples "トピックにサブスクライブします：

    dfx canister call sub init '("Apples")'

### ステップ4: "Apples "トピックにパブリッシュします：

    dfx canister call pub publish '(record { "topic" = "Apples"; "value" = 2 })'

### ステップ5: サブスクリプションを受信します：

    dfx canister call sub getCount

出力は以下のようになるはずです：

    (2 : nat)

<!---
# PubSub

## Overview
This sample project demonstrates how functions may be passed as arguments of inter-canister calls to be used as callbacks.

A common problem in both distributed and decentralized systems is keeping separate services (or canisters) synchronized with one another. While there are many potential solutions to this problem, a popular one is the Publisher/Subscriber pattern or "PubSub". PubSub is an especially valuable pattern on the Internet Computer as its primary drawback, message delivery failures, does not apply.

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/pub-sub
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

### Step 3: Subscribe to the "Apples" topic:

```
dfx canister call sub init '("Apples")'
```

### Step 4: Publish to the "Apples" topic:

```
dfx canister call pub publish '(record { "topic" = "Apples"; "value" = 2 })'
```

### Step 5: Receive your subscription:

```
dfx canister call sub getCount
```

The output should resemble the following:

```
(2 : nat)
```
-->
