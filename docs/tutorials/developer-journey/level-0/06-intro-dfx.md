# 0.6: dfx入門

## 概要

`dfx` は、IC SDK との対話に使用されるコマンドラインユーティリティです。 の作成、管理、 へのデプロイに使用される主要ツールです。dapps Internet Computer

`dfx` の親コマンドには、さまざまな操作を実行するために使用できるいくつかのフラグとサブコマンドがあります。まず、このコマンドの基本的な使い方を見て、それからdfxを使って最初のプロジェクトを作り始めましょう。

## 基本的な使い方

dfxの構文は以下の通りです：

    dfx [subcommand] [flag]

### サブコマンド

以下は開発者の旅シリーズを通して使用する`dfx` 必須サブコマンドのリストです。すべてのサブコマンドのリストについては、[dfxリファレンスドキュメントを](/docs/references/cli-reference/dfx-parent.md)参照してください。

- `build`:プロジェクトのソースコードからcanister の出力をビルドするために使用します。
- `canister`:デプロイされたcanisters を管理するために使用します。
- `deploy`:プロジェクトのソースコードから、1つまたはすべてのcanisters をデプロイします。デフォルトでは、すべてのcanisters がデプロイされます。
- `help`:特定のサブコマンドの使用情報を返します。
- `identity`:ID の作成と管理に使用します。
- `info`:バージョンやポート値などの情報を表示します。
- `ledger`:canister 。
- `new`:新規プロジェクトの作成に使用します。デフォルトでは、Motoko プロジェクトを作成します。
- `ping`:メインネットまたはローカルcanister 実行環境へのネットワーク接続をテストするために使用します。
- `quickstart`:ID とウォレットの初期設定を行います。
- `start`:現在のプロジェクトのローカルcanister 実行環境を開始するために使用します。
- `stop`:ローカルのcanister 実行環境を停止します。
- `upgrade`:インストールされている dfx のバージョンをアップグレードします。
- `wallet`:現在選択されている ID に関連付けられたデフォルトのcycles ウォレットのアドレス、カストディアン、コントローラ、cycles を管理するために使用します。

### フラグ

- `-h` `--help`: 使用情報を表示するために使用します。
- `-q` `--quiet`: 情報メッセージの抑制に使用されます。
- `-v` `--verbose`: 操作の詳細情報を表示します。
- `-V` `--version`: インストールされている dfx のバージョンを表示します。

### オプション

以下は、開発者の旅を通して参照する重要なオプションです。オプションの全リストは[リファレンスドキュメントを](/docs/references/cli-reference/dfx-parent.md)参照してください。

- `--identity <identity>`:コマンドで使用するユーザー ID を指定します。
- `--logfile <logfile>`:コマンドの出力ログを特定のファイルに書き出します。

## dfxの最新バージョンへのアップグレード

`dfx` の新バージョンがリリースされたら、可能な限り最新版をインストールすることをお勧めします。これにより、最新の機能、拡張、修正の恩恵を確実に受けることができます。

最新版にアップグレードするには、`dfx upgrade` コマンドを使用します。このコマンドは`dfx` の現在のバージョンと利用可能な最新バージョンを比較し、新しいバージョンが利用可能であれば、自動的に最新バージョンをダウンロードしてインストールします。

## 特定のバージョンのdfxのインストール

使用したい`dfx` の特定のリリースがある場合、`DFX_VERSION` 環境変数を設定し、インストールスクリプトを実行します。インストー ルスクリプトは、インストール中にこの環境変数を探し、どのバージョンをダウン ロードするかを決定します。変数が設定されていない場合、最新版がダウンロードされます。

`DFX_VERSION` 環境変数を設定し、特定のバージョンの`dfx` をインストールするには、コマンドを実行します：

    DFX_VERSION=0.14.1 sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

## dfxで新しいプロジェクトを作成

IC上のすべてのdapps は**プロジェクトとして**始まります。プロジェクトは`dfx` コマンドとサブコマンドを使って作成します。

はじめに、デフォルトのサンプルアプリを使用して、プロジェクトの作成方法を説明し、新しいプロジェクトが作成されたときに生成されるデフォルトのプロジェクト構造を調べます。

### ステップ 1: ローカル・コンピューターでターミナル・ウィンドウを開きます。

作業ディレクトリ`developer_journey` にいることを確認してください。

### ステップ 2: コマンドで 'hello\_world' という名前で新しいプロジェクトを作成します：

    dfx new hello_world

フラグを使用しない場合、`dfx new` コマンドはデフォルトのMotoko テンプレートを使用して新しいプロジェクトを作成します。Rust プロジェクトテンプレートを使ってプロジェクトを作成するには、`--type=rust` フラグをコマンドに含める必要があります。

この開発者の旅では、開発言語にMotoko を使用するので、このコマンドで追加のフラグを渡す必要はありません。

:::info
 `dfx` で新しいプロジェクトを作成するときは、英数字とアンダースコアのみを使用する必要があります。これは、Motoko 、JavaScript、その他のコンテキストでもプロジェクト名が有効であることを保証するためです。
::：

このコマンドを実行すると、`hello_world` という新しいプロジェクトディレクトリが作成されます。このディレクトリには、プロジェクトのデフォルトテンプレートファイルと、プロジェクトの新しい git リポジトリが含まれます。

### ステップ3: 次に、コマンドを使ってプロジェクトのディレクトリに移動します：

    cd hello_world

## デフォルトのプロジェクト構造を調べる

デフォルトでは、プロジェクトの構造は次のようになっています：

    hello_world/
    ├── README.md      # Default project documentation
    ├── dfx.json       # Project configuration file
    ├── node_modules   # Libraries for frontend development
    ├── package-lock.json
    ├── package.json
    ├── src            # Source files directory
    │   ├── hello_world_backend
    │   │   └── main.mo
    │   ├── hello_world_frontend
    │       ├── assets
    │       │   ├── logo.png
    │       │   ├── main.css
    │       │   └── sample-asset.txt
    │       └── src
    │           ├── index.html
    │           └── index.js
    └── webpack.config.js

このディレクトリでは、次のようなファイルやディレクトリが目立ちます：

- `README.md`:プロジェクトのドキュメントに使用するデフォルトのREADMEファイル。
- `dfx.json`:プロジェクトの設定可能なオプションを設定するためのデフォルトの設定ファイル。
- `src/`:dapp のすべてのソースファイルを含むソースディレクトリ。
- `hello_world_backend`:dapp のバックエンド・コード・ファイルを含むソース・ディレクトリ。
- `hello_world_frontend`:あなたのdapp のフロントエンドのコードファイルを含むソースディレクトリ。
- `hello_world_backend/main.mo`:Motoko dapp のコア・プログラミング・ロジックを含むように変更または置き換えることができます。

## デフォルト設定の確認

デフォルトでは、`dfx.json` ファイルには、新しいプロジェクト用に自動生成されたコンフィギュレーション設定が含まれています。これらの設定は、単純な 'Hello, world' プログラムであるデフォルトのdapp の基本的な機能を提供します。

プロジェクトのデフォルト設定ファイルを確認するには、`dfx.json` ファイルをコード・エディターまたはテキスト・エディターで開きます。内容は以下のようになります：

```
{
  "canisters": {
    "hello_world_backend": {
      "main": "src/hello_world_backend/main.mo",
      "type": "motoko"
    },
    "hello_world_frontend": {
      "dependencies": [
        "hello_world_backend"
      ],
      "frontend": {
        "entrypoint": "src/hello_world_frontend/src/index.html"
      },
      "source": [
        "src/hello_world_frontend/assets",
        "dist/hello_world_frontend/"
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

これらの設定をもう少し調べてみましょう：

- このファイルには、`hello_world_frontend` と`hello_world_backend` の2つのcanisters が定義されています。
- `hello_world_backend` canister には`main` 属性があり、プログラムのコア・ファイル`main.mo` のファイル・パスを指定します。
- `hello_world_backend` canister には`type` 'motoko' があり、プログラミング言語を指定します。canister が Rust で書かれていた場合、この値は 'rust' となります。
- `hello_world_frontend` canister は`hello_world_backend` canister に依存します。つまり、バックエンドcanister がデプロイされ、実行されている必要があります。
- `hello_world_frontend` canister のフロントエンドエンドポイントは`src/hello_world_frontend/src/index.html` です。
- `hello_world_frontend` canister の追加アセットは`source` 設定で指定します。
- 最後に、`hello_world_frontend` canister は`type` に 'assets' を指定し、フロントエンドアセットcanister として設定します。

## デフォルトのプログラムコードのレビュー

さて、デフォルトのプロジェクト構造について調べたところで、`main.mo` ファイルにあるデフォルトのプログラムコードを見てみましょう。これは`src/hello_world_backend` ディレクトリにあります。フロントエンドの開発とcanister ディレクトリにあるデフォルトファイルについては、後のチュートリアルで説明します。

新しいMotoko プロジェクトには、常にデフォルトのテンプレート`main.mo` ファイルが含まれます。このファイルのデフォルトの内容を見るには、`src/hello_world_backend/main.mo` ファイルをコードエディタかテキストエディタで開いてください。コードは以下のようになります：

    actor {
      public query func greet(name : Text) : async Text {
        return "Hello, " # name # "!";
      };
    };

この単純な「Hello, world」プログラムには、いくつかの重要な要素があります：

- このコードでは、`main` 関数ではなく、`actor` 関数を定義しています。Motoko では、`main` 関数はファイル自体に暗黙的に含まれています。
- 単純な`print` 関数を使用する従来の 'Hello, world' の代わりに、このバージョンの 'Hello, world' では、`Text` 型の name 引数を入力とする public`greet` 関数を持つactor を定義しています。
- そして、このプログラムは async キーワードを使用して、"Hello, ", \# 演算子, name 引数, "\!" を使用して構成されるテキスト文字列からなる非同期メッセージを返すことを示します。

actor オブジェクト、クラス、非同期メッセージについては、今後のチュートリアルで説明します。とりあえず、これで`dfx` の紹介は終わりです。

## 次のステップ

- 1.1:ライブ・デモを見る

<!---
# 0.6: Introduction to dfx

## Overview

`dfx` is a command line utility that is used to interact with the IC SDK. It is the primary tool that is used for creating, managing, and deploying dapps onto the Internet Computer. 

The `dfx` parent command has several flags and subcommands that can be used to perform a wide array of operations. First, we'll take a look at basic usage of the command, then we'll get started creating our first project using dfx. 

## Basic usage

The syntax for using dfx is as follows:

```
dfx [subcommand] [flag]
```

### Subcommands

The following is a list of the essential `dfx` subcommands that we'll be using throughout the developer journey series. For the full list of all possible subcommands, check out the [dfx reference documentation](/docs/references/cli-reference/dfx-parent.md).

- `build`: Used to build the canister output from the project's source code. 
- `canister`: Used to manage deployed canisters. 
- `deploy`: Deploys one or all canisters from the project's source code. By default, all canisters are deployed.
- `help`: Returns usage information for a specific subcommand.	
- `identity`: Used to create and manage identities. 
- `info`: Used to display information, such as version or port values. 
- `ledger`: Used to interact with accounts within the ledger canister. 
- `new`: Used to create a new project. By default, creates a Motoko project.
- `ping`: Used to test network connectivity to the mainnet or the local canister execution environment. 
- `quickstart`: Used to perform an initial one-time identity and wallet setup. 
- `start`: Used to start the local canister execution environment for the current project.
- `stop`: Used to stop the local canister execution environment.
- `upgrade`: Used to upgrade the version of dfx installed.	
- `wallet`: Used to manage addresses, custodians, controllers, and cycles for the default cycles wallet associated with the currently selected identity.

### Flags

- `-h`, `--help`: Used to display usage information.
- `-q`, `--quiet`: Used to suppress informational messages.
- `-v`, `--verbose`: Used to display detailed information about operations.
- `-V`, `--version`: Used to display the version of dfx installed. 

### Options

Below are the essential options that we'll be referencing throughout the developer journey. For the full list of options, see the [reference documentation](/docs/references/cli-reference/dfx-parent.md).

- `--identity <identity>`: Used to specify the user identity to be used with the command.
- `--logfile <logfile>`: Used to write the command's output logs to a specific file. 

## Upgrading to the latest version of dfx

When a new version of `dfx` is released, it is recommended that the latest version be installed whenever possible. This ensures that you are benefiting from the latest features, enhancements, and fixes. 

To upgrade to the latest version, the `dfx upgrade` command can be used. This command will compare your current version of `dfx` to the latest version available, and if a new version is available, the command will automatically download and install the newest version. 

## Installing a specific version of dfx

If there is a specific release of `dfx` you'd like to use, you can set the `DFX_VERSION` environment variable, then run the install script. The install script looks for this environment variable during installation to determine which version should be downloaded. If no variable is set, the latest version is downloaded. 

To set the `DFX_VERSION` variable and install a specific version of `dfx`, run the command:

```
DFX_VERSION=0.14.1 sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"
```

## Creating a new project with dfx

All dapps on the IC start off as **projects**. Projects are created using the `dfx` command and subcommands. 

To get started, we'll use the default sample app to demonstrate how to create a project and explore the default project structure that is generated when a new project is created. 

### Step 1: Open a terminal window on your local computer.

Assure that you are in your working directory, `developer_journey`. 

### Step 2: Create a new project with the name 'hello_world' with the command:

```
dfx new hello_world
```

When no flags are used, the `dfx new` command will create a new project using the default Motoko template. To create a project using the Rust project template, the flag `--type=rust` should be included in the command. 

In this developer journey, we will be using Motoko for our development language, so we do not need to pass any additional flags with this command. 

:::info
When creating new projects with `dfx`, only alphanumeric characters and underscores should be used. This is to assure that project names are valid within Motoko, JavaScript, and other contexts. 
:::

This command will create a new project directory called `hello_world` that contains the project's default template files and a new git repository for your project. 

### Step 3: Then, navigate into the project's directory with the command:

```
cd hello_world
```

## Exploring the default project structure

By default, the project structure will resemble the following:

```
hello_world/
├── README.md      # Default project documentation
├── dfx.json       # Project configuration file
├── node_modules   # Libraries for frontend development
├── package-lock.json
├── package.json
├── src            # Source files directory
│   ├── hello_world_backend
│   │   └── main.mo
│   ├── hello_world_frontend
│       ├── assets
│       │   ├── logo.png
│       │   ├── main.css
│       │   └── sample-asset.txt
│       └── src
│           ├── index.html
│           └── index.js
└── webpack.config.js
```

In this directory, the following files and directories are notable:

- `README.md`: The default README file to be used for documenting your project.
- `dfx.json`: The default configuration file used to set configurable options for your project.
- `src/`: The source directory that contains all of your dapp's source files.
- `hello_world_backend`: The source directory that contains your dapp's backend code files.
- `hello_world_frontend`: The source directory that contains your dapp's frontend code files.
- `hello_world_backend/main.mo`: The default template Motoko file that can be modified or replaced to include your dapp's core programming logic. 

## Reviewing the default configuration

By default, the `dfx.json` file will contain some automatically generated configuration settings for your new project. These settings will provide basic functionality for the default dapp, which is a simple 'Hello, world' program. 

To review the default configuration file for the project, open the `dfx.json` file in a code or text editor. The contents will resemble the following:

```
{
  "canisters": {
    "hello_world_backend": {
      "main": "src/hello_world_backend/main.mo",
      "type": "motoko"
    },
    "hello_world_frontend": {
      "dependencies": [
        "hello_world_backend"
      ],
      "frontend": {
        "entrypoint": "src/hello_world_frontend/src/index.html"
      },
      "source": [
        "src/hello_world_frontend/assets",
        "dist/hello_world_frontend/"
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

Let's explore these settings a bit further:

- There are two canisters defined in this file; `hello_world_frontend` and `hello_world_backend`. 
- The `hello_world_backend` canister has a `main` attribute which specifies the file path of the program's core file, `main.mo`.
- The `hello_world_backend` canister has a `type` of 'motoko', which specifies the programming language. If the canister was written in Rust, this value would read 'rust'. 
- The `hello_world_frontend` canister has a dependency of the `hello_world_backend` canister, meaning it relies on the backend canister to be deployed and running for it to be deployed and ran. 
- The `hello_world_frontend` canister has a frontend endpoint of `src/hello_world_frontend/src/index.html`, which specifies the primary frontend asset. 
- Additional assets for the `hello_world_frontend` canister are specified in the `source` configuration. 
- Lastly, the `hello_world_frontend` canister has a `type` of 'assets', configuring it as a frontend asset canister. 

## Reviewing the default program code

Now that we've explored the default project structure, let's take a look at the default program code located in the `main.mo` file. This is located in the `src/hello_world_backend` directory. We will cover frontend development and the default files located in the frontend canister directory in a later tutorial. 

New Motoko projects will always include a default, template `main.mo` file. To take a look at the file's default contents, open the `src/hello_world_backend/main.mo` file in a code or text editor. The code will resemble the following:

```
actor {
  public query func greet(name : Text) : async Text {
    return "Hello, " # name # "!";
  };
};
```

In this simple 'Hello, world' program, there are a few key elements:

- This code defines an `actor` rather than a `main` function as some other languages define. In Motoko, the `main` function is implicit in the file itself.
- Instead of the traditional 'Hello, world' that uses a simple `print` function, this version of 'Hello, world' defines an actor with a public `greet` function that takes an input of a name argument with the type of `Text`. 
- Then, the program uses an async keyword to indicate that the program will return an async message that consists of text string that is constructed using "Hello, ", the # operator, the name argument, and "!".


We'll explore actor objects, classes, and asynchronous messages in a future tutorial. For now, this will wrap up our introduction to `dfx`. 

## Next steps

- 1.1: Exploring a live demo.

-->
