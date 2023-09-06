---

title: Overview
abstract: 
shareImage: /img/how-it-works/ic-overview.jpg
slug: core-ic-protocol-overview
---
# コアInternet Computer Protocol - 概要

サブネットはcanister スマートコントラクトをホストし、ユーザーまたは他のcanister スマートコントラクト（同じサブネットまたは別のサブネットでホストされる可能性がある）から送信された*メッセージを*実行します。
IC上のメッセージは、他のブロックチェーン上のトランザクションに類似しています。
 canister スマートコントラクト宛てのメッセージは、対応するサブネット上のノードがcanister のWasmコードを実行することで実行されます。
Canister コードの実行はcanister ステートを更新します。
 canister がホストされているサブネットのノード上のステートを同期させ続けるためには、すべてのノードが同じメッセージを同じ順序で、つまり完全に決定論的に実行することが保証されなければなりません、
これがICを実現するブロックチェーンベースの複製ステートマシン機能の中核です。

Internet Computer 上の各ノードはレプリカ・プロセスを実行します。レプリカプロセスは、以下の4つのレイヤーからなるレイヤーアーキテクチャで構成されています：

1.  ピアツーピア
2.  コンセンサス
3.  メッセージルーティング
4.  実行

<figure>
<img src="/img/how-it-works/core_protocol_layers.png" alt="4-layer architecture of the Internet Computer" title="4-Layer Core IC Protocol" align="center" style="width:600px">
<figcaption align="left">
The 4-layer architecture of the Core IC Protocol
</figcaption>
</figure>

ピアツーピア層は、ユーザーからのメッセージの受信と、サブネット内のノード間のメッセージ交換を担当します。コンセンサスレイヤーは、サブネット上のすべてのノードが処理するメッセージとその順序に合意できるようにします。メッセージ・ルーティング層は、コンセンサス層から確定したブロックを受け取り、ブロック内のメッセージを適切なcanisters にルーティングします。実行レイヤーは、メッセージング・レイヤーから受け取ったメッセージに対してcanister コードを決定論的に実行します。

上の2つのレイヤは、下の2つのレイヤから受け取ったラウンドのメッセージブロックの決定*論的な実行を*、サブネットの各ノード上で実現します。
ラウンドの開始時に、すべての（正直な）ノードは、サブネットの複製されたステート（そのサブネット上でホストされているすべてのcanisters の現在のステートを含む）を表す、同じステートを保持します。
コンセンサスから受け取った次のブロックのメッセージを完全に決定論的な方法で実行することにより、ブロックのメッセージを実行した後のステートは、実行の決定論により各ノードで同じであることが保証されます。

Canister スマートコントラクトは、同じサブネット上でホストされているか異なるサブネット上でホストされているかに関係なく、メッセージを送信することで相互に通信することができます。 ICコアプロトコルは、ローカル、つまり同じサブネット上で送信された メッセージの両方を処理します、 Local inter- メッセージはコンセンサスを経る必要がありませんが、XNet inter- メッセージはコンセンサスを経る必要があります（前者の方がスループットの点で効率的であり、待ち時間も少なくなります）。
canister canisterscanister
canistercanister 

コアとなるICプロトコルは、その動作において連[*鎖鍵暗号に*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)大きく依存しています。  チェーン・キー暗号の重要なコンポーネントは[*チェーン進化技術で*](https://internetcomputer.org/how-it-works/#Chain-evolution-technology)、新しいノードがサブネットに簡単かつ安全に参加できるようにするなど、ICの長期運用を容易にします。

<!---


# Core Internet Computer Protocol – Overview

A subnet hosts canister smart contracts and executes *messages* sent to them either by users or other canister smart contracts (which may be hosted on the same or another subnet).
Messages on the IC are analogous to transactions on other blockchains.
Messages addressed to a canister smart contract are executed by the nodes on the corresponding subnet by running the Wasm code of the canister.
Canister code execution updates the canister state.
In order to keep the state on the subnet nodes on which a canister is hosted in sync, it must be ensured that every node executes the same messages in the same order, i.e., fully deterministically.
This is the core of the blockchain-based replicated state machine functionality realizing the IC.

Each node on the Internet Computer runs a replica process. The replica process is structured in a layered architecture consisting of the following 4 layers:
1. Peer-to-peer
2. Consensus
3. Message routing
4. Execution

<figure>
<img src="/img/how-it-works/core_protocol_layers.png" alt="4-layer architecture of the Internet Computer" title="4-Layer Core IC Protocol" align="center" style="width:600px">
<figcaption align="left">
The 4-layer architecture of the Core IC Protocol
</figcaption>
</figure>

The Peer-to-Peer layer is responsible for accepting messages from users and exchanging messages between nodes in a subnet. The consensus layer lets all the nodes on the subnet to agree on the messages to be processed, as well as their ordering. The message routing layer picks up the finalized blocks from consensus layer and routes the messages in the blocks to appropriate canisters. The execution layer determinstically executes canister code on the messages received from the messaging layer. 

The upper two layers realize *deterministic execution* of the block of messages for a round received from the lower two layers, on each node of the subnet.
At the beginning of a round, all (honest) nodes hold the same state, representing the replicated state of the subnet (which includes the current state on all canisters hosted on that subnet.
By executing the messages of the next block received from consensus in a completely deterministic manner, it is ensured that the state after executing the messages of the block is the same on each node due to the determinism in execution.

Canister smart contracts can communicate with each other by sending messages, regardless of whether they are hosted on the same or different subnets.
The IC core protocol handles both the inter-canister messages sent locally, i.e., on the same subnet, between canisters, as well as inter-canister messages sent across subnets, so called *XNet messages*.
Local inter-canister messages do not need to go through consensus, while XNet inter-canister messages do (making the former more efficient in terms of throughput and incurring less latency).

The core IC protocol heavily relies on [*chain-key cryptography*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography) for its operation.  A key component of chain-key cryptography is [*chain-evolution technology*](https://internetcomputer.org/how-it-works/#Chain-evolution-technology), which facilitates the long-term operation of the IC, such as allowing new nodes to easily and securely join a subnet.

-->
