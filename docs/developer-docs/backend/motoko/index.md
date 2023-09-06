# canisters 。Motoko

## 概要

[Motoko](/motoko/main/motoko.md)は、Internet Computer のユニークな機能をサポートし、親しみやすく堅牢なプログラミング環境を提供するために、DFINITY によって[特別に設計さ](https://stackoverflow.blog/2020/08/24/motoko-the-language-that-turns-the-web-into-a-computer/)れました。

始めるには、Motoko [canister スマートコントラクトの](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts)構築をサポートする[IC SDKをインストール](../../setup/install/index.mdx)する必要があります。

## チュートリアルとガイド

[1.1：](/docs/tutorials/developer-journey/level-1/1.1-live-demo.md)チュートリアル「[ライブデモの](/docs/tutorials/developer-journey/level-1/1.1-live-demo.md)開発者の旅を[探る](/docs/tutorials/developer-journey/level-1/1.1-live-demo.md)」では、プロジェクトディレクトリの内容やサンプルコードを探ることなく、新しいプロジェクトを作成してデプロイするための基本的な作業の流れを簡単に紹介します。

次に、簡単なMotoko プログラムをいくつか書いてみることで、IC 上で動作するプログラムの作成を実際に体験していただきます。

これらのガイドでは、Motoko プログラミング言語を使用してdapps を記述する方法を説明します。他の言語で書かれたdapps の例については、DFINITY [examples](https://github.com/dfinity/examples)リポジトリを参照してください。

以下のガイドでは、IC 上で動作するdapps を書くための基礎を紹介します：

### クイックスタートMotoko

- [Motoko クイックスタート](./at-a-glance.md)

- [デフォルトプロジェクトの探索では](./explore-templates.md)、新しいプロジェクトを作成するときにワークスペースに追加されるデフォルトのファイルとフォルダを探索することで、プロジェクトを作成するためのワークフローを詳しく見ていきます。

- [ライブラリモジュールのインポート](./phonebook.md)リスト内のキーと値のペアを扱うための、Motoko 基本ライブラリ関数をインポートして使用する方法を説明します。

- [Use integers in calculator functions (電卓関数で整数を使用する](./calculator.md)) は、Motoko を扱う練習と、プロジェクト環境をカスタマイズする方法を学ぶために、dapp シンプルな電卓を書く方法を示します。

- [Increment a natural number](./counter-tutorial.md)は、カウンタの値をインクリメントして返す関数を持つactor を作成するdapp の書き方を説明します。

- [テキスト引数を渡す](./hello-location.md)ターミナルシェルのコマンドラインを使用して関数に引数を渡すさまざまな方法を紹介します。

- [Acceptcycles from a wallet](./simple-cycles.md)は、デフォルトのウォレットcanister から送られたcycles を受け入れる方法を説明します。

### 高度なMotoko

- [Query using anactor](./define-an-actor.md)"Hello, World\!"canister で通常定義される典型的な`print` 関数を、actor （オブジェクト）を`hello` 関数で定義することで置き換える方法を紹介します。

- [複数のactor](./multiple-actors.md)を[使用](./multiple-actors.md)する 1 つのプロジェクトに複数の無関係なactorを含める方法を説明し、同じプロジェクトに複数のcanisters をコンパイルする方法を説明します。

- [Add access control with identities](./access-control.md)複数のユーザー ID を作成して切り替える方法について説明します。

- [Make inter-canister calls](./intercanister-calls.md)あるcanister で定義された関数に対して、同じプロジェクト内の別のcanister から単純な呼び出しを行う方法を説明します。

- [Create scalable apps スケーラブルなアプリケーションを作成](./scalability-cancan.md)する 複数のcanisters を使用して、スケーラブルなアプリケーションを作成する方法を説明します。

### フロントエンドガイド

- [Customize the frontend (フロントエンドをカスタマイズする](../../frontend/custom-frontend.md)) では、シンプルな React フレームワークを使用して、デフォルトのサンプルcanister の新しいフロントエンドを作成する方法を説明し、表示されるインターフェイスをカスタマイズするための基本的な修正方法を説明します。CSS、HTML、JavaScript、Reactまたはその他のフレームワークを使ってユーザーインターフェースを構築する方法をすでに知っている場合は、このチュートリアルを読み飛ばしてかまいません。

- [スタイルシートの追加](../../frontend/add-stylesheet.md)では、Reactを使ってプロジェクトの新しいフロントエンドを作成するときにスタイルシートを追加する方法を説明します。React にスタイルシートを追加する方法をすでに知っている場合は、このチュートリアルを読み飛ばすことができます。

より高度なdapps 、基本的なビルディングブロックの使用方法についてのより詳細な例をカバーする追加のガイドは、[examples](https://github.com/dfinity/examples)リポジトリと[**Motoko プログラミング言語ガイドに**](/motoko/main/about-this-guide.md)あります。

<!---
# Introduction to developing canisters in Motoko

## Overview

[Motoko](/motoko/main/motoko.md) was [specifically designed](https://stackoverflow.blog/2020/08/24/motoko-the-language-that-turns-the-web-into-a-computer/) by DFINITY to support the unique features of the Internet Computer and to provide a familiar yet robust programming environment.

To get started, one should [install the IC SDK](../../setup/install/index.mdx) which supports building Motoko [canister smart contracts](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts).

## Tutorials and guides

The [1.1: Exploring a live demo](/docs/tutorials/developer-journey/level-1/1.1-live-demo.md) developer journey tutorial provides a simplified introduction to the basic work flow for creating and deploying a new project without exploring the contents of the project directory or sample code.

Next, we’ll explore writing a few simple Motoko programs to give you hands-on experience creating programs that run on the IC.

These guides illustrate how to write dapps using the Motoko programming language. For additional examples of dapps written in other languages, see the DFINITY [examples](https://github.com/dfinity/examples) repository.

The following guides introduce the basics for writing dapps that run on the IC:

### Getting started with Motoko

-   [Motoko quick start](./at-a-glance.md)

-   [Explore the default project](./explore-templates.md) takes a closer look at the work flow for creating projects by exploring the default files and folders that are added to your workspace when you create a new project.

-   [Import library modules](./phonebook.md) illustrates how to import and use a few basic Motoko base library functions for working with key-value pairs in a list.

-   [Use integers in calculator functions](./calculator.md) shows you how to write a simple calculator dapp for more practice working with Motoko and to learn more about how you can customize your project environment.

-   [Increment a natural number](./counter-tutorial.md) guides you through the process of writing a dapp that creates an actor with functions to increment and return the value of a counter.

-   [Pass text arguments](./hello-location.md) introduces different ways you can pass arguments to a function using the command-line in a terminal shell.

-   [Accept cycles from a wallet](./simple-cycles.md) illustrates how to accept cycles sent from the default wallet canister.

### Advanced Motoko

-   [Query using an actor](./define-an-actor.md) highlights how to replace the typical `print` function usually defined in a "Hello, World!" canister by defining an actor (object) with a `hello` function.

-   [Use multiple actors](./multiple-actors.md) describes how to include multiple unrelated actors in a single project to illustrate how you can compile multiple canisters for the same project.

-   [Add access control with identities](./access-control.md) describes how to create and switch between multiple user identities.

-   [Make inter-canister calls](./intercanister-calls.md) illustrates how to make simple calls to functions defined in one canister from another canister in the same project.

-   [Create scalable apps](./scalability-cancan.md) describes using multiple canisters to create applications that scale.

### Frontend guides

-   [Customize the frontend](../../frontend/custom-frontend.md) illustrates using a simple React framework to create a new frontend for the default sample canister and guides you through some basic modifications to customize the interface displayed. If you already know how to use CSS, HTML, JavaScript, and React or other frameworks to build your user interface, you can skip this tutorial.

-   [Add a stylesheet](../../frontend/add-stylesheet.md) illustrates how to add a stylesheet when you use React to create a new frontend for your project. If you already know how to add stylesheets to React, you can skip this tutorial.

Additional guides covering more advanced dapps and more detailed examples of how to use the basic building blocks are available in the [examples](https://github.com/dfinity/examples) repository and [**Motoko programming language guide**](/motoko/main/about-this-guide.md).

-->
