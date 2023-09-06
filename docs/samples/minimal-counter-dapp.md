# ミニマムカウンターdapp

## 概要

例dapp は、バックエンドとフロントエンドの両方を持つ非常に基本的なdapp を構築する方法を示しています。バックエンドの機能にはMotoko を使い、フロントエンドにはプレーンな HTML と JavaScript を使っています。dapp はシンプルなカウンタで、フロントエンドのボタンをクリックするとカウンタがインクリメントされます。

この例dapp の目的は、新しいプロジェクトを作成するときにdfxによってインストールされるデフォルトのdapp テンプレートに基づいて、最小限のdapp を構築することです。dapp は、カウンタを持つシンプルなウェブサイトです。ボタンが押されるたびに、カウンタがインクリメントされます。

この例では次のことを説明します：

- IC SDK（dfx）を使用して新しいcanister スマートコントラクトを作成します。
- 新しいプロジェクトの開始点として、デフォルトのプロジェクトをテンプレートとして使用します。
- カウンターのバックエンド関数を追加します（カウント、カウントの取得、カウントのリセット）。
- フロントエンドにバックエンド関数を実装します。
- canister スマートコントラクトをローカルにデプロイします。
- dfx を使用して Candid UI とコマンドラインでバックエンドをテストし、ブラウザでフロントエンドをテストします。

## 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールします。
- \[x\][Node.jsを](https://nodejs.org/en/download/)インストールします。
- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールします。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードします: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ1：プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/minimal-counter-dapp
    npm install
    dfx start --background

### ステップ 2:canister をビルドしてデプロイします：

    dfx deploy

出力は以下のようになります：

    Deployed canisters.
    URLs:
      Frontend canister via browser
        minimal_dapp_assets: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
      Backend canister via Candid interface:
        minimal_dapp: http://127.0.0.1:4943/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=be2us-64aaa-aaaaa-qaabq-cai

### ステップ 3: ウェブ・ブラウザで`minimal_dapp_assets` の URL を開きます。

**クリック・ミー！**」というボタンの後にカウンタ番号が付いたGUIインターフェースが表示されます。ボタンがクリックされるたびに、カウンターの値が1ずつ増えていきます。

## アーキテクチャ

サンプルdapp の3つの主要な部分は、バックエンド、キャンディッドインターフェイス、フロントエンドです。このサンプルプロジェクトは、`dfx new project_name` コマンドを実行すると作成されるデフォルトプロジェクトをベースにしていますが、このプロジェクトでカウンター機能を作成するために、デフォルトプロジェクトのコードのほとんどが置き換えられています。

### Motoko バックエンド

バックエンド関数は`src/minimal_dapp/main.mo` Motoko ファイルにあります。バックエンドはカウンタ値を保存し、カウンタ値を取得、インクリメント、リセットする関数を持っています。

#### カウンタ変数

カウンターを動作させるために3つの関数が作成されています：`count()` `getCount()` と`reset()` です。現在のカウンタ値は、actor に数値として格納されます。

``` javascript
actor {
    var counter : Nat = 0;
}
```

#### count()

`count()` 関数はカウンタ変数をインクリメントします。この関数は、ユーザがフロントエンドのボタンをクリックしたとき、または関数が Candid インターフェースから呼び出されたときに呼び出されます。

``` javascript
public func count() : async Nat {
    counter += 1;
    return counter;
};
```

この関数は、インクリメントされたカウンタ変数を返します。

#### getCount()

`getCount()` 関数は、現在のカウンタ値を返します。

``` javascript
public query func getCount() : async Nat {
    return counter;
};
```

#### reset()

`reset()` 関数は、カウンタ値を 0 にリセットし、その値を返します。

``` javascript
public func reset() : async Nat {
    counter := 0;
    return counter;
};
```

### Candid インターフェース

Candid インターフェースは自動的に作成され、バックエンドをテストする簡単でユーザーフレンドリーな方法を提供する便利な UI を持っています。Candid UIへのアクセス方法については、以下の「**テスト**」セクションをご覧ください。

### フロントエンド

`dfx new project_name` と共にインストールされたデフォルトのプロジェクトには、ページのHTMLを含む`index.html` ファイルとバックエンド関数の実装を含む`index.js` ファイルがあります。このサンプルプロジェクトでは、カウンター機能とバックエンド機能をサポートするためにこれら2つのファイルを変更します。

#### HTML

すべてのHTMLコードは`src/minimal_dapp_assets/src/index.html` ファイルにあり、ほとんどのHTMLはデフォルトのプロジェクトから引き継がれます。ボタンはそのままで、結果を表示する部分も簡略化されています。

``` html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width">
        <title>Minimal Dapp</title>
        <base href="/">

        <link type="text/css" rel="stylesheet" href="main.css" />
    </head>
    <body>
        <img src="logo.png" alt="DFINITY logo" />
        <section>
            <button id="clickMeBtn">Click Me!</button>
        </section>
        <section id="counter"></section>
    </body>
</html>
```

#### ジャバスクリプト

つの`eventlisteners` がJavaScriptファイル、`src/minimal_dapp_assets/src/index.js` 、デフォルトプロジェクトの既存のJavaScriptファイルに追加されています。`eventlistener` 1つはボタンのクリックを検出するためのもので、バックエンドの`count()` 関数を呼び出します。また、`getCount()` でカウンターの初期値を取得するために、ページロード用の`eventlistener` が追加されています。バックエンドの関数はCandidインターフェイスを通してインポートされます。

``` javascript
import { minimal_dapp } from "../../declarations/minimal_dapp";

document.addEventListener("DOMContentLoaded", async () => {
  const counter = await minimal_dapp.getCount();
  document.getElementById("counter").innerText = "Counter: " + counter;
});

document.getElementById("clickMeBtn").addEventListener("click", async () => {
  const counter = await minimal_dapp.count();
  document.getElementById("counter").innerText = "Counter: " + counter;
});
```

#### dfx

dfxはcanister 操作のためのコマンドのサブセットを持っており、そのうちの1つは、前のステップで`main.mo` ファイルに追加されたパブリック関数を呼び出すことを可能にします。以下の例では、初期値は 0 です。`count` は値をインクリメントして 1 を返し、`getCount` は現在の値を返し、`reset` は値を 0 に設定します。

コマンドの使用法：`dfx canister call <project>  <function>`

``` bash
$ dfx canister call minimal_dapp count
(1 : Nat)
```

``` bash
$ dfx canister call minimal_dapp getCount
(1 : Nat)
```

``` bash
$ dfx canister call minimal_dapp reset
(0 : Nat)
```

#### Candid UI

Candid インターフェースは自動的に作成され、便利な UI を備えており、バックエンドをテストするための簡単でユーザーフレンドリーな方法を提供します。UI も自動的に生成され、canister ID は`dfx canister id <canister_name>` コマンドから取得できます。

``` bash
$ dfx canister id __Candid_UI
r7inp-6aaaa-aaaaa-aaabq-cai
$ dfx canister id minimal_dapp
rrkah-fqaaa-aaaaa-aaaaq-cai
```

**http://\<candid\_canister\_id\>.localhost:4943/?id=\<backend\_canister\_id\> を参照してください。**

<!---
# Minimal counter dapp

## Overview

The example dapp shows how to build a very basic dapp with both backend and frontend, using Motoko for the backend functionality and plain HTML and JavaScript for the frontend. The dapp is a simple counter, which will increment a counter by clicking a button in the frontend.

The purpose of this example dapp is to build a minimalistic dapp, based on the default dapp template, installed by dfx when creating a new project. The dapp is a simple website with a counter. Every time a button is pressed, a counter is incremented.

This example covers:

- Create new canister smart contract using the IC SDK (dfx).
- Use the default project as a template as the starting point for the new project.
- Add backend functions for a counter (count, get count and reset count).
- Implement backend functions in the frontend.
- Deploy the canister smart contract locally.
- Test backend with Candid UI and command line using dfx, and test frontend in browser.

## Prerequisites

This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Install [Node.js](https://nodejs.org/en/download/).
- [x] Download and install [git.](https://git-scm.com/downloads)
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/minimal-counter-dapp
npm install
dfx start --background
```

### Step 2: Build and deploy the canister:

```
dfx deploy
```

The output will resemble the following:

```
Deployed canisters.
URLs:
  Frontend canister via browser
    minimal_dapp_assets: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
  Backend canister via Candid interface:
    minimal_dapp: http://127.0.0.1:4943/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=be2us-64aaa-aaaaa-qaabq-cai
```

### Step 3: Open the `minimal_dapp_assets` URL in a web browser.

You will see a GUI interface with a button that says **Click Me!** followed by a counter number. Each time the button is clicked, the counter value will increase by 1. 

## Architecture
The three main parts of the example dapp are the backend, the Candid interface and the frontend. This example project is based on the default project, which is created when running the `dfx new project_name` command, but most of the default project code is replaced to create the counter functionality in this project.

### Motoko backend
The backend functions are located in the `src/minimal_dapp/main.mo` Motoko file. The backend stores the counter value, and has functions to get, increment and reset the counter value.


#### Counter variable
Three functions are created to make the counter work: `count()`, `getCount()` and `reset()`. The current counter value is stored as a number in the actor.


```javascript
actor {
    var counter : Nat = 0;
}
```

#### count()
The `count()` function increments the counter variable. This function is invoked when the user clicks the button on the frontend, or when the function is called through the Candid interface.

```javascript
public func count() : async Nat {
    counter += 1;
    return counter;
};
```

The function returns the incremented counter variable.

#### getCount()
The `getCount()` function returns the current counter value.

```javascript
public query func getCount() : async Nat {
    return counter;
};
```

#### reset()
The `reset()` function resets the counter value to 0 and returns the value.

```javascript
public func reset() : async Nat {
    counter := 0;
    return counter;
};
```

### Candid interface
The Candid interface is automatically created, and it has a convenient UI, which provides an easy, user-friendly way to test the backend. Learn how to access the Candid UI in the **Testing** section below. 

### Frontend
The default project installed with `dfx new project_name` has an `index.html` file with page HTML and an `index.js` file with an implementation of the backend functions. These two files are modified in this example project to support the counter functionality, and the backend functions.

#### HTML
All HTML code is in the `src/minimal_dapp_assets/src/index.html` file, and most of the HTML is carried over from the default project. The button is kept and so is the section showing the result, just simplified.

```html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width">
        <title>Minimal Dapp</title>
        <base href="/">

        <link type="text/css" rel="stylesheet" href="main.css" />
    </head>
    <body>
        <img src="logo.png" alt="DFINITY logo" />
        <section>
            <button id="clickMeBtn">Click Me!</button>
        </section>
        <section id="counter"></section>
    </body>
</html>
```

#### Javascript
Two `eventlisteners` are added to the JavaScript file, `src/minimal_dapp_assets/src/index.js`, the existing JavaScript file from the default project. One `eventlistener` is for detecting button clicks, and it's calling the `count()` function in the backend, and an `eventlistener` for page load is added to get the initial value of the counter with `getCount()`. The backend functions are imported through the Candid interface.

```javascript
import { minimal_dapp } from "../../declarations/minimal_dapp";

document.addEventListener("DOMContentLoaded", async () => {
  const counter = await minimal_dapp.getCount();
  document.getElementById("counter").innerText = "Counter: " + counter;
});

document.getElementById("clickMeBtn").addEventListener("click", async () => {
  const counter = await minimal_dapp.count();
  document.getElementById("counter").innerText = "Counter: " + counter;
});
```

#### dfx
dfx has a subset of commands for canister operations, and one of them enables calling the public functions added to the `main.mo` file in the previous step. In the following examples the initial value is 0. `count` will increment value and return 1, `getCount` will return the current value and `reset` will set the value to 0.

Command usage: `dfx canister call <project>  <function>`

```bash
$ dfx canister call minimal_dapp count
(1 : Nat)
```

```bash
$ dfx canister call minimal_dapp getCount
(1 : Nat)
```

```bash
$ dfx canister call minimal_dapp reset
(0 : Nat)
```

#### Candid UI
The Candid interface is automatically created, and it has a convenient UI, which provides an easy, user-friendly way to test the backend. The UI is also automatically generated, and the canister ID can be retrieved from the `dfx canister id <canister_name>` command.

```bash
$ dfx canister id __Candid_UI
r7inp-6aaaa-aaaaa-aaabq-cai
$ dfx canister id minimal_dapp
rrkah-fqaaa-aaaaa-aaaaq-cai
```

**http://<candid_canister_id>.localhost:4943/?id=<backend_canister_id>**


-->
