# 5: ライティングとデプロイcanisters

## 概要

このガイドでは、Motoko canisters の記述とデプロイの基本について説明します。Motoko の基礎についての詳細情報は、[Motoko の基礎の](infrastructure.md)ページをご覧ください。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていなければ、ローカル・コンピューターでターミナル・ウィンドウを開いてください。

新しいプロジェクトを作成するには、コマンドを実行します：

    dfx new explore_hello

次に、コマンドでプロジェクトに移動します：

    cd explore_hello

## デフォルト設定の確認

デフォルトでは、新しいプロジェクトを作成すると、プロジェクトディレクトリにいくつかのテンプレートファイルが追加されます。これらのテンプレートファイルを編集することで、プロジェクトのコンフィギュレーション設定をカスタマイズしたり、独自のコードをインクルードして開発をスピードアップしたりすることができますcycle 。

`dfx.json` 設定ファイルをテキストエディタで開いて、デフォルトの設定を確認してください。

このように見えるかもしれません：

    {
    "canisters": {
        "explore_hello_backend": {
        "main": "src/explore_hello_backend/main.mo",
        "type": "motoko"
        },
        "explore_hello_frontend": {
        "dependencies": [
            "explore_hello_backend"
        ],
        "frontend": {
            "entrypoint": "src/explore_hello_frontend/src/index.html"
        },
        "source": [
            "src/explore_hello_frontend/assets",
            "dist/explore_hello_frontend/"
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

デフォルト設定のいくつかを見てみましょう。

- `settings` セクションでは、`explore_hello` プロジェクトのWebAssembly モジュール名を`explore_hello` と指定します。

- `canisters.explore_hello` キーでは、コンパイルされるメイン・プログラムが`main` 設定で指定されたパス、この場合は`src/explore_hello/main.mo` にあることを指定し、`type` 設定では、これが`motoko` プログラムであることを示します。

- `canisters.explore_hello_assets` キーには、このプロジェクトのフロントエンド資産に関する設定の詳細を指定します。今は省略しましょう。

- `dfx` 設定は、プロジェクトの作成に使用されたソフトウェアのバージョンを特定するために使用されます。

- `networks` セクションは、接続するネットワークに関する情報を指定します。デフォルトの設定では、ローカルのcanister 実行環境をローカルのホスト・アドレス`127.0.0.1` とポート`4943` にバインドします。

他のInternet Computer ネットワーク・プロバイダーにアクセスできる場合は、`networks` セクションに、それらのプロバイダーに接続するためのネットワーク・エイリアスや URL を含めることができます。

デフォルト設定のままでかまいません。

続行するには、`dfx.json` ファイルを閉じます。

## canister コードの記述

新しいプロジェクトには、常に`main.mo` ソースコード・ファイルのテンプレートが含まれています。このファイルを編集して独自のコードを含めることで、開発をスピードアップできますcycle 。

Motoko プログラミング言語を使用して簡単なdapp を作成するための出発点として、デフォルトの`main.mo` テンプレート・ファイルにあるサンプル・プログラムを見てみましょう。

テキストエディタで`src/explore_hello_backend/main.mo` ファイルを開き、テンプレート内のコードを確認してください：

    actor {
        public func greet(name : Text) : async Text {
            return "Hello, " # name # "!";
        };
    };

このプログラムのいくつかの重要な要素を見てみましょう：

- このサンプル・コードでは、`main` 関数ではなく`actor` 関数を定義しています。Motoko の場合、`main` 関数はファイル自体に暗黙的に含まれています。

- 従来の「Hello, World！」プログラムは、`print` または`println` 関数を使って文字列を印刷する方法を示していますが、この従来のプログラムは、Internet Computer 上で実行されるMotoko dapps の典型的な使用例ではありません。

- このサンプルプログラムでは、print 関数の代わりに、`name` 型の引数`Text` を受け取る public`greet` 関数で`actor` を定義します。

- このプログラムでは、`async` キーワードを使用して、`"Hello, "` 、`#` 演算子、`name` 引数、`"!"` を使用して構築されたテキスト文字列を連結した非同期メッセージを返すことを示します。

`actor` オブジェクトと非同期メッセージ処理を使用するコードについては、もう少し後で詳しく説明します。とりあえず、次のセクションに進んでください。

続行するには、`main.mo` ファイルを閉じます。

## ローカルのcanister 実行環境の開始

デフォルトのプロジェクトをデプロイする前に、ローカルのcanister 実行環境か、Internet Computer ブロックチェーンメインネットに接続する必要があります。

ローカルのcanister 実行環境を起動するには`dfx.json` ファイルが必要なので、プロジェクトのルートディレクトリにいることを確認してください。このガイドでは、ターミナルシェルを2つに分けて、1つのターミナルでネットワーク操作を開始して確認し、もう1つのターミナルでプロジェクトを管理できるようにします。

ローカルのcanister 実行環境を起動するには、まずローカル・コンピューターで新しいターミナル・ウィンドウまたは新しいターミナル・タブを開きます。

:::info
\- これで、**2つのターミナルが開いて**いるはずです。
\-**カレント作業ディレクトリとして** **プロジェクト・ディレクトリが**あるはずです。
::：

以下のコマンドを実行して、ローカルのcanister 実行環境を起動します：

    dfx start

プラットフォームとローカルのセキュリティ設定によっては、警告が表示されるかもしれません。受信ネットワーク接続の許可または拒否を求めるプロンプトが表示されたら、「**許可**」をクリックします。

ローカルの実行環境（canister ）を起動すると、ネットワーク操作に関するメッセー ジを表示するターミナルと、プロジェクト関連のタスクを実行するターミナルが 1 つになります。

ネットワーク操作を表示するターミナルは開いたままにしておき、新しいプロジェクトを作成したターミナルにフォーカスを切り替えます。

## canister 識別子の登録

ローカルの実行環境（canister ）に接続したら、ネットワークに登録し て、プロジェクトに固有のネットワーク**識別子（canister ）を**生成します。

チュートリアル「[1.3: 最初の](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md)開発者の旅[ dapp の](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md) [デプロイ](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md)」では、この手順を`dfx deploy` コマンドのワークフローの一部として実行しました。このガイドでは、各操作を個別に実行する方法を示します。

ローカル・ネットワーク用のcanister 識別子を登録するには、以下のコマンドを実行して、プロジェク ト内のcanisters に対して一意のcanister 識別子を登録します：

    dfx canister create --all

このコマンドを実行すると、`dfx.json` 構成ファイルに定義されているcanisters のネットワーク固有のcanister 識別子が表示されます。

    Creating canister explore_hello_backend...
    explore_hello_backend canister created with canister id: br5f7-7uaaa-aaaaa-qaaca-cai
    Creating canister explore_hello_frontend...
    explore_hello_frontend canister created with canister id: bw4dl-smaaa-aaaaa-qaacq-cai

このコマンドを実行すると、canister 構成ファイルで定義されているcanister のネットワーク固有の`.dfx/local/canister_ids.json` 識別子が表示されます。 実行環境はローカルに接続されているため、これらの 識別子はローカルでのみ有効で、プロジェクトでは ファイルに保存されます。

例えば

    {
    "explore_hello_backend": {
        "local": "br5f7-7uaaa-aaaaa-qaaca-cai"
    },
    "explore_hello_frontend": {
        "local": "bw4dl-smaaa-aaaaa-qaacq-cai"
    }
    }

## をビルドします。dapp

デフォルトの構成設定とプログラム・コードを調べ、ローカルのcanister 実行環境を起動したので、デフォルト・プログラムを実行可能なWebAssembly モジュールにコンパイルしてみましょう。

ローカル・コンピューターのターミナル・ウィンドウで、`explore_hello` プロジェクト・ディレクトリーに移動します。

以下のコマンドを実行して実行可能なcanister をビルドします：

    dfx build

以下のような出力が表示されるはずです：

    Building canisters...
    Building frontend...
    WARN: Building canisters before generate for Motoko
    Generating type declarations for canister explore_hello_frontend:
    src/declarations/explore_hello_frontend/explore_hello_frontend.did.d.ts
    src/declarations/explore_hello_frontend/explore_hello_frontend.did.js
    src/declarations/explore_hello_frontend/explore_hello_frontend.did
    Generating type declarations for canister explore_hello_backend:
    src/declarations/explore_hello_backend/explore_hello_backend.did.d.ts
    src/declarations/explore_hello_backend/explore_hello_backend.did.js
    src/declarations/explore_hello_backend/explore_hello_backend.did

ローカルのcanister 実行環境に接続しているため、`dfx build` コマンドにより、プロジェクトの`.dfx/local/` ディレクトリの下に`canisters` ディレクトリが追加されます。

次のコマンドを実行して、`dfx build` コマンドによって作成された`.dfx/local/canisters/explore_hello_backend` ディレクトリにWebAssembly および関連するアプリケーション・ファイルが含まれていることを確認します。

    ls -l .dfx/local/canisters/explore_hello_backend/

例えば、このコマンドは以下のような出力を返します：

    -rw-rw-rw-  1 pubs  staff      47 Jun 14 15:43 constructor.did
    -rw-r--r--  1 pubs  staff      47 Jun 14 15:43 explore_hello_backend.did
    -rw-r--r--  1 pubs  staff      32 Jun 14 15:43 explore_hello_backend.most
    -rw-r--r--  1 pubs  staff  134640 Jun 14 15:43 explore_hello_backend.wasm
    -rw-r--r--  1 pubs  staff    2057 Jun 14 15:43 index.js
    -rw-rw-rw-  1 pubs  staff       2 Jun 14 15:43 init_args.txt
    -rw-rw-rw-  1 pubs  staff      47 Jun 14 15:43 service.did
    -rw-r--r--  1 pubs  staff     175 Jun 14 15:43 service.did.d.ts
    -rw-r--r--  1 pubs  staff     174 Jun 14 15:43 service.did.js

`canisters/explore_hello_backend` ディレクトリには、以下のキー・ファイルが含まれています：

- `explore_hello_backend.did` ファイルには、メインのdapp のインターフェイス記述が含まれています。

- `index.js` ファイルには、dapp の関数のcanister インタフェースの JavaScript 表現が含まれています。

- `explore_hello_backend.wasm` ファイルには、プロジェクトで使用するアセットのコンパイルされたWebAssembly が含まれています。

`canisters/explore_hello_frontend` ディレクトリには、プロジェクトに関連するフロントエンドアセットを記述する同様のファイルが含まれています。

`canisters/explore_hello_backend` と`canisters/explore_hello_frontend` ディレクトリのファイルに加えて、`dfx build` コマンドは`idl` ディレクトリを作成します。

新しいフォルダ`src/declarations` が作成されたことを確認してください。このフォルダには、wasm を除く`.dfx/local` のフォルダのコピーが含まれます。これらのファイルには秘密は含まれていませんので、残りのソースコードと一緒にコミットすることをお勧めします。

## プロジェクトをローカルにデプロイ

`dfx build` コマンドを実行すると、プロジェクトの`canisters` ディレクトリにいくつかの成果物が作成されます。WebAssembly モジュールと`canister_manifest.json` ファイルは、dapp をInternet Computer ネットワーク上にデプロイするために必要です。

ローカル・コンピューターのターミナル・シェルで、`explore_hello` プロジェクト・ディレクトリーに移動します。

次のコマンドを実行して、`explore_hello` プロジェクトをローカル・ネットワークにデプロイします：

    dfx canister install --all

次のような出力が表示されます：

    Installing code for canister explore_hello_backend, with canister ID br5f7-7uaaa-aaaaa-qaaca-cai
    Installing code for canister explore_hello_frontend, with canister ID bw4dl-smaaa-aaaaa-qaacq-cai
    Uploading assets to asset canister...
    Fetching properties for all assets in the canister.
    Starting batch.

`dfx canister call` コマンドを実行し、次のコマンドを実行して呼び出すdapp と関数を指定します：

    dfx canister call explore_hello_backend greet '("everyone": text)'

このコマンドは

- `explore_hello` を指定します。 **canister**またはdapp 。

- `greet` 呼び出したい特定の**メソッド**または関数。

- `everyone` を 関数に渡す引数として指定します。`greet` 

コマンドによって`greet` 関数の返り値が表示されることを確認します。

例えば

    ("Hello, everyone!")

## デフォルトのフロントエンドを表示

開発環境に`node.js` がインストールされている場合、プロジェクトには、ブラウザで`explore_hello` dapp にアクセスするためのテンプレート`index.js` JavaScript ファイルを使用する簡単なフロントエンドの例が含まれています。

まだ開いていなければ、ローカルコンピュータでターミナルウィンドウを開き、`explore_hello` プロジェクトディレクトリに移動します。

テキストエディタで`src/explore_hello_frontend/src/index.js` ファイルを開き、テンプレート・スクリプトのコードを確認します：

    import { explore_hello } from "../../declarations/explore_hello_backend";
    
    document.getElementById("clickMeBtn").addEventListener("click", async () => {
        const name = document.getElementById("name").value.toString();
        // Interact with explore_hello actor, calling the greet method
        const greeting = await explore_hello_backend.greet(name);
    
        document.getElementById("greeting").innerText = greeting;
    });

テンプレート`index.js` は、新しく作成した`declarations` ディレクトリから`explore_hello` エージェントをインポートします。このエージェントは、`Main.mo` で作成したインタフェースと対話するように自動的に設定され、ユーザが`greeting` ボタンをクリックすると、`AnonymousIdentity` を使用してcanister を呼び出します。

このファイルは、`index.html` テンプレートファイルと連動して、`greet` 機能のための画像アセット、入力フィールド、ボタンを持つ HTML ページを表示します。

続行するには、`index.js` ファイルを閉じます。

以下のコマンドを実行して、プロジェクト用に作成されたフロントエンドアセットを表示します：

    ls -l .dfx/local/canisters/explore_hello_frontend/

コマンドは次のような出力を表示します：

    -rw-r--r--  1 pubs  staff    6269 Dec 31  1969 assetstorage.did
    -rw-r--r--  1 pubs  staff  432762 Jun 14 15:47 assetstorage.wasm.gz
    -rw-rw-rw-  1 pubs  staff    6269 Dec 31  1969 constructor.did
    -rw-r--r--  1 pubs  staff  432762 Jun 14 15:47 explore_hello_frontend.wasm.gz
    -rw-r--r--  1 pubs  staff    2064 Jun 14 15:47 index.js
    -rw-rw-rw-  1 pubs  staff       2 Jun 14 15:47 init_args.txt
    -rw-rw-rw-  1 pubs  staff    6269 Jun 14 15:47 service.did
    -rw-r--r--  1 pubs  staff    6582 Jun 14 15:47 service.did.d.ts
    -rw-r--r--  1 pubs  staff    7918 Jun 14 15:47 service.did.js

これらのファイルは、`dfx build` コマンドがノードモジュールとテンプレート`index.js` ファイルを使用して自動的に生成したものです。

次に、`npm start` で開発サーバを起動します。

出力は以下のようになるはずです：

    > explore_hello_frontend@0.2.0 start
    > webpack serve --mode development --env development
    
    <i> [webpack-dev-server] [HPM] Proxy created: /api  -> http://127.0.0.1:4943
    <i> [webpack-dev-server] [HPM] Proxy rewrite rule created: "^/api" ~> "/api"
    <i> [webpack-dev-server] Project is running at:
    <i> [webpack-dev-server] Loopback: http://localhost:8083/
    <i> [webpack-dev-server] On Your Network (IPv4): http://192.168.0.144:8083/
    <i> [webpack-dev-server] On Your Network (IPv6): http://[fe80::1]:8083/

ブラウザを開き、"Loopback "または "On Your Network (IPv4) "に移動します。URLアドレスに移動します。

サンプル・アプリケーションの HTML ページが表示されていることを確認してください。

例えば

![Sample HTML entry page](_attachments/explore-hello.png)

挨拶を入力し、**［Click Me**］をクリックして挨拶を返します。

例

![Return the name argument](_attachments/greeting.png)

## 次のステップ

canisters のデプロイ、削除、または管理の詳細については、[ canisters ](https://internetcomputer.org/docs/current/developer-docs/setup/manage-canisters) の管理を参照してください。

このガイドの次のステップについては、[ canisters のアップグレード ガイドを](upgrading.md)参照してください。

<!---
# 5: Writing and deploying canisters

## Overview

This guide covers the basics of writing and deploying Motoko canisters. For detailed information about Motoko's fundamentals, check out the [Motoko fundamentals](infrastructure.md) page.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal window on your local computer, if you don’t already have one open.

To create a new project, run the command:

```
dfx new explore_hello
```

Then, navigate into the project with the command:

```
cd explore_hello
```

## Review the default configuration

By default, creating a new project adds some template files to your project directory. You can edit these template files to customize the configuration settings for your project and to include your own code to speed up the development cycle.

Open the `dfx.json` configuration file in a text editor to review the default settings.

It may look like this:

```
{
"canisters": {
    "explore_hello_backend": {
    "main": "src/explore_hello_backend/main.mo",
    "type": "motoko"
    },
    "explore_hello_frontend": {
    "dependencies": [
        "explore_hello_backend"
    ],
    "frontend": {
        "entrypoint": "src/explore_hello_frontend/src/index.html"
    },
    "source": [
        "src/explore_hello_frontend/assets",
        "dist/explore_hello_frontend/"
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

Let’s take a look at a few of the default settings.

-   The `settings` section specifies the name of the WebAssembly module for your `explore_hello` project is `explore_hello`.

-   The `canisters.explore_hello` key specifies that the main program to be compiled is located in the path specified by the `main` setting, in this case, `src/explore_hello/main.mo` and the `type` setting indicates that this is a `motoko` program.

-   The `canisters.explore_hello_assets` key specifies configuration details about frontend assets for this project. Let’s skip those for now.

-   The `dfx` setting is used to identify the version of the software used to create the project.

-   The `networks` section specifies information about the networks to which you connect. The default settings bind the local canister execution environment to the local host address `127.0.0.1` and port `4943`.

If you have access to other Internet Computer network providers, the `networks` section can include network aliases and URLs for connecting to those providers.

You can leave the default settings as they are.

Close the `dfx.json` file to continue.

## Writing canister code

New projects always include a template `main.mo` source code file. You can edit this file to include your own code to speed up the development cycle.

Let’s take a look at the sample program in the default `main.mo` template file as a starting point for creating simple dapp using the Motoko programming language.

Open the `src/explore_hello_backend/main.mo` file in a text editor and review the code in the template:

```
actor {
    public func greet(name : Text) : async Text {
        return "Hello, " # name # "!";
    };
};
```

Let’s take a look at a few key elements of this program:

-   You might notice that this sample code defines an `actor` instead of a `main` function, which some programming languages require. For Motoko, the `main` function is implicit in the file itself.

-   Although the traditional "Hello, World!" program illustrates how you can print a string using a `print` or `println` function, that traditional program would not represent a typical use case for Motoko dapps that run on the Internet Computer.

-   Instead of a print function, this sample program defines an `actor` with a public `greet` function that takes a `name` argument with a type of `Text`.

-   The program then uses the `async` keyword to indicate that the program returns an asynchronous message consisting of a concatenated text string constructed using `"Hello, "`, the `#` operator, the `name` argument, and `"!"`.

We’ll explore code that uses `actor` objects and asynchronous message handling more a little later. For now, you can continue to the next section.

Close the `main.mo` file to continue.

## Start the local canister execution environment

Before you can deploy the default project, you need to connect to either the local canister execution environment, or to the Internet Computer blockchain mainnet.

Starting the local canister execution environment requires a `dfx.json` file, so you should be sure you are in your project’s root directory. For this guide, you should have two separate terminal shells, so that you can start and see network operations in one terminal and manage your project in another.

To start the local canister execution environment, first open a new terminal window or a new terminal tab on your local computer.

:::info
    -   You should now have **two terminals** open.
    -   You should have the **project directory** as your **current working directory**.
:::

Start the local canister execution environment by running the following command:

```
dfx start
```

Depending on your platform and local security settings, you might see a warning displayed. If you are prompted to allow or deny incoming network connections, click **Allow**.

After you start the local canister execution environment, you have one terminal that displays messages about network operations and another for performing project-related tasks.

Leave the terminal that displays network operations open and switch your focus to the terminal where you created your new project.

## Register canister identifiers

After you connect to the local canister execution environment, you can register with the network to generate unique, network-specific **canister identifiers** for your project.

In the [1.3: Deploying your first dapp](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md) developer journey tutorial, this step was performed as part of the `dfx deploy` command work flow. This guide demonstrates how to perform each of the operations independently.

To register canister identifiers for the local network, register unique canister identifiers for the canisters in the project by running the following command:

```
dfx canister create --all
```

The command displays the network-specific canister identifiers for the canisters defined in the `dfx.json` configuration file.

```
Creating canister explore_hello_backend...
explore_hello_backend canister created with canister id: br5f7-7uaaa-aaaaa-qaaca-cai
Creating canister explore_hello_frontend...
explore_hello_frontend canister created with canister id: bw4dl-smaaa-aaaaa-qaacq-cai
```

Because you are connected to the local canister execution environment, these canister identifiers are only valid locally and are stored for the project in the `.dfx/local/canister_ids.json` file.

For example:

```
{
"explore_hello_backend": {
    "local": "br5f7-7uaaa-aaaaa-qaaca-cai"
},
"explore_hello_frontend": {
    "local": "bw4dl-smaaa-aaaaa-qaacq-cai"
}
}
```

## Build the dapp

Now that you have explored the default configuration settings and program code and have started the local canister execution environment, let’s compile the default program into an executable WebAssembly module.

In the terminal window on your local computer, navigate to your `explore_hello` project directory.

Build the executable canister by running the following command:

```
dfx build
```

You should see output similar to the following:

```
Building canisters...
Building frontend...
WARN: Building canisters before generate for Motoko
Generating type declarations for canister explore_hello_frontend:
src/declarations/explore_hello_frontend/explore_hello_frontend.did.d.ts
src/declarations/explore_hello_frontend/explore_hello_frontend.did.js
src/declarations/explore_hello_frontend/explore_hello_frontend.did
Generating type declarations for canister explore_hello_backend:
src/declarations/explore_hello_backend/explore_hello_backend.did.d.ts
src/declarations/explore_hello_backend/explore_hello_backend.did.js
src/declarations/explore_hello_backend/explore_hello_backend.did
```

Because you are connected to the local canister execution environment, the `dfx build` command adds the `canisters` directory under the `.dfx/local/` directory for the project.

Verify that the `.dfx/local/canisters/explore_hello_backend` directory created by the `dfx build` command contains the WebAssembly and related application files by running the following command.

```
ls -l .dfx/local/canisters/explore_hello_backend/
```

For example, the command returns output similar to the following:

```
-rw-rw-rw-  1 pubs  staff      47 Jun 14 15:43 constructor.did
-rw-r--r--  1 pubs  staff      47 Jun 14 15:43 explore_hello_backend.did
-rw-r--r--  1 pubs  staff      32 Jun 14 15:43 explore_hello_backend.most
-rw-r--r--  1 pubs  staff  134640 Jun 14 15:43 explore_hello_backend.wasm
-rw-r--r--  1 pubs  staff    2057 Jun 14 15:43 index.js
-rw-rw-rw-  1 pubs  staff       2 Jun 14 15:43 init_args.txt
-rw-rw-rw-  1 pubs  staff      47 Jun 14 15:43 service.did
-rw-r--r--  1 pubs  staff     175 Jun 14 15:43 service.did.d.ts
-rw-r--r--  1 pubs  staff     174 Jun 14 15:43 service.did.js
```

The `canisters/explore_hello_backend` directory contains the following key files:

-   The `explore_hello_backend.did` file contains an interface description for your main dapp.

-   The `index.js` file contains a JavaScript representation of the canister interface for the functions in your dapp.

-   The `explore_hello_backend.wasm` file contains the compiled WebAssembly for the assets used in your project.

The `canisters/explore_hello_frontend` directory contains similar files to describe the frontend assets associated with your project.

In addition to the files in the `canisters/explore_hello_backend` and the `canisters/explore_hello_frontend` directories, the `dfx build` command creates an `idl` directory.

Verify that a new folder has been created, `src/declarations`. This folder will include copies of the folders from `.dfx/local`, except for the wasm. They do not contain any secrets, and we recommend committing these files along with the rest of your source code.

## Deploy the project locally

You’ve seen that the `dfx build` command creates several artifacts in a `canisters` directory for your project. The WebAssembly modules and the `canister_manifest.json` file are required for your dapp to be deployed on the Internet Computer network.

In a terminal shell on your local computer, navigate to your `explore_hello` project directory.

Deploy your `explore_hello` project on the local network by running the following command:

```
dfx canister install --all
```

The command displays output similar to the following:

```
Installing code for canister explore_hello_backend, with canister ID br5f7-7uaaa-aaaaa-qaaca-cai
Installing code for canister explore_hello_frontend, with canister ID bw4dl-smaaa-aaaaa-qaacq-cai
Uploading assets to asset canister...
Fetching properties for all assets in the canister.
Starting batch.
```

Run the `dfx canister call` command and specify the dapp and function to call by running the following command:

```
dfx canister call explore_hello_backend greet '("everyone": text)'
```

This command specifies:

-   `explore_hello` as the name of the **canister** or dapp you want to call.

-   `greet` as the specific **method** or function you want to call.

-   `everyone` as the argument to pass to the `greet` function.

Verify the command displays the return value of the `greet` function.

For example:

```
("Hello, everyone!")
```

## View the default frontend

If you have `node.js` installed in your development environment, your project includes a simple frontend example that uses a template `index.js` JavaScript file for accessing the `explore_hello` dapp in a browser.

Open a terminal window on your local computer, if you don’t already have one open, and navigate to your `explore_hello` project directory.

Open the `src/explore_hello_frontend/src/index.js` file in a text editor and review the code in the template script:

```
import { explore_hello } from "../../declarations/explore_hello_backend";

document.getElementById("clickMeBtn").addEventListener("click", async () => {
    const name = document.getElementById("name").value.toString();
    // Interact with explore_hello actor, calling the greet method
    const greeting = await explore_hello_backend.greet(name);

    document.getElementById("greeting").innerText = greeting;
});
```

The template `index.js` imports an `explore_hello` agent from our newly created `declarations` directory. The agent is automatically configured to interact with the interface we created in `Main.mo`, and makes calls to our canister using an `AnonymousIdentity` when the user clicks the `greeting` button.

This file works in conjunction with the template `index.html` file to display an HTML page with an image asset, input field, and button for the `greet` function.

Close the `index.js` file to continue.

View the frontend assets created for the project by running following command:

```
ls -l .dfx/local/canisters/explore_hello_frontend/
```

The command displays output similar to the following:

```
-rw-r--r--  1 pubs  staff    6269 Dec 31  1969 assetstorage.did
-rw-r--r--  1 pubs  staff  432762 Jun 14 15:47 assetstorage.wasm.gz
-rw-rw-rw-  1 pubs  staff    6269 Dec 31  1969 constructor.did
-rw-r--r--  1 pubs  staff  432762 Jun 14 15:47 explore_hello_frontend.wasm.gz
-rw-r--r--  1 pubs  staff    2064 Jun 14 15:47 index.js
-rw-rw-rw-  1 pubs  staff       2 Jun 14 15:47 init_args.txt
-rw-rw-rw-  1 pubs  staff    6269 Jun 14 15:47 service.did
-rw-r--r--  1 pubs  staff    6582 Jun 14 15:47 service.did.d.ts
-rw-r--r--  1 pubs  staff    7918 Jun 14 15:47 service.did.js
```

These files were generated automatically by the `dfx build` command using node modules and the template `index.js` file.

Then, start a development server with `npm start`.

The output should resemble the following:

```
> explore_hello_frontend@0.2.0 start
> webpack serve --mode development --env development

<i> [webpack-dev-server] [HPM] Proxy created: /api  -> http://127.0.0.1:4943
<i> [webpack-dev-server] [HPM] Proxy rewrite rule created: "^/api" ~> "/api"
<i> [webpack-dev-server] Project is running at:
<i> [webpack-dev-server] Loopback: http://localhost:8083/
<i> [webpack-dev-server] On Your Network (IPv4): http://192.168.0.144:8083/
<i> [webpack-dev-server] On Your Network (IPv6): http://[fe80::1]:8083/
```

Open a browser and navigate to the "Loopback" or "On Your Network (IPv4)" URL address in a web browser.

Verify that you see the HTML page for the sample application.

For example:

![Sample HTML entry page](_attachments/explore-hello.png)

Type a greeting, then click **Click Me** to return the greeting.

For example:

![Return the name argument](_attachments/greeting.png)

## Next steps

For more information on deploying, deleting or managing canisters, view the [managing canisters documentation](https://internetcomputer.org/docs/current/developer-docs/setup/manage-canisters).

For the next step in this guide, check out the [upgrading canisters guide](upgrading.md)
-->
