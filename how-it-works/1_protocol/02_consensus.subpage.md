---

title: Internet Computer Consensus
abstract:
shareImage: /img/how-it-works/consensus.jpg
slug: consensus
---
# コンセンサス

コンセンサスの目的は、サブネットのノードによって合意されたブロックを生成することであり、これにより実行されるメッセージの順序付けられたシーケンスが生成されることを思い出してください。
これは、プロトコルスタックの上位2つのレイヤー（メッセージルーティングと実行）が、各ノードのラウンドごとに同じ入力を受け取るために重要です。

ICのコンセンサスプロトコルは、次の要件を満たすように設計されています。
低レイテンシ（ほぼ瞬時に終端）、
高スループット、
堅牢性（ノードやネットワークに障害が発生した場合に、レイテンシとスループットを優雅に劣化させる）。ICコンセンサスプロトコルは、[連鎖鍵暗号を](/how-it-works/#Chain-key-cryptography)活用することでこれらの目標を達成します。

*確率的な最終性*（ビットコインのようなプロトコルで行われているような、ブロックチェーン上に十分な数のブロックが構築された時点で、そのブロックを最終的なブロックとみなす*）を*選択するオプションは、2つの理由からICでは受け入れられません。

ICコンセンサスプロトコルは、通信ネットワークに関する最小限の仮定のみで、これらすべての目標を達成します。特に、プロトコルのメッセージが配信されるまでの時間に関する境界を仮定していません。つまり、*同期ネットワークではなく*、*非同期*ネットワークを仮定しているだけです。実際、グローバルに分散している分散型ネットワークでは、*同期*性は現実的な仮定ではありません。純粋に*非同期で*動作するコンセンサスプロトコルを設計することは可能ですが、これらのプロトコルは一般的にレイテンシーが非常に劣ります。良好なレイテンシを達成するために、ICコンセンサスプロトコルはプロトコルメッセージをタイムリーに配信して進捗させる必要があります。しかし、プロトコルの*正し*さは、メッセージの遅延に関係なく、サブネット内の3分の1以下のノードが故障している限り、常に保証されます。

<figure>
<img src="/img/how-it-works/consensus_orders_messages.png" alt="Consensus round yields an ordered sequences of messages" title="Consensus round yields an ordered sequences of messages" align="center" style="width:600px">
</figure>

プロトコルはラウンドごとに進行します。
各ラウンドで、少なくとも1つの公証済みブロックが、前のラウンドで追加された*公証*済みブロックの子としてツリーに追加されます。
うまくいくと、そのラウンドでツリーに追加された公証済みブロックは1つだけになり、そのブロックは*確定として*マークされます。
このプロトコルは、公証されたブロックのツリーには常に、公証されたブロックの一意な連鎖が存在することを保証します。
この公証されたブロックの連鎖が、コンセンサスの出力です。

コンセンサスラウンドには、次の3つの段階があります：

- *ブロックの作成：*各ラウンドで、ブロックメーカーと呼ばれる少なくとも1つのノードが、P2Pを使用してサブネット内のすべてのノードにブロードキャストすることにより、ブロックを提案します。
  これから説明するように、うまくいけばブロックメーカーは1つだけですが、複数のブロックメーカーが存在することもあります。
- *公証：*ブロックが*公証さ*れるには、ノードの少なくとも3分の2がそのノードを検証し、公証をサポートする必要があります。
- *確定：*ブロックが*ファイナライズ*されるには、ノードの少なくとも3分の2がファイナライズをサポートする必要があります。後述するように、ノードは他のブロックの公証をサポートしなかった場合にのみブロックの公証をサポートします。この単純なルールにより、あるラウンドでブロックが公証された場合、そのラウンドでは他の公証されたブロックは存在しないことが保証されます。

次に、コンセンサスラウンドの各フェーズを詳しく見てみましょう。

## ブロック生成

以下で説明するように、*ランダムビーコンと*呼ばれる暗号化メカニズムを使って、（ランダムに選ばれた）1つのノードを現在のラウンドの*プライマリーブロックメーカー*（または*リーダー*）として選びます。
プライマリ・ブロック・メーカーは、イングレス・メッセージ（そのノードに直接送信されたもの、またはP2P経由でサブネット内の他のノードから受信したもの）とXNetメッセージ（他のサブネットからこのサブネットに送信されたもの）で構成されるブロックをアセンブルします。
ブロックをアセンブルした後、プライマリ・ブロック・メーカーはP2Pを使用してサブネット内のすべてのノードにブロードキャストすることで、このブロックを提案します。

ネットワークが遅い場合やプライマリーブロックメーカーが故障している場合、プライマリーブロックメーカーが提案したブロックが妥当な時間内に公証されないことがあります。
この場合、ある程度の遅延の後、同じランダムビーコン機構を使用して、他のブロックメーカーが選ばれ、プライマリーブロックメーカーに取って代わります。
プロトコルロジックは、現在のラウンドで最終的に1つのブロックが公証されることを保証します。

[連鎖鍵暗号の](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)セクションで説明したように、
連鎖鍵暗号は、予測不可能で偏りのない擬似乱数を生成するために使用されます。
これにより、サブネット内の各ノードにランクが割り当てられます。
サブネット内で最もランクの低いノードが、プライマリ・ブロック・メーカーとして機能します。
公証済みブロックが生成されないまま時間が経過すると、ランクの高いノードが徐々にブロック・メーカーになり、ランクの低い（潜在的に欠陥のある）ノードに取って代わります。

プライマリ・ブロック・メーカーに欠陥がなく、プロトコル・メッセージがタイムリーに配信されるシナリオでは、プライマリ・ブロック・メーカーだけがブロックを提案し、このブロックはすぐに公証されて確定します。

<figure>
<img src="/img/how-it-works/block_maker.png" alt="Blockmaker constructs a new block and broadcasts it" title="Blockmaker constructs a new block and broadcasts it" align="center" style="width:600px">
</figure>

## 公証

ブロックがこの妥当性チェックに合格すると、ノードはそのブロックの*公証シェアを*サブネット内のすべてのノードにブロードキャストすることで、ブロックの公証をサポートします。
ブロックは、サブネット内のノードの少なくとも3分の2がその[公証を](https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html)サポートしたときに*公証さ*れます。
この場合、BLSのマルチシグネチャシェアを集約して、ブロックのコンパクトな*公証を*形成してもかまいません。

そうでない場合、ノードは最終的に上位のブロックメーカーが提案したブロックの公証をサポートすることがあります（ただし、あるランクのブロックメーカーが提案したブロックの公証をすでにサポートしている場合、上位のブロックメーカーが提案したブロックの公証はサポートしません）。

<figure>
<img src="/img/how-it-works/consensus_notarization.png" alt="Notarization support of increasing-rank block proposals in a round" title="Notarization support of increasing-rank block proposals in a round" align="center" style="width:600px">
<figcaption align="center">
Notarization support of increasing-rank block proposals in a round
</figcaption>
</figure>

## 最終決定

一度公証されたブロックを取得すると、そのノードはそれ以降他のブロックの公証をサポートしません。
さらに、そのノードが以前に他のブロックの公証をサポートしていなかった場合、そのノードはこのブロックの最終化もサポートします。
ファイナライズシェアとは、BLSマルチシグネチャスキームを使用して計算されたシグネチャシェアです。
サブネット内のノードの少なくとも3分の2がファイナライズをサポートすると、ブロックは*ファイナライズさ*れます。
この場合、BLSマルチシグネチャシェアを集約して、ブロックのコンパクトな*ファイナライズを*形成することができます。

## さらに深く

[コンセンサスの達成Internet Computer](https://medium.com/dfinity/achieving-consensus-on-the-internet-computer-ee9fbfbafcbc)

[コンセンサス白書](https://eprint.iacr.org/2021/632.pdf)

[PODC'22で発表されたエクステンデッド・アブストラクト](//assets.ctfassets.net/ywqk17d3hsnp/1Gutwfrd1lMgiUBJZGCdUG/d3ea7730aba0a4b793741681463239f5/podc-2022-cr.pdf)

[ICウィキのコンセンサス](https://wiki.internetcomputer.org/wiki/IC_consensus_layer)

<!-- https://img.youtube.com/vi/vVLRRYh3JYo/0.jpg -->

[![Watch youtube video](https://i.ytimg.com/vi/vVLRRYh3JYo/maxresdefault.jpg)](https://www.youtube.com/watch?v=vVLRRYh3JYo)

<!---


# Consensus

Each subnet of the IC is its own blockchain that makes progress concurrently to the other subnets: it runs its own instance of the IC core protocol stack, including consensus.
Recall that the goal of consensus is to produce blocks agreed upon by the nodes of the subnet, which yields an ordered sequence of messages to be executed.
This is crucial so that the upper two layers of the protocol stack – message routing and execution – receive the same inputs in every round on each node.

The IC’s consensus protocol is designed to meet the following requirements:
low latency (almost instant finality);
high throughput;
robustness (graceful degradation of latency and throughput in the presence of node or network failures). The IC consensus protocol achieves these goals by leveraging [chain-key cryptography](/how-it-works/#Chain-key-cryptography).

The IC consensus protocol provides _cryptographically guaranteed finality_.
The option of choosing _probabilistic finality_ – similar to what is done in Bitcoin-like protocols, by considering a block final once a sufficient number of blocks have built on top of it in the blockchain – is not acceptable for the IC for two reasons: (1) probabilistic finality is a very weak notion of finality and (2) probabilistic finality would increase the time to finality drastically.

The IC consensus protocol achieves all of these goals making only minimal assumptions about the communication network. In particular, it does not assume any bounds on the time it takes for protocol messages to be delivered – that is, it only assumes an _asynchronous network_ rather than a _synchronous network_. Indeed, for a decentralized network that is globally distributed, _synchrony_ is simply not a realistic assumption. While it is possible to design consensus protocols that work in a purely _asynchronous_ setting, these protocols generally have very poor latency. In order to achieve good latency, the IC consensus protocol requires protocol messages to be delivered in a timely manner to make progress. However, the _correctness_ of the protocol is always guaranteed, regardless of message delays, so long as less than a third of the nodes in the subnet are faulty.

<figure>
<img src="/img/how-it-works/consensus_orders_messages.png" alt="Consensus round yields an ordered sequences of messages" title="Consensus round yields an ordered sequences of messages" align="center" style="width:600px">
</figure>

The consensus protocol maintains a tree of _notarized_ blocks (with a special origin block at the root).
The protocol proceeds in rounds.
In each round, at least one notarized block is added to the tree as a child of a notarized block that was added in the previous round.
When things go right, there will be only one notarized block added to the tree in that round, and that block will be marked as _finalized_.
Moreover, once a block is marked as finalized in this way, all ancestors of that block in the tree of notarized blocks are implicitly finalized.
The protocol guarantees that there is always a unique chain of finalized blocks in the tree of notarized blocks.
This chain of finalized blocks is the output of consensus.

At a high level, a consensus round has the following three phases:

- _Block making:_ In every round, at least one node, called a block maker, proposes a block by broadcasting it to all nodes in the subnet using P2P.
  As we will see, when things go right, there is only one block maker, but sometimes there may be several.
- _Notarization:_ For a block to become _notarized_, at least two thirds of the nodes must validate the node and support its notarization.
- _Finalization:_ For a block to become _finalized_, at least two thirds of the nodes must support its finalization. As we will see, a node will support the finalization of a block only if it did not support the notarization of any other block, and this simple rule guarantees that if a block is finalized in a given round, then there can be no other notarized block in that round.

Let us next look at the different phases of a consensus round in more detail.

## Block Making

A _block maker_ is a node that proposes a block for the current round.
As explained below, a cryptographic mechanism called a _random beacon_ is used to select one node (chosen at random) as the _primary_ block maker (or _leader_) for the current round.
The primary block maker assembles a block consisting of the ingress messages (submitted directly to the node or received from other nodes in the subnet via P2P) and XNet messages (sent to this subnet from other subnets).
After assembling a block, the primary block maker proposes this block by broadcasting it to all nodes in the subnet using P2P.

If the network is slow or the primary block maker is faulty, the block proposed by the primary block maker may not get notarized within a reasonable time.
In this case, after some delay, and using the same random beacon mechanism, other block makers are chosen to step in and supplant the primary block maker.
The protocol logic guarantees that one block eventually gets notarized in the current round.

The block makers for a round are chosen through a random permutation of the nodes of the subnet based on randomness derived from a _random beacon_.
As discussed in the section on [chain-key cryptography](https://internetcomputer.org/how-it-works/#Chain-key-cryptography),
chain-key cryptography may be used to produce unpredictable and unbiasable pseudo-random numbers.
Consensus uses these pseudo-random numbers to define a pseudo-random permutation on the nodes of the subnet.
This assigns a rank to each node in the subnet.
The lowest-rank node in the subnet acts as the primary block maker.
As time goes by without producing a notarized block, nodes of increasing rank gradually step in to supplant the (potentially faulty) nodes of lower rank as block maker.

In the scenario where the primary block maker is not faulty, and protocol messages get delivered in a timely manner, only the primary block maker will propose a block, and this block will quickly become notarized and finalized.

<figure>
<img src="/img/how-it-works/block_maker.png" alt="Blockmaker constructs a new block and broadcasts it" title="Blockmaker constructs a new block and broadcasts it" align="center" style="width:600px">
</figure>

## Notarization

When a node receives a block proposed by a block maker for the round, it validates the block for syntactic correctness.
If the block passes this validity check, the node supports the notarization of the block by broadcasting a _notarization share_ for the block to all nodes in the subnet.
A notarization share is a signature share computed using the [BLS multi-signature scheme](https://crypto.stanford.edu/~dabo/pubs/papers/BLSmultisig.html).
A block becomes _notarized_ when at least two thirds of the nodes in the subnet support its notarization.
In this case, the BLS multi-signature shares may be aggregated to form a compact _notarization_ for the block.

In the case where the block proposed by the primary block maker gets notarized within a certain amount of time, a node will not support the notarization of any other block in that round.
Otherwise, a node may eventually support the notarization of blocks proposed by other block makers of higher rank (but if it has already supported the notarization of a block proposed by a block maker of some rank, it will not support the notarization of blocks proposed by block makers of higher rank).

<figure>
<img src="/img/how-it-works/consensus_notarization.png" alt="Notarization support of increasing-rank block proposals in a round" title="Notarization support of increasing-rank block proposals in a round" align="center" style="width:600px">
<figcaption align="center">
Notarization support of increasing-rank block proposals in a round
</figcaption>
</figure>

## Finalization

In a given round, the logic of the protocol guarantees that a node will always obtain a notarized block (assuming less than a third of the nodes in the subnet are faulty).
Once it obtains a notarized block, the node will not subsequently support the notarization of any other block.
Moreover, if the node did not previously support the notarization of any other block, the node will also support the finalization of this block.
It supports the finalization of this block by broadcasting a _finalization share_ for the block to all nodes in the subnet.
A finalization share is a signature share computed using the BLS multi-signature scheme.
A block becomes _finalized_ when at least two thirds of the nodes in the subnet support its finalization.
In this case, the BLS multi-signature shares may be aggregated to form a compact _finalization_ for the block.

## Go Even Deeper

[Achieving Consensus on the Internet Computer](https://medium.com/dfinity/achieving-consensus-on-the-internet-computer-ee9fbfbafcbc)

[Consensus White Paper](https://eprint.iacr.org/2021/632.pdf)

[Extended Abstract published at PODC'22](//assets.ctfassets.net/ywqk17d3hsnp/1Gutwfrd1lMgiUBJZGCdUG/d3ea7730aba0a4b793741681463239f5/podc-2022-cr.pdf)

[Consensus on the IC wiki](https://wiki.internetcomputer.org/wiki/IC_consensus_layer)

<!-- https://img.youtube.com/vi/vVLRRYh3JYo/0.jpg -!->

[![Watch youtube video](https://i.ytimg.com/vi/vVLRRYh3JYo/maxresdefault.jpg)](https://www.youtube.com/watch?v=vVLRRYh3JYo)

-->
