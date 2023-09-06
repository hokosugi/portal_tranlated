# インターネット・アイデンティティの統合

## 概要

このガイドでは、プロジェクトを Internet Identity に統合してテストする方法を説明します。Internet Identity の開発[ビルドフレーバーと](https://github.com/dfinity/internet-identity/blob/main/README.md#build-features-and-flavors) [agent-js](https://github.com/dfinity/agent-js)ライブラリを使用します。

これはスタンドアロンプロジェクトであり、自分のプロジェクトにコピーすることができます。

- [`dfx`](https://github.com/dfinity/sdk/releases/latest)バージョン0.10.0以降
- Rustup with target`wasm32-unknown-unknown` ([rustup instructionsを](https://rust-lang.github.io/rustup/cross-compilation.html)参照)。
- CMake
- [`ic-wasm`](https://github.com/dfinity/ic-wasm).
- Node.js v16+

## 使用方法

以下のコマンドは、レプリカを起動し、開発用 Internet Identitycanister をインストールし、テストスイートを実行します：

``` bash
git clone https://github.com/dfinity/internet-identity.git
cd internet-identity/demos/using-dev-build
dfx start --background --clean
npm ci
dfx deploy --no-wallet
```

この時点で、レプリカ（現実的な問題としては、Internet Computer のローカルバージョン）が実行され、canisters の 3 つがデプロイされています：

- `internet_identity`Internet Identityの開発バージョン([最新リリースから](https://github.com/dfinity/internet-identity/releases/latest)ダウンロード。 [`dfx.json`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/dfx.json)  .

- `webapp`ID (アンカー) の作成と認証のために`internet_identity` canister を呼び出し、`whoami` canister (後述) を呼び出して ID が有効であることを示す小さな Web アプリ。このウェブアプリケーションのソースは [`index.html`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.html)と [`index.ts`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.ts).

- `whoami`呼び出しが認証されていることをチェックし、「呼び出し元のプリンシパル」を返すシンプルなcanister 。実装はシンプルです：
  
  ``` motoko
  actor {
      public query ({caller}) func whoami() : async Principal {
          return caller;
      };
  };
  ```

IC上では、プリンシパルはリクエストや "コール "を実行する人の識別子です（したがって "caller"）。すべての呼には有効なプリンシパルが必要です。匿名呼び出し用の特別なプリンシパルもあります。Internet Identityを使用する場合、[自己認証プリンシパルを](/references/ic-interface-spec.md#principals)使用します。これは、（TouchIDや Windows Helloなどの背後に隠された）プライベートキーをラップトップに持ち、ブラウザが署名して、 ICに呼び出しを発行する人物であることを証明するために使用するという、非常に洒落た言 い方です。

ICが実際に`whoami` canister にコール（リクエスト）を通すと、すべてがチェックアウトされたことになり、`whoami` canister は、ICがリクエストに追加する情報、つまりあなたのアイデンティティ（プリンシパル）で応答するだけです。

## auth-client ライブラリを使用して Internet Identity

DFINITY は、Internet Identity でログインするための[使いやすいライブラリ (agent-js)](https://github.com/dfinity/agent-js)を提供します。

以下は、ログインし、取得した ID をcanister の呼び出しに使用するために必要な手順です：

``` js
// First we have to create and AuthClient.
const authClient = await AuthClient.create();

// Call authClient.login(...) to login with Internet Identity. This will open a new tab
// with the login prompt. The code has to wait for the login process to complete.
// We can either use the callback functions directly or wrap in a promise.
await new Promise((resolve, reject) => {
  authClient.login({
    onSuccess: resolve,
    onError: reject,
  });
});
```

ユーザーが Internet Identity で認証されると、ID にアクセスできるようになります：

``` js
// Get the identity from the auth client:
const identity = authClient.getIdentity();
// Using the identity obtained from the auth client, we can create an agent to interact with the IC.
const agent = new HttpAgent({ identity });
// Using the interface description of our webapp, we create an Actor that we use to call the service methods.
const webapp = Actor.createActor(webapp_idl, {
  agent,
  canisterId: webapp_id,
});
// Call whoami which returns the principal (user id) of the current user.
const principal = await webapp.whoami();
```

ユーザが Internet Identity で認証されると、ID にアクセスできるようになります。 [`index.ts`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.ts)
裏側で発生する詳細な説明は、[クライアント認証プロトコル仕様を](../../../references/ii-spec.md#client-authentication-protocol)参照。

## canister IDの取得

canisters を使ってみましょう。詳細はどうでもいい？[ヘルパーまで](#helpers)読み飛ばしてください。

canisters (たとえばブラウザでウェブアプリを表示する) と話すには、それぞれのcanister の ID を調べて、`https://localhost:4943/?canisterId=<canister ID>` (`4943` は`dfx` がレプリカへのプロキシ呼び出しに使用するポートです。このポートは通常`dfx.json` で指定されます) という形式の URL を使用する必要があります。`dfx command` の出力でcanister ID を見つけるか、`dfx` の "internal"(文書化されていない)ステート をチェックすることで見つけることができます：

    ~/internet-identity/demos/using-dev-build$ cat .dfx/local/canister_ids.json
    {
      "__Candid_UI": {
        "local": "r7inp-6aaaa-aaaaa-aaabq-cai"
      },
      "internet_identity": {
        "local": "rwlgt-iiaaa-aaaaa-aaaaa-cai"
      },
      "webapp": {
        "local": "rrkah-fqaaa-aaaaa-aaaaq-cai"
      },
      "whoami": {
        "local": "ryjl3-tyaaa-aaaaa-aaaba-cai"
    }

異なるcanister IDが得られるかもしれません（それはまったく問題ありません）。`webapp` canister IDが`rrkah-fqaaa-aaaaa-aaaaq-cai` の場合、ブラウザを [`http://localhost:4943/?canisterId=rrkah-fqaaa-aaaaa-aaaaq-cai`](http://localhost:4943/?canisterId=rrkah-fqaaa-aaaaa-aaaaq-cai)にブラウザーをポイントして、ウェブアプリを見ることができるはずです。万歳！

![](../_attachments/webapp.png)

:::info
実際にウェブアプリを使う場合は、"Internet Identity URL" フィールドが`http://localhost:4943/?canisterId=<canister ID of the internet_identity canister>` を指していることを確認してください。
::：

## ヘルパー

canister IDを把握したり、`canisterId=...` クエリパラメータを使ったりするのは少し面倒です。お好きなコマンドをいくつか紹介します：

- `npm run start`:アプリをビルドし、`localhost:8080` 、コード変更時にホットリロードして提供します。
- `npm run proxy`:Internet Identityを`localhost:8086` 、ウェブアプリを`localhost:8087` 。
- `npm run test`:プロキシを起動し、`internet_identity` canister に対してブラウザテストを実行します。

詳細は [`dfx.json`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/dfx.json)ファイルと[Internet Identityに関するgenesis talkを](https://youtu.be/oxEr8UzGeBo)確認してください。

<!---
# Internet Identity integration

## Overview
This guide shows how to integrate and test a project with Internet Identity. This uses the development [build flavor](https://github.com/dfinity/internet-identity/blob/main/README.md#build-features-and-flavors) of Internet Identity and the [agent-js](https://github.com/dfinity/agent-js) library.

This is a standalone project that you can copy to your own project.


* [`dfx`](https://github.com/dfinity/sdk/releases/latest) version 0.10.0 or later
* Rustup with target `wasm32-unknown-unknown` (see [rustup instructions](https://rust-lang.github.io/rustup/cross-compilation.html)).
* CMake
* [`ic-wasm`](https://github.com/dfinity/ic-wasm).
* Node.js v16+

## Usage

The following commands will start a replica, install the development Internet Identity canister, and run the test suite:

```bash
git clone https://github.com/dfinity/internet-identity.git
cd internet-identity/demos/using-dev-build
dfx start --background --clean
npm ci
dfx deploy --no-wallet
```

At this point, the replica (for all practical matters, a local version of the Internet Computer) is running and three canisters have been deployed:

- `internet_identity`: the development version of Internet Identity (downloaded from the [latest release](https://github.com/dfinity/internet-identity/releases/latest), see [`dfx.json`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/dfx.json)  .
- `webapp`: a tiny webapp that calls out to the `internet_identity` canister for identity (anchor) creation and authentication, and that then calls the `whoami` canister (see below) to show that the identity is valid. You'll find the source of the webapp in [`index.html`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.html) and [`index.ts`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.ts).
- `whoami`: a simple canister that checks that calls are authenticated, and that returns the "principal of the caller". The implementation is simple:

  ```motoko
  actor {
      public query ({caller}) func whoami() : async Principal {
          return caller;
      };
  };
  ```
  
On the IC, a principal is the identifier of someone performing a request or "call" (hence "caller"). Every call must have a valid principal. There is also a special principal for anonymous calls. When using Internet Identity you are using [self-authenticating principals](/references/ic-interface-spec.md#principals), which is a very fancy way of saying that you have a private key on your laptop (hidden behind TouchID, Windows Hello, etc) that your browser uses to sign and prove that you are indeed the person issuing the calls to the IC.

If the IC actually lets the call (request) through to the `whoami` canister, it means that everything checked out, and the `whoami` canister just responds with the information the IC adds to requests, namely your identity (principal).

## Using the auth-client library to log in with Internet Identity

DFINITY provides an [easy-to-use library (agent-js)](https://github.com/dfinity/agent-js) to log in with Internet Identity. 

These are the steps required to log in and use the obtained identity for canister calls:
```js
// First we have to create and AuthClient.
const authClient = await AuthClient.create();

// Call authClient.login(...) to login with Internet Identity. This will open a new tab
// with the login prompt. The code has to wait for the login process to complete.
// We can either use the callback functions directly or wrap in a promise.
await new Promise((resolve, reject) => {
  authClient.login({
    onSuccess: resolve,
    onError: reject,
  });
});
```
Once the user has been authenticated with Internet Identity we have access to the identity:
```js
// Get the identity from the auth client:
const identity = authClient.getIdentity();
// Using the identity obtained from the auth client, we can create an agent to interact with the IC.
const agent = new HttpAgent({ identity });
// Using the interface description of our webapp, we create an Actor that we use to call the service methods.
const webapp = Actor.createActor(webapp_idl, {
  agent,
  canisterId: webapp_id,
});
// Call whoami which returns the principal (user id) of the current user.
const principal = await webapp.whoami();
```
See [`index.ts`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/webapp/index.ts) for the full working example.
A detailed description of what happens behind the scenes is available in the [client auth protocol specification](../../../references/ii-spec.md#client-authentication-protocol).

## Getting the canister IDs

Let's now use those canisters. Don't care about details? Skip to the [helpers](#helpers).

In order to talk to those canisters (for instance to view the webapp in your browser) you need to figure the ID of each canister and then use an URL of the form `https://localhost:4943/?canisterId=<canister ID>` (where `4943` is the port used by `dfx` to proxy calls to the replica; that port is usually specified in the `dfx.json`). You can find the canister IDs in the output of the `dfx command`, or by checking `dfx`'s "internal" (read: non-documented) state:

```
~/internet-identity/demos/using-dev-build$ cat .dfx/local/canister_ids.json
{
  "__Candid_UI": {
    "local": "r7inp-6aaaa-aaaaa-aaabq-cai"
  },
  "internet_identity": {
    "local": "rwlgt-iiaaa-aaaaa-aaaaa-cai"
  },
  "webapp": {
    "local": "rrkah-fqaaa-aaaaa-aaaaq-cai"
  },
  "whoami": {
    "local": "ryjl3-tyaaa-aaaaa-aaaba-cai"
}
```

You might get different canister IDs (and that's totally fine). If the `webapp` canister ID is `rrkah-fqaaa-aaaaa-aaaaq-cai`, you should be able to point your browser to [`http://localhost:4943/?canisterId=rrkah-fqaaa-aaaaa-aaaaq-cai`](http://localhost:4943/?canisterId=rrkah-fqaaa-aaaaa-aaaaq-cai) to see the webapp. Hurray!

![](../_attachments/webapp.png)

:::info
If you actually use the webapp, make sure that the "Internet Identity URL" field points to `http://localhost:4943/?canisterId=<canister ID of the internet_identity canister>`.
:::

## Helpers

Figuring the canister IDs, and using the `canisterId=...` query parameter is all a bit cumbersome. Here are some commands you might like:

- `npm run start`: Build the app and serve it on `localhost:8080` with hot reload on code changes, ideal for hacking on the webapp.
- `npm run proxy`: Start a proxy that serves Internet Identity on `localhost:8086` and the webapp on `localhost:8087` for easy access.
- `npm run test`: Start the proxy and run browser tests against the `internet_identity` canister.

For more information, check the [`dfx.json`](https://github.com/dfinity/internet-identity/blob/main/demos/using-dev-build/dfx.json) file and the [genesis talk on Internet Identity](https://youtu.be/oxEr8UzGeBo).

-->
