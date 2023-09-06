---

title: Peer-to-Peer
abstract:
shareImage: /img/how-it-works/peer-to-peer.jpg
slug: peer-to-peer-p2p
---
# ピアツーピア

Internet Computer のピアツーピア層（P2P）は、サブネットのノード間で、*アーティファクトとも*呼ばれるネットワークメッセージの信頼性の高いセキュアな通信を実現します。
アーティファクトとは、サブネット内でブロードキャストされるネットワークメッセージのことで、
ユーザーによって提出されたcanister スマートコントラクトへの入力や、コンセンサス層によって生成されたブロックなどのプロトコル発信メッセージを含みます。
P2Pは、アーティファクトの安全な最終的なブロードキャスト配信を、それを必要とするすべてのノードに保証します。
P2Pレイヤーはこれにより、ICプロトコルスタックの通信ファブリックとなり、その上のスタックの次のレイヤーであるコンセンサスレイヤーによって、サブネット内のノードにアーティファクトをブロードキャストするために使用されます。

この*非同期通信ネットワークの*仮定は、実世界のネットワークの特性を反映しているため、ICPの通信層とコンセンサス層に使用されています。

## ゴシッププロトコル

P2Pレイヤーは*ゴシップの*基本原理に基づいて構築されています。
通信ネットワークにおけるゴシップは、人間同士のゴシップと同じ基本原理で動作します：
ノードがピアからアーティファクトを受け取ったり、ICプロトコルの一部として自分自身でアーティファクトを作成したりするたびに、そのノードはすべてのピアにこのアーティファクトをゴシップします。
ネットワーク内のすべてのノードがまさにこれを行うことで、潜在的な接続性の問題や悪意のあるノードの行動にもかかわらず、すべてのアーティファクトは*最終的に*サブネット全体に伝搬します。

## 広告

。しかし、単純にすべてのピアにアーティファクトを送信するというナイーブな方法でこれを行うと、ノードがピアの数だけ同じアーティファクトのコピーを受信することになり、重複メッセージの送信のために不必要にネットワーク帯域幅が消費され、サブネットの達成可能なスループットが低下します。

ナイーブなアプローチでは、配信されたアーティファクトの重複は、ノードがアーティファクト自体を送信する代わりに、アーティファクトの*広告を*ピアに送信することで緩和されます。
広告は対応するアーティファクトを指定しますが、アーティファクトを明確に参照するためのハッシュと追加のメタデータを含むだけの小さなメッセージです。
ノードはアドバタイジングを受信すると、対応するアーティファクトを、そのアーティファクトのアドバタイジングを送信した1つ以上のピアに要求することができます。

## アーティファクトの優先順位付け

P2P レイヤーでは、より重要なアーティファクトが他のアーティファクトよりも迅速にサブネットノード全体にブロードキャストされるように、アーティファクトの優先順位付けを行うことができます。
一部のアーティファクトを他のアーティファクトよりも優先順位付けすることは、プロトコルが常に進歩できるようにし、「重要度の低い」トラフィックによってネットワーク帯域幅が不足しないようにするために重要です。
この原則は従来のネットワーキングでよく知られており、ブロックチェーンシステムにも同様に当てはまります。

## セキュリティ

サービス拒否（DOS）攻撃を防ぐため、ノードは同じサブネット（
）内のノードとの接続のみを要求し、受け入れます。サブネットのメンバーシップは[Network Nervous System (NNS)](/how-it-works/#Network-Nervous-System)によって管理されます。
NNSに保存された情報のおかげで、canisters P2Pは
2つのノード間のすべての通信が暗号化され、TLSを使用して認証されることを保証できます。

## さらに深く

[IC wikiのP2P](https://wiki.internetcomputer.org/wiki/IC_P2P_\(peer_to_peer\)_layer)

[P2Pに関するブログ記事](https://medium.com/dfinity/secure-scalability-the-internet-computers-peer-to-peer-layer-6662d451f2cc)

[![Watch youtube video](https://i.ytimg.com/vi/HOQb0lKIy9I/maxresdefault.jpg)](https://www.youtube.com/watch?v=HOQb0lKIy9I)

<!---


# Peer-to-Peer

The peer-to-peer layer (P2P) of the Internet Computer realizes the reliable and secure communication of network messages, also called _artifacts_, between the nodes of a subnet.
Artifacts are the network messages that are to be broadcast in the subnet,
including the input to canister smart contracts submitted by users or protocol-originating messages such as the blocks produced by the consensus layer.
P2P guarantees the secure eventual broadcast delivery of an artifact to all nodes which require it to make progress.
The P2P layer thereby is the communication fabric for the IC protocol stack and is used by the consensus layer, the next layer in the stack above it, to broadcast artifacts to the nodes in the subnet.

It is important to note that broadcast artifacts reach all necessary subnet nodes eventually, that is, no upper bound on when this happens can be given.
This _asynchronous communication network_ assumption is used for the ICP's communication and consensus layers as it reflects the properties of real-world networks.

## Gossip Protocol

The P2P layer builds upon the basic principle of _gossip_.
Gossip in communication networks works along the same basic principle as gossip among people: A node in the subnet is connected with a subset of the other nodes of the subnet – its _peers_.
Whenever a node receives an artifact from a peer or creates one itself as part of the IC protocol, it gossips this artifact to all its peers.
By every node in the network doing exactly this, every artifact _eventually_ propagates through the whole subnet, despite potential connectivity issues or malicious node behavior.

## Adverts

Whenever a subnet node receives or generates an artifact to be broadcast, the node makes the artifact available to all peers.
Every node doing so ensures that the artifact will eventually be delivered to all subnet nodes.
However, doing so in a naïve way by simply sending the artifact to all peers would result in nodes receiving as many copies of the same artifact as the node has peers, which unnecessarily consumes networking bandwidth for transmitting duplicate messages and reduces the achievable throughput of the subnet.

This duplication of delivered artifacts in the naïve approach is mitigated by nodes sending _adverts_ for artifacts to their peers instead of sending the artifacts themselves.
An advert specifies its corresponding artifact, but is a small message only containing the hash of the artifact to unambiguously refer to it and some additional metadata.
A node only pushes adverts for artifacts to its peers.
After receiving an advert, a node may request the corresponding artifact from one or more of its peers who sent it an advert for that artifact.

## Prioritization of Artifacts

The P2P layer allows the prioritization of artifacts such that the more crucial artifacts are broadcast throughout the subnet nodes more quickly than the others.
Prioritizing some artifacts over others is important to ensure that the protocol can always make progress and not be starved of network bandwidth by "less important" traffic.
This principle is well known from traditional networking and applies equally well to a blockchain system.

## Security

To prevent Denial of Service (DOS) attacks, nodes will only request and accept connections with nodes in
the same subnet. Subnet membership is managed by the [Network Nervous System (NNS)](/how-it-works/#Network-Nervous-System).
Thanks to the information stored in the NNS canisters P2P can guarantee that all the communication between
two nodes is encrypted and authenticated, by using TLS.

## Go Even Deeper

[P2P on the IC wiki](<https://wiki.internetcomputer.org/wiki/IC_P2P_(peer_to_peer)_layer>)

[Blogpost on P2P](https://medium.com/dfinity/secure-scalability-the-internet-computers-peer-to-peer-layer-6662d451f2cc)

[![Watch youtube video](https://i.ytimg.com/vi/HOQb0lKIy9I/maxresdefault.jpg)](https://www.youtube.com/watch?v=HOQb0lKIy9I)

-->
