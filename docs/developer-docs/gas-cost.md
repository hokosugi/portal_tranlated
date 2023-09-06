# ガスとcycles コスト

## 概要

Internet Computer は、cycles によってサポートされる計算操作とストレージを必要とします。Cycles は、Internet Computer (ICP) ユーティリティ・トークンの変換から生成されます。

### コスト定義におけるNetwork Nervous System (NNS) の役割

Internet Computer は分散型の公益事業であり、NNS（ネットワークのオープンでアルゴリズム的な統治システム）によって管理されます。NNSは基本的に、計算とストレージの低レベル計算アクションに必要なcycles の数を制御します。個々の計算に必要なcycles の数は、コミュニティからの提案を含め、NNS が検討する多くの factorに基づいて変化します。

### 詳細：で実行されるスマートコントラクトの計算とストレージのトランザクションのコスト。Internet Computer

Canister Internet Computer ブロックチェーン上で実行されるスマートコントラクトの計算は、イーサリアムの「ガス」と同様の役割を果たす「 」によって賄われます。しかし、いくつかの大きな違いがあります。最も基本的な違いの1つは、イーサリアムが「ユーザーペイ」と「 」と「スマートコントラクトペイ」（「リバースガス」と呼ばれることもある）モデルを活用していることです。イーサリアム・ブロックチェーンでは、スマート・コントラクトがトランザクションごとに消費するガスの代金をエンド・ユーザーが送金する必要があるのに対し、 、 スマート・コントラクトにはあらかじめ がチャージされており、コントラクトは効果的に自身の計算費用を支払うことで、ユーザーを責任から解放します。cycles Internet Computer Internet Computer canister cycles

2022年11月下旬、高レプリケーション・アプリケーション・サブネットがInternet Computer で利用可能になりました。このような最初のサブネットはレプリケーションfactor が30のオーダーで開始されましたが、将来的には異なるサイズが利用可能になる可能性があります。Cycles 新しい高レプリケーション・サブネットの価格は、13ノード・サブネットの基本価格からノード数に基づいて線形にスケーリングされます。Bitcoin Mainnet API の価格設定の仕組みは若干異なります。詳細は[Bitcoin API ドキュメントを](./integrations/bitcoin/bitcoin-how-it-works.md)参照してください。

以下の表は、13 ノードのベースラインと 34 ノードのサブネットの例について、cycles と USD での価格を示しています。ここで*n*は価格を計算するサブネットのサイズ、**13\_node\_price**は 13 ノードを持つ基準サイズのサブネット上のトランザクションの価格、**DIV**は整数除算です：

***nノードサブネットの価格 = (13\_node\_price \* n) DIV 13***

高レプリケーション・サブネットにcanisters をデプロイする場合、canister 、サブネットのレプリケーション factor が時間の経過とともに増加した場合、cycles 価格が上昇することを想定しておく必要があります。このため、特定のサイズの高レプリケーション・サブネットの現在の価格よりも多くのcycles 。

2022年12月2日現在のInternet Computer の新機能の管理canister コールのほか、計算およびストレージトランザクションのコストの詳細については、以下を参照してください。
13ノードのアプリサブネットでcanister を実行するコストがどのように計算されるかの詳細な例は、[こちらを](https://wiki.internetcomputer.org/wiki/Comparing_Canister_Cycles_vs_Performance_Counter)参照してください。

| トランザクション | 説明 | 取引手数料の支払いは誰が行うのですか？ | ローカル開発[（IC SDK）](./setup/index.md) | 13ノードアプリケーションサブネット | 34ノードアプリケーションサブネット |
| --- | --- | --- | --- | --- | --- |
| Canister 作成 | サブネット上にcanisters  | 作成canister | 100B / 13 | 100B | 100B / 13 \* 34 |
| 1秒あたりのコンピュート割り当て率 | 予約されたコンピュート割り当ての各パーセント（希少リソース）。 | Canister 割り当てあり | 10M / 13 | 10M | 10M / 13 \* 34 |
| 更新メッセージの実行 | 更新メッセージが実行されるごとに | ターゲットcanister | 590K / 13 | 590K | 590K / 13 \* 34 |
| 更新命令10回実行 | 更新型メッセージ実行時、10命令実行ごとに | Canister 実行命令数 | 4 / 13 | 4 | 4 / 13 \* 34 |
| Xnetコール | canister （リクエストの送信とレスポンスの受信にかかる費用を含む）間コールを実行するごとに発生。 | 送信canister | 260K / 13 | 260K | 260K / 13 \* 34 |
| Xnetバイト送信 | canister （リクエストとレスポンスで送信されるバイト）間通話で送信されるバイトごとに | 送信canister | 1K / 13 | 1K | 1K / 13 \* 34 |
| イングレス・メッセージ受信 | イングレス・メッセージを受信するごとに | 受信canister | 1.2M / 13 | 1.2M | 1.2M / 13 \* 34 |
| イングレス・バイト受信 | イングレス・メッセージで受信したバイトごとに | 受信canister | 2K / 13 | 2K | 2K / 13 \* 34 |
| GBストレージ/秒 | GB/秒のデータを保存する場合 | Canister ストレージ | 127K / 13 | 127K | 127K / 13 \* 34 |
|  |  |  |  |  |  |
| *HTTPSアウトコール* |  |  |  |  |  |
| HTTPSアウトコール（1コールあたり） | HTTPSアウトコールをIC外のサーバーに送信する場合、1メッセージあたり(`http_request`) | 送信canister | 3,060,000 | 49,140,000 | 171,360,000 |
| HTTPS アウトコール要求メッセージサイズ（1 バイトあたり） | HTTPS Outcall を IC 外のサーバに送信する場合のリクエストバイト数 (http\_request) あたり | 送信canister | 400 | 5,200 | 13,600 |
| HTTPS outcall レスポンスメッセージサイズ（1 バイトあたり） | HTTPS outcall を IC 外のサーバに送信する場合、予約されたレスポンス・バイト (http\_request) 1 バイトあたり | 送信canister | 800 | 10,400 | 27,200 |

**Bitcoin API**の価格は、[Bitcoin API ドキュメントを](./integrations/bitcoin/bitcoin-how-it-works.md)ご覧ください。

**Chain-Key Signing APIの**価格は、[Chain-Key Signing / threshold ECDSAのドキュメントを](./integrations/t-ecdsa/t-ecdsa-how-it-works.md)ご覧ください。

:::note

- システムAPIコールは、WebAssembly から見れば通常の関数コールと同じです。各呼び出しにかかる命令数は、実行される作業によって異なります。
  ::：

HTTPSアウトコールの価格は、他のリソースの価格とは若干異なる方法で計算されます：この機能の実装には2次関数の要素があり、これは基本料金に`(3,000,000 + 60,000 * n) * n` 、各リクエストバイトに`400 * n` 、各レスポンスバイトに`800 * n` という式に反映されています。これらの式は、サイズ13と34のサブネットの具体的な値を得るために表で使用されています。

以下のトランザクションのUSDコストは、上記のcycle 。1 XDRは1兆cycles に相当。2022年11月23日現在、1 XDR = $1.308860 の為替レートがこのページで使用されています。USD/XDR の為替レートは変動する可能性があり、換算レートに影響します。XDR の為替レートは[こちらで](https://www.imf.org/external/np/fin/data/rms_sdrv.aspx)ご覧いただけます。

1ヶ月あたりのGBストレージの見積もりコストを算出するために、30日の月を想定しています。

| 取引内容 | 説明 | トランザクション料金は誰が支払うのですか？ | 13ノードアプリケーションサブネット | 34ノードアプリケーションサブネット |
| --- | --- | --- | --- | --- |
| Canister 作成 | サブネット上にcanisters  | 作成canister | $0.130886 | $0.342317 |
| 秒あたりのコンピュート割り当て率 | 予約されたコンピュート割り当て（希少リソース）の各パーセントについて。 | Canister 割り当てあり | $0.0000130886 | $0.0000342317 |
| 更新メッセージの実行 | 更新メッセージが実行されるごとに | ターゲットcanister | $0.0000007722274 | $0.0000020196705 |
| 10回の更新命令実行 | 更新型メッセージ実行時に10命令実行するごとに | Canister 実行命令数 | $0.000000000005235 | $0.000000000013089 |
| Xnetコール | canister （リクエストの送信とレスポンスの受信にかかる費用を含む）間コールを実行するごとに発生します。 | 送信canister | $0.0000003403036 | $0.0000008900248 |
| Xnetバイト送信 | canister 間通話で送信される各バイトに対して（リクエストおよびレスポンスで送信されるバイトに対して） | 送信canister | $0.00000000130886 | $0.00000000342267 |
| イングレス・メッセージ受信 | 受信したイングレス・メッセージごと | 受信canister | $0.000001570632 | $0.000004107806 |
| イングレス・バイト受信 | イングレス・メッセージの受信バイト数分 | 受信canister | $0.00000000261772 | $0.00000000684534 |
| GBストレージ/秒 | GB/秒のデータを保存する場合 | Canister ストレージ付き | $0.00000016622522 | $0.00000043474178 |
|  |  |  |  |  |
| *HTTPSアウトコール* |  |  |  |  |
| HTTPSアウトコール（1コールあたり） | HTTPS アウトコールを IC 外のサーバーに送信する場合、1 メッセージあたり (`http_request`) | 送信canister | $0.0000643173804 | $0.0002242862496 |
| HTTPS Outcallリクエストメッセージサイズ（1バイトあたり） | IC 外のサーバーに HTTPS Outcall を送信する場合、1 リクエストバイトあたり (http\_request) | 送信canister | $0.000000006806072 | $0.000000017800496 |
| HTTPS outcall応答メッセージサイズ（1バイトあたり） | IC 外のサーバーに HTTPS outcall を送信する場合、予約された応答バイト (http\_request) 1 つにつき | 送信canister | $0.000000013612144 | $0.000000035600992 |

米ドルでのトランザクションあたりのコスト（2022 年 11 月 23 日現在の XDR/USD 為替レート）：

30日間の月と仮定

|  |  | 13ノードアプリケーションサブネット | 34ノードアプリケーションサブネット |
| --- | --- | --- | --- |
| GBストレージ/月 | GBのデータを保存する場合/月 | $0.431 | $1.127 |

<!---
# Gas and cycles cost

## Overview

The Internet Computer requires computation operations and storage to be supported by cycles. Cycles are generated from the conversion of Internet Computer (ICP) utility tokens.

### The role of the Network Nervous System (NNS) in defining costs

The Internet Computer is a decentralized public utility, controlled by the NNS –– the network’s open, algorithmic governance system. The NNS fundamentally controls how many cycles are required for low-level computation actions for computation and storage. The number of cycles needed for individual computations will vary based on a number of factors considered by the NNS, including proposals from the community.

### Details: Cost of compute and storage transactions on the Internet Computer

Canister smart contract computations running on the Internet Computer blockchain are fueled by “cycles”, which play a similar role to “gas” on Ethereum. There are several major differences however. One of the most fundamental differences is that Ethereum leverages “user pays” and the Internet Computer and “smart contract pays” (sometimes called “reverse gas”) model. Whereas the Ethereum blockchain requires end users to send payments for the gas smart contracts consume with every transaction, on the Internet Computer, canister smart contracts are pre-charged with cycles, such that contracts effectively pay for their own computation - freeing users from the responsibility.

In late November 2022, high-replication application subnets have been made available on the Internet Computer. The first such subnets launched with a replication factor in the order of 30, different sizes may become available in the future. Cycles prices for the new high-replication subnets are scaled linearly based on the number of nodes from the base prices for 13-node subnets. The pricing mechanics for the Bitcoin Mainnet API is slightly different, see the [Bitcoin API documentation](./integrations/bitcoin/bitcoin-how-it-works.md) for details.

The following tables provide pricing in cycles and USD for the 13-node baseline and the example of 34-node subnets. The linear scaling for a transaction is computed using the following formula, where *n* is the size of the subnet to compute the price for, **13_node_price** is the price for the transaction on the reference-size subnet with 13 nodes, and **DIV** is integer division:

***Price on n-node subnet = (13_node_price \* n) DIV 13***

If you intend to deploy canisters on high-replication subnets, your canister should be prepared for an increase in cycles prices with an increase in the subnet's replication factor when the subnet grows over time. For this reason it is recommended to attach more cycles to a call than the current price for a high-replication subnet of a given size suggests.

See below for details on the cost of compute and storage transactions as well as management canister calls for new features on the Internet Computer as of December 2, 2022.
A thorough example how the cost of running a canister on a 13-node app subnet is computed can be found [here](https://wiki.internetcomputer.org/wiki/Comparing_Canister_Cycles_vs_Performance_Counter).

 | Transaction                          | Description                                                                                                      | Who is responsible for paying the transaction fee? | Local development ([IC SDK](./setup/index.md))             | 13-node Application Subnets           | 34-node Application Subnets        |
 | ------------------------------------ | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------- | ------------------------------------- | ------------------------------------- | ---------------------------------- |
| Canister Created                     | For creating canisters on a subnet                                                                               | Created canister | 100B / 13                             | 100B                                  | 100B / 13 * 34                     |
| Compute Percent Allocated Per Second | For each percent of the reserved compute allocation (a scarce resource).                                         | Canister with allocation | 10M / 13                              | 10M                                   | 10M / 13 * 34                      |
| Update Message Execution             | For every update message executed                                                                                | Target canister | 590K / 13                             | 590K                                  | 590K / 13 * 34                     |
| Ten Update Instructions Execution    | For every 10 instructions executed when executing update type messages                                           | Canister executing instructions | 4 / 13                                | 4                                     | 4 / 13 * 34                        |
| Xnet Call                            | For every inter-canister call performed (includes the cost for sending the request and receiving the response)   | Sending canister | 260K / 13                             | 260K                                  | 260K / 13 * 34                     |
| Xnet Byte Transmission               | For every byte sent in an inter-canister call (for bytes sent in the request and response)                       | Sending canister | 1K / 13                               | 1K                                    | 1K / 13 * 34                       |
 | Ingress Message Reception            | For every ingress message received                                                                               | Receiving canister | 1.2M / 13                             | 1.2M                                  | 1.2M / 13 * 34                     |
 | Ingress Byte Reception               | For every byte received in an ingress message                                                                    | Receiving canister | 2K / 13                               | 2K                                    | 2K / 13 * 34                       |
| GB Storage Per Second                | For storing a GB of data per second                                                                              | Canister with storage | 127K / 13                             | 127K                                  | 127K / 13 * 34                     |
 |                                      |                                                                                                                  |                                       |                                       |                                    |
 | _HTTPS outcalls_                     |                                                                                                                  |                                       |                                       |                                    |
 | HTTPS outcall (per call)                | For sending an HTTPS outcall to a server outside the IC, per message (`http_request`)                            | Sending canister | 3,060,000                             | 49,140,000                                  | 171,360,000                                        | 27,200                     |
| HTTPS outcall request message size (per byte)|	For sending an HTTPS outcall to a server outside the IC, per request byte (http_request) | Sending canister |	400	| 5,200	| 13,600 |
| HTTPS outcall response message size (per byte) |	For sending an HTTPS outcall to a server outside the IC, per reserved response byte (http_request)|	Sending canister | 800	| 10,400	| 27,200 |

Pricing for the **Bitcoin API** is available in the [Bitcoin API documentation](./integrations/bitcoin/bitcoin-how-it-works.md).

Pricing for the **Chain-Key Signing API** is available in the [Chain-Key Signing / threshold ECDSA documentation](./integrations/t-ecdsa/t-ecdsa-how-it-works.md).

:::note
* System API calls are just like normal function calls from the WebAssembly stand point. The number of instructions each call takes depends on the work done.
:::

The pricing for HTTPS outcalls is calculated in a slightly different way as the prices for other resources: The feature has a quadratic component in its implementation, which is reflected through the formula `(3,000,000 + 60,000 * n) * n` for the base fee and `400 * n` each request byte and `800 * n` for each response byte. Those formulas have been used in the table to obtain the concrete values for subnets of sizes 13 and 34.

The USD cost for transactions below is based on the above cycle costs. 1 XDR is equal to 1 Trillion cycles. As of November 23, 2022, the exchange rate for 1 XDR = $1.308860, which is used on this page. The exchange rate for USD/XDR may vary and it will impact the conversion rate. You can view XDR exchange rates [here](https://www.imf.org/external/np/fin/data/rms_sdrv.aspx).

To derive the estimated cost for a GB Storage per month, we assume a 30 day month.

 | Transaction                          | Description                                                                                                      | Who is responsible for paying the transaction fee? |  13-node Application Subnets | 34-node Application Subnets |
 |--------------------------------------|------------------------------------------------------------------------------------------------------------------|---------------------------|---------------------------|-----------------------------|
| Canister Created                     | For creating canisters on a subnet                                                                               | Created canister | $0.130886                   | $0.342317                   |
| Compute Percent Allocated Per Second | For each percent of the reserved compute allocation (a scarce resource).                                         | Canister with allocation | $0.0000130886               | $0.0000342317               |
| Update Message Execution             | For every update message executed                                                                                | Target canister | $0.0000007722274            | $0.0000020196705            |
| Ten Update Instructions Execution    | For every 10 instructions executed when executing update type messages                                           | Canister executing instructions | $0.000000000005235          | $0.000000000013089          |
| Xnet Call                            | For every inter-canister call performed (includes the cost for sending the request and receiving the response)   | Sending canister | $0.0000003403036            | $0.0000008900248            |
| Xnet Byte Transmission               | For every byte sent in an inter-canister call (for bytes sent in the request and response)                       | Sending canister | $0.00000000130886           | $0.00000000342267           |
| Ingress Message Reception            | For every ingress message received                                                                               | Receiving canister | $0.000001570632             | $0.000004107806             |
| Ingress Byte Reception               | For every byte received in an ingress message                                                                    | Receiving canister | $0.00000000261772           | $0.00000000684534           |
| GB Storage Per Second                | For storing a GB of data per second                                                                              | Canister with storage | $0.00000016622522           | $0.00000043474178           |
 |                                      |                                                                                                                  |                             |                             |
 | *HTTPS outcalls*                     |                                                                                                                  |                             |                             |
 | HTTPS outcall (per call)                | For sending an HTTPS outcall to a server outside the IC, per message (`http_request`)                            | Sending canister | $0.0000643173804               | $0.0002242862496 |
 | HTTPS outcall request message size (per byte)	| For sending an HTTPS outcall to a server outside the IC, per request byte (http_request)	| Sending canister | $0.000000006806072 |	$0.000000017800496 |
| HTTPS outcall response message size (per byte)	| For sending an HTTPS outcall to a server outside the IC, per reserved response byte (http_request) | Sending canister | $0.000000013612144	| $0.000000035600992 |

Cost per Transaction in USD (XDR/USD exchange rate as of November 23, 2022):

Assuming a 30-day month — 

|                      |                                    | 13-node Application Subnets | 34-node Application Subnets |
|----------------------|------------------------------------|-----------------------------|-----------------------------|
| GB Storage Per Month | For storing a GB of data per month | $0.431                      | $1.127                      |

-->
