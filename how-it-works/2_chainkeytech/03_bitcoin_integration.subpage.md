---

title: Bitcoin Integration
abstract:
shareImage: /img/how-it-works/btc-content.600x300.jpg
slug: bitcoin-integration
---
# ビットコインの統合

Internet Computer 上のビットコイン統合により、
ビットコイン・スマートコントラクトの作成が初めて可能になります。つまり、
Internet Computer 、実際のビットコインを使用するcanisters スマートコントラクトの形で実行されます。
この統合は、2つの重要なコンポーネントによって可能になります。

最初のコンポーネントは[連鎖鍵署名で](/how-it-works/threshold-ecdsa-signing/)、
、すべてのcanister が ECDSA 公開鍵を取得し、
これらの鍵に関する署名を安全な方法で取得できるようにします。
ビットコインアドレスは ECDSA 公開鍵と結びついているため、canister 上の ECDSA 公開鍵
を持つことは、canister が独自のビットコインアドレスを導出できることを意味します。
 canister が[IC ECDSA インタフェースを](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-sign_with_ecdsa)使用してその公開鍵のいずれに対しても署名を要求できることを考えると、canister はそのビットコインアドレスのいずれかから他のアドレスにビットコインを移動させる有効な署名付きビットコイントランザクショ ンを作成できます。

2つ目のコンポーネントは、ネットワークレベルでのビットコインとの統合です。Internet Computer レプリカ
には、レプリカプロセスの外部プロセスである、いわゆる*ビットコインアダプターを*インスタンス化する機能があります。
最初のステップで、ビットコインアダプターはビットコインのピアツーピアネットワークのノードに関する情報を収集し、十分な数のビットコインノードが発見されると、ランダムに選ばれた5つのビットコインノードに接続します。サブネット内の各レプリカがこの操作を実行するため、サブネット全体がビットコインネットワークへの多くの、ほとんど異なる接続を持ちます。
ビットコインアダプタは、ビットコインブロックチェーンに関する情報を取得するために、ビットコインピアツーピアプロトコルの規格を使用します。各ビットコインアダプターは、完全なビットコインブロックヘッダーチェーンを追跡します。

同時に、ビットコインアダプターはレプリカプロセスと通信し、レプリカ内の現在のビットコインのステートについて学習します。ビットコインアダプターが、レプリカから提供されたブロックヘッダーハッシュをローカルで利用可能なブロックヘッダーチェーンと比較することで、ビットコインブロックがまだレプリカで利用可能になっていないことを知ると、ビットコインアダプターは、接続されているビットコインノードから次の欠けているブロックを要求し、受信するとレプリカに転送します。

レプリカ内部では、ネットワーキング層で受信されたビットコインブロックはICブロックにパックされ、コンセンサス層とメッセージルーティング層で処理され、最終的に実行層の*ビットコインcanister*。ビットコインcanister は、システムサブネットで動作するcanister で、その目的は
ビットコイン関連の機能を他のcanisters に提供することです。特に、ビットコインブロックチェーンのステートに関する情報を保持し、任意のアドレスの残高や未使用のトランザクション出力（UTXO）など、この情報に他のcanisters がアクセスできるようにします。さらに、ブロックに入れられた直近のビットコイン取引の手数料もビットコインcanister から要求できます。

Bitcoincanister は、最後の重要な機能も提供します：それは、canisters がビットコイントランザクションを送信するためのエンドポイントを提供し、ネットワーキングレイヤーで利用可能になり、そこでビットコインアダプターに転送されます。ビットコインアダプターは、接続されているビットコインピアにトランザクションをアドバタイズし、要求に応じてトランザクションを転送します。サブネット内の各レプリカがこのステップを実行するため、すべてのトランザクションはビットコインネットワーク内で迅速に分散されます。

[IC管理canister インターフェースは](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-management-canister)、すべてのBitcoin統合エンドポイントへのアクセスを提供します。
それらの使用は、以下のサンプルフローで説明されています：

<figure>
<img src="/img/how-it-works/bitcoin-integration-flow.png" alt="Bitcoin integration sample flow" title="Bitcoin integration sample flow" align="center" style="width:600px">
</figure>

canister
次に、手数料エンドポイントを呼び出して最近の手数料を取得します。 最後に、 UTXOの一部を入力として使用してビットコイン取引を構築します。各入力について、ECDSA APIが呼び出され、必要な署名が取得されます。最後に、トランザクションが送信されます。
 canister 

[Bitcoin 統合 wiki ページ](https://wiki.internetcomputer.org/wiki/Bitcoin_integration)。

[Bitcoincanister ソースコード](https://github.com/dfinity/bitcoin-canister)。

[動議提案20586](https://dashboard.internetcomputer.org/proposal/20586)。

[ビットコイン統合](https://medium.com/dfinity/btc-icp-mainnet-integration-complete-bringing-smart-contract-functionality-to-bitcoin-9bd81d4ce0ba)の開始

<!---


# Bitcoin Integration

The Bitcoin integration on the Internet Computer makes it possible for the first time to
create Bitcoin smart contracts, that is, smart contracts in the form of canisters running on the
Internet Computer that make use of real bitcoin.
This integration is made possible through two key components.

The first component is [chain-key signatures](/how-it-works/threshold-ecdsa-signing/),
which enables every canister to obtain ECDSA public keys and get signatures with respect to
these keys in a secure manner.
Since Bitcoin addresses are tied to ECDSA public keys, having ECDSA public keys
on a canister means that the canister can derive its own Bitcoin addresses. Given that
the canister can request signatures for any of its public keys using the [IC ECDSA interface](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-sign_with_ecdsa), a canister can create Bitcoin transactions with valid signatures that move bitcoins from any of its Bitcoin addresses to any other address.

The second component is the integration with Bitcoin at the network level. The Internet Computer replicas
have the capability to instantiate a so-called _Bitcoin adapter_, a process external to the replica process.
In a first step, the Bitcoin adapter collects information about nodes in the Bitcoin peer-to-peer network and, once sufficiently many Bitcoin nodes are discovered, it connects to 5 randomly chosen Bitcoin nodes. Since each replica in the subnet performs this operation, the entire subnet has many, mostly distinct connections to the Bitcoin network.
The Bitcoin adapter uses the standard Bitcoin peer-to-peer protocol to get information about the Bitcoin blockchain. Each Bitcoin adapter keeps track of the full Bitcoin block header chain.

At the same time, the Bitcoin adapter communicates with the replica process to learn about the current Bitcoin state inside the replica. If the Bitcoin adapter learns that a Bitcoin block has not been made available to the replica yet by comparing the block header hashes provided by the replica against its locally available block header chain, the Bitcoin adapter requests the next missing block from the connected Bitcoin nodes and forwards it to the replica upon receipt.

Inside the replica, Bitcoin blocks received at the Networking layer are packed into IC blocks and processed in the Consensus and Message Routing layers and finally made available to the _Bitcoin canister_ in the Execution layer. The Bitcoin canister is a canister running in a system subnet whose purpose is to provide
Bitcoin-related functionality to other canisters. In particular, it keeps information about the Bitcoin blockchain state and makes this information accessible to other canisters, such as the balance and unspent transaction outputs (UTXOs) for any address. Additionally, the fees of the most recent Bitcoin transactions that were put into blocks can be requested from the Bitcoin canister as well.

The Bitcoin canister also offers the last piece of crucial functionality: It provides an endpoint for canisters to send Bitcoin transactions, which are made available on the Networking layer where they are forwarded to the Bitcoin adapter. The Bitcoin adapter in turn advertises the transactions to its connected Bitcoin peers and transfers the transaction upon request. Since each replica in the subnet performs this step, every transaction can be dispersed quickly in the Bitcoin network.

The [IC management canister interface](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-management-canister) provides access to all Bitcoin integration endpoints.
Their use is illustrated in the following sample flow:

<figure>
<img src="/img/how-it-works/bitcoin-integration-flow.png" alt="Bitcoin integration sample flow" title="Bitcoin integration sample flow" align="center" style="width:600px">
</figure>

In this figure, a canister first requests the balance and then the UTXOs of a Bitcoin address.
Next, it calls the fee endpoint to get recent fees.
Lastly, the canister builds a Bitcoin transaction using some of the UTXOs as inputs. For each input, the ECDSA API is called to obtain the required signatures. Finally, the transaction is submitted.

[Bitcoin integration wiki page](https://wiki.internetcomputer.org/wiki/Bitcoin_integration).

[Bitcoin canister source code](https://github.com/dfinity/bitcoin-canister).

[Motion Proposal 20586](https://dashboard.internetcomputer.org/proposal/20586).

[Bitcoin integration goes live](https://medium.com/dfinity/btc-icp-mainnet-integration-complete-bringing-smart-contract-functionality-to-bitcoin-9bd81d4ce0ba).

-->
