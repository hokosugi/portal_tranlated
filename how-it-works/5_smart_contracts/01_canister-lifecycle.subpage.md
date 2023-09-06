---

title: Canister smart contracts
abstract:
shareImage: /img/how-it-works/canisters.jpg
slug: canister-lifecycle
---
# Canisters

## スマートコントラクトとはcanisters

Internet Computer スマートコントラクトは、canisters の形で提供されます。これらは、コードとステートを束ねた計算ユニットです。Canisters は、他のcanisters と、ブラウザやモバイルアプリなどICの外部の当事者によって呼び出されるエンドポイントを公開します。エンドポイントには2つのタイプがあります。アップデートは、canisters のステートを変更できるコールで、クエリーコールはそれができません。これらの良いメンタル・モデルは、アップデートはcanister のステートへの書き込みに使用され、クエリーはそのステートからの読み取りに使用されるというものです。
 canister のコードは（Wasm）モジュールで構成されています。 [WebAssembly](https://webassembly.org/)(Wasm)モジュールで構成されます。ステートには、Wasmモジュールの通常のヒープと、canister のライフcycle で重要な役割を果たす特別なタイプのメモリである安定メモリがあります。

## canisters の動作

Canisters は、 -based concurrency modelの とよく似た動作をします。そのコードはシングルスレッドで、他の から完全に分離された状態で実行されます。 は非同期メッセージングによって互いに通信します。メッセージを処理するとき、 はそのステートに変更を加えたり、他の にメッセージを送信したり、あるいは他の を作成したりすることができます。従来の モデルとは異なり、通信は双方向です。 メッセージはリクエストまたはリプライのいずれかです。リクエストを送信するごとに、ICはコールバックを記録し、着呼側が応答を送り返したときに呼び出されます。  ベースのモデルのもう一つの新しい側面は、メッセージ処理と トラッピングの相互作用です。リクエストを処理する間、 は他の にリクエストを送り、元のリクエストに対する応答を生成する前に、(いくつかの) 応答を待つことができる。  がトラップすると、そのステートは最後の発信を行った直後 の時点にロールバックされます。actor actor canisters Canisters canister canisters canisters actor Canister
 canister canister canister canisters
 canister 

## リソースチャージ

実行するとき、canisters 、メモリ、計算、ネットワーク帯域幅の形でリソースを使用します。IC上では、これらはすべて *cycles*.このため、canister はそれぞれ、cycles のローカルアカウントを持っており、実行が進むと、システムからcycles が差し引かれます。メモリ使用量の課金は簡単です。システムはcanister が使用したメモリーを追跡し、canisterの残高を定期的に請求します。効率化のため、この課金は一定間隔で行われますが、毎ラウンドではありません。これとは対照的に、計算に対する課金は計算が実行された時点で行われます。このため、canisters には、IC がメッセージの処理中に実行された命令数をカウントできるようにするコードが組み込まれています。ラウンドごとに、そのラウンド中に実行できる命令数の上限が設定されます。この命令数を超えた場合、実行は一時停止され、次のラウンドで続行されます。ただし、ラウンド中に実行された計算のcycles は、そのラウンドの終了時にすでにチャージされています。バグや悪意のあるcanister が実行コアを完全に乗っ取るのを防ぐため、canister の実行にかかる総ラウンド数も制限されています。

帯域幅の課金も、使用時に行われます。canister が別のcanister にリクエストを送信したい場合、システムはメッセージ送信にかかるcycles の数を計算し(送信コストには固定要素とペイロードのサイズに依存する要素がある)、そのコストをcanisterの残高から差し引く。
さらに、canister メッセージ間では発呼側が返信費用を支払うため、着呼側から最大サイズの返信を 送信するコストも差し引く。最大サイズと実際のリプライサイズの差に相当するcycles は、リプライが到着したときにcanister に払い戻される。

canisters のcycles がなくなると、アンインストールされる(そのコードとステー トは削除されるが、canisters に関連する残りの情報は保持される)。削除が突然起こるのを避けるため、canisters はいわゆる凍結しきい値に関連付けられています。canisterの残高が凍結しきい値を下回ると(システムによる課金によって)、canister は新しいリクエストの処理を停止します。canister が、cycles の残高が凍結しきい値を下回るようなアクション(例えば、cycles のアタッチやメッセージの送信)を実行しようとすると、システムはエラーをスローします。

## Canister 管理

Canisters canisters
 の制御構造は、中央集権型（コントローラに中央集権型エンティティが含まれる場合など）、分散型（コントローラがDAOの場合）、あるいは存在しないこともあり、その場合、 は不変のスマートコントラクトです。コントローラは、ICへの のデプロイとメンテナンスを担当し、 に対する管理操作を実行できる唯一のエンティティです。 そのような操作で最も一般的なものは、ICへの スマートコントラクトのデプロイと、 の開始と停止です。 のコントローラーは、コントローラーの追加や削除、凍結しきい値の変更など、 のパラメーターを変更できます。canisters canister
 canisters canisters
 canister canisters canisters canister 

コントローラは、古いモジュールを置き換える新しいWasmモジュールを提出することで、canisters で実行されるコードを更新できます。デフォルトでは、canister のWasmモジュールを更新すると、Wasmメモリは消去されますが、安定メモリの内容は変更されません。canister の Wasm メモリをシリアライズして安定したメモリに書き込み、 新しい Wasm コードをインストールし、安定したメモリの内容をデシリアライズします。
もちろん、canister は、アップグレードをまたいで永続化する必要があるデータが常に安定したメモリに保存されているようにすることもできます。

[![Watch youtube video](https://i.ytimg.com/vi/c5nv6vIG3OQ/maxresdefault.jpg)](https://www.youtube.com/watch?v=c5nv6vIG3OQ)

本講演では、Internet Computer 上でcanister スマートコントラクトを作成する方法、そのソフトウェアのインストールとアップグレードの方法、canisters をcycles でトップアップする方法について説明します。

<!---


# Canisters

## What are canisters

On the Internet Computer smart contracts come in the form of canisters. These are computational units which bundle together code and state. Canisters expose endpoints which can be called both by other canisters and by parties external to the IC, such as browsers or mobile apps. There are two types of endpoints. Updates are calls that can modify the state of the canisters and queries which cannot do that. A good mental model for these is that updates are used to write to the state of a canister and queries are used to read from that state.
The code of a canister consists of a [WebAssembly](https://webassembly.org/) (Wasm) module. The state consists of the usual heap of the Wasm module, together with stable memory, a special type of memory which plays an important role in the life-cycle of a canister.

## How do canisters work

Canisters behave much like actors from the actor-based concurrency model. Their code is single threaded and is executed in complete isolation of other canisters. Canisters communicate with one another via asynchronous messaging. When processing a message, a canister can make changes to its state, send messages to other canisters, or even create other canisters. Unlike in the traditional actor model, communication is bidirectional. Canister messages are either requests or replies. For each request sent, the IC records a callback to be invoked when the callee sends back a response. If the IC determines that there is no way for the callee to respond then the system will produce a response.
Another novel aspect of the canister based model is the interplay between message processing and canister trapping. While processing a request a canister may send requests to other canisters and wait for (some of) the replies, before producing a reply to the original request.
If a canister traps, its state is rolled back to the point right after it made the last outgoing call.

## Resource charging

As they execute, canisters use resources in the form of memory, computation and network bandwidth. On the IC all of these are paid for using a unit called _cycles_. To this end, each canister has a local cycles account from which the system deducts cycles as execution proceeds. Charging for memory usage is straightforward. The system keeps track of the memory used by the canister and regularly charges the canister’s balance. For efficiency, this charging happens at regular intervals but not every round. In contrast, charging for computation at the time that computation is performed. To this end, the canisters are instrumented with code that allows the IC to count the number of instructions executed while processing a message. Every round, there is an upper bound on the number of instructions that can be executed during that round. If this number of instructions is exceeded, then execution is paused and continued in a subsequent round. However, cycles for the computation performed during any round are already charged at the end of that round. To prevent a buggy or malicious canister from completely taking over an execution core, the total number of rounds the execution of a canister can take is also bounded.

Charging for bandwidth is also done at the moment of use. When a canister wants to send a request to another canister, the system calculates the number of cycles that sending the message costs (the cost of sending has a fixed component and a component that depends on the size of the payload) and deducts the cost from the canister’s balance.
Furthermore, it also deducts the cost of sending a maximal size reply from the callee since for inter-canister messages the caller pays for reply. The cycles corresponding to the difference between the maximal size and the actual size of the reply are refunded to the canister when the reply arrives.

When canisters run out of cycles, they are uninstalled (their code and state are deleted, but the rest of the information associated with the canisters are kept). To avoid that deletion happens too suddenly, canisters have associated a so-called freezing threshold. Once the canister’s balance dips below the freezing threshold (through charging by the system) then the canister stops processing any new requests; replies are still being processed. The system throws an error if at any time a canister attempts to perform an action (e.g. attaching cycles or sending a message) which would result in the cycles balance dipping below the freezing threshold.

## Canister management

Canisters are managed by controllers which can be users or even other canisters.
The control structure of canisters could be centralized (e.g. when the controllers include some centralized entity), decentralized (when the controller is a DAO) or even non-existent, in which case the canister is an immutable smart contract.
Controllers are in charge of deploying and maintaining the canisters to the IC and they are the only entities who are allowed to perform management operations on canisters.
The most common such operations are deploying a canister smart contract to the IC and starting and stopping canisters. The controller of canisters can change the canister parameters, including adding and removing controllers or changing the freezing threshold.

Controllers can update the code that runs on canisters by submitting a new Wasm module which should replace the older one. By default, updating the Wasm module of a canister wipes out the Wasm memory but the content of the stable memory remains unchanged. The IC offers an upgrade mechanism where three actions are executed atomically: serializing the Wasm memory of the canister and writing it to stable memory, installing the new Wasm code and then deserializing the content of the stable memory.
Of course, a canister may ensure at all times that the data that needs to be persisted across upgrades is stored in the stable memory in which case the upgrade process is significantly simpler.

[![Watch youtube video](https://i.ytimg.com/vi/c5nv6vIG3OQ/maxresdefault.jpg)](https://www.youtube.com/watch?v=c5nv6vIG3OQ)

This talk covers how to create canister smart contracts on the Internet Computer, how to install and upgrade their software, and how to top up canisters with cycles.

-->
