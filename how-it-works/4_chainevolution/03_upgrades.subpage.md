---

title: Blockchain Protocol Upgrade
abstract:
# shareImage: /img/how-it-works/protocol-updates.webp
slug: upgrades
---
# ブロックチェーンプロトコルのアップグレード

どんなソフトウェアでも、市場での競争力を維持するために定期的なアップデートが必要です。バグの修正、新機能の追加、アルゴリズムの変更、基盤技術の変更などです。ブロックチェーンプロトコルも同じです。コミュニティとして、私たちは問題を解決するためのより良い方法を学び続け、それに応じてブロックチェーンプロトコルをアップグレードしたいと考えています。例えば、イーサリアムは最近「マージ」というアップグレードを行い、プロトコルをプルーフ・オブ・ワークからプルーフ・オブ・ステークにアップグレードしました。ビットコインは「Taproot」アップグレードを行い、署名検証を改善しました。

ブロックチェーンのプロトコルをアップグレードすることは、その成功のために非常に重要ですが、ビットコインやイーサリアムを含むほとんどのブロックチェーンは、そのように設計されていません。これは主に、ブロックチェーンが単一の権威によって管理されていないためです。すべてのアップグレード提案はコミュニティによって評価されなければなりません。しかし、コミュニティは通常、提案について意見が分かれています。決定を確定し、新機能を構築するための迅速かつ正式な枠組みがないのです。プロトコルのアップグレードはネットワークのフォークを引き起こす可能性があります。その結果、ブロックチェーンのプロトコルをアップグレードするには、コミュニティによる何年もの共同作業が必要になります。イーサリアムは[7年半の間に18回しかプロトコルをアップグレードして](https://ethereum.org/en/history/)いません。

Internet Computer は、アップグレードのたびにコミュニティのコンセンサスを必要としながらも、ユーザーが感じるダウンタイムを最小限に抑え、フォークすることなく簡単にアップグレードできるように設計されたユニークなブロックチェーンです。創設から1年半の間に、Internet Computer は何度もアップグレードされ、決定論的タイムスライス、ビットコイン統合、サービス・ナーバス・システム（SNS）、HTTPSアウトコール、チェーンキーECDSA署名（閾値ECDSAに基づく）、増加した安定メモリなどの重要な機能が追加されました。

プロトコルのアップグレード」機能は、以下の目標で設計されています：(1)Internet Computer Protocol への任意の変更を許可する、(2) アップグレード間のステート保持、(3) ダウンタイムの最小化、(4) アップグレードの自律的な展開。

プロトコルのアップグレードは、Network Nervous System (NNS)と呼ばれるブロックチェーン・ガバナンス・システムにより実現可能です。NNSには「レジストリ」と呼ばれるコンポーネントがあり、Internet Computer のすべてのコンフィギュレーションを保存します。コンフィギュレーションにはバージョン管理システムが実装されています。コンフィギュレーションに変異が起こるたびに、レジストリの新しいバージョンとして表示されます。レジストリには各サブネットのレコードがあり、これにはプロトコルのバージョン、サブネット内のノードのリスト、サブネットで使用される暗号キーの素材などが含まれます。レジストリは希望するコンフィギュレーションを保存することに注意してください。サブネットは実際には古いコンフィギュレーションのいずれかを実行しているかもしれません。

<figure>
<img src="/img/how-it-works/registry-versions.png" alt="Registry implements versioning mechanism" title="Registry implements versioning mechanism" align="center" style="width:700px">
</figure>

プロトコルのアップグレードを開始するには、レジストリの設定を変更する*提案を*NNSに提出する必要があります。この提案は、ICPトークンをステークした人なら誰でも投票できます。投票者の過半数が提案を受け入れれば、レジストリはそれに応じて変更されます。

<figure>
<img src="/img/how-it-works/upgrade-proposal.png" alt="Proposal to upgrade a subnet to a new replica version" title="Proposal to upgrade a subnet to a new replica version" align="center" style="width:700px">
<figcaption align="left">
Proposal to upgrade a subnet to a new replica version. The status of all proposals can be viewed at https://dashboard.internetcomputer.org/governance.
</figcaption>
</figure>

次に、レジストリのバージョンを変更すると、Internet Computer がどのようにアップグレードされるかを説明します。プロトコルのアップグレードはサブネット単位で行われます。各サブネットは多数のノードによって実行されます。各ノードは2つのプロセス、(1)レプリカと(2)オーケストレーターを実行します。レプリカはブロックチェーンを維持する4層のソフトウェアスタックで構成されています。オーケストレーターはレプリカのソフトウェアをダウンロードして管理します。オーケストレーターは定期的にNNSレジストリの更新を問い合わせます。新しいレジストリのバージョンがあれば、オーケストレーターは対応するレプリカソフトウェアをダウンロードし、レプリカに通知します。

各コンセンサスラウンドでは、サブネット内のノードの1つ（*ブロックメーカーと*呼ばれる）がブロックを提案します。ブロックメーカーはすべてのブロックに、レジストリからダウンロードした最新のレジストリ・バージョンを含めます。他のノードは、参照されたレジストリが利用可能な場合にのみブロックを公証します。

サブネット内のすべてのノードがレジストリの最新バージョンにコンセンサスで合意したら、次のステップは新しいバージョンに切り替えることです。フォークを避けるには、すべてのノードが同じブロックの高さでバージョンを調整し、切り替えることが重要です。これを実現するために、コンセンサスプロトコルはエポックに分割されます。各エポックは数百コンセンサスラウンドです（レジストリで設定できます）。エポック期間中、サブネット内のすべてのレプリカは、新しいレプリカのバージョンがレジストリで見つかりブロックに含まれていても、同じレプリカのバージョンを実行します。プロトコルのアップグレードはエポック境界でのみ行われます。

<figure>
<img src="/img/how-it-works/protocol-transition.png" alt="Protocol upgrads happens at epoch boundaries" title="Protocol upgrads happens at epoch boundaries" align="center" style="width:700px">
</figure>

各エポックの最初のブロックは*サマリ・ブロックで*、そのエポックで使用される設定情報（レジストリのバージョンと暗号鍵の材料を含む）で構成されます。エポック*xの*サマリーブロックは、エポック*x*全体で使用されるレジストリのバージョンと、エポック*x+1*全体で使用されるレジストリのバージョンの両方を指定します。したがって、すべてのノードは、エポックが始まるずっと前に、エポックに使用するレジストリのバージョンに合意します。

サブネットのプロトコルのアップグレードがエポック*xの*最初に行われることになっているとします。その後、ノードは新しいメッセージの処理を停止しますが、サマリー・ブロックが確定して実行され、完全な複製ステートが認証されるまで、一連の空のブロックを生成します。その後、すべてのノードが*キャッチアップパッケージ（CUP*）を作成します。CUPには、古いレプリカソフトウェアから新しいレプリカソフトウェアに転送する必要がある関連情報が含まれています。CUPは、新しいレプリカソフトウェアがコンセンサスを再開するのに十分なコンテキストを提供します。レプリカはCUPをオーケストレータに送信します。オーケストレーターは、CUPを入力として新しいレプリカソフトウェアを実行します。ホワイトペーパーのセクション8では、サマリーブロックとキャッチアップパッケージの内容について詳しく説明しています。

<figure>
<img src="/img/how-it-works/handing-cup.png" alt="Catch Up Package is handed over to new replica version" title="Catch Up Package is handed over to new replica version" align="center" style="width:700px">
</figure>

[アップグレードInternet Computer Protocol](https://medium.com/dfinity/upgrading-the-internet-computer-protocol-45bf6424b268)

[ホワイトペーパーのセクション8](https://internetcomputer.org/whitepaper.pdf)

[![Watch youtube video](https://i.ytimg.com/vi/mPjiO2bk2lI/maxresdefault.jpg)](https://www.youtube.com/watch?v=mPjiO2bk2lI)

[![Watch youtube video](https://i.ytimg.com/vi/oEEPLJVX5DE/maxresdefault.jpg)](https://www.youtube.com/watch?v=oEEPLJVX5DE)

<!---


# Blockchain Protocol Upgrade

Any software needs to be updated on a regular basis to stay competitive in the market. This could be to fix bugs, add new features, change the algorithms, change the underlying technology, etc. Blockchain protocols are no different. As a community, we keep learning better ways to solve our problems and would like to upgrade our blockchain protocols accordingly. For example, Ethereum recently had “The Merge” upgrade, which upgraded their protocol from Proof of Work to Proof of Stake. Bitcoin had the “Taproot” upgrade, which improved their signature verification.

While upgrading a blockchain protocol is extremely crucial for its success, most blockchains including Bitcoin and Ethereum are not designed to do so. This is primarily because blockchains are not controlled by a single authority. Every upgrade proposal has to be evaluated by the community. However, the community is usually split on the proposals. There is no quick and formal framework to finalize the decisions and build new features. Upgrades to the protocol potentially cause a fork in the network. As a result, upgrading a blockchain protocol could take years of joint effort by the community. Ethereum went through only [18 protocol upgrades in a 7.5 year time span](https://ethereum.org/en/history/).

The Internet Computer is a unique blockchain that is designed to be easily upgradeable with a minimal user-perceived downtime and without any forks while still requiring consensus by the community for each upgrade. Within 1.5 years after genesis, the Internet Computer has upgraded many times, adding crucial features such as deterministic time slicing, Bitcoin integration, Service Nervous System (SNS), HTTPS outcalls, chain-key ECDSA signatures (based on threshold ECDSA), increased stable memory, etc.

The “protocol upgrades” feature is designed with the following goals: (1) Allow arbitrary changes to the Internet Computer Protocol; (2) Preserve the state between upgrades; (3) Minimize downtime; (4) Roll out upgrades autonomously.

Protocol upgrades are made feasible due to our blockchain governance system called Network Nervous System (NNS). In the NNS, there is a component called “registry”, which stores all the configuration of the Internet Computer. A versioning system is implemented for the configuration. Each mutation to the configuration shows up as a new version in the registry. The registry has a record for each subnet which includes a protocol version, list of nodes in the subnet, cryptographic key material to be used by the subnet, etc. Note that the registry stores the desired configuration. The subnets might actually be running one of the older configurations.

<figure>
<img src="/img/how-it-works/registry-versions.png" alt="Registry implements versioning mechanism" title="Registry implements versioning mechanism" align="center" style="width:700px">
</figure>

To trigger a protocol upgrade, one has to submit a _proposal_ in the NNS to change the configuration of the registry. The proposal can be voted by anyone who staked their ICP tokens. If a majority of voters accept the proposal, then the registry is changed accordingly.

<figure>
<img src="/img/how-it-works/upgrade-proposal.png" alt="Proposal to upgrade a subnet to a new replica version" title="Proposal to upgrade a subnet to a new replica version" align="center" style="width:700px">
<figcaption align="left">
Proposal to upgrade a subnet to a new replica version. The status of all proposals can be viewed at https://dashboard.internetcomputer.org/governance.
</figcaption>
</figure>

We now describe how a change in registry version upgrades the Internet Computer. Protocol upgrades are done on a per-subnet basis. Each subnet is run by many nodes. Each node runs 2 processes — (1) the Replica and (2) the Orchestrator. The replica consists of the 4-layer software stack that maintains the blockchain. The orchestrator downloads and manages the replica software. The orchestrator regularly queries the NNS registry for any updates. If there is a new registry version, the orchestrator downloads the corresponding replica software and informs the replica about it.

In each consensus round, one of the nodes in the subnet (called the _block maker_) proposes a block. In every block, the block maker includes the latest registry version it downloaded from the registry. Other nodes notarize the block only when they have the referenced registry available.

After all the nodes in the subnet agree upon the latest registry version via consensus, the obvious next step is to switch to the new version. To avoid forks, it is crucial that all the nodes coordinate and switch their version at the same block height. To achieve this, the consensus protocol is divided into epochs. Each epoch is a few hundred consensus rounds (can be configured in the registry). Throughout an epoch, all the replicas in the subnet run the same Replica version, even if a newer Replica version is found in the registry and included in the blocks. Protocol upgrades happen only at the epoch boundaries.

<figure>
<img src="/img/how-it-works/protocol-transition.png" alt="Protocol upgrads happens at epoch boundaries" title="Protocol upgrads happens at epoch boundaries" align="center" style="width:700px">
</figure>

The first block in each epoch is a _summary block_, which consists of the configuration information (including registry version and cryptographic key material) that will be used during the epoch. The summary block of epoch _x_ specifies both the registry version to be used throughout epoch _x_, and the registry version to be used throughout epoch _x+1_. Therefore, all the nodes agree on what registry version to use for an epoch long before the epoch starts.

Suppose a protocol upgrade of the subnet is supposed to be done at the beginning of epoch _x_. A blockmaker first proposes the summary block. The nodes then stop processing any new messages, but produce a series of empty blocks until the summary block is finalized, executed and the complete replicated state is certified. Then, all the nodes create a _catch up package (CUP)_, which contains the relevant information that needs to be transferred from the old replica software to the new replica software. The CUP gives enough context for the new replica software to resume consensus. The replicas send the CUP to the orchestrator. The orchestrator runs the new replica software with the CUP as input. Section 8 of the whitepaper describes the contents of the summary block and catch up package in detail.

<figure>
<img src="/img/how-it-works/handing-cup.png" alt="Catch Up Package is handed over to new replica version" title="Catch Up Package is handed over to new replica version" align="center" style="width:700px">
</figure>

[Upgrading the Internet Computer Protocol](https://medium.com/dfinity/upgrading-the-internet-computer-protocol-45bf6424b268)

[Whitepaper, see Section 8](https://internetcomputer.org/whitepaper.pdf)

[![Watch youtube video](https://i.ytimg.com/vi/mPjiO2bk2lI/maxresdefault.jpg)](https://www.youtube.com/watch?v=mPjiO2bk2lI)

[![Watch youtube video](https://i.ytimg.com/vi/oEEPLJVX5DE/maxresdefault.jpg)](https://www.youtube.com/watch?v=oEEPLJVX5DE)

-->
