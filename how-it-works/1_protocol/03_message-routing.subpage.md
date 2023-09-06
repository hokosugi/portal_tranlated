---

title: Message Routing
abstract:
shareImage: /img/how-it-works/messaging-routing.jpg
slug: message-routing
---
# メッセージのルーティング

Internet Computer ブロックチェーンは、ユーザーがcanister スマートコントラクトにメッセージを送信し、canisters 自分自身の間でメッセージを送信することを可能にします。スケーラビリティのために、Internet Computer は多くのサブネットブロックチェーンで構成され、Internet Computer'のNetwork Nervous System は必要に応じて新しいサブネットを追加できます。メッセージ・ルーティング・コンポーネントは、Internet Computer'のすべてのサブネット・ブロックチェーンにわたってcanisters との間でメッセージをルーティングし、新しいサブネットをシームレスに追加できるようにします。

メッセージ・ルーティングは、プロトコル・スタックの2つの上位レイヤーのうち下位のレイヤーです。
ICの運用に不可欠な機能を実装しています。
その責務は、大まかに以下のようにグループ化できます：

- コンセンサスからブロックで受信したメッセージの誘導；
- 誘導成功後の実行レイヤーの呼び出し；
- サブネット内およびサブネット間で実行された結果のcanister メッセージのルーティング；
- サブネットのステートの認証；
- 新しく参加するノードや遅れて参加するノードに対するサブネットのステートの同期。

このレイヤーの名前はメッセージをルーティングする機能から由来していますが、上記のすべての機能はICにとって同様に重要であることに注意してください。
特に、ステート認証と同期は、ノードの再開を可能にするために連鎖進化技術で多用されます。

## メッセージ処理

コンセンサスがメッセージの確定ブロックを生成するたびに、つまり、サブネットのノードの少なくとも3分の2によって正しいとみなされ（公証され）、確定されたブロックが生成されるたびに、このブロックはメッセージルーティングに引き渡されます。
これは、プロトコルスタックの下層と上層の間の移行を意味します：下位の2つのレイヤーは、各ラウンドで、実行するメッセージのブロックについて、サブネット内のすべてのノード間で合意する責任があります。このブロックは、決定論的処理のために上位レイヤーに渡されます。これは、より具体的には、決定論的処理のさらなるオーケストレーションを引き継ぐメッセージ・ルーティングに渡すことを意味します。

メッセージ・ルーティングがメッセージのブロックを受信すると、ブロックはユーザーによって送信されたイングレス・メッセージとcanisters によって送信されたXNetメッセージの両方から構成されることを思い出してください。メッセージはブロックから抽出され、各メッセージはターゲットcanister の入力キューに入れられます。
このプロセスは*インダクションと*呼ばれ、すべてのキューはまとめて*インダクション・プールと*呼ばれます。
メッセージングの実際の実行はサンドボックス内で行われます。サンドボックスはcanister メッセージの実行を担当する仮想マシンと考えることができます。
メッセージのルーティングと実行は決定論的な方法でサブネットのステートを変更します、これはサブネットの複製されたステートマシンの特性を実現するために重要です。
メッセージの実行は、メッセージが実行されたcanister のメモリページに書き込み、ステート内の他のメタデータを変更することができます。
 canisters
このようなメッセージは、ローカル・サブネット上の をターゲットにすることも、別のサブネットをターゲットにすることもできます。 前者の場合、実行によって新しいメッセージをターゲット の入力キューに直接入れることができます、後者の場合、つまり別のサブネットをターゲットにした新しいメッセージの場合、メッセージはターゲットサブネットのいわゆるXNetストリームに入れられ、ストリームが認証された後、ターゲットサブネットのブロックメーカーによってピックアップされます。canister
 canister

## Canister 間メッセージング

上述したように、canister メッセージの実行は、ローカルまたはリモート（異なるサブネット上）canister に送信される新しいインターcanister メッセージの作成につながる可能性があります。
インターcanister メッセージングがどのように機能するか、さらに詳しく見ていきましょう。

### サブネット内インターCanister メッセージング

実行中のcanister メソッドから発信されるサブネット内、つまりローカルなインターcanister メッセージは、以前のコンセンサスラウンドで合意されたメッセージから決定論的に生成されるため、コンセンサスを経る必要はなく、それ以降の実行は完全に決定論的なままです。
これは過渡的に成立します。つまり、インターcanister メッセージは、新しいインターcanister メッセージを生成することができ、その結果、メッセージのツリーが生成されます。
ローカルメッセージの呼び出しは、そのラウンドのcycles 制限をまだ使い果たしていない限り実行できます。cycles 制限を使い切ってもまだローカルメッセージが残っている場合、それらはサブネット内メッセージと同じように処理されます。
このローカルcanister-tocanister メッセージングは、EVMベースのブロックチェーンで慣れ親しまれているような*同期*メッセージ呼び出し*ではない*ことに注意することが重要です。
むしろ、ローカルメッセージはターゲットcanister の入力キューに入れられ、非同期で実行のためにスケジューリングされます。
これはInternet Computer で知られている標準的なインターcanister メッセージング・セマンティクスです。

### サブネット間Canister メッセージング

リモート・サブネット間canister メッセージ、つまり他のサブネット上のcanisters に送信されたメッセージは、ターゲット・サブネットのそれぞれの送信*サブネット・ストリームに*ルーティングされることで処理されます。このルーティングは、決定論的実行cycle の最後に行われます、
ストリーム内のXNetメッセージは、ラウンドごとのステート認証の一部として、BLSしきい値暗号を使用するサブネットによって、ラウンドの最後にメルクル木形式のデータ表現を使用して認証（署名）されます。
受信側サブネットのレプリカは、ブロック作成中（コンセンサスの一部）にXNetメッセージを取得し、閾値署名を検証して、有効なXNetメッセージをコンセンサスブロックに含めます。
XNetストリームのエンコードと認証にメルクル木のようなデータ構造を使用するおかげで、受信側サブネットがラウンドでストリームの一部を消費しても、署名を検証することができます。

## ステート認証

サブネットの複製されたステートには、サブネットの運用に必要なすべての関連情報が含まれます：

- ラウンドごとに認証される項目
  - イングレス・メッセージへの応答
  - 他のサブネットに送信されるXnetメッセージ
  - Canister メタデータ（モジュール・ハッシュ、*認証済み変数）*
- チェックポイントごとに認証される項目：
  - レプリケートされたステート全体

認証は常に、サブネットによってまとめて計算されたBLSの閾値署名を使用して行われます。したがって、認証はサブネット全体によって分散化された方法で計算されます。閾値署名の特性は、サブネットの大多数がステートに同意している場合にのみ、このような認証が存在できることを保証します。

ステート認証とセキュアなXNetメッセージングにより、特に、複数のシャードを持つブロックチェーンが苦労する課題である、サブネットの境界を越えたcanisters 、セキュアで透過的な通信が可能になります。また、ユーザーがレプリケートされたステートの認証された部分、例えばユーザーが送信したメッセージに対するレスポンスを読むことを可能にする重要なビルディングブロックも提供します。さらに、ノードがサブネットの最新のステートに追いつくために、生成以降のすべてのブロックを再生したり、遅れているノードに追いついたりすることなく、効率的にサブネットに参加することができます。このように、メッセージ・ルーティングはICプロトコルのコア・レイヤとして、ICのユニークで特徴的な機能の実現に不可欠です。

### ラウンドごとの認証

ラウンドの終了時、つまり、すべてのメッセージが実行されたとき、または（ラウンドが恣意的に長くならないように）ラウンドのcycles 制限に達したとき、メッセージルーティング層は複製されたステートの一部の認証を実行します。
BLSの閾値署名が計算され、ステートツリーの以下の部分が認証されます。

- イングレス・メッセージへの応答、
- 他のサブネットに送信されるXnetメッセージ、および
- Canister メタデータ（モジュール・ハッシュ、*認証済み変数*）。

イングレス・メッセージに対するレスポンスは、しばしば*イングレス履歴と*呼ばれます。
。認証されたレスポンスは、イングレス・メッセージに対するレスポンスとして、ユーザーによってサブネットの公開鍵に対して読み取られ、検証されます。個々のサブネットの公開鍵は、同じメカニズムを使ってNNSによって認証されます。
ブロックチェーンへのステート変更メッセージに対するレスポンスを検証するこの方法は、トランザクションログからレスポンスを読み取るようなこの分野で見られる他のアプローチと比較すると、非常に強力です。

ラウンドごとのステート認証は、Internet Computer 上のユーザーとサブネット、および異なるサブネット間の相互作用に関連するあらゆるデータ項目が認証されることを保証します。
これは特に、Internet Computer の重要な特徴であり、スケーラビリティの実現要因でもある、安全で検証可能なサブネット間通信を可能にします。

### チェックポイントごとの認証

canister の更新によって変更されたワズム・コードや、canisters の書き込み済み（「ダーティ」）メモリ・ページ、およびレプリケートされたステート内のその他のメタデータは、すべてのラウンドで認証されるわけではありません。その代わり、いわゆる*チェックポイントが*作成されるたびに認証されます。チェックポイントとは、複製されたステートのコピーをディスクに永続化したものです。
このようなチェックポイントは数百ラウンド（または約10分）ごとに書き込まれ、チェックポイントごとにサブネットも認証を計算します。
ステート認証は、前回のチェックポイント認証以降の変更を前回のチェックポイントのマニフェストと呼ばれるものに取り込むことでインクリメンタルに行われます。マニフェストは抽象的には比較的平坦なメルクル木と見なすことができ、変更されたリーフを更新し、変更をツリー上に伝播させることでインクリメンタルな計算を実現できます。
最後に、マニフェストのルートハッシュはサブネットによってBLS閾値署名で署名され、それによってマニフェストの内容全体が認証されます。
署名された結果は、チェックポイントが行われた時点に効率的に追いつくためにノードが使用できるため、*キャッチアップパッケージと*呼ばれます。(
この認証操作の実行時間は、サブネット上の全体的なステートサイズではなく、変更されたメモリページ数に対して線形です。
これは、サブネットが将来的にテラバイトのステートを保持する可能性があり、レプリケートされた複数テラバイトのステートの完全な再認証をチェックポイント間隔ごとに行うことは現実的ではないため、非常に重要です。

## ステートの同期

メッセージルーティング層は、Internet Computer に非常にユニークな別の機能を実装しています。
上述のように、チェックポイントのたびに、サブネットのステート全体が、メルクル木のような構造（マニフェスト）上のBLSしきい値署名を通じてサブネットによって認証され、キャッチアップパッケージの一部として利用できるようになります。
名前がすでに示唆しているように、キャッチアップパッケージは、ノードがしばらくの間ダウンしていたなどの理由で遅れをとっていた場合に、そのノードのキャッチアップを可能にします。さらに、新しいノードを参加させることもできます、
このようなノードは最新のキャッチアップパッケージをダウンロードし、その署名をパブリックサブネットキーで検証することができます。
検証後、新しいノードはチェックポイントに対応するステートをダウンロードできます。
ステートのダウンロードには、ノードのピアから大量の（ギガバイトからテラバイト）データを転送する必要があります。
これは、ステートをチャンク化し、異なるチャンクを異なるピアからダウンロードできるようにするプロトコルを使用することで、すべてのピアから効率的かつ並列に行われます。
技術的には、P2Pレイヤーのアーティファクト転送プロトコルがステートの転送に使用されます。
すべてのチャンクは、キャッチアップパッケージを介して、そのハッシュによって個別に認証されます。マニフェストのツリー状の構造により、キャッチアップパッケージ内のルートハッシュに対して、これらのチャンクのそれぞれを個別に検証することができます。
チャンキングプロトコルは、Bittorrent が多数のピアから大きなファイルをダウンロードするために使用するアプローチと似ていません。

この場合、ローカルのチェックポイントと異なるチャンクのみをダウンロードする必要があるため、転送するデータ量を大幅に削減することができます。フェッチされる新しいステートとローカルで利用可能なステート間の差分を効率的に計算するには、マニフェストの Merkle-tree ライクな構造を利用します。つまり、マニフェストを差分して、異なるステートのチャンクを見つけることができます。

チェックポイントに対応する完全なステートが認証的にダウンロードされると、ノードはチェックポイント以降にサブネットで生成されたすべてのブロックを処理し、それらを「再生」、つまり通常のノード動作時と同じように実行することで、現在のブロックの高さに追いつきます。

他のブロックチェーンで行われているように、サブネット上で最初に作成されたブロックからすべてのブロックを再生する必要があります。
最新のチェックポイントをダウンロードできるステート同期プロトコルのおかげで、ブロックチェーンの最初からすべてのブロックを再生するのとは対照的に、再生する必要があるブロックはわずかです。
これが重要な理由は、ICがパブリッククラウドを置き換えることを意図しているからです、
高いCPU使用率で複数年稼働しているサブネットを考えてみましょう。
この場合、新しくサブネットに参加したノードが、サブネットの創始ブロックから始まるすべてのブロックを再生しようとすると、複数CPU年分の計算をやり直さなければならないため、サブネットに追いつくことは不可能になります。
このように、ステート同期は、ノードが故障して交換が必要になるような実世界の条件下で正常に動作することを望むブロックチェーンにとって必要な機能です。

## さらに深く

メッセージ・ルーティング層の詳細については、[ウィキのページを](https://wiki.internetcomputer.org/wiki/IC_message_routing_layer)ご覧ください。

[![Watch youtube video](https://img.youtube.com/vi/YexfeByBXlo/0.jpg)](https://www.youtube.com/watch?v=YexfeByBXlo)

<!---


# Message Routing

The Internet Computer blockchain enables users to send messages to canister smart contracts and canisters to send messages between themselves. For scalability, the Internet Computer is composed of many subnet blockchains and the Internet Computer's Network Nervous System can add new subnets as required. The message routing component routes messages to and from canisters across all of the Internet Computer's subnet blockchains and ensures that new subnets can be added seamlessly.

Message routing is the lower of the two upper layers of the protocol stack.
It implements functionality that is crucial for the operation of the IC.
Its responsibilities can be roughly grouped as follows:

- Induction of messages received in blocks from consensus;
- Invocation of the execution layer after successful induction;
- Routing of inter-canister messages resulting from execution within and between subnets;
- Certification of the state of the subnet;
- Synchronization of the state of the subnet to newly joining and fallen behind nodes.

Note that, although the layer derives its name from the functionality of routing messages, all the functionality listed above is equally important for the IC.
Particularly, state certification and synchronization are heavily used in chain-evolution technology to enable resumption of nodes.

## Message Processing

Whenever consensus produces a finalized block of messages, that is, a block that has been considered correct (notarized) and finalized by at least two thirds of the subnet's nodes, this block is handed over to message routing.
This marks the transition between the lower and upper half of the protocol stack: The lower two layers are responsible for agreeing, in each round, among all nodes in the subnet on a block of messages to be executed. This block is then handed over to the upper layers for deterministic processing, which, more concretely, means passing it over to message routing which takes over the further orchestration of deterministic processing.

Once message routing receives a block of messages – recall that a block comprises both ingress messages submitted by users and XNet messages sent by canisters – the messages are extracted from the block and each message is placed into the input queue of its target canister.
This process is called _induction_ and all the queues are collectively referred to as _induction pool_.
After induction, the execution layer – the topmost layer of the core IC protocol stack – is triggered to deterministically _schedule_ messages in the induction pool for execution and to execute them.
The actual execution of messaging happens inside a sandbox, which can be thought of as a virtual machine responsible for the execution of canister messages.
Message routing and execution modify the subnet state in a deterministic way, i.e., the state of the node is changed in the same way on every (honest) node of the subnet, which is crucial for achieving the replicated state machine properties of a subnet.
The execution of a message can write to memory pages of the canister the message is executed on and change other metadata in the state.
The execution of a message can also lead to the creation of new messages targeted at other canisters.
Such a message can be either targeted at a canister on the local subnet or another subnet.
In the former case, execution can directly place the new message into the input queue of the target canister.
In the latter case, i.e., a new message that is targeted at another subnet, the message is placed into the so-called XNet stream for the target subnet where they can be picked up by block makers of the target subnets after the streams are certified.

## Inter-Canister Messaging

As mentioned above, the execution of a canister message can lead to the creation of a new inter-canister message sent to a local or remote (on a different subnet) canister.
Let us go deeper into how inter-canister messaging works.

### Intra-Subnet Inter-Canister Messaging

Intra-subnet, i.e., local, inter-canister messages originating from an executing canister method do not need to go through consensus as they deterministically result from messages that have been agreed by a previous consensus round and their further execution remains completely deterministic.
This holds transitively, that is, inter-canister messages can create new inter-canister messages, resulting in a tree of messages.
Local message invocations can be executed as long as the cycles limit for the round has not yet been exhausted. If the cycles limit is exhausted but there are still local messages left, they will be handled in the same way as intra-subnet messages.
It is important to note that this local canister-to-canister messaging is _not synchronous_ message invocation as one might be used to from EVM-based blockchains.
Rather, local messages are put into the input queue of the target canister and are scheduled for execution asynchronously.
This is the standard inter-canister messaging semantics known for the Internet Computer.

### Inter-Subnet Inter-Canister Messaging

Remote inter-canister messages, that is, messages sent to canisters on other subnets, are handled by routing them into the respective outgoing _subnet stream_ for the target subnet. This routing happens at the end of the deterministic execution cycle, i.e., after execution hands back control to message routing.
The XNet messages in the stream are certified (signed) using a Merkle-tree-style data representation at the end of the round by the subnet using BLS threshold cryptography as part of the per-round state certification.
That is, every message in the outgoing stream is certified by the originating subnet.
Replicas on the receiving subnet obtain the XNet messages during block making (part of consensus), validate the threshold signature, and include valid XNet messages in a consensus block.
Thanks to using a Merkle-tree-like datastructure to encode and authenticate the XNet streams, parts of the streams can be consumed in a round by the receiving subnets and signatures can still be validated.

## State Certification

The replicated state of a subnet comprises all the relevant information required for the operation of the subnet:

- Items certified per round:
  - Responses to ingress messages
  - Xnet messages to be sent to other subnets
  - Canister metadata (module hashes, _certified variables_)
- Items certified per checkpoint:
  - The entire replicated state

Certification is always done using BLS threshold signatures computed collectively by the subnet, thus certifications are computed by the subnet as a whole in a decentralized manner. The properties of the threshold signature guarantee that such a certification can only exist if the majority of the subnet agrees on the state.

State certification and secure XNet messaging enable, among others, the secure and transparent communication of canisters across subnet boundaries, a challenge that any blockchain that has multiple shards struggles with. It also provides crucial building blocks to allow users to read certified parts of the replicated state, e.g., responses to messages submitted by them. Furthermore, it allows nodes to join a subnet efficiently without replaying all blocks since genesis or fallen behind nodes to catch up to the most recent state of a subnet. All of this makes message routing an integral layer of the core IC protocol crucial for realizing some of the IC's unique and distinguishing features.

### Per-Round Certification

At the end of a round, i.e., when all messages have been executed or the cycles limit for the round has been reached (to ensure rounds cannot take arbitrarily long), the message routing layer performs a certification of parts of the replicated state.
A BLS threshold signature is computed to certify the part of the state tree containing

- Responses to ingress messages,
- Xnet messages to be sent to other subnets, and
- Canister metadata (module hashes, _certified variables_).

The responses to ingress messages are often referred to as _ingress history_.
The certified responses can be read and validated against the subnet's public key by users as the response to their ingress messages. Each of the public keys of the individual subnets are, in turn, certified by the NNS using the same mechanism. This means that one can verify that certified responses indeed come from the IC only using the public key of the NNS.
This way of validating responses to state-changing messages to a blockchain is extremely powerful when compared to other approaches seen in the field like reading the response from a transaction log.

The per-round state certification ensures that any item of data relevant for interactions of users and subnets and between different subnets on the Internet Computer is authenticated.
This particularly enables secure and verifiable inter-subnet communication, a crucial feature of the Internet Computer as well as an enabler of its scalability.

### Per-Checkpoint Certification

Wasm code changed through canister updates and written-to ("dirty") memory pages of canisters and some other metadata in the replicated state do not get certified in every round. Instead they are only certified whenever a so-called _checkpoint_ is created. A checkpoint is a copy of the replicated state that is persisted to disk.
Such a checkpoint is written every multiple hundred rounds (or around 10 minutes), and for each checkpoint the subnet also computes a certification. This allows newly joining and fallen behind nodes to join in without re-executing all blocks.
The state certification is done incrementally by incorporating the changes since the last checkpoint certification into the so called manifest of the previous checkpoint. The manifest can abstractly be viewed as a relatively flat Merkle tree and the incremental computation can be achieved by updating the leaves that have changed and propagating changes up the tree.
Finally, the root hash of the manifest is signed with a BLS threshold signature by the subnet, thereby certifying the entire contents of the manifest.
The signed result is called a _catch-up package_ as it can be used by nodes to efficiently catch up to the point in time when the checkpoint was made. (Note that a catch-up package also contains other things required to resume, which are omitted here for the sake of simplicity).
The run time of this certification operation is linear in the number of memory pages that have changed and not the overall state size on the subnet.
This is crucial as a subnet can hold terabytes of state in the future and a full recertification of multiple terabytes of replicated state would not be practical at every checkpoint interval.

## State Synchronization

The message routing layer implements another feature quite unique to the Internet Computer.
As described above, on every checkpointing the entire subnet state is certified by the subnet through a BLS threshold signature on a Merkle-tree-like structure – the manifest – and made available as part of a catch-up package.
As the name already suggests, a catch-up package allows a node to catch-up if it has fallen behind, e.g., because it was down for some time. In addition, it allows new nodes to join, e.g., if the subnet is to grow in size or a node needs to be replaced because of having been destroyed in a disaster.
Such a node can download the latest catch-up package and validate its signature with the public subnet key.
Once verified, the new node can download the state corresponding to the checkpoint.
The downloading of the state requires the transfer of large amounts (gigabytes to terabytes) of data from the nodes's peers.
This is done efficiently and in parallel from all peers, by using a protocol that chunks the state and allows for different chunks to be downloaded from different peers.
Technically, the artifact transfer protocol of the P2P layer is used for transferring the state.
Every chunk is authenticated through the catch-up package individually through its hash. The tree-like structure of the manifest allows to verify each of these chunks individually relative to the root hash in the catch-up package.
The chunking protocol is not dissimilar to the approach that Bittorrent uses for downloading large files from many peers.

If a node is not newly added, but only had a downtime and needs to catch up, it may still have an older checkpoint.
In this case, only the chunks different to the local checkpoint need to be downloaded, which can significantly reduce the to-be-transferred data volume. Efficiently computing the diff between a new state that is to be fetched and the locally available state can again make use of the Merkle-tree-like structure of the manifest. That is, one can diff the manifest to find out the chunks of the state that differ.

Once the full state corresponding to the checkpoint has been authentically downloaded, the node catches up to the current block height by processing all the blocks that have been generated in the subnet since the checkpoint and "replaying" them, i.e., executing them as it would during normal node operation, to successively make state transitions of its local state to finally reach the most recent one of the subnet.

Note that without state synchronization it might practically not be possible for nodes to (re-)join in a "busy" subnet: they would need to replay all blocks from the very first block ever created on the subnet as it is done in other blockchains.
Thanks to the state sync protocol allowing to download recent checkpoints, only few blocks need to be replayed as opposed to replaying every block from the start of the blockchain.
The reason why this is important is that the IC is intended to replace public cloud, i.e., to have a high throughput of operations per time unit, much like real-world cloud servers running their applications.
Consider a subnet that has been running for multiple years with high CPU utilization.
This would make it infeasible for a newly joining node to catch up with the subnet when trying to replay all blocks starting with the genesis block of the subnet as it would have to redo multiple CPU years worth of computation.
Thus, state synchronization is a necessary feature for a blockchain that wants to operate successfully under real-world conditions where nodes do fail and need replacement.

## Go even deeper

Check out the [wiki page](https://wiki.internetcomputer.org/wiki/IC_message_routing_layer) describing the message routing layer in more detail.

[![Watch youtube video](https://img.youtube.com/vi/YexfeByBXlo/0.jpg)](https://www.youtube.com/watch?v=YexfeByBXlo)

-->
