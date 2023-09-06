# Rust によるcanisters の開発入門

Rustは、活発な開発者コミュニティが存在する、強力で型に依存しないモダンなプログラミング言語です。Rust はWebAssembly にコンパイルされるため、Internet Computer ブロックチェーン上で動作するdapps を記述するための豊富な開発環境を提供します。

## 概要

Internet Computer ブロックチェーン上にデプロイ可能なdapps を Rust で記述する道を開くために、[IC SDK](../../setup/install/index.mdx) を使用できます。IC SDKは、Motoko プログラミング言語と同様にRustもサポートしています。IC SDKを使用してRustプロジェクトを作成するには、新しいプロジェクトを作成する際に`--type=rust` フラグを追加するだけです。例えば、ここでは`hello_world` という名前の Rust プロジェクトを作成します：

``` bash
dfx new --type=rust hello_world
```

新しいプロジェクトを開始するには、[Rustクイック](4-quickstart.md)スタートを参照してください。

## アーキテクチャ

Rustの開発をサポートするために、IC SDKには[Rustcanister development kit (Rust CDK)](https://github.com/dfinity/cdk-rs)が含まれています。

**ほとんどの開発者はIC SDKを使用しますが、経験豊富なRust開発者はIC SDKを完全に回避し、[Rust CDKを](https://github.com/dfinity/cdk-rs)直接使用することもできます。このドキュメントは、IC SDK を使用して Rustcanisters をビルドすることを前提としています。**

### Rust CDKは以下のクレートで構成されています：

- Rust CDKのコアは [`ic-cdk`](https://crates.io/crates/ic-cdk)クレートです。Rust CDK の中核はクレートで、Rust プログラムがInternet Computer ブロックチェーンシステム API と対話するためのコアメソッドを提供します。

- クレートは [`ic-cdk-macros`](https://crates.io/crates/ic-cdk-macros)クレートは、操作エンドポイントとAPIの構築を容易にする手続きマクロ（例：`update`,`query`,`import` ）を定義します。

- また [`ic-cdk-timers`](https://crates.io/crates/ic-cdk-timers)クレートは、複数の定期的なタスクをスケジュールするAPIを提供します。

RustCanisters の構築を始めるための[例が](https://github.com/dfinity/cdk-rs/tree/main/examples)いくつかあります。

<!---
# Introduction to developing canisters in Rust

Rust is a powerful and type-sound modern programming language with an active developer community. Because Rust compiles to WebAssembly, it offers a rich development environment for writing dapps to run on the Internet Computer blockchain. 

## Overview
To help pave the way for writing dapps in Rust that can be deployed on the Internet Computer blockchain, you can use the [IC SDK](../../setup/install/index.mdx). The IC SDK supports the Rust as well as the Motoko programming language. To create a Rust project using the IC SDK, all one needs to do is add the `--type=rust` flag while creating a new project. For example, here we create a Rust project named `hello_world`:

```bash
dfx new --type=rust hello_world
```

To start a new project see [Rust quick start](4-quickstart.md).

## Architecture

To support Rust development, the IC SDK includes the [Rust canister development kit (Rust CDK)](https://github.com/dfinity/cdk-rs). 

**While using the IC SDK is the typical path for most developers, experienced Rust developers may choose to circumvent IC SDK entirely and use the [Rust CDK](https://github.com/dfinity/cdk-rs) directly. This documentation assumes one is using the IC SDK to build Rust canisters.**

### The Rust CDK consists of the following crates:
- The core of Rust CDK is the [`ic-cdk`](https://crates.io/crates/ic-cdk) crate. It provides the core methods that enable Rust programs to interact with the Internet Computer blockchain system API.

- The [`ic-cdk-macros`](https://crates.io/crates/ic-cdk-macros) crate defines the procedural macros (e.g. `update`, `query`, `import`) that facilitate building operation endpoints and APIs.

- Also, the [`ic-cdk-timers`](https://crates.io/crates/ic-cdk-timers) crate provides an API to schedule multiple and periodic tasks.

There are a few [examples](https://github.com/dfinity/cdk-rs/tree/main/examples) to get you started building Rust Canisters.


-->
