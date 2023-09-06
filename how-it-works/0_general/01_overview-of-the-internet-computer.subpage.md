---

title: Architecture of the Internet Computer
abstract: 
shareImage: /img/how-it-works/ic-architecture.jpg
slug: architecture-of-the-internet-computer
---
# のアーキテクチャInternet Computer

Internet Computer は、スマートコントラクトを安全にホストし実行するための、拡張性の高いブロックチェーンベースのプラットフォームという画期的なアプローチをとっています。
 Internet Computer (IC)を支える技術は、拡張性の欠如など、以前のプロジェクトから学んだ多くの教訓を考慮し、ブロックチェーンプロトコルを一から考え直しました。
 Internet Computer は、無限に拡張でき、スマートコントラクトを安全にホストするために誰もが使用できるオープンで安全なパブリックブロックチェーンネットワークである*ワールドコンピュータに*なることを意図しています。

## Canister スマートコントラクト

IC上のスマートコントラクトは、*canister スマートコントラクトと*呼ばれます。 *canister*
 canister [*WebAssembly (Wasm)*](https://en.wikipedia.org/wiki/WebAssembly)プログラムコードとデータストレージを1つのユニットにバンドルします。
誰でもcanister をInternet Computer 上にデプロイすることができます。
Canisters は複数のマシン、つまりサブネットのノード上で複製され、フォールトトレラントな方法でコードが実行されます。
他のブロックチェーンとは異なり、IC上のスマートコントラクトは、いくつかの可能な*変更可能性ポリシーの*いずれかを尊重することができます：完全に不変（誰にも変更できない）、一方的に変更可能（dApp 開発者によって一方的に変更可能）、または DAO 変異可能（分散化された自律組織によって承認されたように変更可能）。

Canisters を使用して支払います。 *cycles*
そのためには、canisters をcycles で「補充」する必要があります。
Cycles は IC のユーティリティ・トークンである ICP トークンで取得できます。
ICP でcycles を購入すると、供給源から ICP トークンが削除され、対応する価値のcycles が作成されます。
1兆cycles 、1XDR相当のICPで取得できます。XDRは主要通貨で構成されるバスケットで、1XDRは2022年第3四半期時点でおよそ1.3米ドルです。

Canister スマートコントラクトは、他のブロックチェーンのスマートコントラクトよりも強力です：

- Canisters これはICの*逆ガス*モデルを実現するものです。
- Canisters は低料金でギガバイトのメモリーを保持可能。
- ウェブ・ブラウザは、ウェブ・インターフェースやアセットを提供するためのパブリック・クラウドを介さずに、canister スマート・コントラクトと直接対話することができます。これは、他のブロックチェーンのようにパブリッククラウドからUIが提供されるのとは対照的です。
- Canisters 通常のソフトウェアのように、更新や進化が可能です。DAOベースのガバナンススキームは、このアップグレードプロセスを安全かつ分散化することができます。
- Internet Identityを使用すると、canisters 、[*Web認証（WebAuthn）*プロトコルを](https://www.w3.org/TR/webauthn-2/)使用して、安全なハードウェアモジュールに含まれる秘密鍵に基づいてユーザーを認証することができます。*Internet* Identityと呼ばれるこの安全な認証サービスは、スマートコントラクトとしても実装され、他のスマートコントラクトにサービスを提供します。

[canister スマートコントラクトの](https://wiki.internetcomputer.org/wiki/Canister_smart_contract)詳細はIC wikiをご覧ください。

## サブネット・アーキテクチャ

canister
ICのトップレベルのビルディングブロックは、*サブネットワーク*（*サブネット*）です。ICは全体として多数のサブネットで構成され、各サブネットは他のサブネットと同時かつ独立して動作する独自のブロックチェーンです（ただし、他のサブネットとは非同期に通信できます）。各サブネットは、 スマートコントラクトをホストし、最大で合計数百ギガバイトの複製ストレージをホストします。 サブネットは
 canister
*ノードマシン*（*ノード*）で構成されます。 各ノードは、ブロックチェーン技術を用いてサブネットのすべての 、ステート、計算を複製します。これにより、サブネットはブロックチェーンベースの
 canisters
*複製されたステートマシン*、つまり安全でフォールトトレラントかつ改ざん不可能な方法でステートを保持する仮想マシンとなります：サブネット上でホストされる の計算は、たとえサブネット上の一部のノードに欠陥があったとしても（クラッシュしたり、あるいは悪意のある人物にハッキングされたりして）、停止することなく正しく進行します。 システムをスケールアウトするためにICに追加されたノードから新しいサブネットを作成することができます。これは、パブリッククラウドなどの従来のインフラがマシンを追加することでスケールアウトする方法に似ています。このようなスケールアウトアーキテクチャは、ブロックチェーン分野ではむしろ例外であり、無限のスケーリングを可能にします、つまり、ブロックチェーンのセキュリティと回復力をパブリック・クラウドのスケーラビリティと組み合わせることです。canisters

<figure>
<img src="/img/how-it-works/subnet_architecture.png" alt="Architecture: The IC is composed of subnets, each of which is an independent blockchain" title="The IC is composed of subnets, each of which is an independent blockchain" align="center" style="width:600px">
<figcaption align="center">
The IC is composed of subnets, each of which is an independent blockchain.<br>
Canisters within and across subnets communicate through asynchronous messaging.
</figcaption>
</figure>

[サブネットの](https://wiki.internetcomputer.org/wiki/Subnet_blockchain)詳細はIC wikiをご覧ください。

## 非同期メッセージング

前述したように、canister はそのコードとデータ（ステート）をバンドルします。
これにより、canister のステートは他のcanisters のステートから隔離されます。
ユーザーは*イングレスメッセージを*送信することでcanisters と対話します。
Canisters また、同じサブネット上または異なるサブネット上の他のcanisters にメッセージを送信することで、他のcanisters と対話することもできます。
私たちは、ユーザーまたは他のcanisters のいずれかによってcanisters に送信されたメッセージを総称して*メッセージ*または*canister メッセージと*呼びます。
各メッセージは、canister スマートコントラクトコードの実行と（複製された）canister ステートの変更につながる可能性があります。
 canisters に送信されるメッセージは*非同期*です。
メッセージが送信されると、送信者はこの操作によってブロックされず、メッセージに対する応答を受信するまで他の計算を実行できます。
他のほとんどのブロックチェーンでは、スマートコントラクトの呼び出しは同期です、
この非同期メッセージングと分離されたcanister ステートは、異なるcanisters とサブネット間の「緩い結合」をもたらします。
 canisters 間のセキュアな非同期クロスサブネット（XNet）メッセージングと、結果として生じるサブネットの疎結合は、新しいサブネットを追加することによってICのスケーラビリティを解放する重要な原則です。
サブネット上の各canister のステートは、canister への非同期メッセージによってのみ変更できるため、同じサブネット上または異なるサブネット上のcanisters が同時に実行される可能性があります。
プログラミングモデルという点では、非同期通信はICと他の多くのブロックチェーンとの大きな違いですが、前例のないスケーラビリティを実現する鍵でもあります。

## コアInternet Computer Protocol

ICの各サブネットはコアInternet Computer protocol （コアICプロトコル）によって駆動され、その実装がすべてのノードで実行されています。
このプロトコルは次の4つのレイヤーで構成されています：(1) ピアツーピア、(2) コンセンサス、(3) メッセージルーティング、(4) 実行。
このコアICプロトコル・スタックは、あらゆるサブネットのすべてのノード上で実行され、コンセンサスとメッセージ実行の面でサブネットが進歩するように駆動します。
ICの各サブネットは、それによってICの他のサブネットとは独立して進歩を遂げる独自の複製ステートマシンです（ただし、他のサブネットとは非同期に通信します）。

## チェーン・キーとチェーン進化技術

チェーン[*キー*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)暗号は、Internet Computer Protocol の分散運用を可能にする暗号メカニズムの集合です。
[チェーン進化テクノロジーは](https://internetcomputer.org/how-it-works/#Chain-evolution-technology)、ICの長期運用を可能にする特定の暗号ベースのメカニズムを指します。
例えば、連鎖進化技術は、新しいノードが効率的にサブネットに参加したり、ダウンしたノードが効率的にサブネットの残りの部分に追いついたりすることを可能にします。
連鎖鍵と連鎖進化技術の両方が、技術面でICを他のプロジェクトとは一線を画しています。

## ガバナンス

ICは、プラットフォーム・レベルとdApp レベルという複数のレベルでガバナンスを提供します。

### プラットフォーム・ガバナンス

ICは*トークン化されたDAO*、いわゆる*Network Nervous System (NNS)*によって統治されます。
NNS DAOは、canister スマートコントラクトのセットとして実装され、ハイレプリケーションサブネット、すなわち多くのノードを持つサブネット、したがってより強力なセキュリティ特性を持つサブネットに展開されます。
NNSは、ステークされたICPの保有者が提案を行い、それらの提案に投票することを可能にします。

### Dapp ガバナンス

dApps これはプラットフォームの NNS に似ていますが、 を管理するように調整されています。  を管理する誰もが、SNS のインスタンスをパラメータ化してデプロイすることで、トークン化された DAO に自分の の管理を引き渡すことができます。SNS は レベルでトークン化されたガバナンスを実装しており、 エンジニア自身がガバナンスシステムを実装することなく使用することができます。これは画期的なことです。  のコントロールを SNS のインスタンスに引き渡すには、通常、初期段階として分散化スワップを実行し、 のガバナンストークンのスワップを通じて資金を調達します。dApps
 dApp dApp
 dApp dApp
 dApp dApp

## さらに深く

ICがどのように機能し、ワールド・コンピュータのビジョンを実現するのか、さらに詳しく知りたい場合は、このページの各セクションや参照したMediumの記事を読むか、YouTubeのビデオをご覧ください。
情報源を1つにまとめたい場合は、[ホワイトペーパーを](https://dfinity.org/whitepaper.pdf)強くお勧めします。
ただし、少し専門的な部分もありますのでご注意ください。

[Internet Computer for Geeks - White Paper](https://dfinity.org/whitepaper.pdf)
[Internet Computer Dashboard](https://dashboard.internetcomputer.org/)
[Internet Computer Source Code](https://github.com/dfinity/ic)
[Public Repositories for theInternet Computer ](https://github.com/dfinity?q=&type=public&language=&sort=)
[Internet Computer Interface Specification Docs](https://internetcomputer.org/docs/ic-interface-spec.md)
[Internet Computer Primer - Deck](https://dfinity.org/deck/)
[Internet Computer SDK](https://github.com/dfinity/sdk)

[![Watch youtube video](https://i.ytimg.com/vi/YWHTNr8RZHg/maxresdefault.jpg)](https://www.youtube.com/watch?v=YWHTNr8RZHg)

<!---


# Architecture of the Internet Computer

The Internet Computer takes a revolutionary approach to a highly-scalable blockchain-based platform for securely hosting and executing smart contracts.
The technology behind the Internet Computer (IC) has reconceived blockchain protocols from the ground up, considering many lessons learnt from earlier projects, e.g., their lack of scalability.
The Internet Computer intends to become a *World Computer*, an open and secure public blockchain network that can scale infinitely and be used by everyone to host their smart contracts securely.

## Canister smart contracts

A smart contract on the IC is called *canister smart contract*, or just *canister*.
A canister bundles [*WebAssembly (Wasm)*](https://en.wikipedia.org/wiki/WebAssembly) program code and data storage into a single unit.
Anyone can deploy a canister on the Internet Computer.
Canisters are stored and their code executed in a replicated, fault-tolerant manner on multiple machines, that is, the nodes of a subnet. 
Unlike other blockchains, a smart contract on the IC can respect one of several possible *mutability policies*: it can be completely immutable (cannot be changed by anyone), unilaterally mutable (can be changed unilaterally by the dApp developer), or DAO mutable (it can be changed as authorized by a decentralized autonomous organization).


Canisters pay, using *cycles*, for the IC resources they consume.
To this end, canisters need to be “topped up” with cycles.
Cycles can be acquired with the ICP token, the IC's utility token.
Buying cycles with ICP removes the ICP token from the supply and creates an amount of cycles with the corresponding value.
One Trillion cycles can be acquired with ICP worth 1 XDR, where an XDR is a basket comprising major currencies and one XDR is roughly 1.3 USD as of Q3 2022.

Canister smart contracts are more powerful than the smart contracts on other blockchains:
* Canisters paying for their resources, and not the end users, which realizes the *reverse gas* model of the IC.
* Canisters can hold gigabytes of memory for a low fee.
* A web browser can directly interact with a canister smart contract, without involving any public cloud for serving the web interface and assets. This is in stark contrast to the UI being served from the public cloud as is the case for other blockchains.
* Canisters can be updated and evolve, much like regular software. DAO-based governance schemes can make this upgrade process secure and decentralized.
* Using Internet Identity, canisters can authenticate users based on private keys contained in secure hardware modules using the [*Web Authentication (WebAuthn)* protocol](https://www.w3.org/TR/webauthn-2/). This secure authentication service, called *Internet Identity*, is implemented as a smart contract as well and provides its services to other smart contracts.

See more on [canister smart contracts](https://wiki.internetcomputer.org/wiki/Canister_smart_contract) on the IC wiki.

## Subnet architecture

The IC is designed to be highly *scalable* and efficient in terms of hosting and executing canister smart contracts.
The top-level building blocks of the IC are *subnetworks*, or  *subnets*: the IC as a whole consists of many subnets, where each subnet is its own blockchain that operates concurrently with and independently of the other subnets (but can communicate asynchronously with other subnets).
Each subnet hosts canister smart contracts, up to a total of hundreds of gigabytes of replicated storage.
A subnet consists of *node machines*, or *nodes*.
Each node replicates all the canisters, state, and computation of the subnet using blockchain technology.
This makes a subnet a blockchain-based *replicated state machine*, that is, a virtual machine that holds state in a secure, fault-tolerant, and non-tamperable manner: the computations of the canisters hosted on a subnet will proceed correctly and without stopping, even if some of the nodes on the subnet are faulty (either because they crash, or even worse, are hacked by a malicious party)
New subnets can be created from nodes added to the IC to scale out the system, analogous to how traditional infrastructures such as public clouds scale out by adding machines.
Such a scale-out architecture is rather the exception than the rule in the blockchain space and allows for limitless scaling, i.e., combining the security and resiliency properties of blockchains with the scalability properties enjoyed by the public cloud.

<figure>
<img src="/img/how-it-works/subnet_architecture.png" alt="Architecture: The IC is composed of subnets, each of which is an independent blockchain" title="The IC is composed of subnets, each of which is an independent blockchain" align="center" style="width:600px">
<figcaption align="center">
The IC is composed of subnets, each of which is an independent blockchain.<br>
Canisters within and across subnets communicate through asynchronous messaging.
</figcaption>
</figure>

See more on [subnets](https://wiki.internetcomputer.org/wiki/Subnet_blockchain) on the IC wiki.

## Asynchronous messaging

As mentioned earlier, a canister bundles its code and data (state).
This makes the canister state isolated from that of other canisters.
Users interact with canisters by sending *ingress messages*.
Canisters may also interact with other canisters by sending messages to other canisters on the same or different subnets.
We collectively refer to messages sent to canisters, either by users or other canisters, as *messages* or *canister messages*.
Each message can lead to the execution of canister smart contract code and the change of (replicated) canister state.
Messages sent to canisters are *asynchronous*:
when a message is sent, the sender is not blocked by this operation, but can perform other computations until the response to the message is received.
In most other blockchains, smart contract invocation is synchronous, i.e., a call to another smart contract is blocking, and there is one global state.
This asynchronous messaging and isolated canister state results in a "loose coupling" between different canisters and subnets.
The secure asynchronous cross-subnet (XNet) messaging between canisters and the resulting loose coupling of subnets are key principles that unlock the scalability of the IC by means of adding new subnets:
the state of each canister on a subnet can only be changed through asynchronous messages to the canister and thus canisters on the same or different subnets may execute concurrently.
In terms of the programming model, asynchronous communication is a major difference between the IC and most other blockchains; however, it is the key to achieving unprecedented scalability.

## Core Internet Computer Protocol

Each subnet of the IC is driven by the core Internet Computer protocol (core IC protocol), an implementation of which is running on every node.
This protocol consists of the following four layers: (1) peer-to-peer, (2) consensus, (3) message routing, and (4) execution.
This core IC protocol stack is running on all nodes of any subnet and drives the subnet to make progress in terms of consensus and message execution.
Each subnet of the IC thereby is its own replicated state machine that makes progress independently of the other subnets of the IC (but communicates with other subnets asynchronously)

## Chain-Key and chain-evolution technology

Many parts of the protocol of the IC depend on [*chain-key cryptography*](https://internetcomputer.org/how-it-works/#Chain-key-cryptography), also referred to as chain-key technology.
Chain-key cryptography is the collection of cryptographic mechanisms that enable the decentralized operation of Internet Computer Protocol.
[Chain-evolution technology](https://internetcomputer.org/how-it-works/#Chain-evolution-technology) refers to specific cryptography-based mechanisms that enable the IC to operate in the long term.
For example, chain-evolution technology enables new nodes to efficiently join a subnet or nodes that have been down to efficiently catch up with the remaining part of the subnet.
Both chain-key and chain-evolution technology sets the IC apart from other projects in terms of technology.

## Governance

The IC offers governance at multiple levels, the platform level and the dApp level.

### Platform governance

The IC is governed by a *tokenized DAO*, the so-called *Network Nervous System (NNS)*.
The NNS DAO is implemented as a set of canister smart contracts that are deployed on a high-replication subnet, i.e., a subnet with many nodes and hence stronger security properties.
The NNS allows holders of the staked ICP to make proposals and vote on those proposals.

### Dapp governance

dApps on the IC can be governed by an out-of-the-box deployable governance system, the *Service Nervous System (SNS)*, which is similar to the platform's NNS, but tailored to govern dApps.
Everyone controlling a dApp can hand over control over their dApp to a tokenized DAO by parameterizing and deploying an instance of the SNS.
The SNS implements tokenized governance at the dApp level and can be used without the dApp engineers implementing a governance system themselves, which is revolutionary.
Handing over control of a dApp to an instance of the SNS usually includes running a decentralization swap as an early step where funds can be raised through the swap of the dApp's governance tokens.

## Go even deeper

If you want to learn in more detail how the IC works and realizes the vision of a World Computer, read through the sections of the page and the referenced Medium articles, or watch the YouTube videos.
If you prefer to have a single source of information, the [White Paper](https://dfinity.org/whitepaper.pdf) is highly recommended.
However, note that it is a little technical at times.

[Internet Computer for Geeks – White Paper](https://dfinity.org/whitepaper.pdf)
[Internet Computer Dashboard](https://dashboard.internetcomputer.org/)
[Internet Computer Source Code](https://github.com/dfinity/ic)
[Public Repositories for the Internet Computer](https://github.com/dfinity?q=&type=public&language=&sort=)
[Internet Computer Interface Specification Docs](https://internetcomputer.org/docs/ic-interface-spec.md)
[Internet Computer Primer - Deck](https://dfinity.org/deck/)
[Internet Computer SDK](https://github.com/dfinity/sdk)

[![Watch youtube video](https://i.ytimg.com/vi/YWHTNr8RZHg/maxresdefault.jpg)](https://www.youtube.com/watch?v=YWHTNr8RZHg)

-->
