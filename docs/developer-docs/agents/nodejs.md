---

sidebar_position: 2
sidebar_label: "From Node.js"
---
# Node.jsからのIC呼び出し

## 概要

Node.jsからICを呼び出す方法について説明します。WebブラウザのJavaScriptからICを呼び出す方法については、[こちらのガイドを](javascript-intro.md)参照してください。

Node.jsはJavaScriptのランタイムであるため、[JavaScriptエージェントを](https://www.npmjs.com/package/@dfinity/agent)使用してcanister と対話することができます。これは、オラクルを実行したり、既存のNode.jsアプリケーションをICに接続したり、アプリケーションにWebソケット層を導入したりするのに便利です。

この例では、単純な Node.js ウェブソケット・プロバイダを実行し、canister イベント・スタックの追跡をプロキシします。

まず、プロジェクトを作成します。https://github.com/Psychedelic/DIP721、DIP721のサンプルコードを使用し、NFTのコレクションを発行するnodeスクリプトを書いてみましょう。

## 前提条件

- \[x\] このプロジェクトはdfx 0.11.2で導入された機能を使用しています。コマンドで最新版のIC SDKをインストールできます：

<!-- end list -->

    sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"

- \[x\] また、Node.jsが必要です。このガイドはNodeのバージョン16以上で書かれています。[nvmの](https://github.com/nvm-sh/nvm)セットアップがまだの場合は、指示に従ってください。

- \[x\] 以下のパッケージをインストールしてください：

<!-- end list -->

    npm install --save \
    @dfinity/agent \
    @dfinity/principal \
    @dfinity/candid \
    @dfinity/identity \
    @dfinity/identity-secp256k1 \
    @dfinity/assets \
    isomorphic-fetch \
    image-thumbnail \
    mmmagic \
    prettier \
    sha256-file

## プロジェクトの更新

- #### ステップ 1: まず、リポジトリをフォークしてクローンします。

<!-- end list -->

    git clone https://github.com/Psychedelic/DIP721.git

- #### ステップ 2: 次に、`cd` を`DIP721` にコピーします。

このプロジェクトにはすでにcanister のロジックがすべて追加されているので、追加するのはあと少しです。

- #### ステップ 3: コマンドを実行して`package.json` ファイルを追加します：

<!-- end list -->

    npm init -y

- #### ステップ4: 次に、`src` ディレクトリに、`"node"` という新しいディレクトリを追加し、`index.js` という新しいファイルをコマンドで追加します：

<!-- end list -->

    mkdir src/node
    touch /src/node/index.js

- #### ステップ 5:`index.js` ファイルを開き、以下のコードを追加します：

<!-- end list -->

``` js
// src/node/index.js
console.log("Hello world");
```

- #### ステップ 6: プロジェクトディレクトリのルートで、`package.json` ファイルを開きます。

`"scripts"` を更新して`"start": "node --es-module-specifier-resolution=node src/node/index.js"` をインクルードし、`"type": "module"` を追加します。

package.jsonはこのようになるはずです：

``` json
{
  "name": "dip721",
  "version": "1.0.0",
  "description": "## Summary",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "build": "",
    "start": "node --es-module-specifier-resolution=node src/node/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

- #### ステップ 7: すべてが正しくセットアップされていることを確認するために、以下を実行します。

<!-- end list -->

    npm start

コンソールに`Hello world` が表示されます。

## 型の生成

- #### ステップ 8: 次に、`dfx.json` ファイルを開きます。

`nft` に、`declarations -> node_compatibility` の新しいコンフィギュレーションを追加してください：

```
  "canisters": {
    "nft": {
      "package": "nft",
      "candid": "nft.did",
      "type": "rust",
      "declarations": {
        "node_compatibility": true
      }
    },
```

これにより、`node.js` プロジェクト用に自動生成される JavaScript インターフェイスが最適化されます。

- #### ステップ9: さらに、NFTのフロントエンドアセットをホストするために使用する新しいcanister 、`asset` 。

<!-- end list -->

```
    "asset": {
      "type": "assets",
      "source": ["dist"],
      "declarations": {
        "node_compatibility": true
      }
    },
```

- #### ステップ10：最後に、 の設定を削除します：最後に、`dfx` 、`defaults` 、`networks` の設定を削除します。

`dfx` これらの設定により、プロジェクトは`dfx` の特定の古いバージョンにロックされます。

これらのステップの後、`dfx.json` ファイルはこのようになるはずです：

``` json
{
  "version": 1,
  "canisters": {
    "nft": {
      "package": "nft",
      "candid": "nft.did",
      "type": "rust",
      "declarations": {
        "node_compatibility": true
      }
    },
    "asset": {
      "type": "assets",
      "source": ["dist"],
      "declarations": {
        "node_compatibility": true
      }
    },
    "cap-router": {
      "type": "custom",
      "wasm": "cap/wasm/cap_router.wasm",
      "candid": "cap/candid/router.did"
    }
  }
}
```

- #### ステップ11：これで、プロジェクトを立ち上げることができます。

ここでは、`"test"` という単語を 12 回繰り返すシード・フレーズから派生したプリンシパルの例を使用します。このプリンシパルは`rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae` とします。

:::caution
言うまでもなく、これはテスト用のシードフレーズです。canister のデプロイや管理に使用される実際のシードフレーズは、秘密にしておく必要があります。
::：

これらのコマンドはターミナルで実行するか、`setup.sh` ファイルに追加してください。

``` shell
# setup.sh
dfx start --background --clean
dfx deploy nft --argument '(opt record{custodians=opt vec{principal"rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae"}})'
dfx deploy assets
dfx canister call asset authorize "(principal  \"rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae\")"
```

## コードの記述

### 宣言の生成

- #### ステップ 12: 上記のコマンドを実行してcanisters を構築した後、`dfx generate nft` を実行してcanister 用の自動生成インターフェースを作成できます。

<!-- end list -->

    dfx generate nft

このインターフェースは`src/declarations/nft` に配置され、これを使ってcanister とやり取りします。actor をセットアップする前に、ID が必要です。

### シードフレーズからのID

- #### ステップ13:`--es-module-specifier-resolution=node` フラグを使用してコードを実行しているので、コードでは`import` 構文を使用できます。

まず、前述のプリンシパルに解決する ID を設定します。

`src/node` ディレクトリに`identity.js` という新しいファイルを作成し、そのファイルに以下のコードを 挿入します：

``` js
// identity.js
import { Secp256k1KeyIdentity } from "@dfinity/identity-secp256k1";

// Completely insecure seed phrase. Do not use for any purpose other than testing.
// Resolves to "rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae"
const seed = "test test test test test test test test test test test test";

export const identity = await Secp256k1KeyIdentity.fromSeedPhrase(seed);
```

ご覧のとおり、シードフレーズは`test` という単語から派生したもので、12 回繰り返されています。これはテスト目的やローカル開発に便利です。ICにコントラクトをデプロイするときは、seedをプライベートなものに変更してください。

:::caution
本番で使用するシードフレーズは、安全な場所に保管することを忘れないでください。
::：

### シードフレーズのセットアップactor

- #### ステップ 14:`src > node > index.js` ファイルに戻って、actor を設定します。

`nft` `process.env.<canister-id>_CANISTER_ID` の宣言から ユーティリティをインポートし、 エイリアスをインポートします。`createActor` `canisterId` 

canister id 環境変数のロジックは、さまざまな方法でアプリケーションに渡すことができます。次のような方法があります：

- `package.json` スクリプトの最初に編集する`NFT_CANISTER_ID=... node...` 。
- [dotenvを](https://www.npmjs.com/package/dotenv)インストールして、`.env` の隠しファイルから読み込むように設定します。

`.dfx/local/canister_ids.json`この例ではローカルでの開発に焦点を当てますので、単純にローカルの`canister_ids.json` ファイルから読み込みます。

ですから、canister IDをインポートして、actor をセットアップするには、次のようにします：

:::caution
次の例は、より大きなコード・ファイルの一部である**コード・スニペット**です。このスニペットは単独で実行するとエラーを返すことがあります。実行されるべき完全なコードファイルを表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
// src/node/index.js
import fetch from "isomorphic-fetch";
import { HttpAgent } from "@dfinity/agent";
import { createRequire } from "node:module";
import { canisterId, createActor } from "../declarations/nft/index.js";
import { identity } from "./identity.js";

// Require syntax is needed for JSON file imports
const require = createRequire(import.meta.url);
const localCanisterIds = require("../../.dfx/local/canister_ids.json");

// Use `process.env` if available provoded, or fall back to local
const effectiveCanisterId =
  canisterId?.toString() ?? localCanisterIds.nft.local;

const agent = new HttpAgent({
  identity: await identity,
  host: "http://127.0.0.1:4943",
  fetch,
});

const actor = createActor(effectiveCanisterId, {
  agent,
});
```

ここで、actor は完全にセットアップされ、ローカルのcanister とセットアップしたエージェントに電話をかける準備ができています。ローカルに注目しているので、`host` は`http://127.0.0.1:4943` のローカルレプリカを指しています。ICメインネットと通話したい場合は、`host` は`https://icp0.io` を指す必要があります。

:::info
 `4943` はローカルレプリカのデフォルトポートです。ポートを変更した場合、または古いバージョンの`dfx` を使用している場合は、こことコード内の他のインスタンスでポートを更新する必要があります。
::：

### 発行ロジック

それでは、NFTを発行するためのロジックを書いてみましょう。必要なステップは以下の通りです：

- 1: ミントするNFTの設定を解析します。
- 2: アセットとアセットのメタデータを読み込みます。
- 3: サムネイルを生成します。
- 4: アセットをアセットcanister にアップロードします。
- 5: NFTの造幣。

### 設定の解析

- #### ステップ15: 設定を解析するために、項目の配列を使用してJSONファイルに保存します。

asset "名に加え、key-valueメタデータも読み込むことができます。

`src/node` ディレクトリに`nft.json` という新しいファイルを作成し、以下の内容を挿入します：

``` json
// nfts.json
[
  {
    "asset": "example_nft_0.png",
    "metadata": {
      "collection": "examples",
      "sampleKey": "value"
    }
  }
]
```

そして、それを`src/node/index.js` スクリプトで読み込みます：

:::caution
次の例は、より大きなコード・ファイルの一部である**コード・スニペット**です。このスニペットは単独で実行するとエラーを返すことがあります。実行すべき完全なコードファイルを表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
// index.js
import { createRequire } from "node:module";
const require = createRequire(import.meta.url);
const nftConfig = require("./nfts.json");
```

### アセットとメタデータの準備

- #### ステップ16：次に、JSONから`"asset"` パスを使って画像を読み込みましょう。

`src/node/index.js` スクリプトに以下を追加します：

:::caution
次の例は、より大きなコードファイルの一部である**コードスニペット**です。このスニペットは、単独で実行するとエラーを返す可能性があります。実行されるべき完全なコードファイルを表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
import path from "path";
import fs from "fs";
import mmm from "mmmagic";
import { fileURLToPath } from "url";
import sha256File from "sha256-file";
// ...

async function main() {
  nftConfig.forEach(async (nft, idx) => {
    // Parse metadata, if present
    const metadata = Object.entries(nft.metadata ?? []).map(([key, value]) => {
      return [key, { TextContent: value }];
    });

    const filePath = path.join(
      fileURLToPath(import.meta.url),
      "..",
      "assets",
      nft.asset
    );

    const file = fs.readFileSync(filePath); // Blob of file
    const sha = sha256File(filePath); // SHA of file

    // Detect filetype
    const magic = await new mmm.Magic(mmm.MAGIC_MIME_TYPE);
    const contentType = await new Promise((resolve, reject) => {
      magic.detectFile(filePath, (err, result) => {
        if (err) reject(err);
        resolve(result);
      });
    });
  });
}
main();
```

### サムネイルの準備

- #### ステップ 17: サムネイルを準備するには、`sharp` をベースにしたユーティリティ、`image-thumbnail` を使います。

`src/node/index.js` スクリプトに以下を追加してください：

:::caution
次の例は、より大きなコード・ファイルの一部である**コード・スニペット**です。このスニペットは、単独で実行するとエラーを返すことがあります。実行されるべき完全なコードファイルを表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
import imageThumbnail from "image-thumbnail";
// ...
const options = {
  width: 256,
  height: 256,
  responseType: "buffer",
  jpegOptions: { force: true, quality: 90 },
};
const thumbnail = await imageThumbnail(filePath, options);
```

### アセットのアップロード

- #### ステップ18：アセットへのアップロードを簡単にするために、コミュニティライブラリを使用することができますcanister 。canister ID を取得し、エージェントを渡し、2つのファイルをアップロードする必要があります。

`src/node/index.js` スクリプトに以下を追加します：

:::caution
以下の例は、より大きなコードファイルの一部である**コードスニペット**です。このスニペットは、単独で実行するとエラーを返すことがあります。実行するコードファイル全体を表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
import { Blob } from "buffer";
global.Blob = Blob;
import { AssetManager } from "@dfinity/assets";
// ...

const assetCanisterId = localCanisterIds.asset.local;
const assetManager = new AssetManager({
  canisterId: assetCanisterId,
  agent, // re-use agent
  concurrency: 32, // Optional (default: 32), max concurrent requests.
  maxSingleFileSize: 450000, // Optional bytes (default: 450000), larger files will be uploaded as chunks.
  maxChunkSize: 1900000, // Optional bytes (default: 1900000), size of chunks when file is uploaded as chunks.
});

async function main() {
  // ...
  const uploadedFilePath = `token/${idx}${path.extname(nft.asset)}`;
  const uploadedThumbnailPath = `thumbnail/${idx}.jpeg`;

  await assetManager.store(file, { fileName: uploadedFilePath });
  await assetManager.store(thumbnail, { fileName: uploadedThumbnailPath });
}
```

### データと発行の組み立て

- #### ステップ 19: あとは、アップロードしたアセットを指すメタデータを組み立て、NFTをミントするだけです。

`src/node/index.js` スクリプトに以下を追加します：

:::caution
次の例は、より大きなコードファイルの一部である**コードスニペット**です。このスニペットは、単独で実行するとエラーを返すことがあります。実行されるべき完全なコードファイルを表示するには、[最終コードを](#final-code)参照してください。
::：

``` js
async function main() {
  // ...
  const data = [
    [
      "location",
      {
        TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedFilePath}`,
      },
    ],
    [
      "thumbnail",
      {
        TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedThumbnailPath}`,
      },
    ],
    ["contentType", { TextContent: contentType }],
    ["contentHash", { BlobContent: encoder.encode(sha) }],
    ...metadata,
  ];

  // set our minting identity as the recipient - replace if you have airdrops in mind
  const principal = await (await identity).getPrincipal();
  const mintResult = await actor.mint(principal, BigInt(idx), data);

  // Verify token is minted
  const metaResult = await actor.tokenMetadata(0n);
}
```

## 最終コード

以下は、`src/node/index.js` スクリプトの完全版で、プロセスを示すためにコンソールログがいくつか追加されています。

``` js
import fetch from "isomorphic-fetch";
import { HttpAgent } from "@dfinity/agent";
import { canisterId, createActor } from "../declarations/nft/index.js";
import { identity } from "./identity.js";
import { createRequire } from "node:module";
import path from "path";
import fs from "fs";
import mmm from "mmmagic";
import { fileURLToPath } from "url";
import sha256File from "sha256-file";
import { Blob } from "buffer";
import { AssetManager } from "@dfinity/assets";
import imageThumbnail from "image-thumbnail";
import prettier from "prettier";

const require = createRequire(import.meta.url);
const nftConfig = require("./nfts.json");
const localCanisterIds = require("../../.dfx/local/canister_ids.json");

const encoder = new TextEncoder();

const agent = new HttpAgent({
  identity: await identity,
  host: "http://127.0.0.1:4943",
  fetch,
});
const effectiveCanisterId =
  canisterId?.toString() ?? localCanisterIds.nft.local;
const assetCanisterId = localCanisterIds.assets.local;
const actor = createActor(effectiveCanisterId, {
  agent,
});
const assetManager = new AssetManager({
  canisterId: assetCanisterId,
  agent,
  concurrency: 32, // Optional (default: 32), max concurrent requests.
  maxSingleFileSize: 450000, // Optional bytes (default: 450000), larger files will be uploaded as chunks.
  maxChunkSize: 1900000, // Optional bytes (default: 1900000), size of chunks when file is uploaded as chunks.
});

async function main() {
  nftConfig.forEach(async (nft, idx) => {
    console.log("starting upload for " + nft.asset);

    // Parse metadata, if present
    const metadata = Object.entries(nft.metadata ?? []).map(([key, value]) => {
      return [key, { TextContent: value }];
    });

    const filePath = path.join(
      fileURLToPath(import.meta.url),
      "..",
      "assets",
      nft.asset
    );

    const file = fs.readFileSync(filePath);

    const sha = sha256File(filePath);
    const options = {
      width: 256,
      height: 256,
      responseType: "buffer",
      jpegOptions: { force: true, quality: 90 },
    };
    console.log("generating thumbnail");
    const thumbnail = await imageThumbnail(filePath, options);

    const magic = await new mmm.Magic(mmm.MAGIC_MIME_TYPE);
    const contentType = await new Promise((resolve, reject) => {
      magic.detectFile(filePath, (err, result) => {
        if (err) reject(err);
        resolve(result);
      });
    });
    console.log("detected contenttype of ", contentType);

    /**
     * For asset in nfts.json
     *
     * Take asset
     * Check extenstion / mimetype
     * Sha content
     * Generate thumbnail
     * Upload both to asset canister -> file paths
     * Generate metadata from key / value
     * Mint ^
     */

    const uploadedFilePath = `token/${idx}${path.extname(nft.asset)}`;
    const uploadedThumbnailPath = `thumbnail/${idx}.jpeg`;

    console.log("uploading asset to ", uploadedFilePath);
    await assetManager.store(file, { fileName: uploadedFilePath });
    console.log("uploading thumbnail to ", uploadedThumbnailPath);
    await assetManager.store(thumbnail, { fileName: uploadedThumbnailPath });

    const principal = await (await identity).getPrincipal();

    const data = [
      [
        "location",
        {
          TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedFilePath}`,
        },
      ],
      [
        "thumbnail",
        {
          TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedThumbnailPath}`,
        },
      ],
      ["contentType", { TextContent: contentType }],
      ["contentHash", { BlobContent: encoder.encode(sha) }],
      ...metadata,
    ];
    const mintResult = await actor.dip721_mint(principal, BigInt(idx), data);
    console.log("result: ", mintResult);
    const metaResult = await actor.tokenMetadata(0n);
    console.log("new token info: ", metaResult);
    console.log(
      "token metadata: ",
      prettier.format(
        JSON.stringify(metaResult, (key, value) =>
          typeof value === "bigint" ? value.toString() : value
        ),
        { parser: "json" }
      )
    );
  });
}
main();
```

<!---


# Calling the IC from Node.js

## Overview
This article covers connecting to the IC from Node.js in the server environment. For more information about calling IC from JavaScript in a web browser, please, refer to [this guide](javascript-intro.md).

Node.js is a runtime for JavaScript, so you can use the [JavaScript agent](https://www.npmjs.com/package/@dfinity/agent) with it to interact with a canister. This can be useful to run an oracle, connect an existing Node.js application to the IC, or to introduce a websocket layer to your application.

In this example, we will run a simple Node.js websocket provider, proxying a canister keeping track of a stack of events.

First, we need to get started with a project. Let's take the DIP721 example code at https://github.com/Psychedelic/DIP721, and write a node script that will mint a collection of NFTs.

## Prerequisites

- [x] This project uses features introduced in dfx 0.11.2. You can install the latest version of the IC SDK with the command:

```
sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
```

- [x] Also, you will need Node.js. This guide was written for Node version 16 and up. Follow instructions to get set up with [nvm](https://github.com/nvm-sh/nvm) if you have not yet.

- [x] Install the following packages:

```
npm install --save \
@dfinity/agent \
@dfinity/principal \
@dfinity/candid \
@dfinity/identity \
@dfinity/identity-secp256k1 \
@dfinity/assets \
isomorphic-fetch \
image-thumbnail \
mmmagic \
prettier \
sha256-file
```

## Updating the project

- #### Step 1: First, fork and clone the repo.

```
git clone https://github.com/Psychedelic/DIP721.git
```

- #### Step 2: Then, `cd` into `DIP721`. 

The project has all of the canister logic already added, so there is only a little more we need to add.

- #### Step 3: Add a `package.json` file by running the command:

```
npm init -y
```

- #### Step 4: Then, in the `src` directory, add a new directory, `"node"`, with a new file `index.js` with the commands:

```
mkdir src/node
touch /src/node/index.js
```

- #### Step 5: Open the `index.js` file and add the following code:

```js
// src/node/index.js
console.log("Hello world");
```

- #### Step 6: In the root of the project directory, open the `package.json` file. 

Update your `"scripts"` to include `"start": "node --es-module-specifier-resolution=node src/node/index.js"`, and add `"type": "module"`. 

Your package.json should look like this:

```json
{
  "name": "dip721",
  "version": "1.0.0",
  "description": "## Summary",
  "type": "module",
  "main": "index.js",
  "scripts": {
    "build": "",
    "start": "node --es-module-specifier-resolution=node src/node/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

- #### Step 7: To make sure everything is set up correctly, run

```
npm start
```

And see your `Hello world` printed in the console.

## Generating types

- #### Step 8: Next, open up your `dfx.json` file. 

To `nft`, add a new configuration for `declarations -> node_compatibility`:

```
  "canisters": {
    "nft": {
      "package": "nft",
      "candid": "nft.did",
      "type": "rust",
      "declarations": {
        "node_compatibility": true
      }
    },
```

This will optimize the auto-generated JavaScript interface for `node.js` projects.

- #### Step 9: Additionally, we will add a new canister, `asset`, which will be used to host the frontend assets for our NFTs.

```
    "asset": {
      "type": "assets",
      "source": ["dist"],
      "declarations": {
        "node_compatibility": true
      }
    },
```

- #### Step 10: Finally, remove the `dfx` setting as well as the `defaults` and `networks` settings. 

They will lock the project to a specific and outdated version of `dfx`, and we want to use `dfx` 12 or later.

After these steps, your `dfx.json` file should look like this:

```json
{
  "version": 1,
  "canisters": {
    "nft": {
      "package": "nft",
      "candid": "nft.did",
      "type": "rust",
      "declarations": {
        "node_compatibility": true
      }
    },
    "asset": {
      "type": "assets",
      "source": ["dist"],
      "declarations": {
        "node_compatibility": true
      }
    },
    "cap-router": {
      "type": "custom",
      "wasm": "cap/wasm/cap_router.wasm",
      "candid": "cap/candid/router.did"
    }
  }
}
```

- #### Step 11: Now, you can start up your project. 

We will use an example principal, derived from a seed phrase of the word `"test"` 12 times. This principal will be `rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae`.

:::caution
It should go without saying, but this is a testing seed phrase, and any real seed phrase used to deploy or manage a canister should be kept a secret.
:::

You can either run these commands in the terminal, or add them to a `setup.sh` file.

```shell
# setup.sh
dfx start --background --clean
dfx deploy nft --argument '(opt record{custodians=opt vec{principal"rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae"}})'
dfx deploy assets
dfx canister call asset authorize "(principal  \"rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae\")"
```

## Writing code

### Generating declarations

- #### Step 12: After running the above commands to build your canisters, you can run `dfx generate nft` to create an auto-generated interface for your canister.

```
dfx generate nft
```

The interface will be placed into `src/declarations/nft`, and we will use that to interact with the canister. Before we setup the actor, we will need to have an identity.

### Identity from a seed phrase

- #### Step 13: Since we are running the code using the `--es-module-specifier-resolution=node` flag, we can use `import` syntax in our code. 

Let's start by setting up an identity that will resolve to the principal that is mentioned above.

Create a new file in the `src/node` directory called `identity.js` and insert the following code into the file:

```js
// identity.js
import { Secp256k1KeyIdentity } from "@dfinity/identity-secp256k1";

// Completely insecure seed phrase. Do not use for any purpose other than testing.
// Resolves to "rwbxt-jvr66-qvpbz-2kbh3-u226q-w6djk-b45cp-66ewo-tpvng-thbkh-wae"
const seed = "test test test test test test test test test test test test";

export const identity = await Secp256k1KeyIdentity.fromSeedPhrase(seed);
```

As you can see, the seed phrase is derived from the word `test`, repeated 12 times. This is useful for testing purposes and local development. When you are deploying your contract to the IC, you should change the seed out for something private.

:::caution
Remember to store any seed phrase you use in production in a secure place. Use environment variables and never commit a real seed phrase in plaintext in your codebase.
:::

### Setting up an actor

- #### Step 14: Back in `src > node > index.js` file, we can now set up our actor. 

We can import a `createActor` utility from the `nft` declarations, as well as a `canisterId` alias, which by default points to `process.env.<canister-id>_CANISTER_ID`.

You can pass the canister id environment variable logic to your application in a number of ways. You could:

- Edit it into the start of your `package.json` scripts `NFT_CANISTER_ID=... node...`. 
- Install [dotenv](https://www.npmjs.com/package/dotenv) and configure it to read from a hidden `.env` file. 

For the sake of this example, which will focus on local development, we will simply read it from the local `canister_ids.json` file, which can be found in `.dfx/local/canister_ids.json`.

So, to import the canister ID and set up our actor, it will look something like this:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
// src/node/index.js
import fetch from "isomorphic-fetch";
import { HttpAgent } from "@dfinity/agent";
import { createRequire } from "node:module";
import { canisterId, createActor } from "../declarations/nft/index.js";
import { identity } from "./identity.js";

// Require syntax is needed for JSON file imports
const require = createRequire(import.meta.url);
const localCanisterIds = require("../../.dfx/local/canister_ids.json");

// Use `process.env` if available provoded, or fall back to local
const effectiveCanisterId =
  canisterId?.toString() ?? localCanisterIds.nft.local;

const agent = new HttpAgent({
  identity: await identity,
  host: "http://127.0.0.1:4943",
  fetch,
});

const actor = createActor(effectiveCanisterId, {
  agent,
});
```

At the end here, the actor is fully set up and ready to make calls to the local canister and the agent we have set up. Since we are focusing on local, the `host` is pointing to the local replica at `http://127.0.0.1:4943`. If we want to talk to the IC mainnet, the `host` should point to `https://icp0.io`.

:::info
The port `4943` is the default port for the local replica. If you have changed the port, or you are using an older version of `dfx`, you will need to update it here and in the other instances in the code.
:::

### Minting logic

Now, let's go through and write some logic to mint our NFTs. The steps we will need to go through include:

- 1: Parse a config for the NFTs to be minted.
- 2: Load assets and metadata for the assets.
- 3: Generate thumbnails.
- 4: Upload assets to an asset canister.
- 5: Mint the NFT.

### Parse the config

- #### Step 15: To parse the config, we'll store it in a JSON file, using an array of items. 

There's the included "asset" name, plus some key-value metadata that can get loaded as well.

Create a new file in the `src/node` directory called `nft.json` and insert the following content:

```json
// nfts.json
[
  {
    "asset": "example_nft_0.png",
    "metadata": {
      "collection": "examples",
      "sampleKey": "value"
    }
  }
]
```

Then, we can load that in the `src/node/index.js` script:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
// index.js
import { createRequire } from "node:module";
const require = createRequire(import.meta.url);
const nftConfig = require("./nfts.json");
```

### Prepare assets and metadata

- #### Step 16: Next, let's load the image, using the `"asset"` path from JSON.

In the `src/node/index.js` script, add the following:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
import path from "path";
import fs from "fs";
import mmm from "mmmagic";
import { fileURLToPath } from "url";
import sha256File from "sha256-file";
// ...

async function main() {
  nftConfig.forEach(async (nft, idx) => {
    // Parse metadata, if present
    const metadata = Object.entries(nft.metadata ?? []).map(([key, value]) => {
      return [key, { TextContent: value }];
    });

    const filePath = path.join(
      fileURLToPath(import.meta.url),
      "..",
      "assets",
      nft.asset
    );

    const file = fs.readFileSync(filePath); // Blob of file
    const sha = sha256File(filePath); // SHA of file

    // Detect filetype
    const magic = await new mmm.Magic(mmm.MAGIC_MIME_TYPE);
    const contentType = await new Promise((resolve, reject) => {
      magic.detectFile(filePath, (err, result) => {
        if (err) reject(err);
        resolve(result);
      });
    });
  });
}
main();
```

### Prepare thumbnail

- #### Step 17: To prepare the thumbnail, we can use `image-thumbnail`, a utility based on `sharp`.

In the `src/node/index.js` script, add the following:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
import imageThumbnail from "image-thumbnail";
// ...
const options = {
  width: 256,
  height: 256,
  responseType: "buffer",
  jpegOptions: { force: true, quality: 90 },
};
const thumbnail = await imageThumbnail(filePath, options);
```

### Upload assets

- #### Step 18: We can use a community library to simplify uploading to our asset canister. We'll need to get the canister ID, pass our agent, and then upload our two files.

In the `src/node/index.js` script, add the following:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
import { Blob } from "buffer";
global.Blob = Blob;
import { AssetManager } from "@dfinity/assets";
// ...

const assetCanisterId = localCanisterIds.asset.local;
const assetManager = new AssetManager({
  canisterId: assetCanisterId,
  agent, // re-use agent
  concurrency: 32, // Optional (default: 32), max concurrent requests.
  maxSingleFileSize: 450000, // Optional bytes (default: 450000), larger files will be uploaded as chunks.
  maxChunkSize: 1900000, // Optional bytes (default: 1900000), size of chunks when file is uploaded as chunks.
});

async function main() {
  // ...
  const uploadedFilePath = `token/${idx}${path.extname(nft.asset)}`;
  const uploadedThumbnailPath = `thumbnail/${idx}.jpeg`;

  await assetManager.store(file, { fileName: uploadedFilePath });
  await assetManager.store(thumbnail, { fileName: uploadedThumbnailPath });
}
```

### Assemble the data and mint

- #### Step 19: Then, all we have left to do is assemble the metadata pointing to our uploaded assets, and to mint the NFT.

In the `src/node/index.js` script, add the following:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own. To view the full code file that should be run, please see [final code](#final-code).
:::

```js
async function main() {
  // ...
  const data = [
    [
      "location",
      {
        TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedFilePath}`,
      },
    ],
    [
      "thumbnail",
      {
        TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedThumbnailPath}`,
      },
    ],
    ["contentType", { TextContent: contentType }],
    ["contentHash", { BlobContent: encoder.encode(sha) }],
    ...metadata,
  ];

  // set our minting identity as the recipient - replace if you have airdrops in mind
  const principal = await (await identity).getPrincipal();
  const mintResult = await actor.mint(principal, BigInt(idx), data);

  // Verify token is minted
  const metaResult = await actor.tokenMetadata(0n);
}
```

## Final code
Here is the full `src/node/index.js` script, with some console logs added to show process.

```js
import fetch from "isomorphic-fetch";
import { HttpAgent } from "@dfinity/agent";
import { canisterId, createActor } from "../declarations/nft/index.js";
import { identity } from "./identity.js";
import { createRequire } from "node:module";
import path from "path";
import fs from "fs";
import mmm from "mmmagic";
import { fileURLToPath } from "url";
import sha256File from "sha256-file";
import { Blob } from "buffer";
import { AssetManager } from "@dfinity/assets";
import imageThumbnail from "image-thumbnail";
import prettier from "prettier";

const require = createRequire(import.meta.url);
const nftConfig = require("./nfts.json");
const localCanisterIds = require("../../.dfx/local/canister_ids.json");

const encoder = new TextEncoder();

const agent = new HttpAgent({
  identity: await identity,
  host: "http://127.0.0.1:4943",
  fetch,
});
const effectiveCanisterId =
  canisterId?.toString() ?? localCanisterIds.nft.local;
const assetCanisterId = localCanisterIds.assets.local;
const actor = createActor(effectiveCanisterId, {
  agent,
});
const assetManager = new AssetManager({
  canisterId: assetCanisterId,
  agent,
  concurrency: 32, // Optional (default: 32), max concurrent requests.
  maxSingleFileSize: 450000, // Optional bytes (default: 450000), larger files will be uploaded as chunks.
  maxChunkSize: 1900000, // Optional bytes (default: 1900000), size of chunks when file is uploaded as chunks.
});

async function main() {
  nftConfig.forEach(async (nft, idx) => {
    console.log("starting upload for " + nft.asset);

    // Parse metadata, if present
    const metadata = Object.entries(nft.metadata ?? []).map(([key, value]) => {
      return [key, { TextContent: value }];
    });

    const filePath = path.join(
      fileURLToPath(import.meta.url),
      "..",
      "assets",
      nft.asset
    );

    const file = fs.readFileSync(filePath);

    const sha = sha256File(filePath);
    const options = {
      width: 256,
      height: 256,
      responseType: "buffer",
      jpegOptions: { force: true, quality: 90 },
    };
    console.log("generating thumbnail");
    const thumbnail = await imageThumbnail(filePath, options);

    const magic = await new mmm.Magic(mmm.MAGIC_MIME_TYPE);
    const contentType = await new Promise((resolve, reject) => {
      magic.detectFile(filePath, (err, result) => {
        if (err) reject(err);
        resolve(result);
      });
    });
    console.log("detected contenttype of ", contentType);

    /**
     * For asset in nfts.json
     *
     * Take asset
     * Check extenstion / mimetype
     * Sha content
     * Generate thumbnail
     * Upload both to asset canister -> file paths
     * Generate metadata from key / value
     * Mint ^
     */

    const uploadedFilePath = `token/${idx}${path.extname(nft.asset)}`;
    const uploadedThumbnailPath = `thumbnail/${idx}.jpeg`;

    console.log("uploading asset to ", uploadedFilePath);
    await assetManager.store(file, { fileName: uploadedFilePath });
    console.log("uploading thumbnail to ", uploadedThumbnailPath);
    await assetManager.store(thumbnail, { fileName: uploadedThumbnailPath });

    const principal = await (await identity).getPrincipal();

    const data = [
      [
        "location",
        {
          TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedFilePath}`,
        },
      ],
      [
        "thumbnail",
        {
          TextContent: `http://${assetCanisterId}.localhost:4943/${uploadedThumbnailPath}`,
        },
      ],
      ["contentType", { TextContent: contentType }],
      ["contentHash", { BlobContent: encoder.encode(sha) }],
      ...metadata,
    ];
    const mintResult = await actor.dip721_mint(principal, BigInt(idx), data);
    console.log("result: ", mintResult);
    const metaResult = await actor.tokenMetadata(0n);
    console.log("new token info: ", metaResult);
    console.log(
      "token metadata: ",
      prettier.format(
        JSON.stringify(metaResult, (key, value) =>
          typeof value === "bigint" ? value.toString() : value
        ),
        { parser: "json" }
      )
    );
  });
}
main();
```

-->
