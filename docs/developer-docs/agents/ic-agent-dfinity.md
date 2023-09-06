---

sidebar_position: 4
sidebar_label: "From a Rust agent"
---
# RustエージェントからのIC呼び出し

## 概要

DFINTYによるRustエージェントは、アプリケーションを構築し、Internet Computer と対話できるようにする、使いやすいライブラリです。 IC SDKのRustベースの低レベルバックエンドとして機能します。

ic-agentは、Internet Computer を介してInternet Computer に直接接続できるRustクレートです。

エージェントは、複数のバージョンのレプリカAPIと互換性があり、レプリカのようなInternet Computer コンポーネントと通信するための低レベルAPIと、canisters としてデプロイされたソフトウェアアプリケーションと通信するための高レベルAPIの両方を公開するように設計されています。

`ic-agent` を使用しているプロジェクトの一例として、dfx が[あります](https://github.com/dfinity/sdk)。

- Rustエージェントのドキュメントは[こちら](https://docs.rs/ic-agent/latest/ic_agent)。
- Rustエージェントのソースコードは[こちら](https://github.com/dfinity/agent-rs)。

<!---

# Calling IC from a Rust agent

## Overview
The Rust agent by DFINTY is a simple-to-use library that enables you to build applications and interact with the Internet Computer. It serves as a Rust-based low-level backend for the IC SDK.

The ic-agent is a Rust crate that can connect directly to the Internet Computer through the Internet Computer..

The agent is designed to be compatible with multiple versions of the replica API, and to expose both low-level APIs for communicating with Internet Computer components like the replica and to provide higher-level APIs for communicating with software applications deployed as canisters.

One example of a project that uses the `ic-agent` is dfx, which you can find [here](https://github.com/dfinity/sdk).

- Rust agent documentation [here](https://docs.rs/ic-agent/latest/ic_agent).
- Rust agent source code [here](https://github.com/dfinity/agent-rs).

-->
