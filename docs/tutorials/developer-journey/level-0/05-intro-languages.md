# 0.5: 言語入門

## 概要

canisters を開発する場合、最も一般的な開発ワークフローはソフトウェア開発キット（SDK）を使用することです。Motoko と Rust をネイティブにサポートするInternet Computer SDK が最も一般的に使用されています。

ICはWebAssembly モジュールにコンパイルされたdapps をサポートしているため、canister 開発にはさまざまなプログラミング言語を使用できます。ただし、canister 開発キット（CDK）を使用する必要があります。CDKは、canisters の作成と管理に必要な機能とツールを備えたプログラミング言語を提供するIC SDKで使用されるアダプタです。IC SDKには、Motoko とRust用のCDKがバンドルされています。さらに、PythonやTypeScriptなどの追加言語用にコミュニティが作成したCDKがいくつかあり、個別にインストールすることができます。

1つのdapp の開発で複数の言語を使用することが可能です。異なるcanisters は、ICcanisters で使用されるインターフェイス記述言語（IDL）である Candid 言語を使用して互いに通信することができ、異なる言語の複数のcanisters が情報を共有して交換することができます。

## Motoko

Motoko Candidは、IC  の開発用に特別に設計された言語です。 、JavaScript、Ruby、Python、Solidityなどのアプリケーション層言語のバックグラウンドを持つ開発者にとって馴染みのあるルールと構文を備えているため、アプリケーション開発のための学習と使用が容易です。DFINITY canister Motoko 

Motoko は IC 用に開発・設計されているため、この開発者の旅シリーズでは、すべてのチュートリアルとコード・ウォークスルーにMotoko を使用します。

### Motoko 属性を使用します：

- **Candid サポート**:Motoko は、Motoko ランタイムとコンパイラシステムに組み込まれた、完全自動の Candid サポートを備えています。

- **安定メモリの**サポート：Motoko は、自動安定メモリ・サポートをサポートしています。

- **非同期データと制御フローのサポート：**ネイティブにサポートされています。

- **Actor パラダイムのサポート：**ネイティブにサポートされています。

- **IC 固有の静的解析：**複数の安全性チェックによる強制

- **Wasmバイナリサイズ：**Wasmのバイナリサイズは非常に小さいです。

- **ビルド時間**:Motoko のビルド時間は Rust よりも高速です。

- **学習の難易度** Motoko の学習はかなり簡単です。

- **メモリ管理：**メモリは自動ガベージコレクションプロセスで管理されます。

- **外部関数インターフェースのサポート：**未サポート。

## Rust

Rustは、IC SDKとDFINITY Rust CDKを通じてICでサポートされています。IC SDKには、ソフトウェアの一部としてRust CDKが自動的に付属していますが、IC SDKがインストールされていない場合は、Rust CDKを個別にインストールできます。Rust は、すでに Rust 環境に慣れ親しんでいる開発者、C または C++ のバックグラウンドを持つ開発者、または成熟したライブラリエコシステムを持つことが有益な大規模で複雑なプロジェクトを開発する開発者に適した選択肢です。

この開発者の旅シリーズのチュートリアルの多くは、同じ機能を表示する Rust バージョンで利用可能です。すべてのチュートリアルに Rust バージョンが用意されているわけではありませんが、Rust でフォローしたい方のために、該当する場合はリンクしています。

### Rustの属性

- **Candidのサポート：**Candid のサポート： Candid は Rust ライブラリでサポートされています。

- **安定メモリのサポート：**安定したメモリはRustライブラリでサポートされ、場合によっては自動で実行されます。

- **非同期データと制御フローのサポート：**ネイティブにサポートされています。

- **Actor パラダイムのサポート：**一部の言語機能と競合するため、エラーが発生しやすい。

- **IC 固有の静的解析：**静的チェックを含まないため、制限違反時にcanisters がトラップされる可能性があります。

- **Wasmバイナリのサイズ：**Wasmのバイナリは非常に大きく、ツールによる圧縮が必要。

- **ビルド時間：**Rust のビルド時間はMotoko よりも遅いです。

- **学習の難しさ：**Rustは非常に複雑な言語であるため、学習はかなり困難です。

- **メモリ管理：**メモリ管理はアプリケーションに依存し、コンパイラによる強力なサポートがあります。

- **外部関数インターフェースのサポート：**典型的なC言語のFFIと互換性があります。

## キャンディッド

Candidは、サービスのパブリックインターフェースを記述することを主な目的としたインターフェース記述言語です。ICを参照すると、サービスとは、canister の形で展開されるプログラムのことです。各canister には、サービスのインターフェース記述を定義する Candid ファイルがあります。

Candidは言語にとらわれず、Motoko 、Rust、JavaScriptなどの異なる言語で記述されたフロントエンドとサービス間の相互運用性を可能にします。さらに、Candidは、既存のクライアントからの互換性を失うことなく、サービスに新しいパラメータを安全に追加するなど、既存のクライアントを壊すことなく変更を指定することで、サービスインターフェースの進化をサポートします。

Candidインターフェース記述はサービスのパブリックメソッドを定義します。すべてのメソッドは引数と結果の型のシーケンスを持ち、ICに固有のアノテーションを含むことができます。インターフェイス記述により、CLIから直接、ウェブベースのフロントエンドを通して、または他のプログラムや言語からプログラム的にサービスと対話することが可能になります。

Candidは、Internet Computer のdapps の開発に特に適した様々な機能を持っています：

- Candidの実装は、Candidの値をホスト言語の値と型に直接マッピングするため、開発者は抽象的なCandidの値を構築したり分解したりする必要がありません。
- キャンディッドは、シリーズとその関連インターフェースを簡単な方法でアップグレードできるルールを定義しています。
- キャンディッドは高階言語であり、メソッドやサービスへの参照など、プレーンなデータ以上のものを受け取ることができます。
- Candidは、クエリーアノテーションのような特定のIC機能をネイティブにサポートしています。

## コミュニティが開発したCDK

ICコミュニティによって貢献されたCDKがいくつかあります。

### Python

Pythonは、ウェブ開発、AI機能、データ分析によく使われる言語です。読みやすく、汎用性があります。

Pythonは[Demergent Labsが](https://github.com/demergent-labs)開発した[Kybra CDKで](https://demergent-labs.github.io/kybra)利用できます。

### TypeScript

TypeScriptは、[Demergent](https://github.com/demergent-labs) Labsが開発した[Azle CDKから](https://demergent-labs.github.io/azle)利用できます。

### Solidity

Solidityは、ブロックチェーンプラットフォーム上のスマートコントラクトの記述と実装に使用されるオブジェクト指向言語で、最も広く知られているのはEthereumネットワークです。

Solidityは、[Bitfinity EVMチームによって](https://bitfinity.network/)開発された[Bitfinityを通じて](https://docs.bitfinity.network/)IC上でサポートされており、EVMベースのスマートコントラクトを作成する方法を提供しています。

### C++

C++は、icpp Worldによって開発された[icpp-pro CDKを通じて](https://docs.icpp.world/)利用可能です。

## 次のステップ

- 0\.[6: dfxの紹介](06-intro-dfx.md)。

<!---
# 0.5: Introduction to languages 

## Overview

When developing canisters, the most common development workflow is to use a software development kit (SDK). The Internet Computer SDK is the most commonly used, which natively supports Motoko and Rust out of the box. 

Since the IC supports dapps that have been compiled into WebAssembly modules, many different programming languages can be used for canister development. However, a canister development kit (CDK) needs to be used. A CDK is an adapter used by the IC SDK that provides a programming language with the necessary features and tools required to create and manage canisters. The IC SDK comes bundled with CDKs for Motoko and Rust. Additionally, there are several community created CDKs for additional languages, such as Python and TypeScript, that can be installed separately. 

It is possible to use multiple languages within a single dapp's development. Different canisters can communicate to one another using the Candid language, which is an interface description language (IDL) used by IC canisters, allowing for multiple canisters in different languages to share and exchange information. 

## Motoko

Motoko is a language that has been specifically designed by DFINITY for canister development on the IC. It supports the unique features and workflows on the IC while providing a robust yet familiar programming environment.  Motoko is easy to learn and use for application development, as it has a familiar set of rules and syntax for developers that have a background with application-layer languages, such as JavaScript, Ruby, Python, or Solidity. 

Since Motoko has been developed and designed for the IC, this developer journey series will use Motoko for all tutorials and code walkthroughs. 

### Motoko attributes:

- **Candid support:** Motoko has fully automatic Candid support that is built into the Motoko runtime and compiler system. 

- **Stable memory support:** Motoko supports automatic stable memory support. 

- **Asynchronous data and control flow support:** Natively supported.

- **Actor paradigm support:** Natively supported.

- **IC-specific static analysis:** Enforced through multiple safety checks. 

- **Wasm binary size:** Wasm binary size is very small. 

- **Build time:** Motoko has a build time faster than Rust. 

- **Learning difficulty:** Learning Motoko is fairly easy. 

- **Memory management:** Memory is managed using an automatic garbage collection process. 

- **Foreign function interface support:** Not yet supported. 

## Rust

Rust is supported on the IC through the IC SDK and the DFINITY Rust CDK. The IC SDK automatically comes with the Rust CDK as part of the software, but the Rust CDK can be installed separately if the IC SDK is not installed. Rust is a good choice for developers who are already familiar with Rust environments, come from a background in C or C++, or are developing large, complex projects that would benefit from having a mature library ecosystem. 

Many of the tutorials in this developer journey series are available in a Rust version that displays identical functionality. These will be linked where applicable for those that want to follow along with Rust, though not all tutorials will have a Rust version. 

### Rust attributes:

- **Candid support:** Candid is supported through Rust libraries, which needs routine manual intervention and conversion. 

- **Stable memory support:** Stable memory is supported through Rust libraries and is automatic in some cases. 

- **Asynchronous data and control flow support:** Natively supported.

- **Actor paradigm support:** Conflicts with some language features, resulting in being error-prone. 

- **IC-specific static analysis:** Does not include static checking, which may result in canisters trapping when violating restrictions.  

- **Wasm binary size:** Wasm binary is very large and requires compression through tools. 

- **Build time:** Rust has a slower build time than Motoko.

- **Learning difficulty:** Learning Rust can be fairly difficult, as the language is quite complex. 

- **Memory management:** Memory management is application specific and has strong support through the compiler. 

- **Foreign function interface support:** Typical C FFI compatibility. 

## Candid

Candid is an interface description language with the primary purpose to describe the public interface of a service. In reference to the IC, a service is a program deployed in the form of a canister.  Each canister has a Candid file that defines the interface description for the service. 

Candid is language-agnostic, allowing for interoperability between frontends and services that are written in different languages, such as Motoko, Rust, or JavaScript. Additionally, Candid supports service interface evolution by specifying changes without breaking existing clients, such as safely adding new parameters to a service without losing compatibility from existing clients.

A Candid interface description defines the public methods for a service. Every method has a sequence of argument and result types, and can include annotations that are specific to the IC. Interface descriptions make it possible to interact with the service directly from the CLI, through a web-based frontend, or programmatically from another program or language.

Candid has a variety of features that make it a particularly good choice for developing dapps on the Internet Computer. These features include:

- Candid's implementations map the Candid value directly to the values and types of the host language, meaning developers do not construct or deconstruct some abstract Candid value. 
- Candid defines rules for how series and their associated interface can be upgraded in a simple manner. 
- Candid is a higher-order language, meaning it can receive more than plain data such as references to methods and services. 
- Candid has native support for specific IC features, such as query annotation. 

## Community developed CDKs

There are several CDKs that have been contributed by the IC community. 

### Python

Python is a popular language for web development, AI functions, and data analysis. It is readable and versatile.

Python is available through the [Kybra CDK](https://demergent-labs.github.io/kybra) developed by [Demergent Labs](https://github.com/demergent-labs). 

### TypeScript

TypeScript is available through the [Azle CDK](https://demergent-labs.github.io/azle) developed by [Demergent Labs](https://github.com/demergent-labs).

### Solidity 

Solidity is an object-oriented language used for writing and implementing smart contracts on blockchain platforms, with the most widely known being the Ethereum network. 

Solidity is supported on the IC through [Bitfinity](https://docs.bitfinity.network/), developed by the [Bitfinity EVM team](https://bitfinity.network/), which provides a way to create EVM-based smart contracts. 

### C++

C++ is available through the [icpp-pro CDK](https://docs.icpp.world/) developed by icpp World.

## Next steps

- [0.6: Introduction to dfx](06-intro-dfx.md).

-->
