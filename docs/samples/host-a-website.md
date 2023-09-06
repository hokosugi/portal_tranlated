# 静的ウェブサイトのホスティングInternet Computer

## 概要

この例の目的は、アセットcanister を使用して静的ウェブサイトを展開する方法を示すことです。この例のウェブサイトは非常に単純ですが、一般的な静的サイトジェネレータを使用して作成されたような、より高度な静的ウェブサイトでも方法は同じでしょう。

この例で扱うのは

- 非常に単純な HTML ウェブサイトの構築
- `dfx.json` ファイルの作成。
- canister スマート・コントラクトのデプロイ。
- ウェブブラウザでフロントエンドをテストします。

## 前提条件

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。
- \[x\]cycles を含むcycles ウォレットがあること。[fauche quick start](../developer-docs/setup/cycles/cycles-faucet.md)から、または ICP を購入して[メインネットのデプロイガイドに従って](../developer-docs/setup/deploy-mainnet.md)ください。

## プロジェクトのセットアップ

簡単な静的ウェブサイトを作成し、dfx でデプロイするように設定しましょう。

### ステップ 1:`static-ic-website` という名前のフォルダを作成します。

### ステップ 2:`static-ic-website` に、`assets` という名前の新しいフォルダを作成します。

### ステップ 3: assets フォルダの中に、4つのファイルを作成します：

    -   index.html
    -   page-2.html
    -   script.js
    -   style.css

## コンテンツの追加

index.htmlから始めましょう。

### ステップ 4: 以下のコードをファイルに貼り付けます：

**index.html**

    <!DOCTYPE html>
    <html lang="en">
    <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>StaticICWebsite</title>
       <link rel="stylesheet" href="style.css">
    </head>
    <body>
       <h1>My First IC Website</h1>
       <p>Styles are loaded from a stylesheet</p>
       <p id="dynamic-content"></p>
       <a href="page-2.html">Page 2</a>
       <script src="script.js"></script>
    </body>
    </html>

### ステップ5：次に、`page-2.html` を開き、次のコンテンツを追加します。

**ページ-2.html**

    <!DOCTYPE html>
    <html lang="en">
    <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Page 2</title>
       <link rel="stylesheet" href="style.css">
    </head>
    <body>
       <h1>This is page 2</h1>
       <a href="/">Back to index</a>
    </body>
    </html>

### ステップ6：次に、`script.js` に簡単なロジックを追加します。

**script.js**

    document.querySelector("#dynamic-content").innerText =
     "This paragraph is dynamically rendered using JavaScript";

### ステップ7：次に、`style.css` にスタイルを追加します。

**style.css**

    body {
       font-family: sans-serif;
       font-size: 1.5rem;
    }
    
    p:first-of-type {
       color: #ED1E79;
    }

## デプロイするためのdfxの設定

### ステップ8: このウェブサイトをIC上でライブホストして実行するには、`dfx` を設定して、ファイルを認証済みアセットcanister にアップロードする必要があります。

プロジェクトのルートディレクトリ static-ic-website に、新しいファイル`dfx.json` を作成します。次に、以下の内容を追加します：

**dfx.json**

    {
       "canisters": {
           "website": {
               "type": "assets",
               "source": ["assets"]
           }
       }
    }

これにより、`dfx` にアセットcanister を作成し、asset フォルダの内容をアップロードするように指示します。追加の静的アセットをアップロードしたい場合は、asset フォルダに追加するか、ソース設定に追加のフォルダを追加します。

これで、ディレクトリはこのようになるはずです：

    ├── assets
    │   ├── index.html
    │   ├── page-2.html
    │   ├── script.js
    │   └── style.css
    └── dfx.json

## ウェブサイトのデプロイ

### ステップ9: ウェブサイトをデプロイするには、ターミナルのプロジェクトのルートにいることを確認し、このコマンドを実行します：

    dfx deploy --network ic

コンソールに何らかの出力が表示され、次のような成功メッセージが表示されるはずです：

    ...
    
    Uploading assets to asset canister...
    Starting batch.
    Staging contents of new and changed assets:
      /index.html 1/1 (501 bytes)
      /index.html (gzip) 1/1 (317 bytes)
      /page-2.html 1/1 (373 bytes)
      /page-2.html (gzip) 1/1 (258 bytes)
      /script.js 1/1 (117 bytes)
      /style.css 1/1 (102 bytes)
    Committing batch.
    Deployed canisters.

## ライブのウェブサイトを見る

コマンドを実行して、新しいcanisterの ID を見つけてください：

    dfx canister --network ic id website

このコマンドは次のような出力を返します：

    cbopz-duaaa-aaaaa-qaaka-cai

これがあなたの**canister ID** です。このcanister ID を使って`https://<canister-id>.icp0.io` にアクセスし、URL のサブドメインとしてあなた自身のcanister ID を挿入します。

このように、複数ページのウェブサイトが表示されるはずです：

![Static Website](_attachments/static-website.png)

### リソース

フルスタックdapp を構築したいですか？[フルスタックのreactチュートリアルを](https://smartcontracts.org/docs/current/developer-docs/frontend/custom-frontend)ご覧ください。

DFINITY Foundation のエンジニアと開発者コミュニティからのインスピレーションとサポートについては、[開発者フォーラムを](https://forum.dfinity.org)ご覧ください。

<!---
# Hosting a static website on the Internet Computer

## Overview

The purpose of this example is to show how to deploy a static website using an asset canister. While the website in this example is very simple, the method would be the same for a more advanced static website, such as those created by using popular static site generators.

This example covers:

- Building a very simple HTML website.
- Creating a `dfx.json` file.
- Deploying the canister smart contract.
- Testing the frontend in a web browser.

## Prerequisites 

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Have a cycles wallet that contains cycles, either from the [faucet quick start](../developer-docs/setup/cycles/cycles-faucet.md) or by purchasing ICP and following our [mainnet deployment](../developer-docs/setup/deploy-mainnet.md) guide.

## Set up your project

Let’s create a simple static website, and then set it up to deploy with dfx.

### Step 1: Create a folder named `static-ic-website`.

### Step 2: In `static-ic-website`, create another new folder, named `assets`.

### Step 3: Inside your assets folder, create 4 files:

    -   index.html
    -   page-2.html
    -   script.js
    -   style.css

## Add some content

Let’s start with index.html. 

### Step 4: Paste the following code into your file:

**index.html**

    <!DOCTYPE html>
    <html lang="en">
    <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>StaticICWebsite</title>
       <link rel="stylesheet" href="style.css">
    </head>
    <body>
       <h1>My First IC Website</h1>
       <p>Styles are loaded from a stylesheet</p>
       <p id="dynamic-content"></p>
       <a href="page-2.html">Page 2</a>
       <script src="script.js"></script>
    </body>
    </html>

### Step 5: Next, open up `page-2.html` and add this content.

**page-2.html**

    <!DOCTYPE html>
    <html lang="en">
    <head>
       <meta charset="UTF-8">
       <meta http-equiv="X-UA-Compatible" content="IE=edge">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Page 2</title>
       <link rel="stylesheet" href="style.css">
    </head>
    <body>
       <h1>This is page 2</h1>
       <a href="/">Back to index</a>
    </body>
    </html>

### Step 6: Then, add some simple logic to `script.js`.

**script.js**

    document.querySelector("#dynamic-content").innerText =
     "This paragraph is dynamically rendered using JavaScript";

### Step 7: Next,  then add some styles to `style.css`.

**style.css**

    body {
       font-family: sans-serif;
       font-size: 1.5rem;
    }

    p:first-of-type {
       color: #ED1E79;
    }

## Configure dfx to deploy

### Step 8: To host and run this website live on the IC, you will need to configure `dfx` to upload your files to a certified asset canister. 
In the root directory of your project, static-ic-website, create a new file, `dfx.json`. Then, add the following content:

**dfx.json**

    {
       "canisters": {
           "website": {
               "type": "assets",
               "source": ["assets"]
           }
       }
    }

This tells `dfx` that you want to create an asset canister, and that it should upload the contents of the asset folder. If you want to upload additional static assets, you can add them to the asset folder or add additional folders to the source configuration.

Now, your directory should look something like this:

    ├── assets
    │   ├── index.html
    │   ├── page-2.html
    │   ├── script.js
    │   └── style.css
    └── dfx.json

## Deploy your website

### Step 9: To deploy your website, ensure you are in your terminal at the root of the project, and run this command:

```
dfx deploy --network ic
```

You should see some output in your console, and a success message looking something like this:

    ...

    Uploading assets to asset canister...
    Starting batch.
    Staging contents of new and changed assets:
      /index.html 1/1 (501 bytes)
      /index.html (gzip) 1/1 (317 bytes)
      /page-2.html 1/1 (373 bytes)
      /page-2.html (gzip) 1/1 (258 bytes)
      /script.js 1/1 (117 bytes)
      /style.css 1/1 (102 bytes)
    Committing batch.
    Deployed canisters.

## See your live website

Find your new canister’s ID by running the command:

```
dfx canister --network ic id website
```

This command will return output that will look something like this:

```
cbopz-duaaa-aaaaa-qaaka-cai
```

This is your **canister ID**. Take this canister ID and visit `https://<canister-id>.icp0.io`, inserting your own canister ID as the subdomain in the URL.

You should see your live, multi-page website, looking like this:

![Static Website](_attachments/static-website.png)

### Resources

Looking to build a full-stack dapp? Check out the [full-stack react tutorial](https://smartcontracts.org/docs/current/developer-docs/frontend/custom-frontend).

Visit our [developer forum](https://forum.dfinity.org) for inspiration and support from DFINITY Foundation engineers and the developer community.

-->
