# 14:を使ったクエリactor

## 概要

チュートリアルの「[1.3: 最初のdapp](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md)開発者の旅を[デプロイする](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md)」では、actor オブジェクトと非同期メッセージングを含むInternet Computer のシンプルなcanister を初めて見ました。actor ベースのメッセージングを利用するcanisters を書くための次のステップとして、このガイドでは、従来の`Hello, World!` canister を変更してactor を定義し、canister をローカルのcanister 実行環境にデプロイしてテストする方法を説明します。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていない場合は、ローカルコンピュータでターミナルシェルを開きます。

以下のコマンドを実行して、新しいプロジェクトを作成します：

    dfx new actor_hello

以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd actor_hello

## デフォルト設定の変更

[デフォルトプロジェクトの](explore-templates)チュートリアルでは、新しいプロジェクトを作成すると、デフォルトの`dfx.json` 設定ファイルがプロジェクトディレクトリに追加されることを説明しました。このガイドでは、プロジェクトを反映させるためにデフォルトの設定をいくつか変更する必要があります。

`dfx.json` 設定ファイルをテキストエディタで開きます。`actor_hello` プロジェクトのデフォルト設定を確認します。

ソース・ファイルと出力ファイルの名前とパスは、すべて`actor_hello` プロジェクト名を使用していることに注意してください。

例えば、デフォルトのcanister の名前は`actor_hello_backend` で、メイン・コード・ファイルへのデフォルトのパスは`src/actor_hello_backend/main.mo` です。

これらのファイルやディレクトリの名前は変更できます。ただし、変更する場合は、ファイル・システム上でファイルやディレクトリーに使用する名前が、`dfx.json` 設定ファイルで指定した名前と一致していることを確認してください。デフォルトのディレクトリ名とファイル名を使用する場合は、変更する必要はありません。

ファイルから`actor_hello_assets` 構成設定をすべて削除します。

このガイドのサンプルcanister ではフロントエンドのアセットを使用していないので、設定ファイルからこれらの設定を削除することができます。

たとえば、`actor_hello_assets` セクションを削除すると、設定ファイルは次のようになります：

```
    {
    "canisters": {
        "actor_hello": {
        "main": "src/actor_hello/main.mo",
        "type": "motoko"
        }
    },
    "defaults": {
        "build": {
        "packtool": ""
        }
    },
    "version": 1
    }
```

変更を保存し、ファイルを閉じて続行します。

## デフォルトの変更canister

チュートリアルの[デフォルトプロジェクトの探索では](explore-templates)、新しいプロジェクトを作成すると、デフォルトの`src` ディレクトリとテンプレート`main.mo` ファイルが作成されることを説明しました。このガイドでは、テンプレートコードを修正してシンプルな "Hello, World\!" を作成します。canister Motoko でactor を定義します。Motoko では、ICPcanister をMotoko actor として表します。

次のコマンドを実行して、プロジェクトのソース・コード・ディレクトリに移動します：

    cd src/actor_hello_backend

テンプレート・ファイル`main.mo` をテキストエディタで開き、既存の内容を削除します。

次のステップでは、従来の "Hello, World\!" サンプルcanister のようなステートメントを表示するcanister を記述します。canister をInternet Computer 用にコンパイルするには、Motoko コードで`actor` を定義する必要があります。

このコードをコピーして、`main.mo` ファイルに貼り付けます：

    import Debug "mo:base/Debug";
    actor HelloActor {
       public query func hello() : async () {
          Debug.print ("Hello, World from DFINITY \n");
       }
    };

canister を定義しているMotoko actor を詳しく見てみましょう：

- このコードは`print` の機能を提供するために`Debug` モジュールをインポートしています。

- actor は`public query func` 宣言を使用してInternet Computer *クエリ・メソッドを*定義します。このメソッドは、actor のステートに対して永続的な変更を加える必要はありません。クエリとして宣言することで、クエリが完了した後に変更は一時的なものになり、破棄されます。

クエリーコールの使用方法の詳細については、[canisters ](/concepts/canisters-code.md#canister-state) の[クエリーコール](/concepts/canisters-code.md#query-update) [にはプログラムとステートの両方が含ま](/concepts/canisters-code.md#canister-state)れるを参照してください。

変更を保存し、`main.mo` ファイルを閉じます。

## canister のビルドの確認

通常、canister をビルドするには、まずInternet Computer ブロックチェーンのメインネット上で一意のcanister 識別子を予約する必要があります。

しかし、Internet Computer ブロックチェーンメインネットにまったく接続せずにプログラムをコンパイルすることも可能です。`dfx build --check` コマンドは、一時的にハードコードされたcanister 識別子を使用してこれを実現します。

プロジェクト・ディレクトリのルートに戻ります。

以下のコマンドを実行して、一時的にハードコードされた識別子でcanister 実行ファイルをビルドします：

    dfx build --check

`--check` オプションを使用すると、プロジェクトをローカルでビルドして、コンパイルされたことを確認し、生成されたファイルを検査することができます。`dfx build --check` コマンドは一時的な識別子しか使用しないため、次のような出力が表示されるはずです：

    WARN: Building canisters to check they build ok. Canister IDs might be hard coded.
    Building canisters...
    Building frontend...
    WARN: Building canisters before generate for Motoko
    Generating type declarations for canister actor_hello_frontend:
    src/declarations/actor_hello_frontend/actor_hello_frontend.did.d.ts
    src/declarations/actor_hello_frontend/actor_hello_frontend.did.js
    src/declarations/actor_hello_frontend/actor_hello_frontend.did
    Generating type declarations for canister actor_hello_backend:
    src/declarations/actor_hello_backend/actor_hello_backend.did.d.ts
    src/declarations/actor_hello_backend/actor_hello_backend.did.js
    src/declarations/actor_hello_backend/actor_hello_backend.did

canister のコンパイルに成功すると、デフォルトの`.dfx/local/canisters` ディレクトリと`.dfx/local/canisters/actor_hello_backend/` サブディレクトリの出力を確認できます。

例えば、`tree` コマンドを使用して、作成されたファイルを確認することができます：

    tree .dfx/local/canisters

このコマンドは、以下のような出力を表示します：

    .dfx/local/canisters
    ├── actor_hello_backend
    │   ├── actor_hello_backend.did
    │   ├── actor_hello_backend.most
    │   ├── actor_hello_backend.wasm
    │   ├── constructor.did
    │   ├── index.js
    │   ├── init_args.txt
    │   ├── service.did
    │   ├── service.did.d.ts
    │   └── service.did.js
    ├── actor_hello_frontend
    │   ├── actor_hello_frontend.wasm.gz
    │   ├── assetstorage.did
    │   ├── assetstorage.wasm.gz
    │   ├── constructor.did
    │   ├── index.js
    │   ├── init_args.txt
    │   ├── service.did
    │   ├── service.did.d.ts
    │   └── service.did.js
    └── idl
        └── 6m2n7-okd37-knyui-nvnda.did
    
        4 directories, 19 files

## プロジェクトのデプロイ

`dfx build --check` コマンドの出力をローカルのcanister 実行環境やInternet Computer メインネットにデプロイすることはできません。このプロジェクトをデプロイする場合は、次のようにします：

- ローカルのcanister 実行環境またはInternet Computer メインネットのいずれかに接続します。

- 接続固有のcanister 識別子を登録します。

- canister をデプロイします。

これらのステップをもう少し詳しく考えてみましょう。このプロジェクトをデプロイする前に、`dfx` が提供するローカルのcanister 実行環境か、Internet Computer ブロックチェーンメインネットのいずれかに接続する必要があります。ローカルのcanister 実行環境またはメインネットに接続した後、ローカルで定義した識別子を置き換えるために、一意の、**接続固有の** canister 識別子を生成する必要もあります。プロジェクトをローカルにデプロイしてみましょう。

このプロジェクトをローカルにデプロイするには、ターミナルを開き、必要に応じてプロジェクト・ディレクトリに移動します。

以下のコマンドを実行して、ローカルコンピューターでcanister 実行環境を起動します：

    dfx start --background

このガイドでは、`--background` オプションを使用して、ローカルのcanister 実行環境をバックグラウンド・プロセスとして起動できます。このオプションを使用すると、ローカル・コンピュータで別のターミナル・シェルを開くことなく、次のステップに進むことができます。

プロジェクトのディレクトリで以下のコマンドを実行し、ローカルのcanister 実行環境でプロジェクトの新しいcanister 識別子を生成します：

    dfx canister create actor_hello_backend

以下のような出力が表示されるはずです：

    Creating canister actor_hello_backend...
    actor_hello_backend canister created with canister id: dzh22-nuaaa-aaaaa-qaaoa-cai

`dfx canister create` コマンドはまた、接続固有のcanister 識別子を`.dfx/local` ディレクトリの`canister_ids.json` ファイルに保存します。

たとえば、次のようになります：

    {
    "actor_hello_backend": {
        "local": "dzh22-nuaaa-aaaaa-qaaoa-cai"
        }
    }

次のコマンドを実行して、ローカルのcanister 実行環境に`actor_hello_backend` プロジェクトをデプロイします：

    dfx deploy

次のような出力が表示されます：

    Committing batch.
    Committing batch with 18 operations.
    Deployed canisters.
    URLs:
    Frontend canister via browser
        actor_hello_frontend: http://127.0.0.1:8080/?canisterId=d6g4o-amaaa-aaaaa-qaaoq-cai
    Backend canister via Candid interface:
        actor_hello_backend: http://127.0.0.1:8080/?canisterId=dxfxs-weaaa-aaaaa-qaapa-cai&id=dzh22-nuaaa-aaaaa-qaaoa-cai

## クエリーcanister

これで、ローカルのcanister 実行環境にcanister がデプロイされ、`dfx canister call` コマンドを使用してcanister をテストできます。

`dfx canister call` を使用して、次のコマンドを実行して`hello` 関数を呼び出します：

    dfx canister call actor_hello_backend hello

このコマンドによって、ローカルのcanister 実行環境を実行している端末で、`hello` 関数で指定されたテキストとチェックポイント・メッセージが返されることを確認します。

例えば、canister は以下のような出力で "Hello, World fromDFINITY" と表示します：

```
2023-06-14 20:19:25.379721 UTC: [Canister bkyz2-fmaaa-aaaaa-qaaaq-cai] Hello, World from DFINITY 
```

:::info
 Internet Computer メインネットをバックグラウンドではなく別のターミナルで実行している場合、「Hello, World fromDFINITY 」のメッセージはメインネットのアクティビティを表示しているターミナルに表示されることに注意してください。
::：

## 次のステップ

次に、[複数のactor](multiple-actors.md) 。

<!---
# 14: Querying using an actor

## Overview

In the [1.3: Deploying your first dapp](/docs/tutorials/developer-journey/level-1/1.3-first-dapp.md) developer journey tutorial, you had your first look at a simple canister for the Internet Computer involving an actor object and asynchronous messaging. As the next step in learning to write canisters that take advantage of actor-based messaging, this guide illustrates how to modify a traditional `Hello, World!` canister to define an actor, then deploy and test your canister on a local canister execution environment.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal shell on your local computer, if you don’t already have one open.

Create a new project by running the following command:

```
dfx new actor_hello
```

Change to your project directory by running the following command:

```
cd actor_hello
```

## Modify the default configuration

In the [exploring the default project](explore-templates) tutorial, you saw that creating a new project adds a default `dfx.json` configuration file to your project directory. In this guide, you need to modify a few of the default settings to reflect your project.

Open the `dfx.json` configuration file in a text editor. Check the default settings for the `actor_hello` project.

Notice that the names and paths to source and output files all use the `actor_hello` project name.

For example, the default canister name is `actor_hello_backend` and the default path to the main code file is `src/actor_hello_backend/main.mo`.

You can rename any of these files or directories. If you make any changes, however, be sure that the names you use for your files and directories on the file system match the names you specify in the `dfx.json` configuration file. If you plan to use the default directory and file names, no changes are necessary.

Remove all of the `actor_hello_assets` configuration settings from the file.

The sample canister for this guide doesn’t use any frontend assets, so you can remove those settings from the configuration file.

For example, if you remove the `actor_hello_assets` section, the configuration file looks like this:


```
    {
    "canisters": {
        "actor_hello": {
        "main": "src/actor_hello/main.mo",
        "type": "motoko"
        }
    },
    "defaults": {
        "build": {
        "packtool": ""
        }
    },
    "version": 1
    }
```

Save your changes and close the file to continue.

## Modify the default canister

In the [exploring the default project](explore-templates) tutorial, you saw that creating a new project creates a default `src` directory with a template `main.mo` file. In this guide, you modify the template code to create a simple "Hello, World!" canister. by defining an actor in Motoko. In Motoko, an ICP canister is represented as a Motoko actor.

Change to the source code directory for your project by running the following command:

```
cd src/actor_hello_backend
```

Open the template `main.mo` file in a text editor and delete the existing content.

The next step is to write a canister that prints a statement like the traditional "Hello, World!" sample canister. To compile the canister for the Internet Computer, however, your Motoko code must define an `actor`.

Copy and paste this code into the `main.mo` file:

```
import Debug "mo:base/Debug";
actor HelloActor {
   public query func hello() : async () {
      Debug.print ("Hello, World from DFINITY \n");
   }
};
```

Let’s take a closer look at this Motoko actor defining our canister:

-   The code imports a `Debug` module to provide the `print` functionality.

-   The actor uses the `public query func` declaration to define an Internet Computer *query* method. Our method doesn’t need to make any permanent changes to the state of the actor. Declaring it as a query means that any changes it does make are transient and discarded after the query completes.

For more information about using a query call, see [query calls](/concepts/canisters-code.md#query-update) in [canisters include both program and state](/concepts/canisters-code.md#canister-state).

Save your changes and close the `main.mo` file.

## Checking that the canister builds

Usually, in order to build a canister, it’s necessary to first reserve a unique canister identifier on the Internet Computer blockchain mainnet.

However, it’s also possible to compile your program without connecting to the Internet Computer blockchain mainnet at all. The `dfx build --check` command uses a temporary, hard-coded canister identifier to accomplish this.

Navigate back to the root of your project directory.

Build the canister executable with a temporary, hard-coded identifier by running the following command:

```
dfx build --check
```

The `--check` option enables you to build a project locally to verify that it compiles and to inspect the files produced. Because the `dfx build --check` command only uses a temporary identifier, you should see output similar to the following:

```
WARN: Building canisters to check they build ok. Canister IDs might be hard coded.
Building canisters...
Building frontend...
WARN: Building canisters before generate for Motoko
Generating type declarations for canister actor_hello_frontend:
src/declarations/actor_hello_frontend/actor_hello_frontend.did.d.ts
src/declarations/actor_hello_frontend/actor_hello_frontend.did.js
src/declarations/actor_hello_frontend/actor_hello_frontend.did
Generating type declarations for canister actor_hello_backend:
src/declarations/actor_hello_backend/actor_hello_backend.did.d.ts
src/declarations/actor_hello_backend/actor_hello_backend.did.js
src/declarations/actor_hello_backend/actor_hello_backend.did
```

If the canister compiles successfully, you can inspect the output in the default `.dfx/local/canisters` directory and `.dfx/local/canisters/actor_hello_backend/` subdirectory.

For example, you might use the `tree` command to review the files created:

```
tree .dfx/local/canisters
```

The command displays output similar to the following:

```
.dfx/local/canisters
├── actor_hello_backend
│   ├── actor_hello_backend.did
│   ├── actor_hello_backend.most
│   ├── actor_hello_backend.wasm
│   ├── constructor.did
│   ├── index.js
│   ├── init_args.txt
│   ├── service.did
│   ├── service.did.d.ts
│   └── service.did.js
├── actor_hello_frontend
│   ├── actor_hello_frontend.wasm.gz
│   ├── assetstorage.did
│   ├── assetstorage.wasm.gz
│   ├── constructor.did
│   ├── index.js
│   ├── init_args.txt
│   ├── service.did
│   ├── service.did.d.ts
│   └── service.did.js
└── idl
    └── 6m2n7-okd37-knyui-nvnda.did

    4 directories, 19 files
```

## Deploy the project

You cannot deploy the output from the `dfx build --check` command to a local canister execution environment or the Internet Computer mainnet. If you wanted to deploy this project, you would need to do the following:

-   Connect to the either the local canister execution environment or the Internet Computer mainnet.

-   Register a connection-specific canister identifier.

-   Deploy the canister.

Let’s consider these steps in a bit more detail. Before you can deploy this project, you must connect to either your local canister execution environment, provided by `dfx`, or to the Internet Computer blockchain mainnet. After you connect to a local canister execution environment or the mainnet, you must also generate a unique, **connection-specific** canister identifier to replace your locally-defined identifier. To see the steps involved for yourself, let’s deploy the project locally.

To deploy this project locally, open a terminal and navigate to your project directory, if needed.

Start the local canister execution environment on your local computer by running the following command:

```
dfx start --background
```

For this guide, you can use the `--background` option to start the local canister execution environment as background processes. With this option, you can continue to the next step without opening another terminal shell on your local computer.

Generate a new canister identifier for your project on the local canister execution environment by running the following command in your project's directory:

```
dfx canister create actor_hello_backend
```

You should see output similar to the following:

```
Creating canister actor_hello_backend...
actor_hello_backend canister created with canister id: dzh22-nuaaa-aaaaa-qaaoa-cai
```

The `dfx canister create` command also stores the connection-specific canister identifier in a `canister_ids.json` file in the `.dfx/local` directory.

For example:

```
{
"actor_hello_backend": {
    "local": "dzh22-nuaaa-aaaaa-qaaoa-cai"
    }
}
```

Deploy your `actor_hello_backend` project on the local canister execution environment by running the following command:

```
dfx deploy
```

The command displays output similar to the following:

```
Committing batch.
Committing batch with 18 operations.
Deployed canisters.
URLs:
Frontend canister via browser
    actor_hello_frontend: http://127.0.0.1:8080/?canisterId=d6g4o-amaaa-aaaaa-qaaoq-cai
Backend canister via Candid interface:
    actor_hello_backend: http://127.0.0.1:8080/?canisterId=dxfxs-weaaa-aaaaa-qaapa-cai&id=dzh22-nuaaa-aaaaa-qaaoa-cai
```

## Query the canister

You now have a canister deployed on your local canister execution environment and can test your canister by using the `dfx canister call` command.

Use `dfx canister call` to call the `hello` function by running the following command:

```
dfx canister call actor_hello_backend hello
```

Verify that the command returns the text specified for the `hello` function along with a checkpoint message in the terminal running the local canister execution environment.

For example, the canister displays "Hello, World from DFINITY" in output similar to the following:

```
2023-06-14 20:19:25.379721 UTC: [Canister bkyz2-fmaaa-aaaaa-qaaaq-cai] Hello, World from DFINITY 
```

:::info
Note that if you are running the Internet Computer mainnet in a separate terminal instead of in the background, the "Hello, World from DFINITY" message is displayed in the terminal that displays the mainnet activity.
:::

## Next steps
Next, let's take a look at using [multiple-actors](multiple-actors.md).
-->
