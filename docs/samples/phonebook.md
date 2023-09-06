# 電話帳

## 概要

この例では、ウェブブラウザからアクセスできる電話帳アプリケーションを示します。

このアプリケーションは、次のMotoko ソースコードファイルからビルドされています：

- `index.jsx`Web ブラウザーで起動したときにアプリケーションのフロントエンド ユーザー インターフェースを生成するために使用される JavaScript、React、および HTML が含まれています。
- `Main.mo`:actor の定義と、このcanister によって公開されるメソッドが含まれています。

これはMotoko の例で、現在 Rust のバリアントはありません。

## 前提条件

この例には以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\][Node.jsを](https://nodejs.org/en/download/)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードします: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ1：プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/phone-book
    dfx start --background

### ステップ 2: フロントエンドの依存関係をインストールします：

    npm install

### ステップ 3:canister をデプロイします：

    dfx deploy

### ステップ4: 電話帳にアクセスできるURLをメモしておきます。

    echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id www)"

### ステップ5：ウェブブラウザで前述のURLを開きます。

電話帳のエントリーを保存するためのインターフェイスが表示されます：

![Phonebook](./_attachments/phonebook.png)

<!---
# Phone book

## Overview

This example demonstrates a phone book application that is accessible from your web browser.

The application is built from the following Motoko source code files:

- `index.jsx`: contains the JavaScript, React, and HTML used to generate the front-end user interface for the application when it is launched in a web browser.
- `Main.mo`: contains the actor definition and methods exposed by this canister.

This is a Motoko example that does not currently have a Rust variant. 

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Install [Node.js](https://nodejs.org/en/download/).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/phone-book
dfx start --background
```

### Step 2: Install front-end dependencies:

```
npm install
```

### Step 3: Deploy the canister:

```
dfx deploy
```

### Step 4: Take note of the URL at which the phone book is accessible.

```
echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id www)"
```

### Step 5: Open the aforementioned URL in your web browser.

You will see an interface that you can interact with to store phone book entries:

![Phonebook](./_attachments/phonebook.png)




-->
