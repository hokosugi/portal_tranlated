# レベル0：飛行前の操作

- [0.1:Internet Computer](01-ic-overview.md) の[概要](01-ic-overview.md): IC 上でdapps を開発する方法を理解するために、開発者の旅の第一歩は IC のアーキテクチャとその機能を見ることです。このモジュールでは、以下を取り上げます：
  
  - の概要Internet Computer Protocol
    - プロトコル・スタック
      - ピアツーピア層
      - コンセンサス層
      - メッセージ・ルーティング層
      - 実行層
  - 連鎖鍵暗号の概要
    - 閾値署名スキーム。
  - canisters とスマートコントラクトの概要。
  - トークンの概要：
    - ICP。
    - Cycles.
  - ガバナンスの概要：
    - Network Nervous System.
    - サービス神経系.
  - インターネット・アイデンティティの概要.

- 0\.[2:Internet Computer 用語](02-ic-terms.md)このページでは、Internet Computer で構築するときに開発者が知っておくべき、よく使われる用語を紹介します。 このモジュールでは、以下の用語を扱います：
  
  - 概念：
    - Actor.
    - エージェント。
    - 認証変数。
    - 連鎖鍵暗号。
    - Cycles.
    - 分散型アプリケーション (dapp).
    - 分散型自律組織（DAO）。
    - 実行。
    - ICP。
    - プリンシパル。
    - 提案。
    - メッセージ。
    - レプリカ。
    - サブネット。
    - トランザクション。
  - Canister 用語：
    - スマートコントラクト。
    - Canisters.
    - Canister 開発キット（CDK）。
    - Canister 識別子。
    - Canister ステート。
    - コントローラー。
    - アイデンティティ。
    - クエリ。
    - ステート変更。
    - システムcanister.
    - 財布。
  - ツールと製品：
    - `dfx`.
    - インターネットアイデンティティ。
    - 台帳.
    - Motoko.

- 0\.[3: 開発者環境のセットアップ](03-dev-env.md)開発者の旅を始める前に、開発者環境をセットアップする必要があります。開発者環境は、コード・プロジェクトの開発に必要なツールとパッケージで構成されます。このモジュールでは、以下の内容を学びます：
  
  - 開発者環境のセットアップ
    - インターネット接続の確認
    - CLI へのアクセスの確認。
      - Windows ユーザーのためのオプション
    - IC SDKのダウンロードとインストール
    - IDEのダウンロードとインストール
    - gitのダウンロードとインストール
    - Node.jsのダウンロードとインストール。
    - すべてのパッケージとツールが最新バージョンにアップデートされていることを確認します。

- 0\.[4:canisters](04-intro-canisters.md) の[紹介](04-intro-canisters.md)：このページでは、canisters 、そのアーキテクチャを紹介し、開発できるcanisters のさまざまなタイプについて説明します。このモジュールがカバーするもの
  
  - アーキテクチャ：
    - 言語。
    - Actors.
    - なぜコードはWebAssembly にコンパイルされるのですか？
  - canisters の種類：
    - バックエンドcanisters.
    - フロントエンドcanisters.
    - カスタムcanisters 。
  - 単一または複数のcanister アーキテクチャの使用。
  - Canister 通信。
  - Canister コントローラ。
  - Cycles およびリソース課金。

- 0\.[5: 言語の紹介](05-intro-languages.md)：このページでは、dapps の開発に使用できるさまざまな言語について説明し、主にサポートされている 2 つの言語、Motoko と Rust について基本レベルの紹介をします。このモジュールでは
  
  - Motoko.
    - Motokoの属性について説明します。
  - Rust の属性。
    - Rust の属性。
  - Candid.
  - コミュニティが開発したCDK：
    - Python。
    - TypeScript。
    - Solidity。
    - C++.

- 0\.[6: dfx入門](06-intro-dfx.md):`dfx` はIC SDKのためのDFINITY コマンドライン実行環境です。Internet Computer 上でdapps を作成、管理、デプロイするために使用される主要ツールです：
  
  - 基本的な使い方と構文
    - サブコマンド
    - フラグ
    - オプション
  - `dfx` の最新バージョンへのアップグレード。
  - `dfx` の特定バージョンのインストール .
  - `dfx` での新規プロジェクトの作成 .
  - デフォルトのプロジェクト構造の確認
  - デフォルトの構成の確認
  - デフォルトのプログラム・コードの確認

<!---
# Level 0: Pre-flight operations 

- [0.1: Overview of the Internet Computer](01-ic-overview.md): In order to understand how to develop dapps on the IC, the first step in the developer journey is to take a look at the architecture of the IC and how it functions. This module covers:
    - Overview of The Internet Computer Protocol
        - Protocol stack
            - Peer-to peer layer
            - Consensus layer
            - Message routing layer
            - Execution layer
    - Overview of chain-key cryptography:
        - Threshold signature schemes.
    - Overview of canisters and smart contracts.
    - Overview of tokens:
        - ICP.
        - Cycles.
    - Overview of governance:
        - Network Nervous System.
        - Service Nervous System.
    - Overview of Internet Identity.

- [0.2: Internet Computer terminology](02-ic-terms.md): This page introduces some of the most commonly used terminology that developers should be aware of when building on the Internet Computer. This module covers the following terms: 
    - Concepts:
        - Actor.
        - Agent.
        - Certified variables.
        - Chain-key cryptography.
        - Cycles.
        - Decentralized application (dapp).
        - Decentralized autonomous organization (DAO).
        - Execution.
        - ICP.
        - Principal.
        - Proposal.
        - Messages.
        - Replica.
        - Subnet.
        - Transaction.
    - Canister terminology:
        - Smart contracts.
        - Canisters.
        - Canister development kit (CDK).
        - Canister identifier.
        - Canister state.
        - Controller.
        - Identity.
        - Query.
        - State change.
        - System canister.
        - Wallet.
    - Tools and products:
        - `dfx`.
        - Internet Identity.
        - Ledger.
        - Motoko.

- [0.3: Developer environment setup](03-dev-env.md): Before we can begin our developer journey, we need to set up our developer environment. A developer environment is comprised of tools and packages that are required to develop code projects. This module covers:
    - Setting up a developer environment:
        - Confirming an internet connection.
        - Confirming access to a CLI.
            - Options for Windows users.
        - Downloading and installing the IC SDK.
        - Downloading and installing an IDE.
        - Downloading and installing git.
        - Downloading and installing Node.js.
        - Assuring all packages and tools are updated to latest version.

- [0.4: Introduction to canisters](04-intro-canisters.md): This page introduces canisters, their architecture, and discusses the different types of canisters that can be developed. This module covers:
    - Architecture:
        - Languages.
        - Actors.
        - Why is code compiled into WebAssembly?
    - Types of canisters:
        - Backend canisters.
        - Frontend canisters.
        - Custom canisters.
    - Using a single or multiple canister architecture.
    - Canister communication.
    - Canister controllers.
    - Cycles and resource charges.

- [0.5: Introduction to languages](05-intro-languages.md): In this page, we discuss the different languages that can be used to develop dapps, and provide a base-level introduction to the two primarily supported languages: Motoko and Rust. This module covers:
    - Motoko.
        - Motoko's attributes.
    - Rust.
        - Rust's attributes.
    - Candid.
    - Community developed CDKs:
        - Python.
        - TypeScript.
        - Solidity.
        - C++.

- [0.6: Introduction to dfx](06-intro-dfx.md): `dfx` is the DFINITY command-line execution environment for the IC SDK. It is the primary tool used for creating, managing, and deploying dapps onto the Internet Computer. This module covers:
    - Basic usage and syntax:
        - Subcommands.
        - Flags.
        - Options.
    - Upgrading to the latest version of `dfx`.
    - Installing a specific version of `dfx`.
    - Creating a new project with `dfx`.
    - Exploring the default project structure.
    - Reviewing the default configuration.
    - Reviewing the default program code.


-->
