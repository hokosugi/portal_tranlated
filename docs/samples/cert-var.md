# 認定変数

## 概要

この例では、Internet Computer でサポートされている、単一の暗号化認証済み変数の使用を示します。

簡単に言うと、このサンプルコードは、単一の32ビット変数を保持するcanister に対する「レスポンス認証」を示します。これには2つの側面があります：

- Motoko （main.mo）のバックエンド（BE）canister ロジック。
- JS（index.js）内のフロントエンド（FE）ロジック。

FEとICの中間にいる攻撃者と、そこで実行されている "真の "BEcanister を検出するには、以下のいずれかを実行する必要があります：

- フルコンセンサス」を使用するアップデートコールの実行（および ~2 秒の待機）。
- ICとそこで実行されている私たちのcanister の調整を使用して、私たちクライアントが証明する応答を持つ（高速な）クエリーコールを実行します。

FEとBEのコードは、最小限の設定で2番目のアプローチを示しています。BE は 32 ビットの数値として 1 つの認証済み変数を保持し、FE コードはこの数値の「現在の証 明書」を照会して認証します。この証明書は、特別なシステム機能を使用して IC 全体によって署名されます。

FEは、見かけ上のBEからのレスポンス（canister ）を信頼し、その真正性を確認する 前に、4つのチェックを行います：

- システム証明書の検証。
- システム証明書のタイムスタンプが「古すぎない」ことを確認。
- システム証明書のcanister ID を確認。
- レスポンスが証人と一致することを確認。

ステップ 2、3、4 では、FE は証明書（Blob）のデータにアクセスします。

`agent-js` ライブラリの`Certificate` クラスは、ファイルシステムのようにパスを使用してこれらのアイテムにアクセスする方法を提供します。

時間と私たちのデータの場合、エンコーディングはそれぞれ Candid です。IC仕様ではLEB128エンコーディングを使って時間を表現し、認証データではリトルエンディアンを使っています。

これらの数値をデコードするには、適切なライブラリを使うのが理想的です。余分な依存関係を避けるために、NatとNat32のCandid値のエンコーディングが偶然にも同じ表現を使っているという事実を利用します。

我々のデータは、Candidの32ビットNat（リトルエンディアン -- 詳細はMotoko canister を参照）と同じエンコードを選択します。

特に、canister に1つの数値よりも多くのデータがある例や、より複雑なクエリインターフェイスでは、一般的に各クエリ応答を認証するために多くの作業を行うことになります：

- ハッシュを再計算するために証人を使用する（ここでは証人もハッシュも必要ありません。）
- クエリパラメータが証人と一致するかチェック（パラメータがないので、ここでは些細なことです。）
- 上記の理由から、ここではどちらのステップも必要ありません。

これはMotoko の例で、現在のところ Rust バリアントはありません。

## 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールします。
- \[x\][npmを](https://nodejs.org/en/download/)ダウンロードしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードします: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ1: プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/cert-var
    dfx start --background

### ステップ 2: フロントエンドの依存関係をインストールします：

    npm install

### ステップ 3:canister をデプロイします：

    dfx deploy

### ステップ 4: 次に、`webpack.config.js` ファイルを開き、内容を以下のように置き換えます：

    const path = require("path");
    const webpack = require("webpack");
    const HtmlWebpackPlugin = require("html-webpack-plugin");
    const TerserPlugin = require("terser-webpack-plugin");
    
    let localCanisters, prodCanisters, canisters;
    
    try {
      localCanisters = require(path.resolve(".dfx", "local", "canister_ids.json"));
    } catch (error) {
      console.log("No local canister_ids.json found. Continuing production");
    }
    
    function initCanisterIds() {
      try {
        prodCanisters = require(path.resolve("canister_ids.json"));
      } catch (error) {
        console.log("No production canister_ids.json found. Continuing with local");
      }
    
      const network =
        process.env.DFX_NETWORK ||
        (process.env.NODE_ENV === "production" ? "ic" : "local");
    
      canisters = network === "local" ? localCanisters : prodCanisters;
    
      for (const canister in canisters) {
        process.env[canister.toUpperCase() + "_CANISTER_ID"] =
          canisters[canister][network];
      }
    }
    initCanisterIds();
    
    const isDevelopment = process.env.NODE_ENV !== "production";
    const asset_entry = path.join(
      "src",
      "cert_var_assets",
      "src",
      "index.html"
    );
    
    module.exports = {
      target: "web",
      mode: isDevelopment ? "development" : "production",
      entry: {
        // The frontend.entrypoint points to the HTML file for this build, so we need
        // to replace the extension to `.js`.
        index: path.join(__dirname, asset_entry).replace(/\.html$/, ".js"),
      },
      devtool: isDevelopment ? "source-map" : false,
      optimization: {
        minimize: !isDevelopment,
        minimizer: [new TerserPlugin()],
      },
      resolve: {
        extensions: [".js", ".ts", ".jsx", ".tsx"],
        fallback: {
          assert: require.resolve("assert/"),
          buffer: require.resolve("buffer/"),
          events: require.resolve("events/"),
          stream: require.resolve("stream-browserify/"),
          util: require.resolve("util/"),
        },
      },
      output: {
        filename: "index.js",
        path: path.join(__dirname, "dist", "cert_var_assets"),
      },
    
      // Depending in the language or framework you are using for
      // front-end development, add module loaders to the default
      // webpack configuration. For example, if you are using React
      // modules and CSS as described in the "Adding a stylesheet"
      // tutorial, uncomment the following lines:
      // module: {
      //  rules: [
      //    { test: /\.(ts|tsx|jsx)$/, loader: "ts-loader" },
      //    { test: /\.css$/, use: ['style-loader','css-loader'] }
      //  ]
      // },
      plugins: [
        new HtmlWebpackPlugin({
          template: path.join(__dirname, asset_entry),
          cache: false
        }),
        new webpack.EnvironmentPlugin({
          NODE_ENV: 'development',
          CERT_VAR_CANISTER_ID: canisters["cert_var"]
        }),
        new webpack.ProvidePlugin({
          Buffer: [require.resolve("buffer/"), "Buffer"],
          process: require.resolve("process/browser"),
        }),
      ],
      // proxy /api to port 4943 during development
      devServer: {
        proxy: {
          "/api": {
    	static: './',
            target: "http://localhost:4943",
            changeOrigin: true,
            pathRewrite: {
              "^/api": "/api",
            },
          },
        },
        hot: true,
      },
    };

### ステップ 5:`server.js` という新しいファイルを以下の内容で作成します：

    var express = require('express');
    var app = express();
    
    app.get('/', function (req, res) {
      res.send('Hello World!'); // This will serve your request to '/'.
    });
    
    app.listen(4943, function () {
      console.log('Example app listening on port 4943!');
     });

### ステップ6：`src/cert_var_assets/src/index.html` の内容を以下の内容に置き換えます：

    <!doctype html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width">
        <title>Certified Variables</title>
        <base href="/">
    
        <link type="text/css" rel="stylesheet" href="main.css" />
    </head>
    <body style="background-color:powderblue; text-align:center;">
      <div>
        <img src="https://global.discourse-cdn.com/business4/uploads/dfn/original/1X/a6d6c5b4e246cd075a009424601bc981b3086fb4.png" alt="DFINITY logo" />
          <div> <label for="name">New value of variable</label>
            <input id="newValue" alt="New Value" type="number" />
            <button id="setBtn">Set and get!</button>
          </div>
          <pre id="var" style="line-height:1;"></pre>
        </div>
       </div>
    </body>
    </html>

### ステップ 7: フロントエンドをホストするローカルのウェブサーバを起動します。

    npm start

### ステップ8: フロントエンドにアクセスし、そこでデモと対話します：

    http://localhost:4943/?canisterId=$CANISTER_ID

すると、"New value of variable "のエントリと、"Set and get\!"のボタンが表示されるはずです。

数値を入力し、ボタンをクリックしてください。

[認証された変数](./_attachments/cert-var.png)

canister は証明書を更新し、フロントエンドはそれをチェックします。開発者コンソールには、各ステップに関する追加コメントが表示されます。

<!---
# Certified variables

## Overview
This example demonstrates the use of a single cryptographically certified variable, as supported by the Internet Computer.

In a nut shell, this example code demonstrates "response certification" for a canister that holds a single 32-bit variable. It has two sides:

- Backend (BE) canister logic in Motoko (main.mo).
- Frontend (FE) logic in JS (index.js).

To detect an attacker in the middle between the FE and the IC and our "true" BE canister running there, we must either:

- Perform update calls that use "full consensus" (and wait for ~2 sec).
- Perform (fast) query calls whose responses that we, the client, certify, using the coordination of the IC and our canister running there.

The FE and BE code demonstrates the second approach here, in a minimal setting. The BE holds a single certified variable, as a 32-bit number, and the FE code queries and certifies this number's "current certificate". The BE prepares for the FE certification by giving the FE a "current certificate" within the response; this certificate is signed by the entire IC, using a special system feature.

Before the FE trusts the response from the apparent BE canister, it interrogates it, and verifies its authenticity, the FE does four checks:

- Verify system certificate.
- Check system certificate timestamp is not "too old".
- Check canister ID in system certificate.
- Check response matches witness.

For steps 2, 3 and 4, the FE accesses data from the certificate (Blob).

The `Certificate` class from the `agent-js` library provides a way to access those items using their paths, like a filesystem, each addressing a Blob, encoding something.

In the case of time and our data, the encodings are each Candid. The IC spec represents time using a LEB128 encoding, and certified data uses little endian.

Ideally, we should use a proper library to decode these numbers. To prevent an extra dependency, we take advantage of the fact that the Candid value encoding of Nat and Nat32 happen to use the same representation.

Our data we choose to encode the same as a Candid 32-bit Nat (little endian -- see the Motoko canister for details).

Notably, in an example with more data in the canister than a single number, or a more complex query interface, we would generally do more work to certify each query response:

- Use witnesses to re-calculate hash (no witness or hashing needed here.)
- Check query parameters matches witness (no params, so trivial here.)
- Neither of those steps are needed here, for the reasons given above.

This is a Motoko example that does not currently have a Rust variant. 


## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download [npm](https://nodejs.org/en/download/).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/cert-var
dfx start --background
```

### Step 2: Install the front-end dependencies:

```
npm install
```

### Step 3: Deploy the canister:

```
dfx deploy
```

### Step 4: Next, open the `webpack.config.js` file and replace the contents with the following:

```
const path = require("path");
const webpack = require("webpack");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");

let localCanisters, prodCanisters, canisters;

try {
  localCanisters = require(path.resolve(".dfx", "local", "canister_ids.json"));
} catch (error) {
  console.log("No local canister_ids.json found. Continuing production");
}

function initCanisterIds() {
  try {
    prodCanisters = require(path.resolve("canister_ids.json"));
  } catch (error) {
    console.log("No production canister_ids.json found. Continuing with local");
  }

  const network =
    process.env.DFX_NETWORK ||
    (process.env.NODE_ENV === "production" ? "ic" : "local");

  canisters = network === "local" ? localCanisters : prodCanisters;

  for (const canister in canisters) {
    process.env[canister.toUpperCase() + "_CANISTER_ID"] =
      canisters[canister][network];
  }
}
initCanisterIds();

const isDevelopment = process.env.NODE_ENV !== "production";
const asset_entry = path.join(
  "src",
  "cert_var_assets",
  "src",
  "index.html"
);

module.exports = {
  target: "web",
  mode: isDevelopment ? "development" : "production",
  entry: {
    // The frontend.entrypoint points to the HTML file for this build, so we need
    // to replace the extension to `.js`.
    index: path.join(__dirname, asset_entry).replace(/\.html$/, ".js"),
  },
  devtool: isDevelopment ? "source-map" : false,
  optimization: {
    minimize: !isDevelopment,
    minimizer: [new TerserPlugin()],
  },
  resolve: {
    extensions: [".js", ".ts", ".jsx", ".tsx"],
    fallback: {
      assert: require.resolve("assert/"),
      buffer: require.resolve("buffer/"),
      events: require.resolve("events/"),
      stream: require.resolve("stream-browserify/"),
      util: require.resolve("util/"),
    },
  },
  output: {
    filename: "index.js",
    path: path.join(__dirname, "dist", "cert_var_assets"),
  },

  // Depending in the language or framework you are using for
  // front-end development, add module loaders to the default
  // webpack configuration. For example, if you are using React
  // modules and CSS as described in the "Adding a stylesheet"
  // tutorial, uncomment the following lines:
  // module: {
  //  rules: [
  //    { test: /\.(ts|tsx|jsx)$/, loader: "ts-loader" },
  //    { test: /\.css$/, use: ['style-loader','css-loader'] }
  //  ]
  // },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.join(__dirname, asset_entry),
      cache: false
    }),
    new webpack.EnvironmentPlugin({
      NODE_ENV: 'development',
      CERT_VAR_CANISTER_ID: canisters["cert_var"]
    }),
    new webpack.ProvidePlugin({
      Buffer: [require.resolve("buffer/"), "Buffer"],
      process: require.resolve("process/browser"),
    }),
  ],
  // proxy /api to port 4943 during development
  devServer: {
    proxy: {
      "/api": {
	static: './',
        target: "http://localhost:4943",
        changeOrigin: true,
        pathRewrite: {
          "^/api": "/api",
        },
      },
    },
    hot: true,
  },
};
```

### Step 5: Create a new file called `server.js` with the following content:

```
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('Hello World!'); // This will serve your request to '/'.
});

app.listen(4943, function () {
  console.log('Example app listening on port 4943!');
 });
```

### Step 6: Replace the content of the `src/cert_var_assets/src/index.html` with the following content:

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <title>Certified Variables</title>
    <base href="/">

    <link type="text/css" rel="stylesheet" href="main.css" />
</head>
<body style="background-color:powderblue; text-align:center;">
  <div>
    <img src="https://global.discourse-cdn.com/business4/uploads/dfn/original/1X/a6d6c5b4e246cd075a009424601bc981b3086fb4.png" alt="DFINITY logo" />
      <div> <label for="name">New value of variable</label>
        <input id="newValue" alt="New Value" type="number" />
        <button id="setBtn">Set and get!</button>
      </div>
      <pre id="var" style="line-height:1;"></pre>
    </div>
   </div>
</body>
</html>
```

### Step 7: Start a local web server that hosts the front-end.

```
npm start
```


### Step 8: Visit the frontend, and interact with the demo there:

```
http://localhost:4943/?canisterId=$CANISTER_ID
```

This should present an entry for "New value of variable", and a button to "Set and get!".

Enter a number and click the button.

[Certified variables](./_attachments/cert-var.png)

The canister updates its certificate, and the frontend checks it. The developer console contains some additional comments about each step.




-->
