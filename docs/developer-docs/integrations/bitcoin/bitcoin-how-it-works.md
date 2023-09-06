# ビットコインの統合：技術概要

## 概要

ビットコインを可能にするICは、2つの高度なエンジニアリングの課題を解決する必要がありました：

- ビットコインネットワークとICのプロトコルレベルの統合。
- 新しい閾値ECDSAプロトコルに基づく連鎖鍵署名。

このページでは、ビットコイン統合技術の概要を説明します。ckBTCのより深い技術的説明については、[wikiページを](https://wiki.internetcomputer.org/wiki/Chain-key_Bitcoin)ご覧ください。

## ICとビットコインネットワークのプロトコルレベルでの統合

ICとビットコインネットワークのプロトコルレベルでの統合により、ICはビットコインネットワークから直接ビットコインブロックを取得し、含まれるトランザクションを処理することができます。この統合により、IC 上のオンチェーンで完全なビットコイン UTXO セットを維持することが可能になります。Canisters は、完全なビットコイン UTXO セットに対してクエリを実行できます。これにより、canisters 、自分のアドレスを含むあらゆるビットコインアドレスの保有UTXO、ひいては残高を知ることができます。

## チェーンキー ECDSA 署名

Canisters Canisters はまた、ビットコインのトランザクションを作成し、ビットコイン API を介してビットコインネットワークに送信することができます。チェーンキー ECDSA 機能を使用して、ビットコイン ネットワークに送信するトランザクションで UTXO を消費するために、しきい値 ECDSA 署名を要求します。チェーンキーECDSA署名は、ICのプロトコルのチェーンキー暗号ツールボックスの新しいメンバーです。チェーンキーECDSA署名は単なる閾値ECDSAの実装をはるかに超えるもので、例えば、閾値署名を安全かつ実用的に実行可能にするためにシステムの観点から重要な、安全な分散鍵生成と鍵ローテーションのためのプロトコルも含んでいます。ICの連鎖鍵ECDSA署名プロトコルの詳細は[、](../t-ecdsa/t-ecdsa-how-it-works.md)対応するドキュメントのページでご覧いただけます。

プロトコルレベルのビットコイン統合とチェーンキーECDSA署名プロトコルは、それぞれ管理canister のAPIを公開します。これらのAPIは、エンジニアがIC上でBitcoinスマートコントラクトを記述するために使用するシステムレベルのAPIです。これらのAPIは、ビットコインのUTXOとトランザクションの概念を中心に設計された低レベルのAPIであり、使用するのは自明ではなく、ビットコインの仕組みを深く理解する必要があります。チェーンキーECDSA署名APIは、ECDSAのあらゆるユースケース、例えばイーサリアムのような他のブロックチェーンとの統合にも汎用的に有用です。

次に、Bitcoinとの直接統合の背後にある上記の技術の高レベルの概要を説明します。詳細については、[ Internet Computer wiki の Bitcoin ページ](https://wiki.internetcomputer.org/wiki/Bitcoin_integration)および[threshold ECDSA ドキュメント](../t-ecdsa/t-ecdsa-how-it-works.md)ページを参照してください。

## ICとBitcoinネットワークのプロトコルレベルでの統合

2つのネットワーク間の直接的な技術統合を得るために、Internet Computer Protocol を Bitcoin プロトコルと統合しました。この統合は、Internet Computer サブネットの任意の数でアクティブにすることができます。当初は、ビットコインがアクティブ化された専用のサブネットが1つだけ存在し、どのサブネット上のcanisters からのビットコインAPIへのリクエストも、ICのXNet通信機能を使用して、この単一のビットコインがアクティブ化されたサブネットにルーティングされます。この統合には2つの重要な目的があります：

- ビットコイン UTXO セットを取得し、Internet Computer の複製されたステートでチェーン上に保持することで、canisters が発行したビットコイン口座の UTXO セットと残高に関するクエリに回答できるようにします。
- canisters の署名済みビットコイン取引を受け入れ、ビットコイン・ネットワークに送信します。

![Bitcoin Integration](../_attachments/bitcoin_integration.png)

### コンポーネント

ビットコインがアクティブ化されたサブネットでは、通常の NNS 管理ワスムcanister として実装された**BTCcanister**（ビットコインcanister ）が、管理canister の API を介してcanisters にアクセスできるようになっています。BTCcanister はオンチェーンのビットコイン関連のステート（UTXOセット、フォーク解決を可能にする最新のビットコインブロック（「不安定ブロック」）、発信トランザクション）を保持します。

ネットワーキングレイヤーの**ビットコインアダプタプロセスは**、通常のビットコインノードと同様に、ビットコインネットワークのノードに接続します。

### ビットコインUTXOセットの維持

BTCcanister とアダプターは統合されており、IC のプロトコルスタックを介して互いに通信します：BTCcanister は、ビットコインアダプターから受け取った最新のビットコインブロックの後継ブロックを要求します。サブネットの各レプリカのアダプタは、ビットコインネットワークから要求されたブロックを取得し、ブロックを作成するレプリカのアダプタは、コンセンサスを通じて要求されたブロックをビットコインcanister に提供します。Bitcoincanister は、プルーフ・オブ・ワークを検証し、ブロックのトランザクションを抽出し、トランザクションからUTXOを抽出し、トランザクションによって消費されたUTXOと作成されたUTXOを反映するために、レプリケートされたステートで保持されているUTXOセットを更新するという点で、ブロックを処理します。UTXOセットと、まだUTXOセットに吸収されていない最近のブロックのセット（不安定なブロック）の両方が、UTXOとバランス要求に応答するために使用されます。

この実装の複雑さは、フォークを安全に解決し、さまざまな種類の攻撃から保護する分野にあります。例えば、フォークを解決できるようにするために、ビットコインcanister は、維持されている UTXO セットにまだ吸収されていないビットコインブロックを一定数維持する必要がありますが、UTXO セットのものに加えて、与えられたアドレスの UTXO を計算するために、その中の UTXO を効率的に考慮する必要があります。

### トランザクションの送信

Canisters は、対応する管理 API を使用して、ビットコイン にビットコイントランザクションを提出できます。そうすることで、トランザクションはビットコインネットワークにサブミットされるためにキューに入れられます。各サブネットラウンドで、アダプタは保留中のトランザクションをBitcoin から取得し、Bitcoinネットワークに非同期で提出されるようにキューに入れます。これは、サブネットのすべてのレプリカがビットコインネットワークの複数の接続されたノードを介してトランザクションを提出するため、ビットコインネットワーク内のトランザクションの効率的かつ迅速な配布につながります。canister canister canister 

## チェーンキーECDSA署名：新しい閾値ECDSAプロトコルスイート

チェーンキー署名は、新しい閾値ECDSAプロトコルスイート、つまり閾値暗号を使用したECDSA署名プロトコルの実装で実現されます。閾値ECDSAプロトコルでは、ECDSA秘密鍵は複数の当事者間で秘密共有され、当事者のうち適格な定足数のみがそれぞれの秘密鍵共有を使って署名を生成することができます。秘密鍵は再構成された形では存在せず、秘密共有された形でのみ存在します。鍵生成は、各当事者の秘密鍵共有を生成します。例えば、安全な閾値鍵生成のための暗号化マルチパーティ計算プロトコルや、全体的なセキュリティを強化するための定期的な鍵の再共有などです。

現在実装されているECDSA連鎖鍵署名プロトコルは、単一のマスター秘密鍵を使用し、そこからBIP-32のような鍵導出を使用してcanisters 。各canister には、canister id を使用して導出されたルート鍵が1つずつあり、BIP-32の下位互換拡張を使用して、さらに任意の数のcanister 鍵を導出することができます。

ECDSA連鎖鍵署名は、canisters の署名要求に答えるInternet Computer の1つの署名サブネットに展開されます。この署名サブネットは、鍵を管理するcanister のみがこの鍵による署名を要求できることを強制します。canisters によるすべてのAPIコールは、ECDSAサブネットに到達するためにXNetトラフィックを経由する必要があるため、署名を取得するために関連する余分な待ち時間が発生します。

Canisters canisters canisters Canisters は、自身が管理する秘密鍵、すなわちルート秘密鍵および派生秘密鍵による署名を 要求することができる。公開鍵または派生鍵による署名を要求する場合、それぞれのAPIで派生パスを指定するか、 BIP-32を使用して派生を行うことができます。詳細は[ECDSA連鎖鍵署名の](../t-ecdsa/t-ecdsa-how-it-works.md)ページを参照。

## デプロイメントアーキテクチャ

Bitcoin 機能は IC の単一サブネット上で有効化され、canisters から Bitcoin API への API 呼び出しは XNet 通信を介してルーティングされるため、余分な待ち時間が発生します。Bitcoincanister は、XNet の追加レイテンシを回避し、時間単位当たりにより多くのリクエストに応答できるよう、必要に応じて将来的にいくつかの（アプリケーション）サブネット上で追加的にアクティブ化される可能性があります。

閾値ECDSAリクエストは、アクティブな1つの署名サブネットで等しく応答され、別のサブネットはディザスタリカバリのために秘密鍵を秘密共有形式でバックアップします。

## 開発、プリプロダクション、プロダクション環境

ECDSA連鎖鍵署名を含むビットコイン機能は、IC上の開発ライフcycle に必要なすべての段階で利用可能です：

- canisters のローカル開発のための**IC SDK**。
- **Bitcoin testnet**上の最終テスト用プリプロダクション環境としてのICサポート。
- **ビットコインメインネットを**使用したリリースのための本番環境としてのICサポート。

### ローカルSDK

典型的なcanister 開発ワークフローでは、IC 上のcanisters は開発中に[IC SDK](../../setup/install/index.mdx)を使用してローカル環境でコンパイルおよび実行されます。このように、IC SDKは開発ワークフローの最初の段階（環境）です。IC SDKは、ビットコイン統合と閾値ECDSA管理canister APIの両方をサポートできるようになっています。

それぞれ Bitcoin Testnet と Bitcoin Mainnet と統合する機能の IC デプロイメントとは対照的に、SDK はリグレッションテスト（regtest）モードでローカルに実行される`bitcoind` ノードと統合します。リグレッションテストモードで`bitcoind` を使用することは、ビットコイン開発にとって望ましい方法です。開発者を可能な限り容易にするため、私たちはIC SDKをリグテストモードの`bitcoind` と統合し、ICに最高のビットコイン開発体験をもたらしました。スマートコントラクトの開発と自動テストの両方は、まずこのセットアップでローカル環境で行われます。

ローカル SDK 環境を実行する単一のレプリカのビットコインアダプターは、ビットコインテストネットまたはメインネットの複数のノードではなく、ローカルの`bitcoind` ノードに接続します。dfx の関連フラグを確認するには、`dfx start --help` の出力を見てください。

### IC上のビットコインテストネット

スマートコントラクトが受け入れテストの準備ができたら、ICメインネットにデプロイされます（パブリックICテストネットがないことを思い出してください）。これはローカル開発で使用されるのと同じ設定です。この設定は、ビットコインテストネットとテストチェーン鍵ECDSA鍵を使用して、dApp の受け入れテストを実行するために使用されます。

### IC上のビットコイン・メインネット

ビットコイン・スマートコントラクトの開発の最終段階は、ビットコインメインネットを使用するように設定されたビットコインAPIを使用してIC上に展開することです。これがスマートコントラクトの最終的な本番環境であり、現在利用可能です。

## APIの費用と価格

Bitcoin TestnetとBitcoin Mainnet APIのAPIコールのコストをcycles 、USDで以下の表に示します。Bitcoin API の一般的な原則として、一部の API 呼び出しは、*Minimumcycles to send with call* の列に示されているように、呼び出しに最小量のcycles が添付されている必要があります。呼び出しによって消費されなかったCycles は呼び出し元に返されます。比較的大きな最小数cycles を要求することで、将来ビットコインサブネットがレプリケーションfactor の点で成長したときに、既存のスマートコントラクトを壊すことなくAPIコールの価格設定を変更することが可能になります。ビットコインネットワークにビットコイントランザクションを送信するための呼び出しは、課金コストがサブネットのレプリケーションfactor に依存しないため、余分なcycles を添付する必要はありません。

USDでのAPIコールあたりのコストは、2022年11月23日のUSD/XDR為替レートを使用しています。

### ビットコイン・テストネット

| トランザクション | 説明 | 価格 (Cycles) | 価格（米ドル） | コールで送信する最小cycles  |
| --- | --- | --- | --- | --- |
| アドレスのビットコインUTXOセット | ビットコインアドレスのUTXOセットを取得する場合 (`bitcoin_get_utxos`) | 20,000,000 + Wasm命令あたり0.4cycles  | 0.00002617720 ドル + Wasm 命令コスト | 4,000,000,000 |
| ビットコインの手数料パーセンタイル | 直近の取引の手数料パーセンタイル (`bitcoin_get_current_fee_percentiles`) を取得する場合 | 4,000,000 | $0.00000523544 | 40,000,000 |
| アドレスのビットコイン残高 | 指定されたビットコインアドレスの残高を取得するため (`bitcoin_get_balance`) | 4,000,000 | $0.00000523544 | 40,000,000 |
| ビットコイン取引の送信 | ビットコイントランザクションをビットコインネットワークに送信する場合、トランザクションごとに (`bitcoin_send_transaction`) | 2,000,000,000 | $0.00261772000 | n.a. |
| ビットコイン取引ペイロード | ビットコイントランザクションをビットコインネットワークに送信する場合、ペイロードの 1 バイトあたり (`bitcoin_send_transaction`) | 8,000,000 | $0.00001047088 | n.a. |

### ビットコインメインネット

| トランザクション | 取引内容 | 価格 (Cycles) | 価格 (USD) | コールで送信する最小cycles  |
| --- | --- | --- | --- | --- |
| アドレスのビットコインUTXOセット | ビットコインアドレスのUTXOセットを取得する場合 (`bitcoin_get_utxos`) | 50,000,000 + ワズム命令ごとに1cycle  | 0.00006544300 ドル + Wasm 命令コスト | 10,000,000,000 |
| ビットコインの手数料パーセンタイル | 直近の取引の手数料パーセンタイルを取得する場合 (`bitcoin_get_current_fee_percentiles`) | 10,000,000 | $0.00001308860 | 100,000,000 |
| アドレスのビットコイン残高 | 指定されたビットコインアドレスの残高を取得するため (`bitcoin_get_balance`) | 10,000,000 | $0.00001308860 | 100,000,000 |
| ビットコイン取引の送信 | ビットコイントランザクションをビットコインネットワークに送信する場合、トランザクションごとに (`bitcoin_send_transaction`) | 5,000,000,000 | $0.00654430000 | n.a. |
| ビットコイン取引ペイロード | ビットコイントランザクションをビットコインネットワークに送信する場合、ペイロードの 1 バイトあたり (`bitcoin_send_transaction`) | 20,000,000 | $0.00002617720 | n.a. |

:::note
 `bitcoin_get_utxos` コールは、ビットコインブロック処理の一部と、実際に使用された Wasm 命令のcycles コストを償却するベースライン料金によって課金されることに注意してください。これは最も公平な課金方法です。なぜなら、一律の課金方法は少数のUTXOを返すリクエストの公平性を欠き、UTXOの数に比例する課金方法はきれいな方法で定義するのが難しいからです。いくつかの非公式なテスト測定では、返されたUTXOあたり200K未満から1,000K以上cycles 、さらに不安定なブロックの処理に3,000万～5,000万cycles 。UTXOあたりのこの大きなばらつきが、返されたUTXOの数に基づく課金アプローチを使用しない理由ですが、料金の支払いに関して予想される大まかな目安にはなるはずです。UTXOの数が少ないクエリーの場合、ほとんどのコールで、提供されたcycles から差し引かれる料金として、およそ100Mcycles を期待することができます。
::：

## リソース

ビットコインで独自のアプリを構築し始めるには、以下のチュートリアルを参照してください：

- [最初のビットコインのデプロイdapp](../../../samples/deploying-your-first-bitcoin-dapp.md) 。

- [ローカル開発](./local-development.md)。

<!---
# Bitcoin integration: technology overview

## Overview

Bitcoin-enabling the IC has required us to solve two advanced engineering challenges: 
- A protocol-level integration of the IC with the Bitcoin network.
- A chain-key signatures based on a novel threshold ECDSA protocol.

This page provides a general overview of the Bitcoin integration technology. For a deeper technical explanation of ckBTC, please see the [wiki page](https://wiki.internetcomputer.org/wiki/Chain-key_Bitcoin).

## Protocol-level integration of the IC with the Bitcoin network

Through the protocol-level integration of the IC with the Bitcoin network, the IC can obtain Bitcoin blocks directly from the Bitcoin network and process the contained transactions. This integration makes it possible to maintain the full Bitcoin UTXO set on-chain on the IC. Canisters can run queries against the full Bitcoin UTXO set. This allows canisters to know about the held UTXOs, and thus balance, of any Bitcoin address, including their own addresses.

## Chain-key ECDSA signatures

Canisters themselves can have ECDSA keys using a novel chain-key ECDSA signature protocol suite for threshold ECDSA, and so can receive and hold Bitcoin. Canisters also can create Bitcoin transactions and submit them via the Bitcoin API to the Bitcoin network. They use the chain-key ECDSA functionality to request threshold ECDSA signatures to spend UTXOs in a transaction to be submitted to the Bitcoin network. Chain-key ECDSA signatures are a new member of the IC's Chain Key cryptography toolbox of protocols. Chain-key ECDSA signatures are much more than just a threshold ECDSA implementation as they for example also comprise protocols for secure distributed key generation and key rotation, which are crucial from a systems perspective to make threshold signing secure and practically viable. Details regarding the IC's chain-key ECDSA signature protocol can be found on the corresponding documentation page [here](../t-ecdsa/t-ecdsa-how-it-works.md).

The protocol-level Bitcoin integration and chain-key ECDSA signature protocols each expose an API on the management canister. Those APIs are the system-level APIs engineers use to write Bitcoin smart contracts on the IC. The APIs are low-level APIs designed around the concepts of Bitcoin UTXOs and transactions and are non-trivial to use and require an in-depth understanding of how Bitcoin works. The chain-key ECDSA signature API is also generically useful for any ECDSA use case, e.g., integration with other blockchains such as Ethereum.

We next give a high-level overview of the above mentioned technology behind the direct Bitcoin integration. For details, we refer the reader to the [Bitcoin page on the Internet Computer wiki](https://wiki.internetcomputer.org/wiki/Bitcoin_integration) as well as the [threshold ECDSA documentation page](../t-ecdsa/t-ecdsa-how-it-works.md).

## Protocol-level integration of the IC with the Bitcoin network

We integrated the Internet Computer Protocol with the Bitcoin protocol to obtain a direct technical integration between the two networks. This integration can be activated on any number of Internet Computer subnets. At the beginning, there will only be one dedicated Bitcoin-activated subnet and requests to the Bitcoin API from canisters on any subnet will be routed to this single Bitcoin-activated subnet using the IC's XNet communication capabilities. The integration serves two key purposes:

-   Obtaining the Bitcoin UTXO set and keeping it on chain in the replicated state of the Internet Computer to be able to answer queries for UTXO sets and balances of Bitcoin accounts issued by canisters.
-   Accepting signed Bitcoin transactions of canisters and submitting them to the Bitcoin network.

![Bitcoin Integration](../_attachments/bitcoin_integration.png)

### Components

On a Bitcoin-activated subnet, a **BTC canister** (Bitcoin canister), implemented as a regular NNS-managed Wasm canister is made accessible to canisters via an API of the management canister, i.e, the interface is implemented as part of the replica. The BTC canister holds the on-chain Bitcoin-related state: the UTXO set, the most recent Bitcoin blocks to allow for fork resolution ("unstable blocks"), and outgoing transactions.

A **Bitcoin adapter** process at the networking layer connects to nodes of the Bitcoin network, much like a regular Bitcoin node does.

### Maintaining the Bitcoin UTXO set

The BTC canister and adapter are integrated and communicate with each other via the IC's protocol stack: The BTC canister requests successor blocks to the latest Bitcoin blocks it has received from the Bitcoin adapter. The adapter on each replica of the subnet obtains the requested blocks from the Bitcoin network and the adapter of a block-making replica provides a requested block to the Bitcoin canister through consensus. The Bitcoin canister processes blocks in that it validates the proof of work, extracts the block's transactions, extracts the UTXOs from the transactions, and updates the UTXO set maintained in replicated state to reflect the UTXOs consumed and the UTXOs created by the transactions. Both the UTXO set and a set of recent blocks not yet absorbed into the UTXO set (the unstable blocks) are used to respond to UTXO and balance requests.

Significant complexity of the implementation is in the area of securely resolving forks and protecting against various kinds of attacks. For example, in order to be able to resolve forks, the Bitcoin canister needs to maintain a certain number of Bitcoin blocks which are not yet absorbed into the maintained UTXO set, but UTXOs therein must be efficiently considered for calculating the UTXOs of a given address in addition to those in the UTXO set.

### Submitting transactions

Canisters can submit Bitcoin transactions to the Bitcoin canister using the corresponding management canister API. Doing so queues the transactions for being submitted to the Bitcoin network. In every subnet round, Adapters obtain the pending transactions from the Bitcoin canister and queue them for being submitted asynchronously to the Bitcoin network. This leads to an efficient and quick distribution of transactions in the Bitcoin network as every replica of the subnet submits transactions via multiple connected nodes of the Bitcoin network.

## Chain-key ECDSA signatures: a novel threshold ECDSA protocol suite

Chain-key signatures are realized with a novel threshold ECDSA protocol suite, i.e., an implementation of the ECDSA signature protocol using threshold cryptography. In a threshold ECDSA protocol, the private ECDSA key is secret shared between multiple parties and only an eligible quorum of the parties can generate a signature using their respective private key shares. The private key never exists in reconstructed form, but only in its secret-shared form. Key generation generates private key shares for the parties. But chain-key signing comprises much more than just the threshold ECDSA protocol, e.g., cryptographic multi-party computation protocols for secure threshold key generation and periodic key resharing for hardening the overall security.

The ECDSA chain-key signing protocol currently implemented uses a single master private key from which keys for canisters can be derived using BIP-32-like key derivation. Each canister has one such root key derived using the canister id and an arbitrary number of additional canister keys can be derived using a backward-compatible extension of BIP-32.

ECDSA chain-key signing is deployed on one signing subnet of the Internet Computer initially that will answer signing requests of canisters. The signing subnet enforces that only the canister that controls a key may request signatures with this key. All API calls made by canisters need to go through XNet traffic to reach the ECDSA subnet and thus incur the related extra latency accordingly to obtain a signature.

Canisters can query their own or other canisters' public keys, including further derived public keys of canisters. Canisters can request signatures with private keys they control, i.e., their root private key and derived private keys. For requests of public keys or signatures with derived keys, a derivation path can be specified in the respective API or the derivation can be done using BIP-32. You can find more details on the [ECDSA chain-key signatures page](../t-ecdsa/t-ecdsa-how-it-works.md).

## Deployment architecture

The Bitcoin functionality will be activated on a single subnet of the IC and API calls from canisters to the Bitcoin API are routed via XNet communication, thus some extra latency is incurred. The Bitcoin canister may, if needed, in the future be additionally activated on some (application) subnets to avoid the additional XNet latency and to be able to respond to more requests per time unit.

Threshold ECDSA requests will equally be answered by a single active signing subnet, another subnet will back up the private key in secret-shared form for disaster recovery.

## Development, pre-production, and production environment

The Bitcoin functionality including ECDSA chain-key signatures is available in all stages required for the development life cycle on the IC:
-   The **IC SDK** for local development of canisters.
-   IC support as the pre-production environment for final testing on **Bitcoin testnet**.
-   IC support as the production environment for the release using the **Bitcoin mainnet**.

### Local SDK

In the typical canister development workflow, canisters on the IC are compiled and run in the local environment using the [IC SDK](../../setup/install/index.mdx) during their development. Thus, the IC SDK is the first stage, or environment, of the development workflow. The IC SDK has been enabled to support both the Bitcoin integration and threshold ECDSA management canister APIs.

In contrast to the IC deployments of the feature, which integrate with Bitcoin Testnet and Bitcoin Mainnet, respectively, the SDK integrates with a locally-running `bitcoind` node in regression testing (regtest) mode. Using `bitcoind` in regtest mode is the preferred way for Bitcoin development. To facilitate our developers as best as possible, we integrated the IC SDK with `bitcoind` in regtest mode to bring the best Bitcoin development experience to the IC. Both development and automated testing of smart contracts are first done in the local environment with this setup.

The Bitcoin adapter of the single replica running the local SDK environment connects to the local `bitcoind` node instead of multiple nodes of Bitcoin Testnet or Mainnet. To see the relevant flags on dfx, please look at the output of `dfx start --help`.

### Bitcoin testnet on the IC

Once a smart contract is ready for acceptance testing, it is deployed on the IC mainnet (recall, there is no public IC testnet), still using the Bitcoin API set to connect to the Bitcoin testnet. This is the same setting as used in local development. This setup is used to perform the acceptance testing of the dApp, using the Bitcoin testnet and a test chain-key ECDSA key, i.e., no real value is at stake.

### Bitcoin mainnet on the IC

The final stage of development of a Bitcoin smart contract is its deployment on the IC with the Bitcoin API set to use Bitcoin Mainnet. This is the final production environment for the smart contract and is now available.

## API fees & Pricing

The costs of API calls in cycles and USD for the Bitcoin Testnet and Bitcoin Mainnet APIs is presented in the following tables. As a general principle for the Bitcoin API, some API calls must have a minimum amount of cycles attached to the call as indicated in the column *Minimum cycles to send with call*. Cycles not consumed by the call are returned to the caller. Requiring a relatively large minimum number of cycles makes it possible to change the pricing of API calls without breaking existing smart contracts when the Bitcoin subnet grows in terms of replication factor in the future. The call for submitting a Bitcoin transaction to the Bitcoin network does not require extra cycles to be attached as the charged cost is independent of the replication factor of the subnet.

The cost per API call in USD uses the USD/XDR exchange rate of November 23, 2022.

### Bitcoin Testnet

| Transaction                          | Description                                                                                                    | Price (Cycles) | Price (USD) | Minimum cycles to send with call |
|--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------------------------|-----------------------------|------------------|
| Bitcoin UTXO set for an address      | For retrieving the UTXO set for a Bitcoin address (`bitcoin_get_utxos`)                                        | 20,000,000 + 0.4 cycles per Wasm instruction | $0.00002617720 + Wasm instruction cost | 4,000,000,000 |
| Bitcoin fee percentiles              | For obtaining the fee percentiles of the most recent transactions (`bitcoin_get_current_fee_percentiles`)       | 4,000,000                 | $0.00000523544                    | 40,000,000 |
| Bitcoin balance for an address       | For retrieving the balance of a given Bitcoin address (`bitcoin_get_balance`)                                  | 4,000,000                 | $0.00000523544                    | 40,000,000 |
| Bitcoin transaction submission       | For submitting a Bitcoin transaction to the Bitcoin network, per transaction (`bitcoin_send_transaction`)      | 2,000,000,000             | $0.00261772000                    | n.a.       |
| Bitcoin transaction payload          | For submitting a Bitcoin transaction to the Bitcoin network, per byte of payload (`bitcoin_send_transaction`)  | 8,000,000                 | $0.00001047088                    | n.a.       |

### Bitcoin Mainnet

| Transaction                          | Description                                                                                                    | Price (Cycles) | Price (USD) | Minimum cycles to send with call |
|--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------------------------|-----------------------------|------------------|
| Bitcoin UTXO set for an address      | For retrieving the UTXO set for a Bitcoin address (`bitcoin_get_utxos`)                                        | 50,000,000 + 1 cycle per Wasm instruction | $0.00006544300 + Wasm instruction cost | 10,000,000,000 |
| Bitcoin fee percentiles              | For obtaining the fee percentiles of the most recent transactions (`bitcoin_get_current_fee_percentiles`)       | 10,000,000                 | $0.00001308860 | 100,000,000 |
| Bitcoin balance for an address       | For retrieving the balance of a given Bitcoin address (`bitcoin_get_balance`)                                  | 10,000,000                 | $0.00001308860                    | 100,000,000 |
| Bitcoin transaction submission       | For submitting a Bitcoin transaction to the Bitcoin network, per transaction (`bitcoin_send_transaction`)      | 5,000,000,000             | $0.00654430000                    | n.a.       |
| Bitcoin transaction payload          | For submitting a Bitcoin transaction to the Bitcoin network, per byte of payload (`bitcoin_send_transaction`)  | 20,000,000                 | $0.00002617720                    | n.a.       |

:::note
Note that the `bitcoin_get_utxos` call is charged through a baseline fee that amortizes part of the Bitcoin block processing and the cycles cost of the actually-used Wasm instructions. This is the fairest way of charging because a flat fee would be less fair for requests returning a small number of UTXOs, while a fee scaling with the number of UTXOs is hard to define in a clean way. A few informal test measurement have yielded Wasm execution fees anywhere in the range from less than 200K to more than 1,000K cycles per returned UTXO and in addition 30M-50M cycles for processing of the unstable blocks. This wide variance per UTXO was the reason to not use a charging approach based on the number of UTXOs returned, but it should give you a rough indication of what to expect to pay in terms of fees. For queries with a small number of UTXOs, you can expect around 100M cycles as fee to be deducted from the provided cycles on the call for a majority of calls.
:::

## Resources

To start building your own apps with Bitcoin see the following tutorials:

- [Deploying your first Bitcoin dapp](../../../samples/deploying-your-first-bitcoin-dapp.md).

- [Local development](./local-development.md).

-->
