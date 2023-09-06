---

title: Infinite scalability
abstract:
shareImage: /img/how-it-works/infinite-scalability.jpg
slug: scalability
---
# 無限のスケーラビリティ

DFINITY の意味を不思議に思ったことはありませんか？それはDecentralized + Infinityです。Internet Computer は無限に拡張できるように設計されているため、このような名前が付けられました。つまり、Internet Computer は無制限の数のcanisters (スマートコントラクト) をホストし、無制限の量のメモリを保存し、無制限の量のトランザクションを毎秒処理することができます。簡単に言うと、Internet Computer は、大規模なソーシャルメディア・プラットフォームを完全に分散化された方法でホストするように設計されています。

システムのスケーラビリティを向上させるために広く使われているアプローチには、(1)垂直スケーリングと(2)水平スケーリングの2種類があります。垂直スケーリングとは、1台のコンピュータにさらにCPU、RAM、ディスクを追加することです。水平スケーリングとは、システムにさらにコンピュータを追加することです。垂直スケーリングには限界があります。しかし水平スケーリングでは、無制限のスケーラビリティを実現できます。Internet Computer は、水平スケーリングをうまく利用した最初のブロックチェーンの1つです。

Internet Computer のノードはサブネットに分割され、それぞれが数十のノードを含んでいます。サブネット内のノードの集合は一緒に1つのブロックチェーンを維持します。各サブネットは何千ものcanisters をホストし、それらのcanisters によって受信されたメッセージを処理することができます。各サブネットの容量は、canisters の数（数千）、ストレージの量（数百GB）、帯域幅（毎秒数百トランザクション）の点で限られています。しかし、Internet Computer にサブネットが追加されるにつれて、全体の容量もそれに比例して増加します。追加できるサブネットの数に制限はありません。

無限スケーリングの前提条件となるもう1つの重要な設計面は、canisters のサブネット間通信です：あるサブネットのcanister は、他のサブネットのcanister に非同期メッセージを送信できます。XNetメッセージは受信サブネットのコンセンサスレイヤーによって取り込まれ、その完全性は送信サブネットの閾値署名に基づいて検証されます。XNetメッセージングのこのアーキテクチャは、サブネットの "緩やかな結合 "をもたらし、スケールアウト時にボトルネックとなる複数の "シャード "を持つ他のブロックチェーンで使用されているようなシャードチェーンのような中央コンポーネントを必要としません。そのため、新しく追加されたサブネットは、他のどのサブネットともすぐにXNetメッセージを送受信することができ、他の、より単純化されたアーキテクチャのように、サブネットの数が増えても自然にボトルネックになることはありません。

<figure>
<img src="/img/how-it-works/add-new-subnet.png" alt="Internet Computer is divided into subnets" title="Internet Computer is divided into subnets." align="center" style="width:700px">
<figcaption align="left">
The Internet Computer is divided into subnets. Each subnet hosts many canisters. One of the subnets hosts Network Nervous System canisters.
</figcaption>
</figure>

新しいサブネットの作成には2つのステップがあります。(1)Internet Computer に新しいノードを追加する、(2) 利用可能なノードでサブネットを作成する
誰でもノード・[プロバイダーのオンボーディング・プロセスに](https://wiki.internetcomputer.org/wiki/Node_Provider_Documentation)従うことで、ノード・ハードウェアを購入し、Internet Computer に追加することができます。

次に、利用可能なノードで新しいサブネットを作成する方法を説明します。Internet Computer にはNetwork Nervous System (NNS) と呼ばれる分散型ガバナンス・システムがあります。基本的に、NNSはInternet Computer を管理するcanisters のグループから構成されています。NNSには「レジストリ」と呼ばれるコンポーネントがあり、Internet Computer の完全な設定を保存しています。レジストリには各サブネットのレコードがあり、これにはプロトコルのバージョン、サブネット内のノードのリスト、プロトコルの設定パラメータなどが含まれています。

<figure>
<img src="/img/how-it-works/new-subnet-proposal.png" alt="Proposal to create a new subnet" title="Proposal to create a new subnet" align="center" style="width:700px">
<figcaption align="left">
Proposal to create a new subnet. The status of all proposals can be viewed on the [IC Dashboard](https://dashboard.internetcomputer.org/governance).
</figcaption>
</figure>

新しいサブネットを追加するには、レジストリに新しいサブネットのレコードを追加するための*提案を*NNSに提出する必要があります。プロポーザルは、新しいサブネットに含めるノードのリストで構成されます。この提案は、ICPトークンをステークした人なら誰でも投票できます。投票者の過半数が提案を受諾した場合、レジストリcanister はNNSサブネットに、新しいサブネットが使用する暗号鍵材料と、ジェネシスブロックを含むキャッチアップパッケージを、[連鎖鍵暗号方式を](/how-it-works/chain-key-technology/)使用して完全に分散化された方法で生成するよう指示します。その後、レジストリcanister 、サブネットのコンフィギュレーションを含むレコードを追加します。

レジストリにレコードが追加された後、新しいサブネットがどのように作成されるかを説明します。各ノードは2つのプロセス、(1) Replicaと(2) Orchestratorを実行します。レプリカはブロックチェーンを維持する4層のソフトウェアスタックで構成されています。オーケストレーターはレプリカのソフトウェアをダウンロードして管理します。新しいノードがオンボードされると、ノードプロバイダはオーケストレーターソフトウェアを含むIC OSをノードにインストールする必要があります。オーケストレーターは定期的にNNSレジストリに更新を問い合わせます。レジストリのレコードにノードが含まれていることが確認されると、オーケストレータは対応するレプリカソフトウェアをダウンロードし、レジストリに含まれるキャッチアップパッケージを入力としてレプリカを実行します。その後、レプリカはメッセージの受け付けを開始し、コンセンサスプロトコルはキャッチアップパッケージに含まれるジェネシスブロックを拡張します。

<!---


# Infinite scalability

Ever wondered about the meaning behind DFINITY? It’s Decentralized + Infinity. It’s named that way because the Internet Computer is designed to scale infinitely. It means that the Internet Computer can host an unlimited number of canisters (smart contracts), store an unlimited amount of memory, process an unlimited amount of transactions per second. In simple words, Internet Computer is designed to host even large scale social media platforms in a fully decentralized way.

There are two types of widely-used approaches to improve the scalability of a system: (1) Vertical Scaling, and (2) Horizontal Scaling. Vertical scaling means adding more CPU, RAM and disk to a single computer. Horizontal scaling means adding more computers to the system. There is a limit to vertical scaling. But with horizontal scaling, one can achieve unlimited scalability. Internet Computer is one of the first blockchains to successfully use horizontal scaling.

The nodes in the Internet Computer are divided into subnets, each containing a few dozen nodes. The set of nodes in a subnet together maintain one blockchain. Each subnet can host thousands of canisters and process messages received by those canisters. Each subnet has a limited capacity in terms of the number of canisters (a few thousand), amount of storage (hundreds of GBs), and bandwidth (a few hundred transactions per second). But as more subnets are added to the Internet Computer, its overall capacity increases proportionately. There is no limit on the number of subnets that can be added.

Another crucial design aspect that is a prerequisite for limitless scaling is the inter-subnet communication of canisters: A canister of a subnet can send asynchronous messages to any canister on any other subnet. XNet messages are ingested by the receiving subnet's consensus layer and their integrity is validated based on the sending subnet's threshold signature — another application of [chain-key cryptography](/how-it-works/chain-key-technology/). This architecture of XNet messaging leads to a "loose coupling" of the subnets that does not require a central component such as a shard chain as used in other blockchains with multiple "shards" that would create a bottleneck when scaling out. Therefore newly added subnets can immediately send and receive XNet messages to any other subnet and an increasing number of subnets does not hit a natural bottleneck as in other, more simplistic, architectures.

<figure>
<img src="/img/how-it-works/add-new-subnet.png" alt="Internet Computer is divided into subnets" title="Internet Computer is divided into subnets." align="center" style="width:700px">
<figcaption align="left">
The Internet Computer is divided into subnets. Each subnet hosts many canisters. One of the subnets hosts Network Nervous System canisters.
</figcaption>
</figure>

Creating a new subnet has two steps. (1) Adding new nodes to the Internet Computer, and (2) Creating a subnet with the available nodes
Anyone can purchase the node hardware and add it to the Internet Computer by following the [node provider onboarding process](https://wiki.internetcomputer.org/wiki/Node_Provider_Documentation).

We now describe how to create a new subnet with the available nodes. The Internet Computer has a decentralized governance system called Network Nervous System (NNS). Essentially, the NNS consists of a group of canisters that manage the Internet Computer. In the NNS, there is a component called “registry”, which stores the full configuration of the Internet Computer. The registry has a record for each subnet which includes a protocol version, the list of nodes in the subnet, protocol configuration parameters, etc.

<figure>
<img src="/img/how-it-works/new-subnet-proposal.png" alt="Proposal to create a new subnet" title="Proposal to create a new subnet" align="center" style="width:700px">
<figcaption align="left">
Proposal to create a new subnet. The status of all proposals can be viewed on the [IC Dashboard](https://dashboard.internetcomputer.org/governance).
</figcaption>
</figure>

To add a new subnet, one has to submit a _proposal_ to the NNS to add a record for a new subnet to the registry. The proposal consists of the list of nodes to be included in the new subnet. The proposal can be voted on by anyone who staked their ICP tokens. If a majority of voters accept the proposal, then the registry canister instructs the NNS subnet to generate — in a fully decentralized way using [chain-key cryptography](/how-it-works/chain-key-technology/) — the cryptographic key material to be used by the new subnet and a catch up package containing the genesis block. The registry canister then adds a record containing the configuration of the subnet.

We now describe how a new subnet is created after a record is added to the registry. Each node runs 2 processes, the (1) Replica and the (2) Orchestrator. The replica consists of the 4-layer software stack that maintains the blockchain. The orchestrator downloads and manages the replica software. When a new node is onboarded, the node provider has to install IC OS on the node, which contains the orchestrator software. The orchestrator regularly queries the NNS registry for any updates. If the orchestrator sees that the node is included in a registry record, then the orchestrator downloads the corresponding replica software, and runs the replica with the Catch Up Package included in the registry as input. The replica then starts accepting messages and the consensus protocol extends the genesis block present in the catch up package.

-->
