---

sidebar_position: 1
---
# フロントエンドのカスタマイズ

## 概要

シンプルなdapp を作成、ビルド、デプロイする方法の基本を理解し、デフォルトのプロジェクトファイルとサンプルのフロントエンドに慣れたところで、プロジェクトのフロントエンドのユーザーエクスペリエンスをカスタマイズするさまざまな方法を試してみましょう。

このガイドでは、React フレームワークを使用して、デフォルトのサンプルdapp の新しいフロントエンドを作成し、表示されるインターフェイスをカスタマイズするための基本的な修正方法を説明します。この後のガイドでは、ここで紹介するテクニックをさらに詳しく説明しますが、CSS、HTML、JavaScript、React、またはその他のフレームワークを使ってユーザーインターフェースを構築する方法をすでに知っている場合は、このガイドを読み飛ばしてもかまいません。

このガイドでは、Reactフレームワークを使用して、canister のDOM（Document Object Model）を管理する方法を説明します。Reactには独自のカスタムDOM構文があるため、JSXで記述されたフロントエンドコードをコンパイルするには、webpackの設定を変更する必要があります。ReactとJSXの使用方法の詳細については、[ReactのWeb](https://reactjs.org/)サイトで[始めるを](https://react.dev/learn)参照してください。

## 前提条件

ガイドを始める前に、以下を確認してください：

- \[x\] フロントエンド開発用に`node.js` がインストールされており、プロジェクトで`npm install` を使用してパッケージをインストールできること。ローカルのオペレーティングシステムとパッケージマネージャにnodeをインストールする方法については、[Nodeの](https://nodejs.org/en/)ウェブサイトを参照してください。

- \[x\][ダウンロードとインストールの](/developer-docs/setup/install/index.mdx)ページで説明されているように、IC SDK パッケージをダウンロードしてインストールしました。

:::info
このガイドでは、IC SDK バージョン`0.8.0` 以降を使用する必要があります。
::：

- \[x\] IDEとしてVisual Studio Codeを使用している場合、[VS Code extensions for IC development](/developer-docs/setup/vs-code.md)の説明に従って、Motoko のVisual Studio Codeプラグインをインストールしました。

- \[x\] ローカルコンピューターで実行されているIC SDKプロセスを停止しました。

## 新規プロジェクトの作成

カスタムフロントエンド用の新しいプロジェクトディレクトリを作成するにはdapp ：

- #### ステップ1: まだ開いていない場合は、ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ2:Internet Computer プロジェクトに使用しているフォルダがあれば、そこに移動します。

- #### ステップ3：以下のコマンドを実行して、`node.js` がローカルにインストールされていることを確認します：
  
  ```
    which node
    which npm
  ```
  
  `node.js` がインストールされていない場合は、次のステップに進む前にダウンロードしてインストールしてください。ローカルのオペレーティングシステムとパッケージマネージャにnodeをインストールする方法については、[Nodeの](https://nodejs.org/en/)ウェブサイトを参照してください。

- #### ステップ 4: 次のコマンドを実行して新しいプロジェクトを作成します：
  
  ```
    dfx new custom_greeting
  ```
  
  `dfx new custom_greeting` コマンドは、新しい`custom_greeting` プロジェクトを作成します。

- #### ステップ 5: 次のコマンドを実行して、プロジェクトディレクトリに移動します：
  
  ```
    cd custom_greeting
  ```

## Reactフレームワークをインストールします。

Reactを使用したことがない場合は、フロントエンドのコードを編集する前に、[React入門](https://reactjs.org/tutorial/tutorial.html)チュートリアルまたは[Reactウェブサイトを](https://reactjs.org/)調べるとよいでしょう。

必要なフレームワークモジュールをインストールします：

- #### ステップ1: 以下のコマンドを実行してReactモジュールをインストールします：
  
  ```
    npm install --save react react-dom
  ```

- #### ステップ2: 次のコマンドを実行して、必要なTypeScript言語コンパイラーローダーをインストールします：
  
  ```
    npm install --save-dev typescript ts-loader
  ```
  
  これらのモジュールをインストールする代わりに、デフォルトの`package.json` ファイルを編集して、[次の](_attachments/custom-frontend-package.json)ようにプロジェクトの依存関係を追加することもできます。

## デフォルトの設定を確認

このガイドでReactを使用するために変更を加える前に、プロジェクトの`dfx.json` 設定ファイルのデフォルトのフロントエンド設定を確認しましょう。

デフォルトの`dfx.json` 設定ファイルを確認するには：

- #### ステップ1:`dfx.json` 設定ファイルをテキストエディタで開きます。

- #### ステップ2:`canisters` キーが`custom_greeting_frontend` canister の設定を含んでいることに注意してください。
  
  ```
    {
      "canisters": {
        ...
  
        "custom_greeting_frontend": {
          "dependencies": [
            "custom_greeting_backend"
          ],
          "frontend": {
            "entrypoint": "src/custom_greeting_frontend/src/index.html"
          },
          "source": [
            "src/custom_greeting_frontend/assets",
            "dist/custom_greeting_frontend/"
          ],
          "type": "assets"
        }
      }
    }
  ```

#### これが何をするのか

このセクションの設定を見てみましょう。

- canister `custom_greeting_frontend`プロジェクトのフロントエンドアセットはcanister にコンパイルされます。

- アセットcanister はデフォルトでプロジェクトのメインcanister に依存します。

- `frontend.entrypoint` の設定では、dapp のエントリポイントとして使用するファイル（この場合は`index.html` ファイル）へのパスを指定します。別の開始点、たとえばカスタム`first-page.html` ファイルがある場合は、この設定を変更します。

- `source` 設定は、`src` および`dist` ディレクトリへのパスを指定します。`src` 設定は、プロジェクトのビルド時にアセットcanister に含まれる静的アセットに使用するディレクトリを指定します。カスタムのカスケーディングスタイルシート（CSS）やJavaScriptファイルがある場合、このパスで指定されたフォルダにそれらを含めることになります。プロジェクトのビルド後、プロジェクトアセットは`dist` 設定で指定されたディレクトリから提供されます。

- `type` 設定は、`custom_greeting_frontend` が、IC 上で静的アセットをホストするために必要なものがすべて付属している[認定アセットcanister](https://github.com/dfinity/certified-assets) を使用するように指定します。

このガイドでは、`index.jsx` ファイルに React JavaScript を追加しますが、`dfx.json` ファイルのデフォルト設定を変更する必要はありません。

- #### ステップ3:`dfx.json` ファイルを閉じて続行します。

## デフォルトのフロントエンドファイルを確認

このガイドでは、カスタムフロントエンドを通してデフォルトの`main.mo` canister を呼び出します。変更を加える前に、プロジェクトのデフォルトフロントエンドファイルの中身を見てみましょう。

デフォルトのフロントエンドファイルを確認するには

- #### ステップ1:`src/custom_greeting_frontend/src/index.html` ファイルをテキストエディタで開いてください。
  
  このテンプレートファイルは`dfx.json` ファイルの`frontend.entrypoint` 設定で指定されたdapp のデフォルトフロントエンドエントリーポイントです。
  
  このファイルには、`src/custom_greeting_frontend/assets` ディレクトリにある CSS ファイルと画像を参照する標準 HTML が含まれています。デフォルトの`index.html` ファイルには、`name` 引数の入力フィールドとクリック可能なボタンを表示するための標準 HTML 構文も含まれています。
  
  これは[Motoko のデフォルトフロントエンドの](/developer-docs/backend/motoko/explore-templates.md#default-frontend) [例と](/developer-docs/backend/motoko/explore-templates.md#default-frontend)同じです。

- #### ステップ2:`src/custom_greeting_frontend/src/index.js` ファイルをテキストエディタで開いてください。

このファイルにはデフォルトで次のコードが含まれています：

```
    import { custom_greeting_backend } from "../../declarations/custom_greeting_backend";

    document.querySelector("form").addEventListener("submit", async (e) => {
            e.preventDefault();
            const button = e.target.querySelector("button");

            const name = document.getElementById("name").value.toString();

            button.setAttribute("disabled", true);

            // Interact with foo actor, calling the greet method
            const greeting = await custom_greeting_backend.greet(name);

            button.removeAttribute("disabled");

            document.getElementById("greeting").innerText = greeting;

            return false;
    });
```

#### これは何をするのか

- `import` ステートメントはactor を指しており、`custom_greeting_backend` canister を呼び出すことができます。`"../declarations"`

- 宣言はまだ作成されていませんが、これについては後ほど説明します。

- #### ステップ3.  続行するには、`index.js` ファイルを閉じます。

## フロントエンドのファイルの修正

これで、デフォルトのdapp 用に新しいフロントエンドを作成する準備ができました。

フロントエンドのファイルを準備します：

- #### ステップ 1: webpack 設定ファイル (`webpack.config.js`) をテキストエディタで開きます。

- #### ステップ 2: デフォルトの`index.html` を`index.jsx` に置き換えるために、フロントエンドのエントリを修正します。

:::caution
次の例は、より大きなコードファイルの一部である**コードスニペット**です。このスニペットは、単独で実行するとエラーを返すかもしれません。
::：

```
    entry: {
      // The frontend.entrypoint points to the HTML file for this build, so we need
      // to replace the extension to `.js`.
      index: path.join(__dirname, asset_entry).replace(/\.html$/, ".jsx"),
    },
```

- #### ステップ3：`plugins` セクションの上に、次の`module` キーを追加します：

:::caution
次の例は、より大きなコード・ファイルの一部である**コード・スニペット**です。このスニペットは、単独で実行するとエラーを返すことがあります。
::：

```
    module: {
      rules: [
        { test: /\.(js|ts)x?$/, loader: "ts-loader" }
      ]
    },

This setting enables the project to use the `ts-loader` compiler for a React JavaScript `index.jsx` file. Note that there’s a commented section in the default `webpack.config.js` file that you can modify to add the `module` key.
```

終了すると、`webpack.config.js` ファイルは次のような内容になっているはずです：

    require("dotenv").config();
    const path = require("path");
    const webpack = require("webpack");
    const HtmlWebpackPlugin = require("html-webpack-plugin");
    const TerserPlugin = require("terser-webpack-plugin");
    const CopyPlugin = require("copy-webpack-plugin");
    
    const isDevelopment = process.env.NODE_ENV !== "production";
    
    const frontendDirectory = "custom_greeting_frontend";
    
    const frontend_entry = path.join("src", frontendDirectory, "src", "index.html");
    
    module.exports = {
      target: "web",
      mode: isDevelopment ? "development" : "production",
            entry: {
              // The frontend.entrypoint points to the HTML file for this build, so we need
              // to replace the extension to `.js`.
              index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
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
        path: path.join(__dirname, "dist", frontendDirectory),
      },
      module: {
        rules: [
          { test: /\.(js|ts)x?$/, loader: "ts-loader" }
        ]
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
          template: path.join(__dirname, frontend_entry),
          cache: false,
        }),
        new webpack.EnvironmentPlugin([
          ...Object.keys(process.env).filter((key) => {
            if (key.includes("CANISTER")) return true;
            if (key.includes("DFX")) return true;
            return false;
          }),
        ]),
        new webpack.ProvidePlugin({
          Buffer: [require.resolve("buffer/"), "Buffer"],
          process: require.resolve("process/browser"),
        }),
        new CopyPlugin({
          patterns: [
            {
              from: `src/${frontendDirectory}/src/.ic-assets.json*`,
              to: ".ic-assets.json5",
              noErrorOnMissing: true,
            },
          ],
        }),
      ],
      // proxy /api to port 4943 during development.
      // if you edit dfx.json to define a project-specific local network, change the port to match.
      devServer: {
        proxy: {
          "/api": {
            target: "http://127.0.0.1:4943",
            changeOrigin: true,
            pathRewrite: {
              "^/api": "/api",
            },
          },
        },
        static: path.resolve(__dirname, "src", frontendDirectory, "assets"),
        hot: true,
        watchFiles: [path.resolve(__dirname, "src", frontendDirectory)],
        liveReload: true,
      },
    };

- #### ステップ 4: プロジェクトのルート・ディレクトリに`tsconfig.json` という名前の新しいファイルを作成します。

- #### ステップ5：`tsconfig.json` ファイルをテキストエディタで開き、このコードをコピーしてファイルに貼り付けます：

<!-- end list -->

    {
        "compilerOptions": {
          "target": "es2018",        /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
          "lib": ["ES2018", "DOM"],  /* Specify library files to be included in the compilation. */
          "allowJs": true,           /* Allow javascript files to be compiled. */
          "jsx": "react",            /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
        },
        "include": ["src/**/*"],
    }

- #### ステップ 6: 変更を保存し、`tsconfig.json` ファイルを閉じます。

- #### ステップ 7: デフォルトの`src/custom_greeting_frontend/src/index.js` ファイルをテキストエディタで開き、そのファイル内のすべてを削除します。

- #### ステップ8：このコードをコピーして、`index.js` ファイルに貼り付けます。

<!-- end list -->

    import * as React from "react";
    import { render } from "react-dom";
    import { custom_greeting_backend } from "../../declarations/custom_greeting_backend";
    
    const MyHello = () => {
      const [name, setName] = React.useState('');
      const [message, setMessage] = React.useState('');
    
      async function doGreet() {
        const greeting = await custom_greeting_backend.greet(name);
        setMessage(greeting);
      }
    
      return (
        <div style=$$$${{ "fontSize": "30px" }}>
          <div style=$$$${{ "backgroundColor": "yellow" }}>
            <p>Greetings, from DFINITY!</p>
            <p>
              {" "}
              Type your message in the Name input field, then click{" "}
              <b> Get Greeting</b> to display the result.
            </p>
          </div>
          <div style=$$$${{ margin: "30px" }}>
            <input
              id="name"
              value={name}
              onChange={(ev) => setName(ev.target.value)}
            ></input>
            <button onClick={doGreet}>Get Greeting!</button>
          </div>
          <div>
            Greeting is: "
            <span style=$$$${{ color: "blue" }}>{message}</span>"
          </div>
        </div>
      );
    };
    
    render(<MyHello />, document.getElementById("app"));

- #### ステップ9：以下のコマンドを実行して、変更した`index.js` ファイルの名前を`index.jsx` に変更します：
  
  ```
    mv src/custom_greeting_frontend/src/index.js src/custom_greeting_frontend/src/index.jsx
  ```

- #### ステップ10：`webpack.config.js` のエントリーポイントを以下のように変更します：

:::caution
次の例は、より大きなコード・ファイルの一部である**コード・スニペット**です。このスニペットは、単独で実行するとエラーを返すことがあります。
::：

```
    entry: {
      // The frontend.entrypoint points to the HTML file for this build, so we need
      // to replace the extension to `.js`.
      index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
    },
```

- #### ステップ 11: デフォルトの`src/custom_greeting_frontend/src/index.html` ファイルをテキストエディタで開き、本文の内容を以下のように置き換えます：
  
  ```
    <!doctype html>
    <html lang="en">
     <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width">
        <title>custom_greeting</title>
        <base href="/">
        <link type="text/css" rel="stylesheet" href="main.css" />
     </head>
     <body>
        <div id="app"></div>
     </body>
    </html>
  ```

## ローカルのcanister 実行環境を起動します。

`custom_greeting` プロジェクトをビルドする前に、ライブICか、開発環境のローカルで動作しているcanister 実行環境に接続する必要があります。

ローカルで環境を起動するには

- #### ステップ 1: ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

- #### ステップ2: 必要であれば、プロジェクトのルート・ディレクトリに移動します。

- #### ステップ 3: 以下のコマンドを実行して、ローカル・コンピューター上でローカルcanister 実行環境を起動します：
  
  ```
    dfx start --background
  ```

ローカルのcanister 実行環境の起動操作が完了したら、次のステップに進みます。

## の登録、ビルド、デプロイを行います。dapp

ローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

dapp をローカルにデプロイするには：

- #### ステップ 1: 必要であれば、プロジェクトのルート・ディレクトリにいることを確認します。

- #### ステップ 2: 以下のコマンドを実行して、dapp を登録、ビルド、デプロイします：
  
  ```
    dfx deploy
  ```

`dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。

## 新しいフロントエンドの表示

ブラウザでcanister アセットのcanister 識別子を入力することで、デフォルトのdapp の新しいフロントエンドにアクセスできます。

カスタムフロントエンドを見るには

- #### ステップ1: ターミナルの新しいタブかウィンドウを開き、実行します：
  
  ```
    npm start
  ```

- #### ステップ 2: ブラウザを開き、`http://localhost:4943` に移動します。

- #### ステップ3：挨拶を入力するよう促されることを確認します。
  
  例えば
  
  ![Sample frontend](_attachments/react-greeting.png)

- #### ステップ4： 入力フィールドの**Nameを**表示したいテキストに置き換えてから、**Get Greetingを**クリックして結果を確認します。
  
  例えば
  
  ![Greeting result](_attachments/greeting-response.png)

## フロントエンドの修正と変更のテスト

フロントエンドを見た後、いくつかの変更をしたくなるかもしれません。

フロントエンドを修正するには

- #### ステップ 1:`index.jsx` ファイルをテキストエディタで開き、スタイル設定を変更します。

たとえば、フォントファミリーを変更したり、入力フィールドにプレースホルダーを使ったりしたいでしょう：

    import * as React from "react";
    import { render } from "react-dom";
    import { custom_greeting_backend } from "../../declarations/custom_greeting_backend/index.js";
    
    const MyHello = () => {
      const [name, setName] = React.useState('');
      const [message, setMessage] = React.useState('');
    
      async function doGreet() {
        const greeting = await custom_greeting_backend.greet(name);
        setMessage(greeting);
      }
    
      return (
        <div style=$$$${{ "fontFamily": "sans-serif" }}>
          <div style=$$$${{ "fontSize": "30px" }}>
            <p>Greetings, from DFINITY!</p>
            <p>
              {" "}
              Type your message in the Name input field, then click{" "}
              <b> Get Greeting</b> to display the result.
            </p>
          </div>
          <div style=$$$${{ margin: "30px" }}>
            <input
              id="name"
              placeholder="Type text here"
              value={name}
              onChange={(ev) => setName(ev.target.value)}
            ></input>
            <button onClick={doGreet}>Get Greeting!</button>
          </div>
          <div>
            Greeting is: "
            <span style=$$$${{ color: "green" }}>{message}</span>"
          </div>
        </div>
      );
    };
    
    render(<MyHello />, document.getElementById("app"));

- #### ステップ 2: ファイルを保存し、ブラウザで更新されたページを表示します。
  
  例えば
  
  ![Modified styles on your entry page](_attachments/revised-greeting.png)

- #### ステップ3: 新しいメッセージを入力し、新しい挨拶を表示します。例えば
  
  ![Modified greeting result](_attachments/modified-result.png)

## ローカルのcanister 実行環境を停止

dapp のフロントエンドの実験が終わったら、ローカルのcanister 実行環境を停止して、バックグラウンドで実行し続けないようにすることができます。

ローカル・ネットワークを停止するには

- #### ステップ1: webpack開発サーバーが表示されているターミナルで、Control-Cを押してdev-serverを中断します。

- #### ステップ2：ネットワーク操作が表示されているターミナルで、Control-Cを押してローカルネットワークのプロセスを中断します。

- #### ステップ 3: 次のコマンドを実行して、ローカルのcanister 実行環境を停止します：
  
  ```
    dfx stop
  ```

<!---


# Customizing the frontend

## Overview

Now that you have a basic understanding of how to create, build, and deploy a simple dapp and are familiar with the default project files and sample frontend, you might want to start experimenting with different ways to customize the frontend user experience for your project.

This guide illustrates using the React framework to create a new frontend for the default sample dapp and guides you through some basic modifications to customize the interface displayed. Later guides expand on the techniques introduced here, but if you already know how to use CSS, HTML, JavaScript, and React or other frameworks to build your user interface, you can skip this guide.

This guide illustrates using the React framework to manage the Document Object Model (DOM) for your canister. Because React has its own custom DOM syntax, you need to modify the webpack configuration to compile the frontend code, which is written in JSX. For more information about learning to use React and JSX, see [getting started](https://react.dev/learn) on the [React website](https://reactjs.org/).

## Prerequisites

Before starting the guide, verify the following:

-  [x] You have `node.js` installed for frontend development and can install packages using `npm install` in your project. For information about installing node for your local operating system and package manager, see the [Node](https://nodejs.org/en/) website.

-  [x] You have downloaded and installed the IC SDK package as described in the [download and install](/developer-docs/setup/install/index.mdx) page.

:::info
This guide requires you to use the IC SDK version `0.8.0` or later.
:::

-  [x] You have installed the Visual Studio Code plugin for Motoko as described in [VS Code extensions for IC development](/developer-docs/setup/vs-code.md) if you are using Visual Studio Code as your IDE.

-  [x] You have stopped any IC SDK processes running on the local computer.


## Create a new project

To create a new project directory for your custom frontend dapp:

- #### Step 1:  Open a terminal shell on your local computer, if you don’t already have one open.

- #### Step 2:  Change to the folder you are using for your Internet Computer projects, if you are using one.

- #### Step 3:  Check that you have `node.js` installed locally by running the following commands:

        which node
        which npm

    If you don’t have `node.js` installed, you should download and install it before continuing to the next step. For information about installing node for your local operating system and package manager, see the [Node](https://nodejs.org/en/) website.

- #### Step 4:  Create a new project by running the following command:

        dfx new custom_greeting

    The `dfx new custom_greeting` command creates a new `custom_greeting` project.

- #### Step 5:  Change to your project directory by running the following command:

        cd custom_greeting

## Install the React framework

If you’ve never used React before, you might want to explore the [intro to React](https://reactjs.org/tutorial/tutorial.html) tutorial or the [React website](https://reactjs.org/) before editing the frontend code.

To install required framework modules:

- #### Step 1:  Install the React module by running the following command:

        npm install --save react react-dom

- #### Step 2:  Install the required TypeScript language compiler loader by running the following command:

        npm install --save-dev typescript ts-loader

    As an alternative to installing these modules, you can edit the default `package.json` file to add dependencies for your project like [this](_attachments/custom-frontend-package.json).

## Review the default configuration

Before we make any changes to use React for this guide, let’s review the default frontend settings in the `dfx.json` configuration file for your project.

To review the default `dfx.json` configuration file:

- #### Step 1:  Open the `dfx.json` configuration file in a text editor.

- #### Step 2:  Notice that the `canisters` key includes settings for a `custom_greeting_frontend` canister.

        {
          "canisters": {
            ...

            "custom_greeting_frontend": {
              "dependencies": [
                "custom_greeting_backend"
              ],
              "frontend": {
                "entrypoint": "src/custom_greeting_frontend/src/index.html"
              },
              "source": [
                "src/custom_greeting_frontend/assets",
                "dist/custom_greeting_frontend/"
              ],
              "type": "assets"
            }
          }
        }

#### What this does
  Let’s take a look at the settings in this section.

  -   Frontend assets for your project are compiled into their own canister, in this case, a canister named `custom_greeting_frontend`.

  -   The assets canister has a default dependency on the main canister for the project.

  -   The `frontend.entrypoint` setting specifies the path to a file—in this case, the `index.html` file—to use as your dapp entry point. If you had a different starting point—for example, a custom `first-page.html` file—you would modify this setting.

  -   The `source` settings specify the path to your `src` and `dist` directories. The `src` setting specifies the directory to use for static assets that will be included in your assets canister when you build your project. If you have custom cascading stylesheet (CSS) or JavaScript files, you would include them in the folder specified by this path. After building the project, the project assets are served from the directory specified by the `dist` setting.

  -   The `type` setting specifies that the `custom_greeting_frontend` should use the [certified asset canister](https://github.com/dfinity/certified-assets), which comes with everything you need to host static assets on the IC.

  For this guide, we are going to add React JavaScript in an `index.jsx` file, but that won’t require any changes to the default settings in the `dfx.json` file.

- #### Step 3:  Close the `dfx.json` file to continue.

## Review the default frontend files

For this guide, you are going to make calls to the default `main.mo` canister through a custom frontend. Before you make any changes, though, let’s take a look at what’s in the default frontend files for a project.

To review the default frontend files:

- #### Step 1:  Open the `src/custom_greeting_frontend/src/index.html` file in a text editor.

    This template file is the default frontend entry point for the dapp as specified by the `frontend.entrypoint` setting in the `dfx.json` file.

    This file contains standard HTML with references to a CSS file and an image that are located in the `src/custom_greeting_frontend/assets` directory. The default `index.html` file also includes standard HTML syntax for displaying an input field for the `name` argument and a clickable button.

    This is the same default frontend in [Motoko default frontend example](/developer-docs/backend/motoko/explore-templates.md#default-frontend).

- #### Step 2:  Open the `src/custom_greeting_frontend/src/index.js` file in a text editor.

This file by default will contain the following piece of code:

        import { custom_greeting_backend } from "../../declarations/custom_greeting_backend";

        document.querySelector("form").addEventListener("submit", async (e) => {
                e.preventDefault();
                const button = e.target.querySelector("button");

                const name = document.getElementById("name").value.toString();

                button.setAttribute("disabled", true);

                // Interact with foo actor, calling the greet method
                const greeting = await custom_greeting_backend.greet(name);

                button.removeAttribute("disabled");

                document.getElementById("greeting").innerText = greeting;

                return false;
        });

#### What this does
- The `import` statement points to an actor that will allow us to make calls to our `custom_greeting_backend` canister from `"../declarations"`

-  The declarations haven’t been created yet, but we will come back to that.

- #### Step 3.  Close the `index.js` file to continue.

## Modify the frontend files

You are now ready to create a new frontend for the default dapp.

To prepare the frontend files:

- #### Step 1:  Open the webpack configuration file (`webpack.config.js`) in a text editor.

- #### Step 2:  Modify the frontend entry to replace the default `index.html` with `index.jsx`.

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own.
:::

        entry: {
          // The frontend.entrypoint points to the HTML file for this build, so we need
          // to replace the extension to `.js`.
          index: path.join(__dirname, asset_entry).replace(/\.html$/, ".jsx"),
        },

- #### Step 3:  Add the following `module` key above the `plugins` section:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own.
:::

        module: {
          rules: [
            { test: /\.(js|ts)x?$/, loader: "ts-loader" }
          ]
        },

    This setting enables the project to use the `ts-loader` compiler for a React JavaScript `index.jsx` file. Note that there’s a commented section in the default `webpack.config.js` file that you can modify to add the `module` key.

When finished, your `webpack.config.js` file should contain the following content:

```
require("dotenv").config();
const path = require("path");
const webpack = require("webpack");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
const CopyPlugin = require("copy-webpack-plugin");

const isDevelopment = process.env.NODE_ENV !== "production";

const frontendDirectory = "custom_greeting_frontend";

const frontend_entry = path.join("src", frontendDirectory, "src", "index.html");

module.exports = {
  target: "web",
  mode: isDevelopment ? "development" : "production",
        entry: {
          // The frontend.entrypoint points to the HTML file for this build, so we need
          // to replace the extension to `.js`.
          index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
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
    path: path.join(__dirname, "dist", frontendDirectory),
  },
  module: {
    rules: [
      { test: /\.(js|ts)x?$/, loader: "ts-loader" }
    ]
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
      template: path.join(__dirname, frontend_entry),
      cache: false,
    }),
    new webpack.EnvironmentPlugin([
      ...Object.keys(process.env).filter((key) => {
        if (key.includes("CANISTER")) return true;
        if (key.includes("DFX")) return true;
        return false;
      }),
    ]),
    new webpack.ProvidePlugin({
      Buffer: [require.resolve("buffer/"), "Buffer"],
      process: require.resolve("process/browser"),
    }),
    new CopyPlugin({
      patterns: [
        {
          from: `src/${frontendDirectory}/src/.ic-assets.json*`,
          to: ".ic-assets.json5",
          noErrorOnMissing: true,
        },
      ],
    }),
  ],
  // proxy /api to port 4943 during development.
  // if you edit dfx.json to define a project-specific local network, change the port to match.
  devServer: {
    proxy: {
      "/api": {
        target: "http://127.0.0.1:4943",
        changeOrigin: true,
        pathRewrite: {
          "^/api": "/api",
        },
      },
    },
    static: path.resolve(__dirname, "src", frontendDirectory, "assets"),
    hot: true,
    watchFiles: [path.resolve(__dirname, "src", frontendDirectory)],
    liveReload: true,
  },
};
```

- #### Step 4:  Create a new file named `tsconfig.json` in the root directory for your project.

- #### Step 5:  Open the `tsconfig.json` file in a text editor, then copy and paste this code into the file:

```
{
    "compilerOptions": {
      "target": "es2018",        /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
      "lib": ["ES2018", "DOM"],  /* Specify library files to be included in the compilation. */
      "allowJs": true,           /* Allow javascript files to be compiled. */
      "jsx": "react",            /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    },
    "include": ["src/**/*"],
}
```

- #### Step 6:  Save your changes and close the `tsconfig.json` file to continue.

- #### Step 7:  Open the default `src/custom_greeting_frontend/src/index.js` file in a text editor and delete everything in that file.

- #### Step 8:  Copy and paste this code into the `index.js` file.

```
import * as React from "react";
import { render } from "react-dom";
import { custom_greeting_backend } from "../../declarations/custom_greeting_backend";

const MyHello = () => {
  const [name, setName] = React.useState('');
  const [message, setMessage] = React.useState('');

  async function doGreet() {
    const greeting = await custom_greeting_backend.greet(name);
    setMessage(greeting);
  }

  return (
    <div style={{ "fontSize": "30px" }}>
      <div style={{ "backgroundColor": "yellow" }}>
        <p>Greetings, from DFINITY!</p>
        <p>
          {" "}
          Type your message in the Name input field, then click{" "}
          <b> Get Greeting</b> to display the result.
        </p>
      </div>
      <div style={{ margin: "30px" }}>
        <input
          id="name"
          value={name}
          onChange={(ev) => setName(ev.target.value)}
        ></input>
        <button onClick={doGreet}>Get Greeting!</button>
      </div>
      <div>
        Greeting is: "
        <span style={{ color: "blue" }}>{message}</span>"
      </div>
    </div>
  );
};

render(<MyHello />, document.getElementById("app"));
```

- #### Step 9:  Rename the modified `index.js` file as `index.jsx` by running the following command:

        mv src/custom_greeting_frontend/src/index.js src/custom_greeting_frontend/src/index.jsx
    
    
- #### Step 10: Change the entry point in `webpack.config.js` to the following:

:::caution
The following example is a **code snippet** that is part of a larger code file. This snippet may return an error if run on its own.
:::

        entry: {
          // The frontend.entrypoint points to the HTML file for this build, so we need
          // to replace the extension to `.js`.
          index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
        },

- #### Step 11: Open the default `src/custom_greeting_frontend/src/index.html` file in a text editor, then replace the body contents with the following:

        <!doctype html>
        <html lang="en">
         <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width">
            <title>custom_greeting</title>
            <base href="/">
            <link type="text/css" rel="stylesheet" href="main.css" />
         </head>
         <body>
            <div id="app"></div>
         </body>
        </html>

## Start the local canister execution environment

Before you can build the `custom_greeting` project, you need to connect to either the live IC, or a canister execution environment running locally in your development environment.

To start the environment locally:

- #### Step 1:  Open a new terminal window or tab on your local computer.

- #### Step 2:  Navigate to the root directory for your project, if necessary.

- #### Step 3:  Start the local canister execution environment on your local computer by running the following command:

        dfx start --background

After the local canister execution environment completes its startup operations, you can continue to the next step.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment, you can register, build, and deploy your dapp locally.

To deploy the dapp locally:

- #### Step 1:  Check that you are still in the root directory for your project, if needed.

- #### Step 2:  Register, build, and deploy your dapp by running the following command:

        dfx deploy

The `dfx deploy` command output displays information about the operations it performs.

## View the new frontend

You can now access the new frontend for the default dapp by entering the canister identifier for the assets canister in a browser.

To view the custom frontend:

- #### Step 1:  Open a new tab or window of your terminal and run:

        npm start

- #### Step 2:  Open a browser and navigate to `http://localhost:4943`.

- #### Step 3:  Verify that you are prompted to type a greeting.

    For example:

    ![Sample frontend](_attachments/react-greeting.png)

- #### Step 4:  Replace **Name** in the input field with the text you want to display, then click **Get Greeting** to see the result.

    For example:

    ![Greeting result](_attachments/greeting-response.png)

## Revise the frontend and test your changes

After viewing the frontend, you might want to make some changes.

To modify the frontend:

- #### Step 1:  Open the `index.jsx` file in a text editor and modify its style settings. 
For example, you might want to change the font family and use a placeholder for the input field by making changes similar to this:

```
import * as React from "react";
import { render } from "react-dom";
import { custom_greeting_backend } from "../../declarations/custom_greeting_backend/index.js";

const MyHello = () => {
  const [name, setName] = React.useState('');
  const [message, setMessage] = React.useState('');

  async function doGreet() {
    const greeting = await custom_greeting_backend.greet(name);
    setMessage(greeting);
  }

  return (
    <div style={{ "fontFamily": "sans-serif" }}>
      <div style={{ "fontSize": "30px" }}>
        <p>Greetings, from DFINITY!</p>
        <p>
          {" "}
          Type your message in the Name input field, then click{" "}
          <b> Get Greeting</b> to display the result.
        </p>
      </div>
      <div style={{ margin: "30px" }}>
        <input
          id="name"
          placeholder="Type text here"
          value={name}
          onChange={(ev) => setName(ev.target.value)}
        ></input>
        <button onClick={doGreet}>Get Greeting!</button>
      </div>
      <div>
        Greeting is: "
        <span style={{ color: "green" }}>{message}</span>"
      </div>
    </div>
  );
};

render(<MyHello />, document.getElementById("app"));
```

- #### Step 2:  Save the file and view the updated page in your browser.

    For example:

    ![Modified styles on your entry page](_attachments/revised-greeting.png)

- #### Step 3:  Type a new message and see your new greeting. For example:

    ![Modified greeting result](_attachments/modified-result.png)

## Stop the local canister execution environment

After you finish experimenting with the frontend for your dapp, you can stop the local canister execution environment so that it doesn’t continue running in the background.

To stop the local network:

- #### Step 1:  In the terminal that displays the webpack development server, press Control-C to interrupt the dev-server.

- #### Step 2:  In the terminal that displays network operations, press Control-C to interrupt the local network process.

- #### Step 3:  Stop the local canister execution environment by running the following command:

        dfx stop

-->
