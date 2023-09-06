# ノードとサブネットのブロックチェーン

## 概要

Internet Computer **サブネット・ブロックチェーンは**、ソフトウェア操作を実行するための物理的ハードウェアとCPUやメモリなどのリソースを提供します。各サブネットは、 ブロックチェーン・プロトコルのソフトウェア・コンポーネントを実行する、Internet Computer **ノードと**呼ばれる接続されたピアコンピュータである、独立に所有され制御されるいくつかの分散型マシンで構成されるブロックチェーンです。

各ノード上で実行されるInternet Computer ブロックチェーンのソフトウェアコンポーネントは、サブネットのブロックチェーン内のすべてのノードにわたってステートと計算を複製するため、**レプリカと**呼ばれます。

## レプリカのアーキテクチャ

レプリカの中核となるコンポーネントは、以下の論理レイヤーで構成されています：

- **ピアツーピア**(P2P)ネットワーキング層：ユーザー、サブネットブロックチェーン内の他のノード、他のサブネットブロックチェーンからのメッセージを収集し、アドバタイズします。ピアツーピア層が受信したメッセージは、サブネット内のすべてのノードに複製され、セキュリティ、信頼性、回復力を確保します。

- **コンセンサスレイヤーは**、ユーザーや異なるサブネットから受信したメッセージを選択してシーケンス化し、公証可能なブロックチェーンブロックを作成して、進化するブロックチェーンを形成するビザンチンフォールトトレラントコンセンサスによって最終化します。最終化されたブロックはメッセージ・ルーティング層に送られます。

- **メッセージ・ルーティング**層は、サブネット間でユーザーとシステムによって生成されたメッセージをルーティングし、dapps の入力キューと出力キューを管理し、実行のためにメッセージをスケジューリングします。

- メッセージ・ルーティング層から受け取ったメッセージを処理することで、スマート・コントラクトの実行に関わる決定論的計算を計算する**実行環境**。

次の図は、開発環境のローカルcanister 実行環境としてデプロイされたInternet Computer ブロックチェーンプロトコルコンポーネントの概要を簡略化したものです。

![Internet Computer components in a developer’s environment](_attachments/SDK-protocol-local-overview.svg)

:::info
開発者として、あなたのdapps やユーザーとあなたのdapps とのやり取りがInternet Computer ブロックチェーンアーキテクチャを通じてどのようにルーティングされるか、またはブロックチェーンネットワーク上でどのように複製されるかについて詳細を知る必要はありません。しかし、開発環境にはデプロイの実行環境を提供するためのレプリカ・コンポーネントが含まれており、本番デプロイのワークフローを現実的に理解することができるため、主要コンポーネントの一般的な理解は役立ちます。
::：

## サブネットのブロックチェーン

いわゆる**サブネットは**、canisters のセットを実行できる独自のブロックチェーンを作成するために、コンセンサスメカニズムの個別のインスタンスを実行するレプリカの集合体です。各サブネットは他のサブネットと通信することができ、**ルートサブネットによって**制御されます。**ルートサブネットは** [チェーンキー](/references/glossary.md#chain-key)暗号を使って様々なサブネットに権限を委譲します。

Internet Computer はサブネットを使用することで、無限に拡張することができます。従来のブロックチェーン（および個々のサブネット）の問題点は、単一のノードマシンの計算能力に制限されることです。なぜなら、すべてのノードが[コンセンサス](/references/glossary.md#consensus)アルゴリズムに参加するために、ブロックチェーン上で起こるすべてのことを実行しなければならないからです。複数の独立したサブネットを並行して実行することで、Internet Computer 、このシングルマシンの壁を突破することができます。

すべてのcanister が同じセキュリティ、サイズ、機能要件を持つわけではないため、すべてのサブネットが同じ構成を持つわけではありません。たとえば、`system` サブネット（[NNSと](/references/glossary.md#network-nervous-system-nns)その他の重要なサービスの多くを含む）は、canisters のために、cycles を課金しません。なぜなら、それらのcanisters は、どんな状況でも利用可能であるべきだからです。他のサブネットは、例えば、異なる機能を有効にしたり無効にしたりすることができます（ビットコインの統合など。

本稿執筆時点では、`system` と`application` の2つの主なサブネット・タイプがあります。ほぼすべてのcanisters はアプリケーション・サブネットで実行されています。`application` サブネットと比較して、`system` サブネットには以下の特徴があります：

- cycles アカウンティングは行われません。
- 1コールあたりの命令数制限に余裕があります。
- Wasmモジュールのサイズ制限に余裕があります。

<!---
# Nodes and subnet blockchains

## Overview

Internet Computer **subnet blockchains** provide physical hardware and resources, like CPU and memory, for performing software operations. Each subnet is a blockchain that consists of some number of decentralized, independently owned and controlled machines, connected peer computers called **nodes**, that run the software components of the Internet Computer blockchain protocol.

The Internet Computer blockchain software components that run on each node are called a **replica** because they replicate state and computation across all of the nodes in a subnet blockchain.

## Replica architecture

The core components of a replica are organized into the following logical layers:

-   A **peer-to-peer** (P2P) networking layer that collects and advertises messages from users, from other nodes in its subnet blockchain, and from other subnet blockchains. Messages received by the peer-to-peer layer are replicated to all of the nodes in the subnet to ensure the security, reliability, and resiliency.

-   A **consensus** layer that selects and sequences messages received from users and from different subnets to create blockchain blocks that can be notarized and finalized by Byzantine Fault Tolerant Consensus forming the evolving blockchain. These finalized blocks are delivered to the message routing layer.

-   A **message routing** layer that routes user- and system-generated messages between subnets, manages the input and output queues for dapps, and schedules messages for execution.

-   An **execution environment** that calculates the deterministic computation involved in executing a smart contract by processes the messages it receives from the message routing layer.

The following diagram provides a simplified overview of the Internet Computer blockchain protocol components deployed as a local canister execution environment in a development environment.

![Internet Computer components in a developer’s environment](_attachments/SDK-protocol-local-overview.svg)

:::info
As a developer, it isn’t necessary to know the details about how your dapps and user interactions with your dapps are routed through the Internet Computer blockchain architecture or replicated on the blockchain network. However, a general understanding of the key components can be useful because the development environment includes the replica components to provide an execution environment for deployment and a realistic sense of the workflow for a production deployment.
:::

## Subnet blockchains

A so-called **subnet** is a collection of replicas that run a separate instance of the consensus mechanism in order to create their own blockchain on which a set of canisters can run. Each subnet can communicate with other subnets and is controlled by the **root subnet**, which uses [chain key](/references/glossary.md#chain-key) cryptography to delegate its authority to the various subnets.

The Internet Computer uses subnets to allow it to scale indefinitely. The problem with traditional blockchains (and individual subnets) is that they are limited by the computing power of a single node machine, because every node has to run everything that happens on the blockchain to participate in the [consensus](/references/glossary.md#consensus) algorithm. Running multiple independent subnets in parallel allows the Internet Computer to break through this single-machine barrier.

Because not every canister has the same security,  size, or feature requirements, not every subnet has the same configuration. The `system` subnet (which contains the [NNS](/references/glossary.md#network-nervous-system-nns) and a bunch of other critical services), for example, does not charge any cycles for its canisters, because those canisters should be available in all circumstances. Other subnets can, for example, have different features enabled or disabled (such as the Bitcoin integration.

At the time of writing, there are two main subnet types: `system` and `application`. Almost all canisters run on application subnets. In comparison with the `application` subnet, the `system` subnet has the following characteristics:
- No cycles accounting takes place.
- More generous per-call instruction limit.
- More generous Wasm module size limit.

-->
