# 0.1:概要Internet Computer

## 概要

**Internet Computer (IC)**は、データやプログラムをホストするために使用できる、安全で透明なブロックチェーンベースのネットワークです。IC上でホストされるプログラムとそのデータは、**分散型アプリケーションと**呼ばれ、しばしば次のように略されます。 **dapps**.

Dapps **スマートコントラクトの**開発とデプロイメントによって作成されます。 **canisters**と呼ばれます。各canister は、**サブネットと**呼ばれる**ノード**上で動作する独立したブロックチェーンネットワーク上でホストされています。

これらの用語については、次のセクション「[Internet Computer 用語](02-ic-terms.md)」でもう少し掘り下げて説明します。

dapps をIC上で開発する方法を理解するために、まずICのアーキテクチャとその機能を見てみましょう。

## ICのアーキテクチャInternet Computer Protocol

Internet Computer の中核はInternet Computer Protocol (ICP)です。ICPは、各サブネットのノード上で実行される4層の技術スタックです。この4層スタックの実装により、各サブネットはブロックチェーンベースの**複製ステートマシンを**作成することができ、他のサブネットと非同期通信を行いながら、他のサブネットから独立して動作することができます。各サブネットは、エンドユーザーや他のサブネットから受信した**メッセージを**処理します。

### プロトコルスタック

ICPは4つのレイヤーで構成されています：

1.  ピアツーピア
2.  コンセンサス
3.  メッセージ・ルーティング
4.  実行

ピアツーピア層とコンセンサス層は、受信したメッセージを選択し順序付けし、**ブロックの**形で上位2つの層に提供します。メッセージ・ルーティング層と実行層は、順序付けられたメッセージを含むこれらのブロックを受け取ると、サブネット上のすべてのノードにわたって完全に決定論的な方法でそれらを実行することができます。これはメッセージ実行の**ラウンドを示して**おり、サブネット内のすべてのノードがメッセージ実行の各ラウンドで同じ開始ステートから同じ終了ステートへ遷移する、複製ステートマシンの実装を示しています。

#### ピアツーピア

ICPテクノロジー・スタックでは、最初のレイヤーはピアツーピア・レイヤーです。このレイヤーはサブネット内のノード間の通信を担当し、そのための信頼性の高いセキュアなチャネルを提供します。ピアツーピア通信を通じて、サブネット上のノードは**アーティファクトと**呼ばれるメッセージをサブネット内のすべてのノードにブロードキャストできます。

このトピックをさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/peer-to-peer-p2p/)ご覧ください。

#### コンセンサス

コンセンサスレイヤーは、サブネット内のすべてのノードが、サブネット上で処理されるメッセージとその順序に同意することを保証する役割を担います。これは、メッセージの実行時にすべてのノードが同じステート遷移を行うことを保証するために重要です。IC上の各サブネットは、他のサブネットから独立してコンセンサスを実行します。

ICコンセンサスプロトコルのユニークな点は、**暗号的に保証された最終性を**提供することです。これは、ビットコインで使用されているような、**確率的な最終性を**提供する他のコンセンサスプロトコルとは異なります。

このトピックについてもっと詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/consensus/)ご覧ください。

#### メッセージルーティング

メッセージルーティング層は、コンセンサス層からメッセージのブロックを受け取り、これらのメッセージを関連するターゲットcanisters の入力キューに入れます。このプロセスは**誘導と**呼ばれます。そして、この誘導プロセスが**実行**プロセスのトリガーとなり、実行されたcanisters' 出力キューに新しいcanister メッセージが生成されます。実行が完了すると、出力キュー内のメッセージは、メッセージルーティング コンポーネントによって、受信者にルーティングされます。

このトピックについてさらに詳しく知りたいですか？[このドキュメントを](https://internetcomputer.org/how-it-works/message-routing/)参照してください。

#### 実行

実行レイヤーは、canister スマートコントラクトコードの実行を担当します。実行はWebAssembly (Wasm) を使って行われます。各ノードで実行されるWasm仮想マシンがこのプロセスを担当します。Wasmのバイトコードは決定論的に実行できるため、Wasmが使用されます。

サブネット上のcanister キューに誘導されたメッセージは、キュー内のすべてのメッセージが実行されるか、ラウンドのcycles リミットに達するまで、実行レイヤによって実行されます。

このトピックについてさらに詳しく知りたいですか?[こちらのドキュメントを](https://internetcomputer.org/how-it-works/execution/)ご覧ください。

## 連鎖鍵暗号

Internet Computer のもう一つのコア・コンポーネントは、チェーン・キー**暗号と**総称される高度な暗号メカニズムの数々です。これらのチェーンキー暗号方式により、ICは他のブロックチェーンネットワークでは不可能なスケーラビリティと機能性を実現することができます。

連鎖鍵暗号方式の中核にあるのは閾**値署名方式**です。閾値署名スキームは通常のデジタル署名スキームと似ていますが、閾値署名スキームの秘密署名キーはサブネット内のすべてのレプリカに分散されます。これにより、サブネット上のレプリカの1つ、あるいはその大部分を侵害しても、鍵が盗まれることのない安全なレイヤーを提供します。

連鎖鍵暗号方式：

- ブロックチェーン・ネットワーク全体を同期させなくても、署名の検証によってICが受信したコンテンツを誰でも検証できます。
- 新しいサブネットやノードを追加したり、故障したノードを自律的に復旧させることができます。
- チェーンキー暗号は、canisters 、ランダム性を必要とするアルゴリズムに使用できる擬似ランダム性のソースを提供します。

[**チェーンキー署名は**](https://internetcomputer.org/how-it-works/chain-key-technology/)、他のブロックチェーン・ネットワークを対象とするトランザクションを、ICを使用して完全にオンチェーンで計算する能力を提供します。これにより、ビットコインやイーサリアムなどの他のブロックチェーンとの統合が可能になります。

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/chain-key-technology/)ご覧ください。

## Canisters スマートコントラクト

IC上のスマートコントラクトは **canisters**Canisters はコードとステートの両方を束ねた計算単位です。canister は、ブラウザやモバイルアプリなどの外部サービスから呼び出されたり、他のcanisters から呼び出されたりする関数を定義できます。

Canisters は、非同期メッセージを通じて相互に通信することができます。メッセージの実行は分離して行われるため、同時実行のレベルを上げることができます。

Canister コードはさまざまな言語で書くことができます。現在、 と Rust はMotoko [IC SDK](/docs/developer-docs/setup/install/index.mdx) を通して によってサポートされ、保守されています。また、Python や Typescript など、コミュニティによって開発された SDK もいくつかあります。DFINITY 

canister は**コントローラによって**管理されます。コントローラは、中央集権的なエンティティ、DAOのような分散型エンティティ、またはコントローラを全く持たないことができます。コントローラは、canister を IC にデプロイし、実行を開始または停止し、更新されたコードをcanister にプッシュできる唯一の存在です。

コントローラーはまた、canister に の支払いに十分な **cycles**コントローラはまた、canister が、ネットワーク帯域幅、メモリ、計算能力を含む のリソースを賄うに足る十分なリソースを含んでいることを保証する責任を負います。メインネットに配置されたcanister が実行する各実行には、cycles のコストがかかります。

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/canister-lifecycle/)ご覧ください。

## トークン

Internet Computer のユーティリティ・トークンは**ICPで**、Internet Computer Protocol にちなんで命名されました。このユーティリティ・トークンは、NNSの議決権を持つためにステークされたり、 に電力を供給するために使用されるICPを購入するために使用されるなど、ネットワーク内のいくつかの機能に使用されます。 **cycles**canisters を購入するために使用されるなど、ネットワーク内でいくつかの機能に使用されます。

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/tokenomics/)ご覧ください。

## ガバナンス

### はNetwork Nervous System

Internet Computer は、**分散型自律組織（DAO）**である**Network Nervous System (NNS)** によって統治されています。NNSはICの分散型システムを構成する独立ノードを調整します。NNSは、ノードがどのサブネットに属するべきか、ノードがどのプロトコルのバージョンを実行すべきか、ノードがいつプロトコルの新しいバージョンにアップグレードされるべきか、などの決定を提案し、投票する責任があります。ICPトークンをステークし、提案に投票することで、誰でもNNSに参加できます。

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/nns/)ご覧ください。

### サービス・ナーバス・システム

Dapps IC上で展開されたサービスは、IC自体が管理されるのと同じ方法で管理されます。SNS は NNS のバージョンであり、単一の をトークン化して分散化する役割を担います。一旦 のコントロールが SNS に引き渡されると、 のネイティブトークンをステークした人々によって投票される提案を通じて管理することができます。dapp dapp dapp

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/sns/)ご覧ください。

## インターネット・アイデンティティ

Internet Computer は、**Internet Identity (II)** として知られる、安全で高度な暗号認証のパイオニアです。II は、管理が難しく悪用されやすい従来のユーザー名とパスワードに代わる手段として、すべてのユーザーデバイスで動作し、ユーザーのプライバシーを保護するように設計されています。

IIは、IC上のdapps と統合することができ、あなたのデータを収集するウェブサイトからの保護を提供することで、オンライン・アイデンティティを保護するのに役立ちます。IIは、ユーザーが訪問するウェブサイトごとに新しい匿名の独立したアカウントを作成する機能を通じて、これを実現します。

このトピックについてさらに詳しく知りたいですか？[こちらのドキュメントを](https://internetcomputer.org/how-it-works/web-authentication-identity/)ご覧ください。

## 次のステップ

- 0\.[2:Internet Computer 専門用語](02-ic-terms.md)。

<!---
# 0.1: Overview of the Internet Computer

## Overview

The **Internet Computer (IC)** is a secure and transparent blockchain-based network that can be used to host data and programs. Programs and their data hosted on the IC are referred to as **decentralized applications**, often abbreviated to **dapps**. 

Dapps are created by the development and deployment of **smart contracts**, which are known as **canisters** on the IC. Each canister is hosted on an independent blockchain network running on **nodes** called a **subnet**.

We'll dive into these terms a bit further in the next section, [Internet Computer terminology](02-ic-terms.md). 

In order to understand how to develop dapps on the IC, first let's take a look at the architecture of the IC and how it functions. 

## The Internet Computer Protocol 

At the core of the Internet Computer is the Internet Computer Protocol (ICP). The ICP is a 4-layer technology stack that runs on the nodes of each subnet. Through the implementation of this 4-layer stack, each subnet is capable of creating a blockchain-based **replicated state machine** which is able to operate independently of other subnets while communicating asynchronously with them. Each subnet processes **messages**, which are received from end-users or other subnets. 

### Protocol stack

The ICP is comprised of four layers:

1. Peer-to-peer.
2. Consensus.
3. Message routing.
4. Execution. 

Together, the peer-to-peer and consensus layers select and order incoming messages, then provide them to the upper two layers in the form of **blocks**. When the message routing and execution layers receive those blocks that contain ordered messages, they can execute them in a completely deterministic manner across every node on the subnet. This demonstrates a **round** of message execution, and showcases the implementation of the replicated state machine, where every node within the subnet transitions from the same starting state to the same ending state in each round of message execution. 

#### Peer-to-peer

In the ICP technology stack, the first layer is the peer-to-peer layer. This layer is responsible for the communication between the nodes within a subnet, and facilitates a reliable and secure channel to do so. Through peer-to-peer communication, nodes on a subnet can broadcast messages, referred to as **artifacts**, to all nodes in the subnet. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/peer-to-peer-p2p/)

#### Consensus

The consensus layer is responsible for assuring that all nodes in a subnet agree on the messages that are processed on the subnet and the order that they are processed in. This is important to ensure that all nodes make the same state transition when executing messages. Each subnet on the IC runs consensus independently of the other subnets. 

A unique aspect of the IC consensus protocol is that it provides **cryptographically guaranteed finality**, which is different in comparison to other consensus protocols, such as the one used by Bitcoin, which provides **probabilistic finality**. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/consensus/)

#### Message routing

The message routing layer receives a block of messages from the consensus layer, then places these messages into the input queues of their associated target canisters. This process is called **induction**. The induction process then triggers the **execution** process, which may result in new canister messages in the executed canisters' output queues. After execution has completed, messages in the output queues are routed to their recipient by the message routing component.

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/message-routing/)

#### Execution

The execution layer is responsible for executing canister smart contract code. Execution is done using WebAssembly (Wasm). There is a Wasm virtual machine that runs on each node that is responsible for this process. Wasm is used since Wasm bytecode can be executed deterministically. 

Messages that have been inducted into canister queues on the subnet are executed by the execution layer until either all messages in the queue have been executed or until the cycles limit for the round has been reached. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/execution/)

## Chain-key cryptography

Another core component of the Internet Computer is an array of advanced cryptographic mechanisms, which are collectively referred to as **chain-key cryptography**. These chain-key cryptographic methods allow the IC to achieve scalability and functionalities that aren't possible on other blockchain networks. 

At the core of chain-key cryptography is a **threshold signature scheme**. A threshold signature scheme is similar to an ordinary digital signature scheme, though the secret signing key in a threshold signature scheme is distributed among all the replicas in a subnet. This provides a layer of security where a key cannot be stolen by compromising one, or even a large portion, of the replicas on the subnet.

Through chain-key cryptography:
- Anyone can verify content received by the IC by validating a signature without needing to sync the entire blockchain network. 
- New subnets and nodes can be added, or faulty nodes can be recovered, autonomously. 
- Chain-key cryptography provides a source of pseudo-randomness that can be used by canisters for algorithms that require randomness. 

[**Chain-key signatures**](https://internetcomputer.org/how-it-works/chain-key-technology/) provide the ability for transactions that are targeted at other blockchain networks to be computed fully on-chain using the IC. This allows for integrations with other blockchains such as Bitcoin and Ethereum. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/chain-key-technology/)
## Canisters and smart contracts

Smart contracts on the IC are referred to as **canisters**. Canisters are computational units that bundle together both code and state. A canister can define functions that can be called by external services, such as browsers or mobile apps, or that can be called by other canisters. 

Canisters are able to communicate with each other through asynchronous messages. The execution of messages is done in isolation, allowing for increased levels of concurrent execution.

Canister code can be written in a number of different languages. Currently, Motoko and Rust are supported and maintained by DFINITY through the [IC SDK](/docs/developer-docs/setup/install/index.mdx), and there are several community-developed SDKs such as Python and Typescript. 

A canister is managed by **controllers**. A controller can be a centralized entity, a decentralized entity such as a DAO, or it can have no controller at all, which would make it an immutable smart contract. Controllers are the only ones that can deploy the canister to the IC, start or stop their execution, and push updated code to the canister. 

Controllers are also responsible for assuring that a canister contains sufficient **cycles** to pay for the canister's resources, which include network bandwidth, memory, and computational power. Each execution performed by a canister deployed on the mainnet has a cost of cycles. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/canister-lifecycle/)

## Tokens

The Internet Computer's utility token is **ICP**, named after the Internet Computer Protocol. This utility token is used for several functions within the network, such as being staked to have voting power in the NNS, or being used to purchase **cycles**, which are used to power canisters deployed on the mainnet network. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/tokenomics/)

## Governance 

### The Network Nervous System

The Internet Computer is governed by the **Network Nervous System (NNS)**, which is a **decentralized autonomous organization (DAO)**. The NNS coordinates the independent nodes that make up the decentralized system of the IC. The NNS is responsible for proposing and voting on decisions such as which subnet a node should belong to, which protocol version the nodes should run, and when nodes should be upgraded to a new version of the protocol. Anyone can participate in the NNS by staking ICP tokens and voting on proposals. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/nns/)

### Service Nervous System

Dapps deployed on the IC can be governed in the same manner that the IC itself is governed - through a DAO, referred to as an **SNS, or Service Nervous System**. An SNS is a version of the NNS that is responsible for tokenizing and decentralizing a single dapp. Once a dapp's control has been handed over to an SNS, it can be managed through proposals that are voted on by those that have staked the dapp's native token. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/sns/)

## Internet Identity

The Internet Computer has pioneered a secure and advanced form of cryptographic authentication, known as **Internet Identity (II)**. II Is designed to work across all user devices and protect user privacy as a means to replace traditional usernames and passwords, which can be hard to manage and easy to exploit. 

II can be integrated with dapps on the IC and helps secure your online identity by providing protection from websites collecting your data. II does this through the ability for users to create new anonymous, independent accounts for each website that they visit. 

Want to go further into this topic? Check out [this documentation.](https://internetcomputer.org/how-it-works/web-authentication-identity/)

## Next steps

- [0.2: Internet Computer terminology](02-ic-terms.md).

-->
