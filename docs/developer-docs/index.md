---

sidebar_position: 1
---
# はじめに

[Internet Computer ブロックチェーンは](https://wiki.internetcomputer.org/wiki/Introduction_to_ICP)、dapps を設計、構築、リリースする新しい方法で革命を起こすお手伝いをします。

## 概要

Internet Computer は、[canister スマートコントラクトを](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts)実行するブロックチェーンです。スマートコントラクトは、WebAssembly バイトコードと、バイトコードが実行されるメモリページを束ねたコードユニットです。Internet Computer は並行して実行される個々の[サブネットのブロックチェーンで](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#subnet-architecture)構成され、[チェーンキー暗号の](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)使用によって互いに接続されています。つまり、あるサブネットで実行されているcanisters は、Internet Computer ブロックチェーンの他のサブネットでホストされているcanisters をシームレスに呼び出すことができます。さらに、Internet Computer のガバナンス・システムは、新しいサブネットを追加することでInternet Computer の容量を動的に増やすことができ、dapps のスケールアウトを可能にします。

開発者はこのように、異なるサブネット上で並行して実行される複数のcanisters から成る新しいdapps を構築し、場合によっては既にInternet Computer 上で実行されている既存のcanisters と統合することができます。

[ICP](https://wiki.internetcomputer.org/wiki/Introduction_to_ICP)wikiの[ICPブロックチェーン入門を](https://wiki.internetcomputer.org/wiki/Introduction_to_ICP)参照。

## 開発者のワークフロー

Internet Computer ブロックチェーン上で実行されるdapps を開発するためのワークフローは、大きく分けて2つあります。

![Development paths](_attachments/local-remote-path-workflow.svg)

- **ローカル開発：**自分のコンピューター上でInternet Computer ブロックチェーンをシミュレートするローカルcanister 実行環境を起動します。そして、ローカルの実行環境でcanisters を書き、コンパイルし、インストールし、繰り返し更新します。これにより、cycles を使用しなくても、canisters をローカルでテストすることができます。

- **オンチェーンデプロイメント:** dapp の準備ができたら、Internet Computer ブロックチェーンメインネットにデプロイし、世界中で利用できるようにします。cycles [トークンとcycles](/concepts/tokens-cycles.md) で説明したように、Internet Computer ブロックチェーンメインネット上で動作させるには、canisters が必要です。

どの開発ワークフローを選択するかにかかわらず、canister を初めてデプロイする際には、ローカル実行環境またはInternet Computer のいずれかで、canister に一意の[プリンシパル識別子が](/references/glossary.md#principal)作成されることに留意してください。例えば、canister の開発をローカルで開始し、Internet Computer にデプロイする場合、canister は通常、ローカルの実行環境上とInternet Computer ブロックチェーンメインネット上では異なる識別子を持つことになります。また、新しいcanister をデプロイする前に、あるいはコードを書く前に、プリンシパル識別子を登録することも可能です。

## ガスコストと価格

Internet Computer は現在存在するブロックチェーンの中で最も安価なブロックチェーンの一つです。

Internet Computer では、"cycle"が処理、メモリ、ストレージ、ネットワーク帯域幅の形で消費されるリソースの測定単位です。すべてのcanister にはcycles アカウントがあり、canister によって消費されたリソースが課金されます。Internet Computerのユーティリティ・トークン（ICP）は、cycles に変換して、canister に転送することができます。Cycles は、canister メッセージ間に添付することによって、canisters 間で転送することもできます。

ICPは常に、1兆cycles が1 XDRに対応するという慣例に従って、[XDRで](https://en.wikipedia.org/wiki/Special_drawing_rights)測定されたICPの現在の価格を使用してcycles に変換できます。

\*\*1GBのデータを保存するのにかかるコストは年間約5ドルで、わずか1セントで約10\_000のトランザクションを実行できます。 \*\*

完全なコスト表は[こちら](./gas-cost.md)。

<!---

# Introduction

The [Internet Computer blockchain](https://wiki.internetcomputer.org/wiki/Introduction_to_ICP) is poised to help you start a revolution with a new way to design, build, and release dapps.

## Overview

The Internet Computer is a blockchain that runs [canister smart contracts](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts), which are code units bundling together WebAssembly bytecode and the memory pages the bytecode runs in. The Internet Computer is composed of individual [subnet blockchains](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#subnet-architecture) running in parallel and connected together by the use of [chain key cryptography](https://internetcomputer.org/how-it-works/#Chain-key-cryptography). This means that canisters running on a subnet can seamlessly call canisters hosted in any other subnet of the Internet Computer blockchain. Moreover, the governance system of the Internet Computer can dynamically increase the capacity of the Internet Computer by adding new subnets, allowing dapps to scale out.

Developers can thus build new dapps consisting of multiple canisters running in parallel on different subnets and possibly integrate them with existing canisters already running on the Internet Computer.

See [introduction to ICP blockchain](https://wiki.internetcomputer.org/wiki/Introduction_to_ICP) on the IC wiki.

## Developer workflow at a glance

At a high-level, there are two main possible workflows for developing dapps that run on the Internet Computer blockchain.

![Development paths](_attachments/local-remote-path-workflow.svg)

- **Local development:** you start a local canister execution environment simulating the Internet Computer blockchain on your computer. Then you write, compile, install and iteratively update your canisters in the local execution environment. This gives you the possibility to test your canisters locally without the need to use cycles to power them.

- **On-chain deployment:** once your dapp is ready you can then deploy it to the Internet Computer blockchain mainnet, making it available for the world to use it. Note that your canisters need to have cycles to be able to run on the Internet Computer blockchain mainnet, as discussed in [tokens and cycles](/concepts/tokens-cycles.md).

Regardless of the development workflow you choose, keep in mind that when you deploy a canister for the first time, either on a local execution environment or on the Internet Computer, a unique [principal identifier](/references/glossary.md#principal) is created for your canister. For example, if you start developing your canister locally and then deploy it to the Internet Computer, then your canister will generally have a different identifier on the local execution environment and on the Internet Computer blockchain mainnet. Note that it is also possible for you to register a principal identifier for your new canister before deploying it or even writing any line of code.

## Gas cost and pricing

The Internet Computer is one of the cheapest blockchains that exist today. 

On the Internet Computer, a "cycle" is the unit of measurement for resources consumed in the form of processing, memory, storage, and network bandwidth. Every canister has a cycles account to which resources consumed by the canister are charged. The Internet Computer’s utility token (ICP) can be converted to cycles and transferred to a canister. Cycles can also be transferred between canisters by attaching them to an inter-canister message.

ICP can always be converted to cycles using the current price of ICP measured in [XDR](https://en.wikipedia.org/wiki/Special_drawing_rights) using the convention that one trillion cycles correspond to one XDR.

**Storing one GB of data costs around 5$ per year, and for just one cent you can perform around 10_000 transactions. **

To see the full cost table, go [here](./gas-cost.md).

-->
