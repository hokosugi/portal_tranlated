# 2: プロジェクト組織

## 概要

チュートリアル「[0.6: Introduction to dfx](/docs/tutorials/developer-journey/level-0/06-intro-dfx.md)developer journey」から IC SDK の見学を始められた方は、Internet Computer 上で動作するdapps を作成するための基本的なワークフローをすでにご覧になったことでしょう。 では、新しいプロジェクトを作成するときにワークスペースに追加されるデフォルトのファイルとフォルダを調べながら、そのワークフローを詳しく見てみましょう。

プレビューとして、次の図は、Internet Computer をコンピューター上でローカルに実行する場合の開発作業の流れを示しています。

![Development work flow](_attachments/dev-workflow-explore.svg)

Motoko プロジェクトのコード構成を調べるには、まず、次のコマンドを実行して新しいプロジェクトを作成します：

    dfx new explore_hello

`dfx new explore_hello` コマンドを実行すると、新しいプロジェクト名の下にデフォルトのプロジェクト・ディレクトリー構造と、プロジェクト用の新しい Git リポジトリーを含む、新しい`explore_hello` プロジェクトが作成されます。`node.js` がローカルにインストールされている場合、新しいプロジェクトを作成すると、フロントエンドのテンプレートコードと依存関係も追加されます。

JavaScript、Motoko 、その他のコンテキストでプロジェクト名を使用する際に有効になるように、英数字とアンダースコアのみを使用してください。ダッシュや特殊文字を含めることはできません。

以下のコマンドを実行して、デフォルトのディレクトリ構造を表示します：

    ls -l explore_hello

デフォルトでは、プロジェクトのディレクトリ構造には、少なくとも 1 つのソース・サブディレク トリ、テンプレート`README.md` ファイル、デフォルト`dfx.json` 設定ファイルが含まれています。

`node.js` がインストールされているかどうかによって、プロジェクト・ディレクトリには以下のファイルの一部またはすべてが含まれるかもしれません：

    explore_hello/
    ├── README.md      # default project documentation
    ├── dfx.json       # project configuration file
    ├── node_modules   # libraries for frontend development
    ├── package-lock.json
    ├── package.json
    ├── src            # source files directory
    │   ├── explore_hello_backend
    │   │   └── main.mo
    │   ├── explore_hello_frontend
    │       ├── assets
    │       │   ├── logo.png
    │       │   ├── main.css
    │       │   └── sample-asset.txt
    │       └── src
    │           ├── index.html
    │           └── index.js
    └── webpack.config.js

少なくとも、デフォルトのプロジェクト・ディレクトリには以下のフォルダとファイルが含まれます：

- リポジトリでプロジェクトを文書化するためのデフォルトの`README` ファイル。

- プロジェクトの設定可能なオプションを設定するためのデフォルトの`dfx.json` 設定ファイル。

- dapp で必要なすべてのソースファイルを格納するデフォルトの`src` ディレクトリ。

デフォルトの`src/explore_hello_backend/` ディレクトリにはテンプレート`main.mo` ファイルが含まれており、これを変更したり置き換えたりすることで、コア・プログラミング・ロジックを含めることができます。

このガイドでは基本的な使い方を説明するため、`main.mo` ファイルのみを使用します。`node.js` がインストールされている場合、プロジェクトディレクトリにはdapp のフロントエンドインターフェースを定義するために使用できる追加のファイルとディレクトリが含まれています。フロントエンドの開発と`assets` フォルダ内のテンプレートファイルについては、少し後で説明します。

## デフォルトの設定を確認

デフォルトでは、新しいプロジェクトを作成すると、プロジェクトディレクトリにいくつかのテンプレートファイルが追加されます。これらのテンプレートファイルを編集することで、プロジェクトのコンフィギュレーション設定をカスタマイズしたり、開発スピードを上げるために独自のコードを含めることができますcycle 。

プロジェクトのデフォルト設定ファイルを確認するには、`dfx.json` の設定ファイルをテキストエディタで開き、デフォルト設定を確認します。

ファイルの内容は以下のようになっているはずです：

```
{
  "canisters": {
    "explore_hello_backend": {
      "main": "src/explore_hello_backend/main.mo",
      "type": "motoko"
    },
    "explore_hello_frontend": {
      "dependencies": [
        "dexplore_hello_backend"
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
}%        
```

デフォルト設定のいくつかを見てみましょう。

- このファイルには、`explore_hello_frontend` と`explore_hello_backend` の2つのcanisters が定義されています。
- `explore_hello_backend` canister には`main` 属性があり、プログラムのコア・ファイル`main.mo` のファイル・パスを指定します。
- `explore_hello_backend` canister には`type` 'motoko\` があり、プログラミング言語を指定します。canister が Rust で書かれていた場合、この値は 'rust' となります。
- `explore_hello_frontend` canister は`explore_hello_backend` canister に依存します。つまり、バックエンドcanister がデプロイされ、実行されていることに依存しています。
- `explore_hello_frontend` canister のフロントエンドエンドポイントは`src/explore_hello_frontend/src/index.html` です。
- `explore_hello_frontend` canister の追加アセットは`source` 設定で指定します。
- 最後に、`explore_hello_frontend` canister は`type` に 'assets' を指定し、フロントエンドアセットcanister として設定します。

## デフォルトのプログラムコードを確認

新しいプロジェクトには常にテンプレート`main.mo` ソースコードファイルが含まれています。このファイルを編集して独自のコードを含めることで、開発をスピードアップできますcycle 。

Motoko プログラミング言語を使ってシンプルなdapp を作成する出発点として、デフォルトの`main.mo` テンプレートファイルにあるサンプルプログラムを見てみましょう。

プロジェクトのデフォルト・サンプル・プログラムを確認するには、テキストエディタで`src/explore_hello_backend/main.mo` ファイルを開き、テンプレート内のコードを確認します：

    actor {
      public query func greet(name : Text) : async Text {
        return "Hello, " # name # "!";
      };
    };

このプログラムのいくつかの重要な要素を見てみましょう：

- このサンプル・コードでは、`main` 関数ではなく`actor` 関数を定義しています。Motoko の場合、`main` 関数はファイル自体に暗黙的に含まれています。

- 従来の「Hello, World！」プログラムは、`print` または`println` 関数を使って文字列を印刷する方法を示していますが、この従来のプログラムは、Internet Computer 上で実行されるMotoko dapps の典型的な使用例ではありません。

- このサンプルプログラムでは、print 関数の代わりに、`name` 型の引数`Text` を受け取る public`greet` 関数で`actor` を定義します。

- このプログラムでは、`async` キーワードを使用して、`"Hello, "` 、`#` 演算子、`name` 引数、`"!"` を使用して構築されたテキスト文字列を連結した非同期メッセージを返すことを示します。

`actor` オブジェクトと非同期メッセージ処理を使用するコードについては、もう少し後で詳しく説明します。とりあえず、次のセクションに進んでください。

## 次のステップ

次に、dapp をデプロイする前に、[開発者環境を](./dev-env.md)セットアップしましょう。

<!---
# 2: Project organization

## Overview
If you started your tour of the IC SDK with the [0.6: Introduction to dfx](/docs/tutorials/developer-journey/level-0/06-intro-dfx.md) developer journey tutorial, you have already seen the basic work flow for creating dapps that run on the Internet Computer. Now, let’s take a closer look at that work flow by exploring the default files and folders that are added to your workspace when you create a new project.

As a preview, the following diagram illustrates the development work flow when running the Internet Computer locally on you computer.

![Development work flow](_attachments/dev-workflow-explore.svg)

To explore the code organization of a Motoko project, start by creating a new project by running the following command:

```
dfx new explore_hello
```

The `dfx new explore_hello` command creates a new `explore_hello` project, including a default project directory structure under the new project name and a new Git repository for your project. If you have `node.js` installed locally, creating a new project also adds some template frontend code and dependencies.

To ensure that project names are valid when used in JavaScript, Motoko, and other contexts, you should only use alphanumeric characters and underscores. You cannot include dashes or any special characters.

View the default directory structure by running the following command:

```
ls -l explore_hello
```

By default, the project directory structure includes at least one source subdirectory, a template `README.md` file, and a default `dfx.json` configuration file.

Depending on whether you have `node.js` installed, your project directory might include some or all of the following files:

```
explore_hello/
├── README.md      # default project documentation
├── dfx.json       # project configuration file
├── node_modules   # libraries for frontend development
├── package-lock.json
├── package.json
├── src            # source files directory
│   ├── explore_hello_backend
│   │   └── main.mo
│   ├── explore_hello_frontend
│       ├── assets
│       │   ├── logo.png
│       │   ├── main.css
│       │   └── sample-asset.txt
│       └── src
│           ├── index.html
│           └── index.js
└── webpack.config.js
```

At a minimum, the default project directory includes the following folders and files:

-   A default `README` file for documenting your project in the repository.

-   A default `dfx.json` configuration file to set configurable options for your project.

-   A default `src` directory for all of the source files required by your dapp.

The default `src/explore_hello_backend/` directory includes a template `main.mo` file that you can modify or replace to include your core programming logic.

Because this guide focuses on the basics of getting started, you are only going to use the `main.mo` file. If you have `node.js` installed, your project directory includes additional files and directories that you can use to define the frontend interface for your dapp. Frontend development and the template files in the `assets` folder are discussed a little later.

## Review the default configuration

By default, creating a new project adds some template files to your project directory. You can edit these template files to customize the configuration settings for your project and to include your own code to speed up the development cycle.

To review the default configuration file for your project, open the `dfx.json` configuration file in a text editor to review the default settings.

The contents of the file should resemble the following:

```
{
  "canisters": {
    "explore_hello_backend": {
      "main": "src/explore_hello_backend/main.mo",
      "type": "motoko"
    },
    "explore_hello_frontend": {
      "dependencies": [
        "dexplore_hello_backend"
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
}%        
```


Let’s take a look at a few of the default settings.

- There are two canisters defined in this file; `explore_hello_frontend` and `explore_hello_backend`. 
- The `explore_hello_backend` canister has a `main` attribute which specifics the file path of the program's core file, `main.mo`.
- The `explore_hello_backend` canister has a `type` of 'motoko`, which specifies the programming language. If the canister was written in Rust, this value would read 'rust'. 
- The `explore_hello_frontend` canister has a dependency of the `explore_hello_backend` canister, meaning it relies on the backend canister to be deployed and running for it to be deployed and ran. 
- The `explore_hello_frontend` canister has a frontend endpoint of `src/explore_hello_frontend/src/index.html`, which specifies the primary frontend asset. 
- Additional assets for the `explore_hello_frontend` canister are specified in the `source` configuration. 
- Lastly, the `explore_hello_frontend` canister has a `type` of 'assets', configuring it as a frontend asset canister. 

## Review the default program code

New projects always include a template `main.mo` source code file. You can edit this file to include your own code to speed up the development cycle.

Let’s take a look at the sample program in the default `main.mo` template file as a starting point for creating simple dapp using the Motoko programming language.

To review the default sample program for your project, open the `src/explore_hello_backend/main.mo` file in a text editor and review the code in the template:

```
actor {
  public query func greet(name : Text) : async Text {
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

## Next steps

Next, let's set up our [developer environment](./dev-env.md) before deploying the dapp. 

-->
