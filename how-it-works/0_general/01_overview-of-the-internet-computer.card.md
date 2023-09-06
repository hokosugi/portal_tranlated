---

title: Architecture of the Internet Computer
---
![](/img/how-it-works/ic-architecture.jpg)

# のアーキテクチャInternet Computer

Internet Computer (IC)は、スマートコントラクトの形式でプログラムとデータをホストし、スマートコントラクト上で安全かつ信頼できる方法で計算を実行し、無限に拡張できるオープンで安全な*ブロックチェーンベースのネットワーク*である*ワールドコンピュータの*ビジョンを実現します。

Internet Computer 上のスマートコントラクトは、*canister スマートコントラクトと*呼ばれます。 *canisters*[*WebAssembly *](https://en.wikipedia.org/wiki/WebAssembly)
それぞれの は、 がコードを実行するときにのみ変更される、独自の分離されたデータストレージを持っています。canister canister 

Canisters
サブネットは独立したブロックチェーンであり、グローバルに分散されたデータセンターに配置された*ノードマシン*（*ノード*）上で稼働します。1つのサブネットは何万もの スマートコントラクトを安全にホストすることができ、合計で数百ギガバイトのメモリになります。現在、数十のサブネットがあり、将来的には数千に増加します。 サブネット上でホストされる各 について、そのコードとデータはサブネット内のすべてのノードに保存され、そのコードはサブネット内のすべてのノードによって実行されます。ストレージと計算のこのレプリケーションは、 スマートコントラクトがサブネット内の一部のノードに不具合があっても（クラッシュしたり、悪意のあるパーティにハッキングされたりして）実行し続けられるよう、
 canister
 canister
 canister *フォールトトレランスを*実現するために不可欠です。 このレプリケーションは、ブロックチェーンに支えられた、高スループット、低レイテンシーのコンセンサスメカニズムと 実行用の効率的な仮想マシンを実装したコア
 WebAssembly *Internet Computer Protocol （ICP*）によって実現されます。

ICのマルチサブネットアーキテクチャは、よく知られたシャーディングアプローチよりもはるかに強力です。それは、異なるサブネット上のスマートコントラクトが互いにシームレスに通信することを可能にするからです。
Canisters *非同期メッセージを*介して通信します。つまり、メッセージを送信するときにブロックするのではなく、最終的に応答が到着したときにその応答を処理します。
この新しいアプローチによるインターcanister コールは、単にサブネットを追加することによってICをスケールアウトすることができます。

コアとなるICPは、（[閾値暗号に](https://en.wikipedia.org/wiki/Threshold_cryptosystem)基づく）高度な暗号プロトコルのツールボックスである[*連鎖鍵暗号を*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)多用しており、前例のないスケーラビリティを備えたICの分散運用を可能にしています。
連鎖鍵暗号には、障害のあるノードやプロトコルのアップグレードに対処する方法など、運用上の懸念に堅牢かつ安全に対処するための洗練された技術のコレクションも含まれており、私たちはこれを[*連鎖進化技術（chain-evolution technology*](https://internetcomputer.org/how-it-works/#Chain-evolution-technology)
 ）と呼んでいます（例えば、他のブロックチェーンのように、創始ブロックから始まるすべてのブロックを検証することなく、ノードが簡単にサブネットに参加できるようにするなど）。
連鎖鍵暗号ツールボックスのもう一つの構成要素は[*連鎖鍵署名*](https://internetcomputer.org/how-it-works/#Chain-key-transactions)です。
、閾値暗号を使用して、canister 、他のブロックチェーンと相互作用（書き込み）することができます。

完全な分散化の要件を満たすために、ICにはガバナンスに対する完全な分散化アプローチが必要です。
ICプラットフォームのガバナンスは、[*Network Nervous System （NNS*](https://internetcomputer.org/how-it-works/#Network-nervous-system)）と呼ばれる*トークン化された分散型自律組織（DAO*）を通じて実現されます。
IC上の各個人dapp は、dapp の*サービス神経システム（SNS*）に基づく、すぐに使えるトークン化されたDAOをカスタマイズしてデプロイすることで、NNSと同様の独自のガバナンスシステムを持つことができます。

このシステムは [Internet Computer](https://dashboard.internetcomputer.org/)は、DFINITY Foundationによって2021年5月10日にローンチされ、オープンソース化されました。Internet Computer は現在、ICPトークン保有者によって管理される独立したネットワークですが、DFINITY はその進化をサポートし続けています。

[より深く](/how-it-works/architecture-of-the-internet-computer/)

<!---


![](/img/how-it-works/ic-architecture.jpg)

# Architecture of the Internet Computer

The Internet Computer (IC) realizes the vision of a *World Computer* – an open and secure *blockchain-based network* that can host programs and data in the form of smart contracts, perform computations on smart contracts in a secure and trustworthy way, and scale infinitely.

Smart contracts on the Internet Computer are called *canister smart contracts*, or *canisters*, each consisting of a bundle of [*WebAssembly (Wasm)*](https://en.wikipedia.org/wiki/WebAssembly) bytecode and smart contract data storage.
Each canister has its own, isolated, data storage that is only changed when the canister executes code.

Canisters are hosted on *subnets*, the top-level architectural building block of the IC.
A subnet is an independent blockchain, running on *node machines*, or *nodes*, deployed in globally-distributed data centers.
A single subnet can securely host tens of thousands of canister smart contracts, totalling in hundreds of gigabytes of memory – there are currently dozens of subnets, growing to thousands in the future.
For each canister hosted on a subnet, its code and data is stored on every node in the subnet, and its code is executed by every node in the subnet.
This replication of storage and computation is essential to achieve *fault tolerance*, so that canister smart contracts will continue to execute even if some nodes in the subnet are faulty (either because they crash, or even worse, are hacked by a malicious party).
This replication is powered by the core *Internet Computer Protocol (ICP)*, which implements a high-throughput, low-latency consensus mechanism and an efficient virtual machine for WebAssembly execution, backed by a blockchain.

The IC's multi-subnet architecture is much more powerful than the well-known sharding approach because it enables smart contracts on different subnets to communicate with each other seamlessly – much like services in a traditional [microservices architecture]( https://en.wikipedia.org/wiki/Microservices), but fully on chain.
Canisters communicate via *asynchronous messages*, i.e., they don't block on sending a message, but process the response when it eventually arrives. 
This novel approach to inter-canister calls allows for scaling out the IC by simply adding more subnets.

The core ICP makes heavy use of [*chain-key cryptography*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography), a toolbox of advanced cryptographic protocols (based on [threshold cryptography](https://en.wikipedia.org/wiki/Threshold_cryptosystem)) that enables the decentralized operation of the IC with unprecedented scalability.
Chain-key cryptography also includes a sophisticated collection of technologies for robustly and securely addressing operational concerns, such as how to deal with faulty nodes or protocol upgrades, which we call [*chain-evolution technology*](https://internetcomputer.org/how-it-works/#Chain-evolution-technology) 
(for example, enabling nodes to easily join a subnet without validating every block beginning from the genesis block, as in other blockchains).
Another building block in the chain-key crypto toolbox are [*chain-key signatures*](https://internetcomputer.org/how-it-works/#Chain-key-transactions).
They enable a canister to interact with (write to) other blockchains using threshold cryptography.

Having scalable and decentralized technology to power the operation of the network is not enough.
In order to meet the requirements of complete decentralization, the IC needs a fully decentralized approach to governance.
Governance of the IC platform is accomplished through a *tokenized Decentralized Autonomous Organization (DAO)*, which is called the [*Network Nervous System (NNS)*](https://internetcomputer.org/how-it-works/#Network-nervous-system).
Each individual dapp on the IC can have its own governance system similar to the NNS by customizing and deploying an out-of-the-box tokenized DAO based on the *Service Nervous System (SNS)* for the dapp.

The [Internet Computer](https://dashboard.internetcomputer.org/) was launched and open-sourced on May 10th 2021 by the DFINITY Foundation. The Internet Computer is now an independent network controlled by ICP token holders but DFINITY continues supporting its evolution.

[Go deeper](/how-it-works/architecture-of-the-internet-computer/)

-->
