---

title: Fault tolerance
abstract:
shareImage: /img/how-it-works/fault-tolerance.jpg
slug: fault-tolerance
---
# フォールト・トレランス

ハードウェアの故障、バグ、停電、あるいは攻撃によるもの：個々のノードはいつでも故障する可能性があります。Internet Computer はフォールトトレラントな設計になっています。つまり、一部のノードが故障したり誤動作したりしても、ネットワークは前進します。

## ノード障害の処理

各ラウンドでは、コンセンサスレイヤーによってブロックが提案され、ブロック内のメッセージは実行レイヤーによって処理されます。サブネットが進行するためには、提案されたブロックとその結果のステートに、サブネット内の少なくとも2/3のノードが署名する必要があります。サブネット内のノードの1/3未満が失敗または誤動作する限り、たとえ任意のビザンチンな方法であっても、サブネットは進行を続けます。

サブネット内のノードの1/3未満が故障し、サブネットの残りのノードが前進を続けるというシナリオを考えてみましょう。ここでは、障害が発生したノードが自動的に回復し、正常に動作している他のノードに追いつく方法について説明します。新しく参加したノードも同じプロセスを使用して、サブネットの既存のノードに追いつきます。

ここで1つの自然な解決策を紹介します。障害が発生したノードや新しく参加したノードは、ピアから逃したコンセンサスブロックをすべてダウンロードし、各ブロックを1つずつ処理することができます。残念ながら、新しいノードは、サブネットの生成からすべてのブロックを処理しなければならない場合、追いつくのに長い時間がかかります。もう1つの解決策は、障害が発生したノードや新しく参加したノードに、ピアから最新のステートを直接同期させることです。しかし、ピアは新しいブロックを処理するたびにステートを更新し続けます。ピアがステートを更新している間に最新のステートを同期すると、不整合が発生する可能性があります。

ICPはこの2つのアプローチをミックスしています。コンセンサスプロトコルは*エポックに*分割されます。各エポックは、数百のコンセンサスラウンドで構成されます。各エポックの最初に、すべてのノードはブロックチェーンのステートのバックアップコピーを作成し、*キャッチアップパッケージ（CUP*）を作成します。高さ*h*の CUP には、高さ*h* からコンセンサスを再開するために必要なすべての関連情報が含まれています。その後、正常に動作している各ノードがCUPをブロードキャストします。

サブネット内のすべてのノードは、ピアからブロードキャストされたCUPメッセージを聞きます。あるノードが、受信したCUPに有効な署名があり（サブネット内のノードの少なくとも2/3によって署名されている）、ローカルで利用可能なステートハッシュとは異なるブロックチェーンステートハッシュがあることを観測したとします。その後、ノードは[ステート同期プロトコルを](https://www.youtube.com/watch?v=WaNJINjGleg)開始して、その高さ（CUPが発行された高さ）でブロックチェーンのステートを同期します。ブロックチェーンのステートはメルクルツリーとして構成され、現在のところ最大半テラバイトのサイズに達することができます。同期ノードはブロックチェーンのステートのほとんどをすでに持っている可能性があり、すべてをダウンロードする必要はないかもしれません。そのため、同期ノードはピアのブロックチェーンのステートのうち、自分のローカルステートと異なるサブツリーだけをダウンロードしようとします。同期ノードはまず、ブロックチェーン・ステートのルートの子を要求します。その後、同期ノードは自分のローカルステートと異なるサブツリーを再帰的にダウンロードします。

<figure>
<img src="/img/how-it-works/state-sync.png" alt="Catching-up replica syncing state from up-to-date replica" title="Catching-up replica syncing state from up-to-date replica" align="center" style="width:700px">
<figcaption align="left">
The catching-up replica only syncs the parts of the replicated state that differ from the up-to-date replica
</figcaption>
</figure>

障害が発生したノードや新しく参加したノードがブロックチェーンのステートを同期している間、正常に機能しているノードは新しいブロックを処理し続け、進捗を確認します。正常に機能しているノードは、ブロックチェーンのステートのバックアップコピー（CUPと同時に作成）を使用して、ステートを同期ノードに提供します。同期ノードはブロックチェーンのステートを同期し終わると、CUP以降に生成されたコンセンサスブロックを要求し、ブロックを1つずつ処理します。完全に同期されると、そのノードは他のノードと同様に定期的にメッセージを処理できるようになります。

## 定期的なサブネット障害の処理

まれに、サブネット全体がスタックして処理が進まないことがあります。非決定論的な実行につながるソフトウェアのバグなど、さまざまな理由でサブネットが失敗することがあります。また、サブネット内の1/3以上のノードが同時に故障した場合にも起こります。この場合、正常に機能しているノードがキャッチアップ・パッケージ（CUP）の作成と署名に失敗するため、障害が発生したノードは自動的に回復できません。

サブネットに障害が発生すると、復旧には手動による介入が必要になります。簡単に言うと、サブネット・ノードがCUPの自動作成と署名に失敗すると、誰かが手動でCUPを作成する必要があります。CUPは、サブネット内のノードの少なくとも2/3によってステートが認証される最大のブロックチェーンの高さで作成する必要があります。サブネットのノードは当然、セキュリティ上の理由から手動で作成されたCUPを信用できません。したがって、CUPが有効であるというコミュニティのコンセンサスが必要です。Internet Computer 、ブロックチェーンのガバナンス・システムとして [Network Nervous System](/how-it-works/#Network-Nervous-System)(NNS）があります。作成されたCUPをサブネットで使用するには、NNSに手動で提案を提出する必要があります。ICPをステークした人は誰でもその提案に投票できます。投票者の過半数が提案を受け入れれば、CUPはNNSレジストリに格納されます。

各ノードは(1)レプリカと(2)オーケストレータの2つのプロセスを実行します。レプリカはブロックチェーンを維持する4層のソフトウェアスタックで構成されています。オーケストレーターはレプリカソフトウェアをダウンロードして管理します。オーケストレーターは定期的にNNSレジストリに更新を問い合わせます。オーケストレータがレジストリで新しいCUPを観測した場合、オーケストレータは新しく作成されたCUPを入力としてレプリカ・プロセスを再起動します。前述のように、高さ*h*の CUP は、高さ*h* からのコンセンサスの再開に関連する情報を持っています。レプリカが起動すると、CUPのブロックチェーン・ステート・ハッシュがローカルのステート・ハッシュと異なることを確認した場合、*ステート同期プロトコルを*開始します。ステートが同期されると、レプリカはコンセンサスブロックの処理を再開します。

この回復プロセスはNNSにプロポーザルを提出する必要があるため、通常のサブネット（NNSのサブネットではない）を回復する場合にのみ機能することに注意してください。サブネットを回復するこのプロセスは、多くのInternet Computer ドキュメントで*災害復旧と*呼ばれています。

## NNScanister 障害の処理

Internet Computer には [Network Nervous System](/how-it-works/#Network-Nervous-System)(NNS)と呼ばれる特別なサブネットがあり、Internet Computer 全体を管理するcanisters をたくさんホストしています。これには、ルートcanister 、ガバナンスcanister 、台帳canister 、レジストリcanister などが含まれます。

NNSのサブネットが前進を続けている間に、NNSのcanister に障害が発生したとします。これは、canisterのコードにソフトウェアのバグがある可能性があります。この場合、canister を「アップグレード」する必要があります。つまり、canister を新しいウェブアセンブリ（WASM）コードで再起動する必要があります。一般的に言って、Internet Computer の各canister には、「コントローラ」のリストがあります。コントローラは、サブネットの管理canister にリクエストを送信することで、canisterのWASMコードをアップグレードする権利を持っています。ライフラインcanister は、ルートcanister のコントローラとして割り当てられます。ルートcanister は、他のすべてのNNScanisters のコントローラとして割り当てられています。ルートcanister は、他のNNScanisters （管理canister を呼び出して）をアップグレードするメソッドを持っています。同様に、ライフラインcanister はルートcanister をアップグレードするメソッドを持っています (管理canister を呼び出すことで)。

ガバナンスcanister が機能しているとします。そして、失敗したcanister をアップグレードするために、ルート/ライフラインcanisterのメソッドを呼び出す NNS 提案を手動で提出することができます。ICPを張った人は誰でもこの提案に投票できます。投票者の過半数が承認すれば、失敗したcanister はアップグレードされます。

## NNSサブネット障害の処理

最悪の場合、NNSサブネット全体が立ち往生して進展しない可能性があります。このような場合、NNSサブネットにノードを提供したすべてのノード・プロバイダが手動で介入し、CUPを作成して、新しいCUPでノードを再起動する必要があります。

[![Watch youtube video](https://i.ytimg.com/vi/H7HCqonSMFU/maxresdefault.jpg)](https://www.youtube.com/watch?v=H7HCqonSMFU)

[![Watch youtube video](https://i.ytimg.com/vi/WaNJINjGleg/maxresdefault.jpg)](https://www.youtube.com/watch?v=WaNJINjGleg)

<!---


# Fault tolerance

Whether due to hardware failure, bugs, power outages, or even attacks: Individual nodes may fail at any time. The Internet Computer is designed to be fault tolerant, which means that the network will make progress even if some nodes fail or misbehave.

## Handling node failures

In each round a block is proposed by the consensus layer and the messages in the block are processed subsequently by the execution layer. The proposed block and the resulting state need to be signed by at least 2/3rd of the nodes in the subnet in order for the subnet to make progress. As long as less than 1/3rd of the nodes in a subnet fail or misbehave, even in an arbitrary, Byzantine manner, the subnet will continue making progress.

Let us consider a scenario where less than 1/3rd of the nodes in a subnet fail while the remaining nodes of the subnet continue to make progress. We will now describe how a failed node can recover automatically and catch up with the other normally-operating nodes. A newly joined node also uses the same process to catch up with the existing nodes in the subnet.

Here’s one natural solution. A failed or newly joined node could download all the consensus blocks it missed from its peers, and process each block one by one. Unfortunately, new nodes will take a long time to catch up if they have to process all the blocks from subnet genesis. Another solution is to let the failed or newly joined node directly sync the latest state from its peers. However, the peers are continuously updating their state as they process new blocks. Syncing the latest state while the peers are updating it could lead to inconsistencies.

ICP follows a mix of both the approaches. The consensus protocol is divided into _epochs_. Each epoch comprises a few hundred consensus rounds. At the beginning of each epoch, all the nodes make a backup copy of their blockchain state, and create a _catch-up package (CUP)_. The CUP at height _h_ contains all relevant information required for consensus to resume from height _h_. This includes the hash of the blockchain state after processing the block at height _h_. The CUP is then signed by at least 2/3rd of the nodes in the subnet. Each normally-operating node then broadcasts the CUP.

All the nodes in the subnet listen to the CUP messages broadcast by their peers. Suppose a node observes that a received CUP has a valid signature (signed by at least 2/3 of the nodes in the subnet) and has a different blockchain state hash than the locally available state hash. Then the node initiates the [state sync protocol](https://www.youtube.com/watch?v=WaNJINjGleg) to sync the blockchain state at that height (the height at which the CUP is published). The blockchain state is organized as a Merkle tree and can currently reach a size of up to half a terabyte. The syncing node might already have most of the blockchain state and may not need to download everything. Therefore, the syncing node tries to download only the subtrees of the peers’ blockchain state that differ from its local state. The syncing node first requests for the children of the root of the blockchain state. The syncing node then recursively downloads the subtrees that differ from its local state.

<figure>
<img src="/img/how-it-works/state-sync.png" alt="Catching-up replica syncing state from up-to-date replica" title="Catching-up replica syncing state from up-to-date replica" align="center" style="width:700px">
<figcaption align="left">
The catching-up replica only syncs the parts of the replicated state that differ from the up-to-date replica
</figcaption>
</figure>

Note that while the failed/newly joined nodes are syncing the blockchain state, the well-functioning nodes continue to process new blocks and make progress. The well-functioning nodes use their backup copy of the blockchain state (created at the same time as the CUP) to supply the state to syncing nodes. After the syncing node finishes syncing the blockchain state, it will request the consensus blocks generated since the CUP and process the blocks one by one. Once fully synced, the node can then process messages regularly like the other nodes.

## Handling regular subnet failures

In rare cases, an entire subnet can get stuck and fail to make progress. A subnet can fail due to many reasons such as software bugs that lead to non-deterministic execution. This can also happen when more than 1/3rd of the nodes in the subnet fail at the same time. In this case, the well-functioning nodes fail to create and sign a catch-up package (CUP), and thereby the failed nodes cannot recover automatically.

When a subnet fails, manual intervention is needed for recovery. In a nutshell, as the subnet nodes fail to create and sign a CUP automatically, someone needs to manually create a CUP. The CUP needs to be created at the maximum blockchain height where the state is certified by at least 2/3rd of the nodes in the subnet. The subnet nodes naturally cannot trust a manually created CUP for security reasons. Therefore, we need a community consensus that the CUP is valid. The Internet Computer has a blockchain governance system called the [Network Nervous System](/how-it-works/#Network-Nervous-System) (NNS). We need to manually submit a proposal to the NNS to use the created CUP for the subnet. Anyone who staked their ICP can vote on the proposal. If a majority of the voters accept the proposal, the CUP is stored in the NNS registry.

Each node runs 2 processes — (1) Replica and (2) Orchestrator. The replica consists of the 4-layer software stack that maintains the blockchain. The orchestrator downloads and manages the replica software. The orchestrator regularly queries the NNS registry for any updates. If the orchestrator observes a new CUP in the registry, then the orchestrator restarts the replica process with the newly created CUP as input. As described earlier, the CUP at height _h_ has information relevant to resume the consensus from height _h_. Once the replica starts, it will initiate a _state sync protocol_ if it observes that the blockchain state hash in the CUP differs from the local state hash. Once the state is synced, it will resume processing consensus blocks.

Note that this recovery process requires submitting a proposal to the NNS, and therefore works only for recovering regular subnets (not the NNS subnet). This process of recovering a subnet is often termed as _disaster recovery_ in many Internet Computer docs.

## Handling NNS canister failures

The Internet Computer has a special subnet called the [Network Nervous System](/how-it-works/#Network-Nervous-System) (NNS) which hosts a lot of canisters that govern the entire Internet Computer. This includes the root canister, governance canister, ledger canister, registry canister, etc.

Suppose a canister in the NNS fails while the NNS subnet continues to make progress. This could be due to a software bug in the canister’s code. We then need to “upgrade” the canister, i.e., restart the canister with a new Web Assembly (WASM) code. Generally speaking, each canister in the Internet Computer has a (possibly empty) list of “controllers”. The controller has the right to upgrade the canister’s WASM code by sending a request to the subnet’s management canister. The lifeline canister is assigned as a controller for the root canister. The root canister is assigned as a controller for all the other NNS canisters. The root canister has a method to upgrade other NNS canisters (via calling the management canister). Similarly, the lifeline canister has a method to upgrade the root canister (via calling the management canister).

Suppose the governance canister is working. Then we can manually submit an NNS proposal to call the root/lifeline canister’s method to upgrade the failed canister. Anyone who staked ICP can vote on the proposal. If a majority of the voters accept, then the failed canister will be upgraded.

## Handling NNS subnet failures

In the worst case, the entire NNS subnet could get stuck and fail to make progress. In such a case, all the node providers who contributed a node to the NNS subnet need to manually intervene, create a CUP and restart their node with the new CUP.

[![Watch youtube video](https://i.ytimg.com/vi/H7HCqonSMFU/maxresdefault.jpg)](https://www.youtube.com/watch?v=H7HCqonSMFU)

[![Watch youtube video](https://i.ytimg.com/vi/WaNJINjGleg/maxresdefault.jpg)](https://www.youtube.com/watch?v=WaNJINjGleg)

-->
