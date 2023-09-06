---

title: "Internet Computer Execution Layer"
abstract:
shareImage: /img/how-it-works/execution.jpg
slug: execution-layer
---
# 実行

実行レイヤーはICコアプロトコルスタックの最上位レイヤーです。
コンセンサスによって合意され、メッセージルーティングによってcanister キューに誘導されたメッセージを決定論的にスケジューリングして実行し、それによってすべてのノードで決定論的な方法でサブネットのステートを変更します。
サブネットのすべてのノードで同じメッセージシーケンスを実行することで、ラウンド終了後にサブネットの各ノードで同じ終了ステートが得られることが保証されます。

canister IC上のスマートコントラクトは、スマートコントラクトプログラムを表すWeb Assembly (Wasm) バイトコードと、そのステートを表すメモリページのセットで構成されます。
Wasmバイトコードは、canister をインストールまたは更新することで変更できます。
スマートコントラクトのステートは、canister スマートコントラクトでメッセージを実行するときに変更されます。
バイトコードとメモリページの両方、つまり、 のステート、canister canister
サブネットの各ノードが同じ ステートを保持し、ラウンドごとに各ノードで同じようにステートが遷移することを保証することが、複製されたステートマシンを実現する基盤であり、ブロックチェーンを非常にユニークなものにしているセキュリティと回復力の特性です。canister 

<figure>
<img src="/img/how-it-works/canister.png" alt="Structure of a canister" title="Structure of a canister" align="center" style="width:600px">
</figure>

## メッセージの複製実行

複製された実行はラウンド単位で進行します。
1つのICラウンドにおいて、メッセージルーティング層はcanister 入力キュー内のメッセージ（のサブセット）を実行するために実行層を1回呼び出します。
ラウンドのメッセージの実行に必要な労力（CPUcycles ）に応じて、キュー内のすべてのメッセージが実行されるか、ラウンドの上限（cycles ）に達し、メッセージの一部が将来のラウンドの実行に委ねられた状態でラウンドが終了します。

各メッセージの実行によって、canister のステートのメモリページが変更されたり（オペレーティングシステムの用語では「ダーティ」になったり）、同じサブネットや異なるサブネット上の他のcanisters への新しいメッセージが生成されたり、イングレスメッセージの場合に応答が生成されたりします。
メモリページへの変更は追跡され、対応するページに「ダーティ」としてのフラグが付けられるので、ステートを認証するときに処理することができます。

メッセージの実行がローカル・サブネットのcanister をターゲットにした新しいcanister メッセージの生成につながる場合、このメッセージはターゲットcanister の入力キューで実行されることによって直接キューに入れられ、同じラウンドまたは次のラウンドでスケジュールされます。新しいメッセージの生成とキューイングは完全に決定論的であるため、サブネットのすべてのノードでまったく同じように発生するので、このメッセージはコンセンサスを経る必要はありません。

他のサブネットをターゲットにした新しいメッセージは、ターゲット・クロス・サブネット・キュー（XNetキュー）に入れられ、ラウンドごとのステート認証の一部として、ラウンドの最後にサブネットによって認証されます。
受信サブネットは、発信サブネットの公開鍵で署名を検証することで、XNetメッセージがサブネットによって認証されていることを確認できます。

実行レイヤーは、複数のcanisters を異なる CPU コアで同時に実行するように設計されています。
これは、各canister がそれぞれ独立したステートを持っており、canister 通信が非同期であるために可能です。
このようなサブネット内での同時実行の形態と、IC のすべてのサブネットがcanisters を同時に実行する機能により、IC はパブリッククラウドのようにスケーラブルになります：ICはサブネットを追加することでスケールアウトします。

<figure>
<img src="/img/how-it-works/execution_layer.png" alt="Execution layer process consensus blocks and updates state" title="Execution layer process consensus blocks and updates state" align="center" style="width:400px">
</figure>

## 非複製メッセージ実行

非複製メッセージ実行、別名クエリーは、単一のノードによって実行され、命令型プログラミング言語の通常の関数呼び出しのように同期的に応答を返す操作です。
クエリーはサブネットの複製されたステートを変更できませんが、*アップデート*コールは変更できます。
クエリーは、その名前が示すように、基本的にサブネットの1つのレプリカで実行される読み取り操作であり、侵害されたレプリカが任意の結果を返すことができるという関連する信頼モデルがあります。

アップデートコールと同様に、クエリーはノード上の複数のスレッドによって同時に実行されます。
ただし、クエリーは複製された方法で実行されないため、サブネットのすべてのノードが異なるクエリーを同時に実行できます。
したがって、サブネットのクエリースループットはサブネット内のノード数が増加するにつれて直線的に増加しますが、アップデートコールのパフォーマンスはノード数が増加するにつれて低下します。

クエリーは、イーサリアム・ブロックチェーン上のローカルまたはクラウドのイーサリアム・ノードにおける読み取り操作に似ています。
 dApp クエリーは、クリティカルでない操作にのみ使用すべきです。
読み取るべき情報項目がクリティカルな場合、例えば、意思決定の根拠となる財務データなどでは、アップデート・コールを使用してそのような情報を取得すべきです。アップデート・コールの応答は、サブネットによってBLS閾値署名で認証され、サブネットの公開鍵で検証可能だからです。

## 決定論的時間スライシング

各実行ラウンドはブロックチェーン・ブロックの作成と並行して進行し、これはおよそ1秒に1回起こります。
。このため、1ラウンドで実行できる計算量は制限され、既存のノード・ハードウェアを考えると、現在の限界は約20億命令です。

しかし、Internet Computer 、200億命令まで必要とする長いタスクを処理することができ、コードのインストールのような特殊なタスクでは2000億命令まで処理することができます。
これは「決定論的時間スライシング」（DTS）と呼ばれる技術を使用して実現されています。
その結果、ブロック生成速度を低下させることなく、1つのタスクが複数のラウンドにまたがることができます。
DTSはスマートコントラクトに対して自動的かつ透過的であるため、開発者はこれを使用するために特別なコードを書く必要はありません。

## メモリ処理

canister バイトコードとステート（総称してメモリ）の管理は、実行レイヤーの重要な責務の1つです。
1つのサブネットが保持できる複製されたステートは、ノードマシンの利用可能なRAMによって制限されるのではなく、利用可能なSSDストレージによって制限されます。
しかし、利用可能なRAMはサブネットのパフォーマンス、特にメモリページのアクセスレイテンシに影響します。
しかし、これはワークロードのアクセスパターンに大きく依存します - 従来のコンピュータシステムと同様です。

ICを構成するノードマシンは、複製された大量のcanister ステートとWasmコードを保持し、メモリアクセス時に良好なパフォーマンスを達成できるよう、数十テラバイトのハイエンドSSDストレージと半テラバイト以上のRAMを搭載しています。
 canisters を実行する際に得られるステートは、メッセージルーティングのステート管理コンポーネントによって認証（つまりデジタル署名）されます。イングレス履歴や他のサブネットワークに送信されるメッセージなど、ステートの一部の認証はラウンドごとに行われます。サブネットワークのステート全体（そのサブネットワークがホストするすべてのcanisters のステートを含む）は、（はるかに長い）チェックポイント間隔ごとに1回認証されます。

canister ステートを表すメモリーページは、実行レイヤーによってSSDに永続化され、canister プログラマーがこれを処理する必要はありません。
すべてのメモリーページが透過的に永続化されることで、*直交永続*化が可能になり、スマートコントラクトのプログラマーは、他のブロックチェーンや従来のITシステムのように、ストレージからの読み出しやストレージへの書き込みから解放されます。
これにより、スマートコントラクトの実装が劇的に簡素化され、dApp のTCOを削減し、市場投入を早めることができます。
プログラマーは、canister スマートコントラクトの完全なステートを常にヒープまたは安定したメモリーに持つことができます。
その違いは、canister コードの更新時にヒープがクリアされるのに対し、安定したメモリは更新中も安定したままであることで、これがその名前の由来となっています。
ヒープ上のステートで、canister 更新によって保存されるものはすべて、更新前にプログラマがcanister 安定したメモリに転送し、更新後にそこから復元する必要があります。
ベストプラクティスとしては、アップグレードの前後で大容量のストレージをシャッフルするのを避けるため、大きなcanister ステートを安定したメモリに直接保持することです。
こうすることで、アップグレード操作で許容されるcycles の上限を超えるリスクも回避できます。

## Cycles 会計

canister の実行は、Internet Computer のリソースを消費します。 *cycles*.canisters 各canister はローカルのcycles アカウントを保持し、そのアカウントが十分なcycles を保持していることを保証するのは、開発者、開発者のグループ、または分散型自律組織（DAO）である可能性があるそのメンテナの責任です。
このリソース課金モデルは*リバースガスモデルとして*知られており、ICの大量導入の促進要因となっています。

技術的には、スマートコントラクトメッセージの実行命令をカウントするコードでWasmバイトコードがICにインストールまたは更新されると、canister で実行されているWasmコードがインスツルメンテーションされます。
これにより、特定のメッセージが実行された場合に課金される正確な金額（cycles ）を決定論的に決定することができます。
 canister のバイトコードフォーマットとしてWasmを使用することは、Wasm自体がその実行において大部分が決定論的であるフォーマットであるため、決定論に到達するのに大いに役立ちました。
 cycles チャージが完全に決定論的であることは、すべてのノードが与えられた操作に対してまったく同じ量のcycles をチャージし、サブネットの複製されたステートマシンの特性が維持されるようにするために極めて重要です。

canister がWasmコードとcanister ステートの両方で使用するメモリは、cycles で課金される必要があります。
パブリッククラウドと同様に、消費されたストレージは時間単位ごとに課金されます。
他のブロックチェーンと比較して、IC上にデータを保存するのは非常に安価です。
さらに、イングレスメッセージの受信、XNetメッセージの送信、Web 2.0サーバーへのHTTPS Outcallsなどのネットワーキング活動は、cycles でcanister 。

IC上のリソースの価格は非常に競争的です。
特定のリソース、たとえばWasm命令の実行の価格は、サブネットのレプリケーションfactor 、つまりサブネットを動かすノードの数によってスケールします。

<figure>
<img src="/img/how-it-works/execution_consumes_cycles.png" alt="Execution layer consumes cycles" title="Execution layer consumes cycles" align="center" style="width:600px">
</figure>

## 乱数生成

多くのアプリケーションでは、安全な乱数生成器が必要です。しかし、実行の一部としてナイーブな方法で乱数を生成することは、すべてのノードが異なる乱数を計算することになるため、決定論を破壊することになります。
ICは、実行層が*ランダムテープと*呼ばれる分散型擬似乱数生成器にアクセスすることで、この問題を解決します。ランダムテープは[連鎖鍵暗号を](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)使用して構築されます。毎ラウンド、サブネットワークは新鮮な閾値BLS署名を生成します。この署名はその性質上、予測不可能で一様に分布します。この署名は暗号擬似ランダム生成器のシードとして使用できます。これにより、canister スマートコントラクトは、Internet Computer のもう一つのユニークな特徴である、高効率で安全な乱数ソースにアクセスすることができます。

<!---


# Execution

The execution layer is the topmost layer of the IC core protocol stack.
It deterministically schedules and executes the messages that have been agreed on by consensus and inducted into the canister queues by message routing, thereby changing the state of the subnet in a deterministic manner on all the nodes.
The execution of the same sequence of messages on every node of the subnet guarantees that the same ending state is obtained on each node of the subnet after completion of the round.

A canister smart contract on the IC consists of a Web Assembly (Wasm) bytecode representing the smart contract program and a set of memory pages representing its state.
The Wasm bytecode can be modified by installing or updating the canister.
The smart contract state gets modified when executing messages on the canister smart contract.
Both the bytecode and the memory pages, i.e., the state, of the canister, are maintained by every node machine of the subnet the canister is installed on.
Each node in the subnet holding the same canister state and ensuring that the state transitions in the same way on every node in every round is the foundation of realizing a replicated state machine and the security and resilience properties thereof that make blockchains so unique.

<figure>
<img src="/img/how-it-works/canister.png" alt="Structure of a canister" title="Structure of a canister" align="center" style="width:600px">
</figure>

## Replicated Message Execution

Replicated execution proceeds in rounds.
In one IC round, the message routing layer invokes the execution layer once for executing (a subset of) the messages in the canister input queues.
Depending on how much effort (CPU cycles) the execution of the messages of a round requires, a round ends with all messages in the queues being executed or the cycles limit of the round being reached and parts of the messages left to future rounds for execution.

Each message execution can lead to memory pages of the canister's state being modified (becoming "dirty" in operating systems terminology), new messages to other canisters on the same or different subnets being created, or a response to be generated in case of an ingress message.
Changes to memory pages are tracked and corresponding pages flagged as "dirty" so that they can be processed when certifying the state.

When a message execution leads to the generation of a new canister message targeted at a canister in the local subnet, this message can be queued up directly by execution in the input queue of the target canister and scheduled in the same round or an upcoming round. This message does not need to go through consensus since the generation and enqueuing of the new message is completely deterministic and thus happens in exactly the same way on all the nodes of the subnet.

New messages targeted at other subnets are placed into the target cross-subnet queue (XNet queue) and are certified by the subnet at the end of the round as part of the per-round state certification.
The receiving subnet can verify that the XNet messages are authenticated by the subnet by validating the signature with the originating subnet's public key.

The execution layer is designed at its core to execute multiple canisters concurrently on different CPU cores.
This is possible because each canister has its own isolated state and canister communication is asynchronous.
This form of concurrent execution within a subnet together with the capability of all of the IC's subnets executing canisters concurrently makes the IC scalable like the public cloud: The IC scales out by adding more subnets.

<figure>
<img src="/img/how-it-works/execution_layer.png" alt="Execution layer process consensus blocks and updates state" title="Execution layer process consensus blocks and updates state" align="center" style="width:400px">
</figure>

## Non-replicated Message Execution

Non-replicated message execution, aka queries, are operations executed by a single node and return a response synchronously, much like a regular function invocation in an imperative programming language.
The key difference to messages, which are also called _update calls_, is that queries cannot change the replicated state of the subnet, while update calls can.
Queries are, as the name suggests, essentially read operations performed on one replica of the subnet, with the associated trust model of a compromised replica being able to return any arbitrary result of its choice.

Analogous to update calls, queries are executed concurrently by multiple threads on a node.
However, all the nodes of the subnet can concurrently execute different queries because queries are not executed in a replicated way.
Query throughput of a subnet thus increases linearly with an increasing number of nodes in the subnet, while the update call performance decreases with an increasing number of nodes.

Queries are similar to read operations on a local or cloud Ethereum node on the Ethereum blockchain.
A dApp should use queries for non-critical operations only.
Whenever an information item to be read is critical, e.g., financial data based on which decisions are made, update calls should be used to obtain such information as the response of an update call is certified by the subnet with a BLS threshold signature and verifiable with the subnet's public key.

## Deterministic Time Slicing

Each execution round progresses alongside the creation of blockchain blocks, which happens roughly once every second.
This restricts how much computation can be performed in a single round, with the current limit being around 2 billion instructions given the existing node hardware.

However, the Internet Computer can handle longer tasks that need up to 20 billion instructions, and some special tasks, like code installation, can even go up to 200 billion instructions.
This is acheived using a technique called "Deterministic Time Slicing" (DTS).
The idea is to pause a lengthy task at the end of one round and continue it in the next.
As a result, a task can span multiple rounds without slowing down the block creation rate.
DTS is automatic and transparent to smart contracts, so developers don't need to write any special code to use it.

## Memory Handling

Management of the canister bytecode and state (collectively memory) is one of the key responsibilities of the execution layer.
The replicated state that can be held by a single subnet is not bounded by the available RAM in the node machines, but rather by the available SSD storage.
Available RAM, however, impacts the performance of the subnet, particularly the access latency of memory pages.
This depends a lot on the access patterns of the workload, however, – much like in traditional computer systems.

The node machines that comprise the IC are equipped with tens of terabytes of high-end SSD storage and over half a terabyte of RAM to be able to hold large amounts of replicated canister state and Wasm code and achieve good performance when accessing memory.
The states obtained while executing canisters are certified (i.e. digitally signed) by the state management component of message routing. Certification of some parts of the states, including the ingress history and the messages that are sent to other subnetworks are certified every round. The entire state of a subnetwork, including the state of all canisters hosted by that subnetwork is certified once every (much longer) checkpointing interval.

Memory pages representing canister state are persisted to SSD by the execution layer, without canister programmers needing to take care of this.
Having all memory pages transparently persisted enables _orthogonal persistence_ and frees the smart contract programmers from reading from and writing to storage as on other blockchains or as in traditional IT systems.
This dramatically simplifies smart contract implementation and helps reduce the TCO of a dApp and go to market faster.
Programmers can always have the full canister smart contract state on the heap or in stable memory.
The difference is that the heap is cleared on updates of the canister code, while stable memory remains stable throughout updates, hence its name.
Any state on the heap that is to be preserved through a canister update must be transferred to stable memory by a canister programmer before an update and restored from there after the update.
Best practices are that large canister state be held directly in stable memory to avoid shuffling around large amounts of storage before and after each upgrade.
This also avoids the risk of exceeding the cycles limit allowed in an upgrade operation.

## Cycles Accounting

The execution of a canister consumes resources of the Internet Computer, which are paid for with _cycles_. Each canister holds a local cycles account and ensuring that the account holds sufficient cycles is the responsibility of its maintainer, which can be a developer, a group of developers or a decentralized autonomous organization (DAO) – users do never pay for sending messages to canisters on the IC.
This resource charging model is known as _reverse gas model_ and is a facilitator for mass adoption of the IC.

Technically, the Wasm code running in a canister gets instrumented, when the Wasm bytecode is installed or updated on the IC, with code that counts the executed instructions for smart contract messages.
This allows for deterministically determining the exact amount of cycles to be charged for a given message being executed.
Using Wasm as bytecode format for canister has helped greatly to reach determinism as Wasm itself is a format that is largely deterministic in its execution.
It is crucial that the cycles charging be completely deterministic so that every node charges exactly the same amount of cycles for a given operation and that the replicated state machine properties of the subnet are maintained.

The memory the canister uses in terms of both its Wasm code and canister state needs to be paid for with cycles as well.
Much like in the public cloud, consumed storage is charged for per time unit.
Compared to other blockchains, it is very inexpensive to store data on the IC.
Furthermore, networking activities such as receiving ingress messages, sending XNet messages, and making HTTPS Outcalls to Web 2.0 servers are paid for in cycles by the canister.

Pricing for a resource on the IC is extremely competitive.
Prices for a given resource, e.g., executing Wasm instructions, scale with the replication factor of the subnet, i.e., the number of nodes that power the subnet.

<figure>
<img src="/img/how-it-works/execution_consumes_cycles.png" alt="Execution layer consumes cycles" title="Execution layer consumes cycles" align="center" style="width:600px">
</figure>

## Random Number Generation

Many applications benefit from, or require a secure random number generator. Yet, generating random numbers in the naïve way as part of execution trivially destroys determinism as every node would compute different randomness.
The IC solves this problem by the execution layer having access to a decentralised pseudorandom number generator called the _random tape_. The random tape is built using [chain-key cryptography](https://internetcomputer.org/how-it-works/#Chain-key-cryptography). Every round, the subnetwork produces a fresh threshold BLS signature which, by its very nature, is unpredictable and uniformly distributed. This signature can then be used as seed in a cryptographic pseudorandom generator. This gives canister smart contracts access to a highly-efficient and secure random number source, which is another unique feature of the Internet Computer.

-->
