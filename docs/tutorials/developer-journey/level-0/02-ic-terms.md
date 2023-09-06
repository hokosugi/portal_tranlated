# 0.2:Internet Computer 専門用語

## 概要

このページでは、開発者がInternet Computer でビルドする際に知っておくべき、よく使われる用語を紹介[します](/docs/references/glossary.md)。 このページでは多くの用語を扱いますが、Internet Computer に関連する用語をすべて網羅しているわけではありません。

## 概念

- **Actor:** actor は、カプセル化されたステートを持つプロセスであり、並行して実行されている他のactorと通信します。Actorは、逐次受信される非同期メッセージを通じて通信します。Actorは、自身のプライベートステートを変更できますが、他のactorを変更できるのは、メッセージを通じて間接的にのみです。Actorは、actor モデルの一部であり、canisters で並行および非同期の計算に使用されます。

- **エージェント：**エージェントとは、Internet Computer のパブリックインターフェイスを呼び出すために使用されるライブラリのことです。外部エージェントの例としては、JavaScript エージェントや Rust エージェントがあります。

- **認証変数：**認証変数とは、アップデートまたはインターcanister コールの処理中に、canister がサブネットの標準ステートに格納するデータの一部です。このデータはクエリーコール時に使用され、canister 、データの価値を証明する証明書をユーザーに返すことができます。

- **連鎖鍵暗号：**チェーンキー暗号は、ICが他のブロックチェーンネットワークでは不可能なスケーラビリティと機能性を実現することを可能にする、高度な暗号メカニズムの配列です。これらの暗号プロトコルは、ICを構成するノードのオーケストレーションに役立ちます。連鎖鍵暗号の一例として、ICの単一の公開鍵があり、これによりどのデバイスもICからの成果物の真正性を検証することができます。

- **Cycles**:cycle canisterこれらのリソースには、計算処理能力、メモリ、ストレージ、ネットワーク帯域幅が含まれます。Cycles は、メインネットにデプロイされたcanister によって実行されるたびに消費されます。ICのユーティリティ・トークンICPは、cycles に変換してcanister に転送し、canister の消費リソースの支払いに使用することができます。ICPは、[XDRで](https://internetcomputer.org/docs/current/references/glossary#xdr)測定されるICPの現在の価格を使用してcycles 、1兆cycles が1 XDRに対応します。XDRは特別引出権（SDR）の通貨コードで、国際通貨基金（IMF）が定義・管理する補足的な外貨資産です。

- **分散型アプリケーション(dapp)：**分散型アプリケーション(dapp)とは、canister または、Internet Computer 上に展開されたプログラムやサービスを提供する複数の相互運用可能なcanisters を指します。

- **分散型自律組織（DAO）：**分散型自律組織（DAO）とは、中央集権的な権威が存在しない統治形態。意思決定は、通常DAOのトークンをステークすることによって決定される利害関係者によって、DAOに提案された意思決定に対する投票を通じて行われます。すべてのDAO活動はオンチェーンで実行され、活動の透明性と検証可能な記録を提供します。

- **実行：**ICにおける実行とは、canister スマートコントラクトコードの実行を担う実行レイヤーを指します。実行はWebAssembly (Wasm) を使って行われます。

- **ICP：**ICPは、Internet Computer のネイティブユーティリティトークンです。ICPは、トークンのステーキングを通じてネットワークのガバナンスに参加するために使用したり、canister のリソース消費に対する支払いに使用できるcycles に変換したりすることができます。

- **プリンシパル**：IC に関するプリンシパルとは、IC によって認証されるエンティティ。

- **提案：**提案とは、IC またはそのサブシステムのいずれかの側面に対する修正案を記述した文言。プロポーザルは、資格のあるneuron の所有者によって、ICコミュニティからの検討のために提出することができます。プロポーザルが提出されると、投票プロセスを経て、提案された修正が採用されるか否かが決定されます。

- **メッセージ**ICに関するメッセージとは、あるcanister から別のcanister に送信されるデータ、またはユーザーからcanister に送信されるデータのこと。

- **レプリカ：** Internet Computer 、レプリカはノードがサブネットに参加するために必要なプロトコル・コンポーネントの集まり。

- **サブネット：** Internet Computer 、サブネットは、コンセンサス・アルゴリズムの独自の実装を実行することで、ブロックチェーン・ネットワークの独立したインスタンスを運用するノードの集まりです。サブネットは連鎖鍵暗号を通じて他のサブネットと相互作用します。

- **トランザクション：**トランザクションとは、あるアカウントから別のアカウントにICPを移転する台帳プロセスを指します。トランザクションには、通常の送金トランザクション、発行トランザクション、バーントランザクションがあります。

## Canister 用語

- **スマートコントラクト：**スマートコントラクトはステートフルなプログラムで、コントラクトの設定に従ってイベントやアクションを自動的に実行、制御、文書化するように設計されています。Internet Computer 上で、スマートコントラクトはデータとコードを束ねたcanister の形で展開されます。

- **Canister:** canister は、コードとステートの両方を束ねたスマートコントラクトの計算ユニットの一種です。IC 上でdapp を開発する場合、canister ファイルがコード開発の主な場所となります。Canister タイプは、バックエンドcanisters 、フロントエンドcanisters 、カスタムcanisters の 3 つに分類されます。

- **Canister アカウント：** canister アカウントは、 が所有する元帳アカウント。canister

- **Canister 開発キット（CDK）：**開発キット（CDK）： 開発キット（CDK）は、 SDK で使用されるアダプタで、 を作成および管理するために必要な機能と特徴を備えたプログラミング言語を提供します。canister Internet Computer canisters

- **Canister 識別子：** canister 識別子（ID）は、 のグローバルに一意な識別子で、その識別子と対話するために使用できます。canister 

- **Canister ステート：** canister ステートとは、任意の時点における の状態全体を指します。ステートは、システム・ステートとユーザー・ステートの2つの部分に分けられます。システム・ステートには、 に代わってICが維持する補助ステートが含まれ、 のバランス、入出力キュー、計算割り当て、その他のメタデータなどの情報が含まれます。ユーザー・ステートには、 モジュール・インスタンスが含まれます。 は、メッセージを送信するときなどシステムAPIを介して、または実行のために を消費するときなど暗黙的に、その ステートと対話します。canister canister cycles WebAssembly canister cycles canister 

- **コントローラ：**IC上のコントローラとは、個々のcanister 。コントローラは、個人、組織（DAOなど）、またはcanister の管理権限を持つ別のcanister である可能性があります。 コントローラは、プリンシパルによって識別されます。コントローラは、canister のアップグレードやcanister の削除などの機能を実行できる。

- **アイデンティティ：**アイデンティティ:canisters に関して、アイデンティティはcanisters のアクセス制御に使用されるプリンシパル値です。 アイデンティティはcanisters のコントローラとして設定でき、cycles ウォレットを関連付けることができます。

- **クエリ：**クエリとは、canister 。クエリは同期的に実行され、canister 。クエリの結果を検証するためにコンセンサスを取る必要はありません。

- **ステート変更：**ステート変更とは、canister 内に格納されている情報を変更する操作、関数コール、またはトランザクションの結果を指します。単純な例としては、リストから値を削除するアップデートコールを行う関数があります。この関数の結果は、canister のステートの変更です。

- **システムcanister**: システムcanisters はプリインストールされたcanisters で、Internet Computer を維持するために使用される特定のタスクを実行します。

- **ウォレット：** canisters に関して、ウォレットはcanister のcycles ウォレットを指します。cycles ウォレットにはcanister のリソース消費の支払いに使用されるcycles が含まれます。

## ツールと製品

- **dfx:** `dfx` は、canisters の作成、管理、Internet Computer へのデプロイに使用される主要ツール。`dfx` は IC SDK のコンポーネント。

- **Internet Identity：**Internet Identity はInternet Computer のネイティブ認証システムです。

- **台帳：**IC に関連して、台帳canister は、アカウントとそれに対応するトランザクションを保存するために使用されるシステムcanister です。

- **Motoko** Motoko Internet Computer actor モデルやスマートコントラクトのコンパイル（ ）など、IC 独自の機能や属性をサポートするために特別に設計されていますWebAssembly**。**

## 次のステップ

- 0\.[3: 開発者環境のセットアップ](03-dev-env.md)。

<!---
# 0.2: Internet Computer terminology 

## Overview

This page introduces some of the most commonly used terminology that developers should be aware of when building on the Internet Computer. This page covers many terms, but does not include every single term that is related to the Internet Computer. For the full glossary of terms, please see [here.](/docs/references/glossary.md)

## Concepts 

- **Actor:** An actor is a process with an encapsulated state that communicates with other actors that are concurrently running. Actors communicate through asynchronous messages that are received sequentially. Actors can modify their own private state, but can only alter other actors indirectly through messages.  Actors are a part of the actor model, which is used by canisters for concurrent and asynchronous computation. 

- **Agent:** An agent is a library used to make calls to the Internet Computer public interface. Examples of external agents include the JavaScript and Rust agents. 

- **Certified variables:** A certified variable is a piece of data that a canister stores in the subnet's canonical state during the processing of an update or inter-canister call. This data is used when a query call is made, so that the canister can return a certificate to the user to prove the data's value. 

- **Chain-key cryptography:** Chain-key cryptography is an array of advanced cryptographic mechanisms which allow the IC to achieve scalability and functionalities that aren't possible on other blockchain networks. These cryptographic protocols help orchestrate the nodes that make up the IC. One example of chain-key cryptography is the IC's single public key, which allows any device to verify the authenticity of artifacts from the IC. 

- **Cycles**: A cycle in regards to the IC is a unit of measurement used for resources consumed by a canister. These resources include compute processing power, memory, storage, and network bandwidth. Cycles are consumed for every execution performed by a canister that has been deployed on the mainnet. The IC's utility token ICP can be converted into cycles and transferred into a canister to be used to pay for that canister's consumed resources. ICP can be converted to cycles using the current price of ICP measured in [XDR](https://internetcomputer.org/docs/current/references/glossary#xdr), where one trillion cycles corresponds to one XDR. XDR is the currency code for special drawing rights (SDR), which are supplementary foreign exchange assets defined and maintained by the International Monetary Fund (IMF).

- **Decentralized application (dapp):** A decentralized application (dapp) refers to a canister or several interoperable canisters that provide a program or service that has been deployed on the Internet Computer. 

- **Decentralized autonomous organization (DAO):** A decentralized autonomous organization (DAO) is a form of governance where there is no centralized form of authority. Decisions are made by stakeholders, usually determined by staking the DAO's token, through voting on decisions proposed to the DAO. All DAO activity is executed on-chain, providing a transparent and verifiable record of activity. 

- **Execution:** Execution in reference to the IC refers to the execution layer, which is responsible for executing canister smart contract code. Execution is done using WebAssembly (Wasm). 

- **ICP:** ICP is the native utility token of the Internet Computer. ICP can be used to participate in the network's governance through token staking, or it can be converted into cycles which can be used to pay for a canister's resource consumption. 

- **Principal:** A principal in regards to the IC is an entity that can be authenticated by the IC. 

- **Proposal:** A proposal is a statement that describes a suggested modification to an aspect of the IC or any of its subsystems. Proposals can be submitted for consideration from the IC community by eligible neuron owners. Once a proposal has been submitted, it undergoes a voting process that determines if the proposed modification will be adopted or rejected. 

- **Messages:** A message in regards to the IC is data sent from one canister to another canister, or data sent from a user to a canister. 

- **Replica:** On the Internet Computer, the replica is the collection of protocol components necessary for a node to participate in a subnet. 

- **Subnet:** On the Internet Computer, a subnet is a collection of nodes that operate an independent instance of the blockchain network by running their own implementation of the consensus algorithm. Subnets interact with other subnets through chain-key cryptography. 

- **Transaction:** A transaction refers to the ledger process of transferring ICP from one account to another. A transaction can be a regular transfer transaction, a minting transaction, or a burning transaction. 


## Canister terminology

- **Smart contracts:** Smart contracts are stateful programs that are designed to automatically execute, control, or document events and actions according to the configuration of the contract. On the Internet Computer, smart contracts are deployed in the form of a canister, which bundles together data and code. 

- **Canister:** A canister is a type of smart contract computational unit that bundle together both code and state. When developing a dapp on the IC, canister files are the primary place that code development happens. Canister types are split into three categories; backend canisters, frontend canisters, and custom canisters.

- **Canister account:** A canister account is a ledger account owned by a canister. 

- **Canister development kit (CDK):** A canister development kit (CDK) is an adapter used by the Internet Computer SDK that provides a programming language with the functionality and features necessary to create and manage canisters.

- **Canister identifier:** The canister identifier (ID) is a globally-unique identifier for a canister that can be used to interact with it.

- **Canister state:** A canister state refers to the entire state of a canister at any given point in time. The state is divided into two portions: the system state and the user state. The system state contains the auxiliary state maintained by the IC on behalf of the canister and contains information such as the balance of cycles, input and output queues, compute allocation, and other metadata. The user state contains a WebAssembly module instance. A canister interacts with its canister state either through the system API such as when sending messages, or implicitly such as when consuming cycles for execution. 

- **Controller:** A controller on the IC refers to the controller of an individual canister. A controller can be a person, organization (such as a DAO), or another canister that has administrative rights over a canister. Controllers are identified by their principal. Controllers are capable of functions such as upgrading the canister or deleting the canister. 

- **Identity:** In regards to canisters, an identity is a principal value used for access control of canisters. Identities can be set as controllers of canisters and can have cycles wallets associated with them. 

- **Query:** A query is a method used to execute operations on a canister. Queries are performed synchronously and are made to any node that hosts the canister. Consensus is not required to verify the result of the query. 

- **State change:** A state change refers to the result of any operation, function call, or transaction that changes the information stored within a canister. A simple example would be a function that makes an update call that removes a value from a list. The result of this function is a change to the canister's state. 

- **System canister:** System canisters are pre-installed canisters that perform specific tasks that are used to help maintain the Internet Computer. 

- **Wallet:** In regards to canisters, a wallet refers to the cycles wallet for a canister. The cycles wallet contains the cycles used to pay for the canister's resource consumption. 

## Tools and products 

- **dfx:** `dfx` is the primary tool used for creating, managing, and deploying canisters on the Internet Computer. `dfx` is a component of the IC SDK. 

- **Internet Identity:** Internet Identity is the Internet Computer's native authentication system. 

- **Ledger:** In reference to the IC, the ledger canister is a system canister used to store accounts and their corresponding transactions. 

- **Motoko:** Motoko is a programming language developed and designed for programming on the Internet Computer. It was designed specifically to support the IC's unique features and attributes, such as the actor model and smart contract compilation to WebAssembly. 

## Next steps

- [0.3: Developer environment setup](03-dev-env.md).

-->
