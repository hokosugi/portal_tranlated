---

sidebar_position: 2
---
# スタイルシートの追加

## 概要

カスケーディングスタイルシートはフロントエンドのユーザーインターフェイスの重要な部分です。デフォルトのスターターは、編集可能な`main.css` ファイルをインポートするように設定されていますが、スタイルシートをJavaScriptファイルにインポートしたり、Syntactically Awesome Style Sheets、別名SCSSのような別の形式を使用することを好むかもしれません。このガイドでは、スタイルシートをインポートするようにwebpackを設定する方法を、contactdapp の構築を通して説明します。すでにwebpackベースのプロジェクトにカスケーディングスタイルシート（CSS）を追加する方法を知っている場合は、このガイドをスキップすることができます。

このガイドでは、Reactフレームワークを使用してフロントエンドのDOM（Document Object Model）を管理する方法を説明します。Reactには独自のカスタムDOM構文があるため、JSXで記述されたフロントエンドコードをコンパイルするためにwebpackの設定を変更する必要があります。React と JSX の使用方法の詳細については、[React の Web サイト](https://reactjs.org/)で[始めるを](https://react.dev/learn)参照してください。

## 前提条件

ガイドを始める前に、以下を確認してください：

- \[x\] フロントエンド開発用に`node.js` がインストールされており、プロジェクトで`npm install` を使用してパッケージをインストールできること。ローカルのオペレーティングシステムとパッケージマネージャにnodeをインストールする方法については、[Nodeの](https://nodejs.org/en/)ウェブサイトを参照してください。

- \[x\][ダウンロードとインストールの](/developer-docs/setup/install/index.mdx)ページで説明されているように、IC SDK パッケージをダウンロードしてインストールしました。

:::info
このガイドでは、IC SDK バージョン`0.8.0` 以降を使用する必要があります。
::：

- \[x\] IDEとしてVisual Studio Codeを使用している場合、[VS Code extensions for IC development](/developer-docs/setup/vs-code.md)の説明に従って、Motoko のVisual Studio Codeプラグインをインストールしました。

- \[x\] ローカルコンピュータで実行されているローカルcanister 実行環境プロセスを停止しました。

## 新規プロジェクトの作成

カスタムフロントエンド用の新しいプロジェクトディレクトリを作成するにはdapp ：

- #### ステップ 1: まだ開いていない場合は、ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ2:Internet Computer プロジェクトに使用しているフォルダがあれば、そこに移動します。

- #### ステップ 3: 必要であれば、`node.js` がローカルにインストールされていることを確認します。

- #### ステップ4：次のコマンドを実行して、新しいプロジェクトを作成します：
  
  ```
    dfx new contacts
  ```

- #### ステップ 5: 以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：
  
  ```
    cd contacts
  ```

## Reactフレームワークのインストール

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

- #### ステップ 3: 次のコマンドを実行して、必要なスタイル・ローダーをインストールします：
  
  ```
    npm install --save-dev style-loader css-loader
  ```
  
  `npm install` 、脆弱性が報告された場合は、`npm audit fix` 、報告された脆弱性の修正を試みてください。
  
  これらのモジュールをインストールする代わりに、デフォルトの`package.json` ファイルを編集して、プロジェクトの依存関係を追加することもできます。例えば、このようにします：

<!-- end list -->

```
    {
        "name": "contacts_assets",
        "version": "0.1.0",
        "description": "",
        "keywords": [],
        "scripts": {
        "build": "webpack"
        },
        "devDependencies": {
        "assert": "2.0.0",
        "buffer": "6.0.3",
        "css-loader": "^5.2.1",
        "events": "3.3.0",
        "html-webpack-plugin": "5.3.1",
        "process": "0.11.10",
        "stream-browserify": "3.0.0",
        "style-loader": "^2.0.0",
        "terser-webpack-plugin": "5.1.1",
        "ts-loader": "^8.1.0",
        "typescript": "^4.2.4",
        "util": "0.12.3",
        "webpack-cli": "4.5.0",
        "webpack": "5.24.4"
        },
        "dependencies": {
        "@dfinity/agent": "0.10.0",
        "@dfinity/candid": "0.10.0",
        "@dfinity/principal": "0.10.0",
        "react-dom": "^17.0.2",
        "react": "^17.0.2"
        }
    }
```

    The version of the JavaScript agent in this example `package.json` file is `0.10.0`. In most cases, however, you would want to use the latest version of the agent available. When you create a new project, the `dfx new` command automatically retrieves the latest version of the JavaScript agent for you. You can also manually retrieve the latest version after creating a project by running the `npm install --save @dfinity/agent` command.

## デフォルトのプログラムを修正

このガイドでは、連絡先情報を保存して検索できるようにするコードをメイン・プログラムに追加します。

デフォルト・プログラムを変更するには

- #### ステップ 1:`src/contacts_backend/main.mo` ファイルをテキストエディタで開き、既存の内容を削除します。

- #### ステップ2: このコードをコピーして、ファイルに貼り付けます：

<!-- end list -->

    import List "mo:base/List";
    import AssocList "mo:base/AssocList";
    
    actor Contact {
    
      var contacts : ContactsMap = List.nil();
    
      type Name = Text;
      type Phone = Nat;
    
      type Entry = {
        name : Name;
        address1 : Text;
        address2 : Text;
        email : Text;
        phone : Phone;
      };
    
      type ContactsMap = AssocList.AssocList<Name, Entry>;
    
      func nameEq(lhs : Name, rhs : Name) : Bool {
        return lhs == rhs;
      };
    
      public func insert(name : Name, address1 : Text, address2 : Text, email : Text, phone : Phone) : async () {
         let newEntry : Entry = {
           name;
           address1;
           address2;
           email;
           phone;
         };
    
         let (newContacts, _) = AssocList.replace(
           contacts,
           name,
           func(n: Name, m: Name) : Bool { n == m },
           ?newEntry
         );
         contacts := newContacts;
      };
    
      public query func lookup(name : Name) : async ?Entry {
        return AssocList.find(contacts, name, nameEq);
      };
    };

- #### ステップ 3: 変更を保存し、`main.mo` ファイルを閉じます。

## フロントエンドファイルの修正

これで、プログラムの新しいフロントエンドを作成する準備ができました。

- #### ステップ1: webpackの設定ファイル(`webpack.config.js`)をテキストエディタで開きます。

- #### ステップ 2: フロントエンドのエントリを修正して、デフォルトの index.html を index.jsx に置き換えます。
  
  ```
    entry: {
      // The frontend.entrypoint points to the HTML file for this build, so we need
      // to replace the extension to `.js`.
      index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
    },
  ```

- #### ステップ3:`plugins` セクションの上にある`module` キーのコメント例を探し、次の行のコメントを外します：
  
  ```
    module: {
      rules: [
        { test: /\.(js|ts)x?$/, loader: "ts-loader" },
        { test: /\.css$/, use: ['style-loader','css-loader'] }
      ]
    },
  ```
  
  これらの設定により、プログラムが`ts-loader` コンパイラを使用し、CSS ファイルをインポートできるようになります。

:::info
注意:`.scss` または`.sass` ファイルのサポートを追加したい場合は、`sass-loader` をインストールする必要があります：

```
    npm install --save react react-dom

and then add this additional rule beneath the `css-loader` rule in `webpack.config.js`:

    module: {
      rules: [
        // ...
        {
          test: /\.s[ac]ss$/i,
          use: [
            // Creates `style` nodes from JS strings
            "style-loader",
            // Translates CSS into CommonJS
            "css-loader",
            // Compiles Sass to CSS
            "sass-loader",
          ],
        },
      ]
    },
```

:::

- #### ステップ 4: 変更を保存し、`webpack.config.js` ファイルを閉じて続行します。

- #### ステップ 5: プロジェクトのルート・ディレクトリに`tsconfig.json` という名前の新しいファイルを作成します。

- #### ステップ6：`tsconfig.json` ファイルをテキストエディタで開き、このコードをコピーしてファイルに貼り付けます：

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

- #### ステップ 7: 変更を保存して、`tsconfig.json` ファイルを閉じます。

## プロジェクトにスタイルシートを追加

これで、新しいカスケーディング・スタイルシートを作成し、プロジェクトに追加する準備ができました。

スタイルシートを追加するには

- #### ステップ 1:`src/contacts_frontend/assets` ディレクトリに移動します。
  
  ```
    cd src/contacts_frontend/assets/
  ```

- #### ステップ2：`main.css` ファイルをテキストエディタで開き、既存の内容を削除します。

- #### ステップ3: フロントエンドのスタイルプロパティを定義します。
  
  既存のコードを以下のように置き換えてください：

<!-- end list -->

    html {
    background-color: bisque;
    }
    
    body {
        font-family: Arial, Helvetica, sans-serif;
        display: block;
        margin: 10px;
    }
    
    h1 {
        color: darkblue;
        font-size: 32px;
    }
    
    div.new-entry {
        margin: 30px 20px 30px 20px;
    }
    
    .new-entry > div {
        margin-bottom: 15px;
    }
    
    table {
        margin-top: 12px;
        border-top: 1px solid darkblue;
        border-bottom: 1px solid darkblue;
    }
    
    #form {
        margin: 30px 0 30px 20px;
    }
    
    button {
        line-height: 20px;
    }
    
    #lookupName {
        margin-right: 12px;
    }

- #### ステップ 4: 変更を保存し、`main.css` ファイルを閉じます。

- #### ステップ 5:`src/contacts_frontend/src` ディレクトリに移動します。

- #### ステップ 6: デフォルトの`index.js` ファイルをテキストエディタで開き、既存の内容を削除します。

- #### ステップ7：このコードをコピーして、`index.js` ファイルに貼り付けます：

<!-- end list -->

    import * as React from "react";
    import { render } from "react-dom";
    import { contacts } from "../../declarations/contacts_backend";
    import "../assets/main.css";
    
    const Contact = () => {
      async function doInsert() {
        let name = document.getElementById("newEntryName").value;
        let add1 = document.getElementById("newEntryAddress1").value;
        let add2 = document.getElementById("newEntryAddress2").value;
        let email = document.getElementById("newEntryEmail").value;
        let phone = document.getElementById("newEntryPhone").value;
        contacts_backend.insert(name, add1, add2, email, parseInt(phone, 10));
      }
    
      async function lookup() {
        let name = document.getElementById("lookupName").value;
        contacts_backend.lookup(name).then((opt_entry) => {
          let entry;
    
          if (opt_entry.length == 0) {
            entry = { name: "", description: "", phone: "" };
          } else {
            entry = opt_entry[0];
          }
    
          document.getElementById("newEntryName").value = entry.name;
          document.getElementById("newEntryAddress1").value = entry.address1;
          document.getElementById("newEntryAddress2").value = entry.address2;
          document.getElementById("newEntryEmail").value = entry.email;
          document.getElementById("newEntryPhone").value = entry.phone.toString();
        });
      }
    
      return (
        <div className="new-entry">
          <h1>My Contacts</h1>
          <div>
            Add or update contact information:
            <form id="contact">
              <table>
                <tbody>
                  <tr>
                    <td>Name:</td>
                    <td>
                      <input id="newEntryName"></input>
                    </td>
                  </tr>
                  <tr>
                    <td>Address 1 (street):</td>
                    <td>
                      <input id="newEntryAddress1"></input>
                    </td>
                  </tr>
                  <tr>
                    <td>Address 2 (city and state):</td>
                    <td>
                      <input id="newEntryAddress2"></input>
                    </td>
                  </tr>
                  <tr>
                    <td>Email:</td>
                    <td>
                      <input id="newEntryEmail"></input>
                    </td>
                  </tr>
                  <tr>
                    <td>Phone:</td>
                    <td>
                      <input id="newEntryPhone" type="number"></input>
                    </td>
                  </tr>
                </tbody>
              </table>
            </form>
          </div>
          <div>
            <button onClick={() => doInsert()}>Add Contact</button>
          </div>
          <div>
            Lookup name:{" "}
            <input id="lookupName" style=$$$${{ lineHeight: "20px" }}></input>
            <button onClick={() => lookup()}>Lookup</button>
          </div>
        </div>
      );
    };
    
    document.title = "DFINITY CONTACT EXAMPLE";
    
    render(<Contact />, document.getElementById("contacts"));

- #### ステップ8：以下のコマンドを実行して、変更した`index.js` ファイルの名前を`index.jsx` に変更します：
  
  ```
    mv index.js index.jsx
  ```

- #### ステップ 9: デフォルトの`src/contacts_assets/src/index.html` ファイルをテキストエディタで開き、`main.css` のリンクを削除して、`body` の内容を`<div id="contacts"></div>` で更新します。
  
  例えば
  
  ```
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width" />
        <title>contacts</title>
        <base href="/" />
      </head>
      <body>
        <main>
          <div id="contacts"></div>
        </main>
      </body>
    </html>
  ```

- #### ステップ10：プロジェクト・ディレクトリのルートに戻ります。

## ローカルネットワークの開始

`contacts` プロジェクトを構築する前に、ローカルのcanister 実行環境に接続する必要があります。

ローカルで環境を起動するには

- #### ステップ 1: ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

- #### ステップ 2: 以下のコマンドを実行して、ローカル・コンピューター上でローカルのcanister 実行環境を起動します：
  
  ```
    dfx start --background
  ```
  
  環境が起動操作を完了したら、次の手順に進みます。

## を登録、ビルド、デプロイします。dapp

開発環境のローカルcanister 実行環境に接続したら、テスト用にdapp を登録、ビルド、デプロイできます。

dapp をデプロイするには：

- #### ステップ 1: 必要に応じて、プロジェクトのルート・ディレクトリにいることを確認します。

- #### ステップ 2: 次のコマンドを実行して、dapp を登録、ビルド、デプロイします：
  
  ```
    dfx deploy
  ```
  
  `dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。
  
  canister 実行環境をローカルで実行しているため、`dfx deploy` コマンドを実行したときに表示される識別子はあなたのマシンでのみ有効であることに注意してください。
  
  canisters を IC 上にデプロイするには、`--network` コマンドラインオプションを使用して、ローカル環境ではなくInternet Computer にデプロイすることを指定する必要があります：
  
  ```
    dfx deploy --network=ic
  ```

- #### ステップ 3: Webpack 開発サーバーを起動します：
  
  ```
    npm start
  ```

## フロントエンドの表示

`contacts` dapp のフロントエンドにアクセスできます。

フロントエンドを表示するには

- #### ステップ 1: ブラウザーを開き、`http://localhost:8080` に移動します。

- #### ステップ 2: "My Contacts" フォームが表示されることを確認します。
  
  例えば
  
  ![Sample frontend](_attachments/mycontacts-form.png)

- #### ステップ3： 「名前」、「住所」、「Eメール」の入力フィールドにテキストを入力し、「電話番号」の入力フィールドに番号を入力して、「**連絡先の追加**」をクリックして、1つまたは複数のテストレコードを作成します。

- #### ステップ4: フォームフィールドをクリアし、\[検索名\]フィールドに連絡先名を入力し、\[**検索\]**をクリックして保存された連絡先情報を確認します。
  
  入力する**検索**名は、追加した連絡先の名前と完全に一致する必要があることに注意してください。

## スタイルシートの変更とテスト

連絡先\]dapp を表示した後、いくつかの変更を加えることができます。

スタイルシートのプロパティを変更するには

- #### ステップ1:`src/contacts_assets/assets/mycontacts.css` ファイルをテキストエディタで開き、スタイル設定を変更します。
  
  たとえば、背景色を変更したり、入力フォームのスタイルを変更したりします。
  
  開いているブラウザウィンドウで、変更内容がすぐに更新されるはずです。

## フロントエンドまたはバックエンドのコードの変更

さらに詳しく調べたい場合は、このガイドのフロントエンドまたはバックエンドのコードを変更して試してみるとよいでしょう。例えば、以下のようにガイドを変更してみてください：

- 例えば、`onClick` イベントの一部として、新しいコンタクトを追加した後に入力フィールドをクリアするようにフロントエンドのコードを変更します。

- `Name` フィールドで文字列の完全一致ではなく部分一致を行うようにMotoko プログラム関数を変更します。(`dfx deploy` を実行して、ローカル環境で変更をテストする必要があります）。

- Motoko プログラムを変更して、別のフィールドに基づいて検索できるようにします。

## ローカルのcanister 実行環境を停止します。

プログラムの実験が終わったら、ローカル環境を停止して、バックグラウンドで実行し続けないようにすることができます。

ローカルの開発環境を停止するには

- #### ステップ1: webpack開発サーバーが表示されているターミナルで、Control-Cを押して開発サーバーを中断します。

- #### ステップ 2: 次のコマンドを実行してInternet Computer ネットワークを停止します：
  
  ```
    dfx stop
  ```

<!---


# Adding a stylesheet

## Overview

Cascading stylesheets are an important part of any frontend user interface. The default starter is configured to import a `main.css` file that you can edit, but you may prefer to import your stylesheet into a JavaScript file, or to use an alternate format such as Syntactically Awesome Style Sheets, aka SCSS. This guide illustrates how to configure webpack to import a stylesheet by walking through building a contact dapp. If you already know how to add cascading stylesheets (CSS) to a webpack-based project, you can skip this guide.

This guide illustrates using the React framework to manage the Document Object Model (DOM) for your frontend. Because React has its own custom DOM syntax, you need to modify the webpack configuration to compile the frontend code, which is written in JSX. For more information about learning to use React and JSX, see [getting started](https://react.dev/learn) on the [React website](https://reactjs.org/).

## Prerequisites

Before starting the guide, verify the following:

- [x]   You have `node.js` installed for frontend development and can install packages using `npm install` in your project. For information about installing node for your local operating system and package manager, see the [Node](https://nodejs.org/en/) website.

- [x]   You have downloaded and installed the IC SDK package as described in the [download and install](/developer-docs/setup/install/index.mdx) page.

:::info
This guide requires you to use the IC SDK version `0.8.0` or later.
:::

- [x]   You have installed the Visual Studio Code plugin for Motoko as described in [VS Code extensions for IC development](/developer-docs/setup/vs-code.md) if you are using Visual Studio Code as your IDE.

- [x]   You have stopped any local canister execution environment processes running on the local computer.


## Create a new project

To create a new project directory for your custom frontend dapp:

- #### Step 1:  Open a terminal shell on your local computer, if you don’t already have one open.

- #### Step 2:  Change to the folder you are using for your Internet Computer projects, if you are using one.

- #### Step 3:  Verify that you have `node.js` installed locally, if necessary.

- #### Step 4:  Create a new project by running the following command:

        dfx new contacts

- #### Step 5:  Change to your project directory by running the following command:

        cd contacts

## Install the React framework

If you’ve never used React before, you might want to explore the [intro to React](https://reactjs.org/tutorial/tutorial.html) tutorial or the [React website](https://reactjs.org/) before editing the frontend code.

To install required framework modules:

- #### Step 1:  Install the React module by running the following command:

        npm install --save react react-dom

- #### Step 2:  Install the required TypeScript language compiler loader by running the following command:

        npm install --save-dev typescript ts-loader

- #### Step 3:  Install the required style loaders by running the following command:

        npm install --save-dev style-loader css-loader

    If the `npm install` command reports a vulnerability, you might also want to run the `npm audit fix` command to attempt to fix the vulnerability reported before continuing.

    As an alternative to installing these modules, you can edit the default `package.json` file to add dependencies for your project. For example, like this:
    
```
    {
        "name": "contacts_assets",
        "version": "0.1.0",
        "description": "",
        "keywords": [],
        "scripts": {
        "build": "webpack"
        },
        "devDependencies": {
        "assert": "2.0.0",
        "buffer": "6.0.3",
        "css-loader": "^5.2.1",
        "events": "3.3.0",
        "html-webpack-plugin": "5.3.1",
        "process": "0.11.10",
        "stream-browserify": "3.0.0",
        "style-loader": "^2.0.0",
        "terser-webpack-plugin": "5.1.1",
        "ts-loader": "^8.1.0",
        "typescript": "^4.2.4",
        "util": "0.12.3",
        "webpack-cli": "4.5.0",
        "webpack": "5.24.4"
        },
        "dependencies": {
        "@dfinity/agent": "0.10.0",
        "@dfinity/candid": "0.10.0",
        "@dfinity/principal": "0.10.0",
        "react-dom": "^17.0.2",
        "react": "^17.0.2"
        }
    }
```

    The version of the JavaScript agent in this example `package.json` file is `0.10.0`. In most cases, however, you would want to use the latest version of the agent available. When you create a new project, the `dfx new` command automatically retrieves the latest version of the JavaScript agent for you. You can also manually retrieve the latest version after creating a project by running the `npm install --save @dfinity/agent` command.

## Modify the default program

For this guide, you are going to modify the main program to with code that allows you to store and look up contact information.

To modify the default program:

- #### Step 1:  Open the `src/contacts_backend/main.mo` file in a text editor and delete the existing content.

- #### Step 2:  Copy and paste this code into the file:

```
import List "mo:base/List";
import AssocList "mo:base/AssocList";

actor Contact {

  var contacts : ContactsMap = List.nil();

  type Name = Text;
  type Phone = Nat;

  type Entry = {
    name : Name;
    address1 : Text;
    address2 : Text;
    email : Text;
    phone : Phone;
  };

  type ContactsMap = AssocList.AssocList<Name, Entry>;

  func nameEq(lhs : Name, rhs : Name) : Bool {
    return lhs == rhs;
  };

  public func insert(name : Name, address1 : Text, address2 : Text, email : Text, phone : Phone) : async () {
     let newEntry : Entry = {
       name;
       address1;
       address2;
       email;
       phone;
     };

     let (newContacts, _) = AssocList.replace(
       contacts,
       name,
       func(n: Name, m: Name) : Bool { n == m },
       ?newEntry
     );
     contacts := newContacts;
  };

  public query func lookup(name : Name) : async ?Entry {
    return AssocList.find(contacts, name, nameEq);
  };
};
```

- #### Step 3:  Save your changes and close the `main.mo` file to continue.

## Modify the frontend files

You are now ready to create a new frontend for your program.

- #### Step 1:  Open the webpack configuration file (`webpack.config.js`) in a text editor.

- #### Step 2:  Modify the frontend entry to replace the default index.html with index.jsx.

        entry: {
          // The frontend.entrypoint points to the HTML file for this build, so we need
          // to replace the extension to `.js`.
          index: path.join(__dirname, frontend_entry).replace(/\.html$/, ".jsx"),
        },

- #### Step 3:  Locate the commented example for the `module` key above the `plugins` section, then uncomment the following lines:

        module: {
          rules: [
            { test: /\.(js|ts)x?$/, loader: "ts-loader" },
            { test: /\.css$/, use: ['style-loader','css-loader'] }
          ]
        },

    These settings enable your program to use the `ts-loader` compiler and to import CSS files.

:::info
    Note: if you want to add support for `.scss` or `.sass` files, you should install `sass-loader` with:

        npm install --save react react-dom

    and then add this additional rule beneath the `css-loader` rule in `webpack.config.js`:

        module: {
          rules: [
            // ...
            {
              test: /\.s[ac]ss$/i,
              use: [
                // Creates `style` nodes from JS strings
                "style-loader",
                // Translates CSS into CommonJS
                "css-loader",
                // Compiles Sass to CSS
                "sass-loader",
              ],
            },
          ]
        },
:::

- #### Step 4:  Save your changes and close the `webpack.config.js` file to continue.

- #### Step 5:  Create a new file named `tsconfig.json` in the root directory for your project.

- #### Step 6:  Open the `tsconfig.json` file in a text editor, then copy and paste this code into the file:

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

- #### Step 7:  Save your changes and close the `tsconfig.json` file to continue.

## Add a stylesheet to your project

You are now ready to create a new cascading stylesheet and add it to your project.

To add a stylesheet:

- #### Step 1:  Change to the `src/contacts_frontend/assets` directory.

        cd src/contacts_frontend/assets/

- #### Step 2:  Open the `main.css` file in a text editor and delete the existing content.

- #### Step 3:  Define some style properties for the frontend.

    Replace the existing code with the following:

```
html {
background-color: bisque;
}

body {
    font-family: Arial, Helvetica, sans-serif;
    display: block;
    margin: 10px;
}

h1 {
    color: darkblue;
    font-size: 32px;
}

div.new-entry {
    margin: 30px 20px 30px 20px;
}

.new-entry > div {
    margin-bottom: 15px;
}

table {
    margin-top: 12px;
    border-top: 1px solid darkblue;
    border-bottom: 1px solid darkblue;
}

#form {
    margin: 30px 0 30px 20px;
}

button {
    line-height: 20px;
}

#lookupName {
    margin-right: 12px;
}
```

- #### Step 4:  Save your changes and close the `main.css` file to continue.

- #### Step 5:  Change to the `src/contacts_frontend/src` directory.

- #### Step 6:  Open the default `index.js` file in a text editor and delete the existing content.

- #### Step 7:  Copy and paste this code into the `index.js` file:

```
import * as React from "react";
import { render } from "react-dom";
import { contacts } from "../../declarations/contacts_backend";
import "../assets/main.css";

const Contact = () => {
  async function doInsert() {
    let name = document.getElementById("newEntryName").value;
    let add1 = document.getElementById("newEntryAddress1").value;
    let add2 = document.getElementById("newEntryAddress2").value;
    let email = document.getElementById("newEntryEmail").value;
    let phone = document.getElementById("newEntryPhone").value;
    contacts_backend.insert(name, add1, add2, email, parseInt(phone, 10));
  }

  async function lookup() {
    let name = document.getElementById("lookupName").value;
    contacts_backend.lookup(name).then((opt_entry) => {
      let entry;

      if (opt_entry.length == 0) {
        entry = { name: "", description: "", phone: "" };
      } else {
        entry = opt_entry[0];
      }

      document.getElementById("newEntryName").value = entry.name;
      document.getElementById("newEntryAddress1").value = entry.address1;
      document.getElementById("newEntryAddress2").value = entry.address2;
      document.getElementById("newEntryEmail").value = entry.email;
      document.getElementById("newEntryPhone").value = entry.phone.toString();
    });
  }

  return (
    <div className="new-entry">
      <h1>My Contacts</h1>
      <div>
        Add or update contact information:
        <form id="contact">
          <table>
            <tbody>
              <tr>
                <td>Name:</td>
                <td>
                  <input id="newEntryName"></input>
                </td>
              </tr>
              <tr>
                <td>Address 1 (street):</td>
                <td>
                  <input id="newEntryAddress1"></input>
                </td>
              </tr>
              <tr>
                <td>Address 2 (city and state):</td>
                <td>
                  <input id="newEntryAddress2"></input>
                </td>
              </tr>
              <tr>
                <td>Email:</td>
                <td>
                  <input id="newEntryEmail"></input>
                </td>
              </tr>
              <tr>
                <td>Phone:</td>
                <td>
                  <input id="newEntryPhone" type="number"></input>
                </td>
              </tr>
            </tbody>
          </table>
        </form>
      </div>
      <div>
        <button onClick={() => doInsert()}>Add Contact</button>
      </div>
      <div>
        Lookup name:{" "}
        <input id="lookupName" style={{ lineHeight: "20px" }}></input>
        <button onClick={() => lookup()}>Lookup</button>
      </div>
    </div>
  );
};

document.title = "DFINITY CONTACT EXAMPLE";

render(<Contact />, document.getElementById("contacts"));
```

- #### Step 8:  Rename the modified `index.js` file as `index.jsx` by running the following command:

        mv index.js index.jsx

- #### Step 9:  Open the default `src/contacts_assets/src/index.html` file in a text editor, then remove the `main.css` link and update the `body` contents with `<div id="contacts"></div>`.

    For example:

        <!DOCTYPE html>
        <html lang="en">
          <head>
            <meta charset="UTF-8" />
            <meta name="viewport" content="width=device-width" />
            <title>contacts</title>
            <base href="/" />
          </head>
          <body>
            <main>
              <div id="contacts"></div>
            </main>
          </body>
        </html>

- #### Step 10: Navigate back to the root of your project directory.

## Start the local network

Before you can build the `contacts` project, you need to connect to the local canister execution environment.

To start the environment locally:

- #### Step 1:  Open a new terminal window or tab on your local computer.

- #### Step 2:  Start the local canister execution environment on your local computer by running the following command:

        dfx start --background

    After the environment completes its startup operations, you can continue to the next step.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment in your development environment, you can register, build, and deploy your dapp for testing.

To deploy the dapp:

- #### Step 1:  Check that you are still in the root directory for your project, if needed.

- #### Step 2:  Register, build, and deploy your dapp by running the following command:

        dfx deploy

    The `dfx deploy` command output displays information about the operations it performs.

    Keep in mind that because you are running the canister execution environment locally, the identifiers displayed when you run the `dfx deploy` command are only valid on your machine.

    To deploy canisters on the IC, you must specify that you are deploying to the Internet Computer and not your local environment by using the `--network` command-line option:

        dfx deploy --network=ic

- #### Step 3:  Start the Webpack development server:

        npm start

## View the frontend

You can now access the frontend for the `contacts` dapp.

To view the frontend:

- #### Step 1:  Open a browser and navigate to the `http://localhost:8080`.

- #### Step 2:  Verify that you are prompted with a "My Contacts" form.

    For example:

    ![Sample frontend](_attachments/mycontacts-form.png)

- #### Step 3:  Create one or more test records by entering text in the Name, Address, and Email input fields and a number in the Phone input field, then clicking **Add Contact**.

- #### Step 4:  Clear the form fields and type a contact name in the Lookup name field, then click **Lookup** to see the stored contact information.

    Keep in mind that the **Lookup name** you type must be an exact match for the name of a contact you added.

## Modify the stylesheet and test your changes

After viewing the Contacts dapp, you might want to make some changes.

To change stylesheet properties:

- #### Step 1:  Open the `src/contacts_assets/assets/mycontacts.css` file in a text editor and modify its style settings.

    For example, you might want to change the background color or style the input form.

    You should see the changes update immediately in your open browser window.

## Modify the frontend or backend code

If you want to explore further, you might want to experiment with modifying the frontend or backend code for this guide. For example, you might want try modifying the guide to do the following:

-   Change the frontend code to clear the input fields after adding a new contact, for example, as part of an `onClick` event.

-   Change the Motoko program functions to do partial instead of exact string matching on the `Name` field. (You will need to run `dfx deploy` to test your changes on the local environment).

-   Change the Motoko program to allow lookups based on a different field.

## Stop the local canister execution environment

After you finish experimenting with your program, you can stop the local environment so that it doesn’t continue running in the background.

To stop the local development environment:

- #### Step 1:  In the terminal that displays your webpack dev server, press Control-C to interrupt the development server.

- #### Step 2:  Stop the Internet Computer network by running the following command:

        dfx stop

-->
