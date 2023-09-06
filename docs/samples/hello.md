# こんにちは

## 概要

このサンプルは、2つのcanisters で構成される単純なdapp を示しています：

- シンプルなバックエンドcanister,`hello`, アプリケーションのロジックを実装します。

- シンプルなフロントエンドアセットcanister,`hello_assets`,dapp’s ウェブユーザーインターフェースのアセットを提供します。

dapp これは、どこにでもある「Hello, world\!

## アーキテクチャ

このサンプルは、クイックスタート・ドキュメントに記載されているように、`dfx new` を実行して作成されたデフォルト・プロジェクトに基づいています。

サンプルコードは、[samples](https://github.com/dfinity/examples)リポジトリから [Motoko](https://github.com/dfinity/examples/tree/master/motoko/hello)と[Rustの](https://github.com/dfinity/examples/tree/master/rust/hello)両方で利用できます。

Canister `hello` Motoko で実装されていても、Rust で実装されていても、同じ Candid インターフェースを提供します：

    service : {
      greet: (text) -> (text);
    }

フロントエンドcanister,`hello_assets` は、引数のためのテキストボックスと、その引数で挨拶する関数を呼び出すためのボタンを備えた HTML ページを表示します。呼び出しの結果はメッセージボックスに表示されます。

![hello frontend](_attachments/hello.png)

フロントエンドcanister は`dfx` によって提供される汎用のcanister ですが、ブラウザに提供するアセットは dfx プロジェクトの設定とプロジェクトファイルによって決まります。

フロントエンドcanister とそのアセットはどちらのプロジェクトでも同じです。

## Motoko バリアント

この例では、2つのcanister スマートコントラクトからなる、単純なdapp を示します：

- シンプルなバックエンドcanister 、hello。Motoko のアプリケーションのロジックを実装しています。
- シンプルなフロントエンドアセットcanister, hello\_assetsdapp'のウェブユーザーインターフェースのアセットを提供します。

この例は、`dfx new hello` を実行して作成されたデフォルトのプロジェクトに基づいています。

### 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。

- \[x\]`node.js` (ウェブフロントエンドを構築するため)をインストールします。

- #### ステップ 1: ターミナルウィンドウを開きます。

まだの場合は、コマンドでデフォルトのプロジェクトを作成します：

`dfx new hello`
`cd hello`

- #### ステップ2:canister のローカル実行環境を起動します：

`dfx start --background`

- #### ステップ 3: 必要であれば、以下のコマンドを実行して、必要なノードモジュールがプロジェクトディレクトリで利用可能であることを確認します：

`npm install`

- #### ステップ 4: コマンドでプロジェクトを登録、ビルド、デプロイします：

`dfx deploy`
`npm start`

- #### ステップ 5: hellocanister の greet 関数を呼び出します：

`dfx canister call hello_backend greet everyone`

- #### ステップ 6: 次の結果を確認してください：

`("Hello, everyone!")`

これまでのステップでは、dfxを使ってhello（バックエンド）canister の関数を直接呼び出しています。canister hello\_assets が提供するdapp のウェブユーザーインターフェイスにアクセスするには、以下のようにします：

- #### ステップ 7: hello\_frontend アセットの URLcanister を決定します。

`echo "http://localhost:4943/?canisterId=$(dfx canister id hello_frontend)"`

- #### ステップ 8: ブラウザで URL に移動します。

ブラウザには、サンプルアセット画像ファイル、入力フィールド、ボタンがあるシンプルな HTML ページが表示されるはずです。

- #### ステップ 9: テキスト "everyone" を入力してボタンをクリックすると、バックエンドの hellocanister から返される挨拶が表示されます。

### トラブルシューティング

ウェブページが正しく表示されなかったり、間違った内容が表示されたりする場合は、ブラウザのキャッシュをクリアする必要があるかもしれません。

あるいは、新しいプライベートブラウザウィンドウで URL を開き、キャッシュを消去してください。

## Rust の亜種

この例では、2つのcanister スマートコントラクトからなる、単純なdapp を示します：

- 単純なバックエンドcanister 、hello、アプリケーションのロジックをRustで実装。
- シンプルなフロントエンドアセットcanister, hello\_assetsdapp'のウェブユーザーインターフェースのアセットを提供します。

この例は、`dfx new --type=rust hello` を実行して作成されたデフォルトのプロジェクトに基づいています。

### 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。

- \[x\]`node.js` (ウェブフロントエンドを構築するため)をインストールします。

- #### ステップ 1: ターミナルウィンドウを開きます。

まだの場合は、コマンドでデフォルトのプロジェクトを作成します：

    dfx new --type=rust hello
    cd hello

- #### ステップ2:canister のローカル実行環境を起動します：

<!-- end list -->

    dfx start --background

- #### ステップ 3: 必要であれば、以下のコマンドを実行して、必要なノードモジュールがプロジェクトディレクトリで利用可能であることを確認します：

<!-- end list -->

    npm install

- #### ステップ 4: コマンドでプロジェクトを登録、ビルド、デプロイします：

<!-- end list -->

    dfx deploy
    npm start

- #### ステップ 5: hellocanister の greet 関数を呼び出します：

<!-- end list -->

    dfx canister call hello_backend greet everyone

- #### ステップ 6: 次の結果を確認してください：

<!-- end list -->

    ("Hello, everyone!")

これまでのステップでは、dfxを使ってhello（バックエンド）canister の関数を直接呼び出しています。canister hello\_assets が提供するdapp のウェブユーザーインターフェイスにアクセスするには、以下のようにします：

- #### ステップ 7: hello\_frontend アセットの URLcanister を決定します。

<!-- end list -->

    echo "http://localhost:4943/?canisterId=$(dfx canister id hello_frontend)"

- #### ステップ 8: ブラウザで URL に移動します。

ブラウザには、サンプルアセット画像ファイル、入力フィールド、ボタンがあるシンプルな HTML ページが表示されるはずです。

- #### ステップ 9: テキスト "everyone" を入力してボタンをクリックすると、バックエンドの hellocanister から返される挨拶が表示されます。

### トラブルシューティング

ウェブページが正しく表示されなかったり、間違った内容が表示されたりする場合は、ブラウザのキャッシュをクリアする必要があるかもしれません。

あるいは、新しいプライベートブラウザウィンドウでURLを開き、キャッシュを消去してください。

### リソース

- [ic-cdk](https://docs.rs/ic-cdk/latest/ic_cdk/)
- [ic-cdk-macros](https://docs.rs/ic-cdk-macros)
- [JavaScript APIリファレンス](https://erxue-5aaaa-aaaab-qaagq-cai.ic0.app/)

<!---
# Hello, world!

## Overview 
This sample demonstrates a simple dapp consisting of two canisters:

-   A simple backend canister, `hello`, implementing the logic of the application.

-   A simple frontend asset canister, `hello_assets`, serving the assets of the dapp’s web user interface.

It is the dapp equivalent of the ubiquitous 'Hello, world!' and can be seen running [here on the IC](https://6lqbm-ryaaa-aaaai-qibsa-cai.ic0.app/).

## Architecture

This sample is based on the default project created by running `dfx new` as described in the quick start documents.

The sample code is available from the [samples](https://github.com/dfinity/examples) repository in both [Motoko](https://github.com/dfinity/examples/tree/master/motoko/hello) and [Rust](https://github.com/dfinity/examples/tree/master/rust/hello).

Canister `hello`, whether implemented in Motoko or Rust, presents the same Candid interface:

    service : {
      greet: (text) -> (text);
    }

The frontend canister, `hello_assets`, displays an HTML page with a text box for the argument and a button for calling the function greet with that argument. The result of the call is displayed in a message box.

![hello frontend](_attachments/hello.png)

The frontend canister is a generic canister provided by `dfx` but the assets it serves to browsers are determined by the dfx project settings and project files.

The frontend canister and its assets are identical for both projects.

## Motoko variant

This example demonstrates a dead simple dapp consisting of two canister smart contracts:

- A simple backend canister, hello, implementing the logic of the application in Motoko.
- A simple frontend asset canister, hello_assets serving the assets of the dapp's web user interface.

This example is based on the default project created by running `dfx new hello`.

### Prerequisites
This example requires an installation of:
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Install `node.js` (to build the web frontend).

- #### Step 1: Open a terminal window.

If you haven't already, create a default project with the command:

`dfx new hello`
`cd hello`

- #### Step 2: Start a local canister execution environment:

`dfx start --background`

- #### Step 3: Ensure that the required node modules are available in your project directory, if needed, by running the following command:

`npm install`

- #### Step 4: Register, build and deploy the project with the command:

`dfx deploy`
`npm start`

- #### Step 5: Call the hello canister's greet function:

`dfx canister call hello_backend greet everyone`

- #### Step 6: Observe the following result:

`("Hello, everyone!")`

The previous steps use dfx to directly call the function on the hello (backend) canister. To access the web user interface of the dapp, that is served by canister hello_assets, do the following:

- #### Step 7: Determine the URL of the hello_frontend asset canister.

`echo "http://localhost:4943/?canisterId=$(dfx canister id hello_frontend)"`

- #### Step 8: Navigate to the URL in your browser.
The browser should display a simple HTML page with a sample asset image file, an input field, and a button.

- #### Step 9: Enter the text "everyone" and click the button to see the greeting returned by the backend hello canister.

### Troubleshooting
If the web page doesn't display properly, or displays the wrong contents, you may need to clear your browser cache.

Alternatively, open the URL in a fresh, in-private browser window to start with a clean cache.

## Rust variant
This example demonstrates a dead simple dapp consisting of two canister smart contracts:

- A simple backend canister, hello, implementing the logic of the application in Rust.
- A simple frontend asset canister, hello_assets serving the assets of the dapp's web user interface.

This example is based on the default project created by running `dfx new --type=rust hello`.

### Prerequisites 
This example requires an installation of:
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Install `node.js` (to build the web frontend).

- #### Step 1: Open a terminal window.

If you haven't already, create a default project with the command:

```
dfx new --type=rust hello
cd hello
```

- #### Step 2: Start a local canister execution environment:

```
dfx start --background
```

- #### Step 3: Ensure that the required node modules are available in your project directory, if needed, by running the following command:

```
npm install
```

- #### Step 4: Register, build and deploy the project with the command:

```
dfx deploy
npm start
```

- #### Step 5: Call the hello canister's greet function:

```
dfx canister call hello_backend greet everyone
```

- #### Step 6: Observe the following result:

```
("Hello, everyone!")
```

The previous steps use dfx to directly call the function on the hello (backend) canister. To access the web user interface of the dapp, that is served by canister hello_assets, do the following:

- #### Step 7: Determine the URL of the hello_frontend asset canister.

```
echo "http://localhost:4943/?canisterId=$(dfx canister id hello_frontend)"
```

- #### Step 8: Navigate to the URL in your browser.
The browser should display a simple HTML page with a sample asset image file, an input field, and a button.

- #### Step 9: Enter the text "everyone" and click the button to see the greeting returned by the backend hello canister.

### Troubleshooting
If the web page doesn't display properly, or displays the wrong contents, you may need to clear your browser cache.

Alternatively, open the URL in a fresh, in-private browser window to start with a clean cache.

### Resources
- [ic-cdk](https://docs.rs/ic-cdk/latest/ic_cdk/)
- [ic-cdk-macros](https://docs.rs/ic-cdk-macros)
- [JavaScript API Reference](https://erxue-5aaaa-aaaab-qaagq-cai.ic0.app/)

-->
