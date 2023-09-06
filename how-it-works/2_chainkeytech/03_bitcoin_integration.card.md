---

title: Bitcoin integration
abstract: This feature enables canisters to hold and transfer bitcoin, making it possible to build Bitcoin smart contracts.
coverImage: /img/how-it-works/btc-content.600x300.webp
---
![](/img/how-it-works/btc-content.600x300.webp)

# ビットコインの統合

Internet Computer 、ビットコインの統合は2つの柱から成り立っています：チェーンキー署名と、Internet Computer ノードとビットコインのピアツーピアネットワーク間の直接対話です。
チェーンキー署名により、canisters が独自のビットコインアドレスを持ち、これらのアドレスが保持するビットコインを使用する有効なトランザクションを作成することが可能になる一方、
 Internet Computer とビットコインネットワーク間の直接メッセージ交換は、Internet Computer
 のアドレス残高などのビットコインブロックチェーンステートに関する情報を維持し、canisters から発信されるビットコイントランザクションをビットコインネットワークに送信する役割を果たします。

[より深く](/how-it-works/bitcoin-integration/)

<!---


![](/img/how-it-works/btc-content.600x300.webp)

# Bitcoin integration

The Bitcoin integration on the Internet Computer rests on two pillars: Chain-key signatures and a direct interaction between Internet Computer nodes and the Bitcoin peer-to-peer network.
While chain-key signatures make it possible for canisters to have their own Bitcoin addresses and create valid transactions spending bitcoins held by these addresses,
the direct message exchange between the Internet Computer and the Bitcoin network serves to maintain information about the Bitcoin blockchain state, such as address balances, in the Internet Computer
and to transmit Bitcoin transactions originating from canisters to the Bitcoin network.

[Go deeper](/how-it-works/bitcoin-integration/)

-->
