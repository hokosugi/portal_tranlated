---

sidebar_position: 1
sidebar_label: From a JavaScript agent
---
# JavaScriptエージェントからのICの呼び出し

## 概要

このガイドでは、Web ブラウザの JavaScript から IC に接続する方法について説明します。Node.jsからICを呼び出す方法については、[こちらのガイドを](nodejs.md)参照してください。

エージェントが何をするのかの説明をお探しの場合は、[エージェントの概要を](../index.md)参照してください。

スマートコントラクトは、キャンディッドインターフェース宣言言語（IDL）を使用して独自のAPIを定義することができ、パブリックAPIを介して呼び出しに応答します。

ICは、`queries` と`updates` の2種類の呼び出しをサポートしています。クエリーは高速で、ステートを変更することはできません。更新はコンセンサスを経て行われ、完了までに約2～4秒かかります。

更新には待ち時間がかかるため、アプリケーションのパフォーマンスをモデリングするためのベストプラクティスは、更新を非同期かつ早期に行うことです。もし時間より前に更新を行い、canister のメモリにすでに「キャッシュ」しておくことができれば、ユーザはそのデータを要求する際に、より良い経験をすることができます。同様に、アプリケーションが更新を行う必要がある場合は、更新中にインタラクションがブロックされるのを避けるのが最善です。現実的な範囲では**楽観的レンダリングを**使用し、呼び出しがすでに成功したかのようにアプリケーションを続行します。

## 前提条件

Internet Computer で JavaScript を使い始めるには、開発環境に以下が含まれていることを推奨します：

- \[canister の作成と管理のための IC SDK。
- \[x\] Node JS (12、14、または 16)。
- \[x\] 実験したいcanister.
  - 提案
    - `dfx new` スタータープロジェクト。
    - [dfinity](https://github.com/dfinity/examples)/examplesからの例。

このガイドでは、コマンドによって作成されたプロジェクトを使用します：

    dfx new hello

## 簡単な呼び出し

アプリケーションからICに話しかけるには、canister インタフェースから始めます。まずはとても簡単なものから始めましょう。

ほとんどの場合、`dfx.json` でcanister を定義し、`dfx generate` コマンドで宣言を自動生成するようにプロジェクトを構成する方が簡単です。

Hello, world！」の例では、`dfx.json` ファイルはこのようになります：

    {
      "canisters": {
        "hello_backend": {
          "main": "src/hello_backend/main.mo",
          "type": "motoko"
        },
        "hello_frontend": {
          "dependencies": [
            "hello_backend"
          ],
          "frontend": {
            "entrypoint": "src/hello_frontend/src/index.html"
          },
          "source": [
            "src/hello_frontend/assets",
            "dist/hello_frontend/"
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
      "output_env_file": ".env",
      "version": 1
    }

次に、インターフェイス・ファイルを生成するために、コマンドを実行します：

    dfx generate

このコマンドを実行することで、dfxは自動的にプロジェクト内の`src/declarations` ディレクトリに以下を書き込みます。

    |── src
    │   ├── declarations
    │   │   ├── hello_backend
    │   │   │   ├── hello_backend.did
    |   |   |   ├── hello_backend.did.d.js
    │   │   │   ├── hello_backend.did.d.ts
    │   │   │   ├── hello_backend.did.js
    │   │   │   ├── index.d.ts
    │   │   │   └── index.js

そして、`hello_backend` canister の Candid インターフェース・ファイルの内容を見ることができます：

    # src/declarations/hello_backend/hello_backend.did
    service : {
      greet: (text) -> (text);
    }

これは Candid インターフェースです。これは新しい特別な型を定義せず、単一のメソッド`greet` を持つ`service` インターフェイスを定義します。Greet は、`text` 型の引数を 1 つ受け取り、`text` で応答します。`query` とラベル付けされていない限り、すべてのコールはデフォルトでアップデートとして扱われます。

JSでは、`text` は`string` の型にマッピングされます。Candidの型とJSでの等価物の完全なリストは[Candidの型](../../references/candid-ref.md)リファレンスで見ることができます。

それぞれの`src/declarations` ファイルをもう少し調べてみましょう。

#### hello\_backend.did

`hello_backend.did` は、上で見たようにインターフェースを定義します。

#### hello.did.d.ts

このファイルはこのようになります：

``` ts
import type { Principal } from '@dfinity/principal';
import type { ActorMethod } from '@dfinity/agent';
export interface _SERVICE { 'greet' : ActorMethod<[string], string> };
```

`_SERVICE` エクスポートには`greet` メソッドがあり、引数の配列と戻り値の型があります。これは、[Actorメソッドとして](https://agent-js.icp.xyz/agent/interfaces/ActorMethod.html)型付けされ、引数を受け取り、宣言で指定された型で解決されるプロミスを返すハンドラになります。

#### hello.did.js

次に、`hello.did.js` を見てみましょう。

``` js
export const idlFactory = ({ IDL }) => {
  return IDL.Service({ 'greet' : IDL.Func([IDL.Text], [IDL.Text], []) });
};

```

`did.d.ts` の宣言とは異なり、この`idlFactory` は実行時に利用できる必要があります。`idlFactory` は [actor](https://agent-js.icp.host/agent/interfaces/Actor.html)インターフェイスによってロードされます。インターフェイスは、IC APIと提供されたcandid仕様に従ってネットワーク・コールを構造化する処理を行います。

この factory は、再び`greet` メソッドを持つサービスを表し、引数は前と同じです。ただし、`IDL.Func` には 3 番目の引数があり、ここでは空の配列になっています。これは関数に付加されるアノテーションを表し、最も一般的なものは`"query"` です。

#### index.js

`index.js` ファイルでは、スマート・コントラクトのインターフェイスをカスタマイズしたactor をセットアップするために、先に説明した各部分をまとめています。これは、`process.env` 変数を使用して、使用しているデプロイコンテキストに基づいてcanister の ID を決定するなど、いくつかのことを行いますが、最も重要な点は`createActor` エクスポートにあります。

``` js
 export const createActor = (canisterId, options) => {
  const agent = new HttpAgent({ ...options?.agentOptions });
  
  // Fetch root key for certificate validation during development
  if(process.env.NODE_ENV !== "production") {
    agent.fetchRootKey().catch(err=>{
      console.warn("Unable to fetch root key. Check to ensure that your local replica is running");
      console.error(err);
    });
  }
  // Creates an actor with using the candid interface and the HttpAgent
  return Actor.createActor(idlFactory, {
    agent,
    canisterId,
    ...options?.actorOptions,
  });
}
```

このコンストラクタは、まず`HTTPAgent` を作成します。これは JS`fetch` API をラップし、public API を介した呼び出しをエンコードするために使用します。また、オプションでレプリカのルートキーを取得します。canister 最後に、自動的に生成されたインターフェイスを使用してactor を作成し、`canisterId` と`HTTPAgent` を渡します。

この`actor` インスタンスは、すべてのサービスメソッドをメソッドとして呼び出すように設定されています。`dfx new` テンプレートにデフォルトで用意されているように、これがすべてセットアップされると、canister API に変更を加えるたびに`dfx generate` を実行するだけで、フロントエンドのコードで完全なインターフェイスが自動的に同期されます。

このインターフェースは型付けが簡単なので、このアプリケーションのために、JavaScriptインターフェースとTypeScript宣言を自動的に生成することができます。これには2つの方法があります。`didc` ツールを使って手動でインターフェースを生成することができます。`dfinity/candid` リポジトリの[releases](https://github.com/dfinity/candid/releases)タブからダウンロードしてください。

## ブラウザ

ブラウザのコンテキストは最も簡単です。`fetch` APIが利用でき、ほとんどのアプリはURLによって、`https://icp0.io` と話す必要があるのか、ローカルのレプリカと話す必要があるのかを簡単に判断できます。

ブラウザで動作するアプリを構築する場合、考慮すべき点がいくつかあります：

### パフォーマンス

ICの更新は、ユーザーにとって2～4秒程度と遅く感じるかもしれません。アプリケーションを構築する際には、この待ち時間を考慮し、いくつかのベストプラクティスに従うことを検討してください：

- アップデートの結果を待つ間、UIインタラクションをブロックすることは避けましょう。代わりに、ユーザーが他の更新やインタラクションを継続できるようにし、成功したことを非同期でユーザーに知らせます。
- canister 間での呼び出しはなるべく避けましょう。バックエンドが他のcanisters と話をする必要がある場合、その時間はすぐに増えてしまいます。
- `Promise.all` 、1つ1つ呼び出すのではなく、まとめて複数の呼び出しを行いましょう。
- アセットやデータをフェッチする必要がある場合は、`raw.icp0.io` のエンドポイントcanisters に直接`fetch` コールをかけることができます。

## バンドル

便利でトラブルシューティングを少なくするために、バンドルラーを使用してコードを組み立てることをお勧めします。私たちは標準のWebpack configを提供しますが、Rollup、Vite、Parcel、または他のものを利用することもできます。このパターンでは、canister ID 用に`.env.development` と`.env.production` 環境変数ファイルを生成するスクリプトを実行することをお勧めします。これはバンドルラーのかなり標準的なアプローチで、[dotenv を](https://www.npmjs.com/package/dotenv)使用して簡単にサポートできます。

以下は、これらのファイルをマップするために実行できるスクリプトの例です：

``` js
// setupEnv.js
const fs = require("fs");
const path = require("path");

function initCanisterEnv() {
  let localCanisters, prodCanisters;
  try {
    localCanisters = require(path.resolve(
      ".dfx",
      "local",
      "canister_ids.json"
    ));
  } catch (error) {
    console.log("No local canister_ids.json found");
  }
  try {
    prodCanisters = require(path.resolve("canister_ids.json"));
  } catch (error) {
    console.log("No production canister_ids.json found");
  }

  const network =
    process.env.DFX_NETWORK ||
    (process.env.NODE_ENV === "production" ? "ic" : "local");

  const canisterConfig = network === "local" ? localCanisters : prodCanisters;

  const localMap = localCanisters
    ? Object.entries(localCanisters).reduce((prev, current) => {
        const [canisterName, canisterDetails] = current;
        prev[canisterName.toUpperCase() + "_CANISTER_ID"] =
          canisterDetails[network];
        return prev;
      }, {})
    : undefined;
  const prodMap = prodCanisters
    ? Object.entries(prodCanisters).reduce((prev, current) => {
        const [canisterName, canisterDetails] = current;
        prev[canisterName.toUpperCase() + "_CANISTER_ID"] =
          canisterDetails[network];
        return prev;
      }, {})
    : undefined;
  return [localMap, prodMap];
}
const [localCanisters, prodCanisters] = initCanisterEnv();

if (localCanisters) {
  const localTemplate = Object.entries(localCanisters).reduce((start, next) => {
    const [key, val] = next;
    if (!start) return `${key}=${val}`;
    return `${start ?? ""}
          ${key}=${val}`;
  }, ``);
  fs.writeFileSync(".env.development", localTemplate);
}
if (prodCanisters) {
  const prodTemplate = Object.entries(prodCanisters).reduce((start, next) => {
    const [key, val] = next;
    if (!start) return `${key}=${val}`;
    return `${start ?? ""}
        ${key}=${val}`;
  }, ``);
  fs.writeFileSync(".env", localTemplate);
}
```

次に、`dfx generate; node setupEnv.js` の`"prestart"` と`"prebuild"` コマンドを追加します。環境変数を扱う方法については、お好みのバンドルラのドキュメントに従ってください。

<!---

# Calling the IC from a JavaScript agent

## Overview

This guide covers connecting to the IC from JavaScript in a web browser. For more information about calling IC from Node.js, please, refer to [this guide](nodejs.md).

If you are looking for an explanation of what an agent does, see the [agent overview](../index.md).

Smart contracts are able to define their own API's using the Candid Interface Declaration Language (IDL), and they will respond to calls through the public API.

The IC supports two types of calls - `queries` and `updates`. Queries are fast and cannot change state. Updates go through consensus, and will take around 2-4 seconds to complete. 

As a result of the latency for updates, best practices around modeling your application's performance will have you make updates asynchronously and early. If you can make an update ahead of time and have it already "cached" in your canister's memory, your users will have a better experience requesting that data. Similarly, if your application needs to make an update, it is best to avoid blocking interaction while your update is taking place. Use **optimistic rendering** wherever practical, and proceed with your application as if the call has already succeeded.

## Prerequisites
To get started with JavaScript on the Internet Computer, we recommend your development environment includes:

- [x] IC SDK for canister creation and management.
- [x] Node JS (12, 14, or 16).
- [x] A canister you want to experiment with.
  - Suggestions: 
    - `dfx new` starter project.
    - An example from [dfinity/examples](https://github.com/dfinity/examples).

For this guide, we'll use the project created by the command:

```
dfx new hello
```

## A simple call

Talking to the IC from your application starts with the canister interface. Let's take a very simple one to begin with.

In most cases, it is easier to configure your project to have a canister defined in `dfx.json`, and to generate your declarations automatically using the `dfx generate` command. 

For our 'Hello, world!' example, the `dfx.json` file looks like this:

```
{
  "canisters": {
    "hello_backend": {
      "main": "src/hello_backend/main.mo",
      "type": "motoko"
    },
    "hello_frontend": {
      "dependencies": [
        "hello_backend"
      ],
      "frontend": {
        "entrypoint": "src/hello_frontend/src/index.html"
      },
      "source": [
        "src/hello_frontend/assets",
        "dist/hello_frontend/"
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
  "output_env_file": ".env",
  "version": 1
}
```


Then, to generate the interface files, run the command:

```
dfx generate
```

By running this command, dfx will automatically write the following to your `src/declarations` directory inside your project.

```
|── src
│   ├── declarations
│   │   ├── hello_backend
│   │   │   ├── hello_backend.did
|   |   |   ├── hello_backend.did.d.js
│   │   │   ├── hello_backend.did.d.ts
│   │   │   ├── hello_backend.did.js
│   │   │   ├── index.d.ts
│   │   │   └── index.js
```

Then, you can view the contents of the Candid interface file for the `hello_backend` canister:

```
# src/declarations/hello_backend/hello_backend.did
service : {
  greet: (text) -> (text);
}
```

This is a Candid interface. It defines no new special types and defines a `service` interface with a single method, `greet`. Greet accepts a single argument, of type `text`, and responds with `text`. Unless labeled as a `query`, all calls are treated as updates by default.

In JS, `text` maps to a type of `string`. You can see a full list of Candid types and their JS equivalents at the [Candid types](../../references/candid-ref.md) reference.

Let's explore each of these `src/declarations` files a bit more. 

#### hello_backend.did

`hello_backend.did` defines your interface, as we saw above.

#### hello.did.d.ts

This file will look something like this:

```ts
import type { Principal } from '@dfinity/principal';
import type { ActorMethod } from '@dfinity/agent';
export interface _SERVICE { 'greet' : ActorMethod<[string], string> };
```

The `_SERVICE` export includes a `greet` method, with typings for an array of arguments and a return type. This will be typed as an [ActorMethod](https://agent-js.icp.xyz/agent/interfaces/ActorMethod.html), which will be a handler that takes arguments and returns a promise that resolves with the type specified in the declarations.

#### hello.did.js

Next, let's look at `hello.did.js`.

```js
export const idlFactory = ({ IDL }) => {
  return IDL.Service({ 'greet' : IDL.Func([IDL.Text], [IDL.Text], []) });
};

```

Unlike our `did.d.ts` declarations, this `idlFactory` needs to be available during runtime. The `idlFactory` gets loaded by an [actor](https://agent-js.icp.host/agent/interfaces/Actor.html) interface, which is what will handle structuring the network calls according to the IC API and the provided candid spec.

This factory again represents a service with a `greet` method, and the same arguments as before. You may notice, however, that the `IDL.Func` has a third argument, which here is an empty array. That represents any additional annotations the function may be tagged with, which most commonly will be `"query"`.

#### index.js

In the `index.js` file, each of the previously explained pieces are pulled together to set up a customized actor with your smart contract's interface. This does a few things, like using `process.env` variables to determine the ID of the canister, based on which deploy context you are using, but the most important aspect is in the `createActor` export.

```js
 export const createActor = (canisterId, options) => {
  const agent = new HttpAgent({ ...options?.agentOptions });
  
  // Fetch root key for certificate validation during development
  if(process.env.NODE_ENV !== "production") {
    agent.fetchRootKey().catch(err=>{
      console.warn("Unable to fetch root key. Check to ensure that your local replica is running");
      console.error(err);
    });
  }
  // Creates an actor with using the candid interface and the HttpAgent
  return Actor.createActor(idlFactory, {
    agent,
    canisterId,
    ...options?.actorOptions,
  });
}
```

This constructor first creates a `HTTPAgent`, which is wraps the JS `fetch` API and uses it to encode calls through the public API. We also optionally fetch the root key of the replica, for non-mainnet deployments. Finally, we create an actor using the automatically generated interface for the canister we will call, passing it the `canisterId` and the `HTTPAgent` we have initialized.

This `actor` instance is now set up to call all of the service methods as methods. Once this is all set up, like it is by default in the `dfx new` template, you can simply run `dfx generate` whenever you make changes to your canister API, and the full interface will automatically stay in sync in your frontend code.

Since this interface is easily typed, we are able to automatically generate a JavaScript interface, as well as TypeScript declarations, for this application. This can be done in two ways. You can manually generate an interface using the `didc` tool, download it by going to the [releases](https://github.com/dfinity/candid/releases) tab of the `dfinity/candid` repository.

## Browser

The browser context is the easiest to account for. The `fetch` API is available, and most apps will have an easy time determining whether they need to talk to `https://icp0.io` or a local replica, depending on their URL. 

When you are building apps that run in the browser, here are some things to consider:

### Performance

Updates to the IC may feel slow to your users, at around 2-4 seconds. When you are building your application, take that latency into consideration, and consider following some best practices:

* Avoid blocking UI interactions while you wait for the result of your update. Instead, allow users to continuing to make other updates and interactions, and inform your users of success asynchronously. 
* Try to avoid making inter-canister calls. If the backend needs to talk to other canisters, the duration can add up quickly.
* Use `Promise.all` to make multiple calls in a batch, instead of making them one-by-one.
* If you need to fetch assets or data, you can make direct `fetch` calls to the `raw.icp0.io` endpoint for canisters.

## Bundlers

We recommend using a bundler to assemble your code for convenience and less troubleshooting. We provide a standard Webpack config, but you may also turn to Rollup, Vite, Parcel, or others. For this pattern, we recommend running a script to generate `.env.development` and `.env.production` environment variable files for your canister ids, which is a fairly standard approach for bundlers, and can be easily supported using [dotenv](https://www.npmjs.com/package/dotenv). 

Here is an example script you can run to map those files:

```js
// setupEnv.js
const fs = require("fs");
const path = require("path");

function initCanisterEnv() {
  let localCanisters, prodCanisters;
  try {
    localCanisters = require(path.resolve(
      ".dfx",
      "local",
      "canister_ids.json"
    ));
  } catch (error) {
    console.log("No local canister_ids.json found");
  }
  try {
    prodCanisters = require(path.resolve("canister_ids.json"));
  } catch (error) {
    console.log("No production canister_ids.json found");
  }

  const network =
    process.env.DFX_NETWORK ||
    (process.env.NODE_ENV === "production" ? "ic" : "local");

  const canisterConfig = network === "local" ? localCanisters : prodCanisters;

  const localMap = localCanisters
    ? Object.entries(localCanisters).reduce((prev, current) => {
        const [canisterName, canisterDetails] = current;
        prev[canisterName.toUpperCase() + "_CANISTER_ID"] =
          canisterDetails[network];
        return prev;
      }, {})
    : undefined;
  const prodMap = prodCanisters
    ? Object.entries(prodCanisters).reduce((prev, current) => {
        const [canisterName, canisterDetails] = current;
        prev[canisterName.toUpperCase() + "_CANISTER_ID"] =
          canisterDetails[network];
        return prev;
      }, {})
    : undefined;
  return [localMap, prodMap];
}
const [localCanisters, prodCanisters] = initCanisterEnv();

if (localCanisters) {
  const localTemplate = Object.entries(localCanisters).reduce((start, next) => {
    const [key, val] = next;
    if (!start) return `${key}=${val}`;
    return `${start ?? ""}
          ${key}=${val}`;
  }, ``);
  fs.writeFileSync(".env.development", localTemplate);
}
if (prodCanisters) {
  const prodTemplate = Object.entries(prodCanisters).reduce((start, next) => {
    const [key, val] = next;
    if (!start) return `${key}=${val}`;
    return `${start ?? ""}
        ${key}=${val}`;
  }, ``);
  fs.writeFileSync(".env", localTemplate);
}
```

Then, you can add `"prestart"` and `"prebuild"` commands of `dfx generate; node setupEnv.js`. Follow documentation for your preferred bundler on how to work with environment variables.

-->
