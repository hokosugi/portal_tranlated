# 機能統合

## 概要

これまでのセクションでは、IC上でcanisters の構築を開始する方法を説明しましたが、ここではdapp に様々な（時には高度な）追加機能を統合する方法を説明します。アイデンティティから台帳の統合、他のブロックチェーンとの統合、ICから外部への通信、さらにはdapp の制御の分散化まで、すべてここにあります。

## ビットコインとの統合

Bitcoinネットワークと直接統合することで、IC上のcanisters 、Bitcoinネットワーク上のトランザクションで直接、Bitcoinの受信、保有、送信を行うことができます。Canisters 、Bitcoinネットワーク上でBitcoinを保有する一般ユーザーとまったく同じように行動することができます。

- [ビットコインの統合](./bitcoin/index.md)。
- [どのように機能](./bitcoin/bitcoin-how-it-works.md)するか
- [ローカル開発には](./bitcoin/local-development.md)、ローカルでの実験方法を示すチュートリアルが含まれています。
- [Chain-key Bitcoin](./bitcoin/ckbtc.md)には、チェーンキービットコイン（ckBTC）の概要が記載されています。

## HTTPS アウトコール

IC上のHTTP(S)アウトコールは、canisters 、ブロックチェーンの外部にあるHTTP(S)サーバーに直接コールを行い、その応答をcanister のさらなる処理で使用することを可能にし、複製されたステートをそれらの入力を使用して安全にアップデートすることができます。ブロックチェーン史上初の試みであり、オラクルの必要性を軽減します。

- [HTTPSアウトコールは](./https-outcalls/index.md)、ICが外の世界とどのように通信できるかの概要を示します。
- また、オラクルとの比較も行っています[。](./https-outcalls/https-outcalls-how-it-works.md)

## ICP台帳

Internet Computer Protocol (ICP)は、台帳canister と呼ばれる専用のcanister を使ってICPの管理を実装しています。単一の台帳canister があり、NNSサブネット上の他の台帳canisters と共に動作します。台帳canister はアカウントとトランザクションを保持するスマートコントラクトです。

- ICP元帳の基本を見るには、[元帳の概要を](./ledger/index.md)ご覧ください。
- [Interactは](./ledger/interact-with-ledger.md)、ICP台帳とやり取りするためのコマンドとプロトコルフローを示します。
- [ローカル・セットアップ](./ledger/ledger-local-setup.md)ローカル環境で台帳を試す方法を説明します。
- [新しいトークンのデプロイ](./ledger/deploy-new-token.md)新しいトークンをデプロイしてRosettaに接続する方法を説明します。

## インターネットID

Internet Identity は、ユーザーが Web3 サービスやdapps とのセッションを作成し、従来のブロックチェーン取引に署名することを可能にします。

- [Internet Identityでは](../../references/ii-spec.md)、Internet Identityの概要を説明します。

## ロゼッタ

RosettaはCoinbaseによって導入されたオープン規格で、取引所、ブロックエクスプローラー、ウォレットにおけるブロックチェーントークンの統合を簡素化します。このドキュメントは、CeFi取引所で取引可能なトークンをIC上に展開したい場合や、ブロックエクスプローラーやウォレットに取り組んでいる場合に役立つ可能性があります。

- [Rosettaのページでは](./rosetta/index.md)、Rosettaノードのセットアップ方法とFAQについて説明しています。
- [Transfers](./rosetta/transfers.md)では、Rosetta Construction API を使って ICP を送金する方法を詳しく説明しています。
- [Neuron lifecycle s (IC asset allows controllers to participate the governance of the network by submitting and voting on proposals)の概要です。](./rosetta/neuron-lifecycle.md) neuron
- [Staking support](./rosetta/staking-support.md)は、Rosetta API の拡張を指定し、資金のステーキングと IC 上のガバナンスneuronの管理を可能にします。
- [ステーキングのチュートリアルでは](./rosetta/staking-tutorial.md)、neuron の作成プロセスを説明します。
- [Hotkeys](./rosetta/hotkeys.md) neuron 管理用のホットキーの生成方法について説明します。

## サービス・ナーバス・システム（SNS）

NNS が IC をコントロールするオープントークン化された DAO であるのと同様に、SNS はアルゴリズム DAO であり、開発者がdapps のために分散化されたトークンベースのガバナンスシステムを作成することを可能にします。 このセクションでは[SNS ドキュメントの概要を](./sns/index.md)説明し、次に開発者向けのドキュメントを提供します。

- [SNS の紹介。](./sns/introduction/sns-intro-high-level.md)
- [SNS 立ち上げの準備方法の紹介。](./sns/tokenomics/index.md)
- [SNS統合ドキュメント](./sns/integrating/index.md)
- [SNSテストに関する文書](./sns/testing/testing-before-launch.md)
- [SNS立ち上げの紹介](./sns/launching/launch-summary.md)
- [SNSの管理方法に関する情報。](./sns/managing/manage-sns-intro.md)

## 閾値ECDSA

ブロックチェーン上の閾値ECDSA実装は、秘密鍵を安全に保管し、適格なエンティティの要求に応じて、そのエンティティのみに署名を発行するハードウェアセキュリティモジュール（HSM）のオンチェーンペンダントと見なすことができます。ECDSAベースの）ブロックチェーンとの直接統合を促進することが特に重要です。

- [Threshold ECDSAでは](./t-ecdsa/index.md)、この機能で実現できることの概要を説明しています。
- 詳細については、[How it worksを](./t-ecdsa/t-ecdsa-how-it-works.md)ご覧ください。

<!---
# Functionality integrations

## Overview

While previous sections guide you to start building canisters on the IC, here you can see how to integrate various (sometimes advanced) extra functionality to your dapp. From identity to ledger integrations, to integrating with other blockchains, to communicating from the IC to the outside world, and even decentralizing control of your dapp, it's all here.

## Bitcoin Integration
Integrate directly with the Bitcoin network allowing canisters on the IC to receive, hold, and send Bitcoin, all directly with transactions on the Bitcoin network. Canisters can act exactly like regular users holding bitcoin on the Bitcoin network.

- [Bitcoin Integration](./bitcoin/index.md).
- [How it works](./bitcoin/bitcoin-how-it-works.md).
- [Local development](./bitcoin/local-development.md) contains a tutorial showing how to experiment locally.
- [Chain-key Bitcoin](./bitcoin/ckbtc.md) provides an overview of chain-key Bitcoin (ckBTC).

## HTTPS Outcalls
HTTP(S) outcalls on the IC enable canisters to directly make calls to HTTP(S) servers external to the blockchain and use the response in the further processing of the canister such that the replicated state can safely be updated using those inputs. A first in blockchain history, and alleviates the need for oracles.

* [HTTPS outcalls](./https-outcalls/index.md) gives an overview of how the IC can communicate with the world outside.
* [How it works](./https-outcalls/https-outcalls-how-it-works.md) to dive further into the details and gives a comparison against oracles.

## ICP Ledger
The Internet Computer Protocol (ICP) implements management of ICP using a specialized canister, called the ledger canister. There is a single ledger canister which runs alongside other canisters on the NNS subnet. The ledger canister is a smart contract that holds accounts and transactions.

* [Ledger overview](./ledger/index.md) to get a view of the ICP ledger basics.
* [Interact](./ledger/interact-with-ledger.md) shows the commands and protocol flows to interact with the ICP ledger.
* [Local setup](./ledger/ledger-local-setup.md) shows how to experiment with the ledger in your local environment.
* [Deploy new token](./ledger/deploy-new-token.md) describes how to deploy a new token and connect to Rosetta.

## Internet Identity
Internet Identity allows users to create sessions with Web3 services and dapps, and sign traditional blockchain transactions.
* [Internet Identity](../../references/ii-spec.md) gives an overview of Internet Identity.

## Rosetta
Rosetta is an open standard introduced by Coinbase to simplify the integration of blockchain-based tokens in exchanges, block explorers, and wallets. This documentation might help if you want to deploy a token on the IC that aims to be tradable on CeFi exchanges or if you are working on a block explorer or wallet.
* The [Rosetta page](./rosetta/index.md) describes how to set up a Rosetta node and answers some FAQs.
* [Transfers](./rosetta/transfers.md) details how to transfer ICP using the Rosetta Construction API.
* [Neuron lifecycle](./rosetta/neuron-lifecycle.md) gives an overview of neurons (IC assets allowing controllers to participate in the governance of the network by submitting and voting on proposals).
* [Staking support](./rosetta/staking-support.md) specifies extensions of the Rosetta API enabling staking funds and managing governance neurons on the IC.
* [Staking tutorial](./rosetta/staking-tutorial.md) walks through the process of creating a neuron.
* [Hotkeys](./rosetta/hotkeys.md) explains how to generate a hotkey for neuron management.

## Service Nervous System (SNS)
Similar to how the NNS is the open tokenized DAO that controls the IC, SNSs are algorithmic DAOs that allow developers to create decentralized, token-based governance systems for their dapps. This section provides an [overview of the SNS documentation](./sns/index.md) and then provides the documentation aimed at developers.

* [An introduction to the SNS.](./sns/introduction/sns-intro-high-level.md)
* [An introduction to how to prepare for an SNS launch.](./sns/tokenomics/index.md)
* [SNS integration documentation.](./sns/integrating/index.md)
* [SNS testing documentation.](./sns/testing/testing-before-launch.md)
* [An introduction to the SNS launch.](./sns/launching/launch-summary.md)
* [Information on how to manage an SNS.](./sns/managing/manage-sns-intro.md)

## Threshold ECDSA
A threshold ECDSA implementation on a blockchain can be viewed as the on-chain pendant to a hardware security module (HSM) that stores private keys securely and issues signatures on request of the eligible entities, and only to those. It is particularly important to facilitate direct integration with (ECDSA-based) blockchains.

* [Threshold ECDSA](./t-ecdsa/index.md) gives an overview of what can be achieved with this feature.
* See [How it works](./t-ecdsa/t-ecdsa-how-it-works.md) to dive further into the details.








-->
