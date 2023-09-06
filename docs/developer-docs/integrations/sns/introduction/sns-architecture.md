---

sidebar_position: 2
---
# SNSアーキテクチャ

## 概要

SNS のコアアーキテクチャは、Internet Computer プラットフォームを管理する DAO であるNetwork Nervous System
 (NNS) のアーキテクチャに酷似しています。
これには、分散型の意思決定を可能にするガバナンスシステムと、各 SNS に固有のトークンを定義する台帳canister
 が含まれます。
SNS とは対照的に、NNS には IC
プラットフォームを運営するために重要なcanisters が追加されています（例、cycles canister cycles
また、SNSにのみ存在する 、特にSNSの起動プロセスで使用される分散化 スワップ 。canisters
 canister 

## システム機能としてのSNS（NNSコミュニティとの接続）

SNScanisters
 のコードがICによって管理されているという点で、SNSはICによってシステム機能として提供されています。[(ここで](dao-alternatives.md)、
は、SNSのコードを使用する代替方法やDAOを作成する方法についての簡単な説明です。)
より具体的には、これはNNSコミュニティがオリジナルのSNScanisters' コード
を承認し、新しい改良版SNSを継続的に承認していることを意味します。

### SNSワズムモジュールcanister (SNS-W){\#SNS-W}

承認されたすべてのSNScanister のバージョンは、SNS**ワズムモジュールcanister (SNS-W)**と呼ばれるNNScanister,
に保存されます。
SNSが作成されると、SNS-Wが関与し、最新バージョンの
SNScanister を展開する責任を負います。
SNSを更新する必要がある場合は、SNS-Wに
SNScanisters の新バージョンを追加するNNS提案によって行われます。
その後、各SNSコミュニティは、SNS提案によって、承認された新バージョンをSNSインスタンスに採用することを決定できます。

### カスタマイズ可能性

それにもかかわらず、個々のSNSは、
神経系パラメータと呼ばれるパラメータを選択することによってカスタマイズすることができます。
さまざまな投票形式やトークノミクスを実現するために設定することができます。

## SNSサブネット

SNSは、*SNSサブネット*上でホストされます。このサブネットはSNSを排他的にホストするため、
、エンドユーザーの検証が簡素化されます。ユーザーは、SNSがSNSサブネット上で稼働していること（
）を確認するだけで、前項で説明したように、基礎となるコードがNNSコミュニティによって承認されていることを推測できます（
）。

## SNScanisters

SNSは以下のもので構成されていますcanisters ：

- ガバナンスcanister 。
- 台帳canister とアーカイブcanisters.
- インデックスcanister.
- ルートcanister.
- 分散スワップcanister.

### SNSガバナンスcanisters

ガバナンス** canister**ガバナンスの決定に参加できる人を定義し、これらの決定の実行を自動的にトリガーします。
SNS が統治するdapp をどのように進化させるかの提案である**プロポーザルと**、ガバナンスの参加者を定義する**neurons を保存します。** Neuron
SNS トークンをneuron にステークすることで、誰でもガバナンスの参加者になることができます。
提案が採用されると、ガバナンスシステムは自動的に、自律的に、定義されたメソッドを呼び出す形で提案の実行をトリガーします。したがって、ほとんどの場合、これらの決定は完全にチェーン上で実行されます。

### SNS台帳canister アーカイブとインデックス付き

**台帳canister**は、
[ICRC-1 規格](https://github.com/dfinity/ICRC-1)
を実装し、SNSごとに異なる固有のトークンを含んでいます。
この*ような*トークンを**SNSトークンと**呼びます。
各SNSにおいて、このSNSの台帳は、どのアカウントがいくつのSNSトークンを所有しているか、そしてそれらのアカウント間の取引履歴
を保存します。
 canister のメモリが限られていても、台帳の履歴を完全に保持するために、台帳canister は、台帳のブロック履歴を保存する**アーカイブcanisters**を生成します。
さらに、ウォレットやその他のフロントエンドは、特定のアカウントに関連するすべてのトランザクションを表示する必要があります。
これを容易にし、すべてのフロントエンドが自分自身でこれを実装する必要がないようにするために、
**インデックスcanister**は、特定のアカウントに関連するトランザクションのマップを提供します。

### SNSルートcanister

**ルートcanister**は、他の SNScanisters
 と SNS が管理するdapp canisters をアップグレードする責任があります。

### SNS（分散化）スワップcanister

**分散化スワップcanister** 、略してスワップcanister は、SNS立ち上げの主なcanister 関与
です。ユーザーはICPトークンをスワップに提供し、スワップが成功した場合、その見返りとしてステークされたSNSトークン（SNSneurons）を得ることができます。
したがって、ICPとSNSトークンは「スワップ」されます。
これにより、1）SNSは初期資金を集めることができ、
2）多くの異なる参加者にneurons、ひいては議決権を分配することができ、ガバナンスが分散化されます。

<!---

# SNS architecture

## Overview
The core architecture of the SNS closely resembles the architecture of the Network Nervous System
(NNS), the DAO that governs the Internet Computer platform.
It includes a governance system that enables decentralized decision making and a ledger canister
that defines a token unique to each SNS.
In contrast to the SNS, the NNS contains additional canisters that are important to run the IC
platform (e.g., the cycles minting canister that is responsible for creating cycles, the registry
that stores the network topology etc.).
There are also a few canisters that only exist on the SNS, most notably the decentralization 
swap canister that is used during the launch process of an SNS.

## SNS as a system functionality (connection to the NNS community)
SNSs are provided as a system functionality by the IC in that the code for the SNS canisters
is maintained by the IC. ([Here](dao-alternatives.md)
is a brief description of alternative ways to use the SNS code or how to create a DAO.)
More concretely, this means that the NNS community approved the original SNS canisters' code
and continuously approves new improved SNS versions.

### SNS wasm modules canister (SNS-W){#SNS-W}
All approved SNS canister versions are stored on an NNS canister,
called the **SNS wasm modules canister (SNS-W)**.
When an SNS is created, SNS-W is involved and responsible for deploying the latest version of 
the SNS canister.
When the SNS should be updated, this happens by an NNS proposal that adds a new version of the 
SNS canisters to SNS-W.
Each SNS community can then simply decide - by SNS proposal - to adopt these new, approved versions in their SNS instance.

### Customizability
Individual SNSs can nevertheless be customized by choosing parameters, 
called nervous system parameters, that
can be configured to realise different forms of voting and tokenomics.

## The SNS subnet
SNSs are hosted on an _SNS subnet_. Since this subnet exclusively hosts SNSs,
this simplifies the verification for end users: users can simply verify that an SNS is running
on the SNS subnet and infer that the underlying code has been approved by the NNS community as
explained in the previous paragraph.

## SNS canisters
The SNS consists of the following canisters:
* The governance canister.
* The ledger canister and archive canisters.
* The index canister.
* The root canister.
* The decentralization swap canister.

### SNS governance canisters
The **governance canister** defines who can participate in governance decisions and automatically triggers the execution of these decisions.
It stores **proposals** that are suggestions on how to
evolve the dapp that the SNS governs and **neurons** that define who the governance participants are. Neurons facilitate stake-based voting as they contain staked SNS tokens.
Anyone can be a participant in governance by staking SNS tokens in a neuron.
When a proposal is adopted, the governance system automatically and autonomously triggers the execution of the proposal in the form calling a defined method. In most cases, these decisions are therefore executed fully on chain.

### SNS ledger canister with archive and index
The **ledger canister** implements the
[ICRC-1 standard](https://github.com/dfinity/ICRC-1)
and contains a unique token that is different for each SNS. We call this _kind_ of tokens **SNS tokens**.
In each SNS, this SNS's ledger stores which accounts own how many SNS tokens and
the history of transactions between them.
To keep the full ledger history even though a canister has limited
memory, the ledger canister spawns **archive canisters** that store the ledger block history.
Moreover, wallets and other frontends will need to show all transactions that are
relevant for a given account.
To facilitate this and ensure that not every frontend has to implement this themselves,
the **index canister** provides a map of which transactions are relevant for a given account.

### SNS root canister
The **root canister** is responsible for upgrading the other SNS canisters
and the dapp canisters that the SNS governs.

### SNS (decentralization) swap canister
The **decentralization swap canister**, or swap canister for short, is the main canister involved
in the SNS launch. Users can provide ICP tokens to the swap and, if the swap is successful, they get staked SNS tokens (in SNS neurons) in return. 
Hence, the ICP and the SNS tokens are "swapped".
This facilitates that 1) the SNS can collect initial funding and
2) the distribution of neurons and thus of voting power to many different participants, which makes the governance decentralized.


-->
