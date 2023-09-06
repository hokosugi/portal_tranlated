---

title: Certified Variables
abstract:
shareImage: /img/how-it-works/certified-variables.jpg
slug: response-certification
---
# 認証された変数

なぜブロックチェーンから得たデータを信用するのですか？すべてのトランザクションとそれに続くスマートコントラクトのステートへの変更は、ブロックチェーンのコンセンサスプロトコルを介して行われます。しかし、コンセンサス・プロトコルに基づいて正しさを検証するのは面倒です。[ビットコインのSPVや](https://en.bitcoinwiki.org/wiki/Simplified_Payment_Verification) [イーサリアムのライトクライアントなど](https://geth.ethereum.org/docs/interface/les)、より効率的な仕組みの場合でも、クライアントはブロックヘッダーのダウンロードや検証など、かなりの量の作業を行わなければなりません。このため、モバイルアプリケーションやウェブアプリケーションなど、稼働時間やリソースが制限されているアプリケーションでは、中央集権的な仲介者にデフォルトを設定することなくブロックチェーンのデータを操作することは困難です。

Internet Computer は違います：[連鎖鍵暗号を](/how-it-works/chain-key-technology)使用することで、Internet Computer は、Internet Computer に属する単一の永続的な公開鍵で検証可能な[デジタル署名を](https://en.wikipedia.org/wiki/Digital_signature)生成することができます。しかし、従来のデジタル署名とは異なり、秘密鍵の材料は*決して*単一の場所に存在しません。秘密鍵は常に多くの異なるノード間で安全に分散されており、有効な署名はこれらのノードの大多数が暗号化プロトコルで協力した場合にのみ生成されます。クライアント・アプリケーションは、Internet Computer の公開鍵を埋め込むだけで、Internet Computer から受信したすべての認証済み応答を、応答を受信した特定のノードを信頼することなく、即座に検証することができます。

Internet Computer の認証機能は、*認証変数を通して* canisters に公開されます。アプリケーションの観点からは、コンセンサスを経たトランザクション中にcanister のステートが変更されると、canister へのアップデートコール中に認証変数を設定することができます。証明書はその後のクエリーコールで読み取ることができるため、canister はクライアントのリクエストに信頼できる方法で応答することができますが、コンセンサスによる追加の遅延は発生しません。認証済み変数は、[認証済みアセットや](/how-it-works/asset-certification/) [インターネットIDなど](/how-it-works/web-authentication-identity/)、Internet Computer の高度な機能の多くも支えています。

技術的には、各サブネット（canister ）は、サブネットによって認証される32バイトの値を1つ指定できます。[Merkleツリーや](https://en.wikipedia.org/wiki/Merkle_tree)、より一般的には[認証されたデータ構造などの](https://cs.brown.edu/research/pubs/pdfs/2003/Tamassia-2003-ADS.pdf)よく知られた概念は、この単一の32バイト値から任意のデータ量に認証を拡張するために使用することができます。[certified-mapの](https://github.com/dfinity/cdk-rs/tree/main/library/ic-certified-map)ようなライブラリは、開発者がこの機能に簡単にアクセスできるようにします。

[![Watch youtube video](https://i.ytimg.com/vi/3mZHEfICi_U/maxresdefault.jpg)](https://www.youtube.com/watch?v=3mZHEfICi_U)

[ Internet Computer レスポンスが本物として認証される方法](https://medium.com/dfinity/how-internet-computer-responses-are-certified-as-authentic-2ff1bb1ea659?)

[認証された変数とアセット](https://assets.ctfassets.net/ywqk17d3hsnp/7AaD21HKM8kV3GguC8qWB4/5023bc305edb6fa3bd4aa593e72335c2/2021-06-10_Certified_variables_and_assets__1_.pdf)

<!---


# Certified Variables

Why do you trust data obtained from a blockchain? Well, all transactions and the subsequent changes to smart contract state made their way through the blockchain consensus protocol, which guarantees correctness as long as the underlying trust assumptions hold. But verifying correctness based on the consensus protocol is tedious: A client has to download and validate the blockchain data. Even in the case of more efficient mechanisms such as [Bitcoin's SPV](https://en.bitcoinwiki.org/wiki/Simplified_Payment_Verification) or [Ethereum's light clients](https://geth.ethereum.org/docs/interface/les), clients still have to perform significant amounts of work, such as downloading and validating block headers. This makes it difficult for applications with restricted uptime and resources, such as mobile or web applications, to operate on blockchain data without defaulting to centralized intermediaries.

The Internet Computer is different: Using [chain-key cryptography](/how-it-works/chain-key-technology), the Internet Computer can generate [digital signatures](https://en.wikipedia.org/wiki/Digital_signature) that can be validated with a single, permanent public key belonging to the Internet Computer. Unlike with traditional digital signatures, however, the private key material _never_ exists in a single place. It is always securely distributed between many different nodes, and valid signatures can only be generated when the majority of these nodes cooperates in a cryptographic protocol. A client application only has to embed the Internet Computer's public key, and can immediately validate all certified responses it receives from the Internet Computer, without putting any trust into the particular node it received the response from.

The Internet Computer's certification feature is exposed to canisters through _certified variables_. From an application perspective, certified variables can be set during an update call to a canister, when the canister changes its state during a transaction that went through consensus. The certificate can then be read in a subsequent query call, so the canister can respond to a client's request in a trustworthy way but without incurring the additional delay of consensus. Certified variables also underlie many of the Internet Computer's advanced features such as [certified assets](/how-it-works/asset-certification/) and [Internet Identity](/how-it-works/web-authentication-identity/).

More technically, each canister can specify a single 32-byte value that will be certified by the subnet. Well-known concepts such as [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree) or, more generally, [authenticated data structures](https://cs.brown.edu/research/pubs/pdfs/2003/Tamassia-2003-ADS.pdf) can be used to extend the certification from this single 32-byte value to arbitrary amounts of data. Libraries such as [certified-map](https://github.com/dfinity/cdk-rs/tree/main/library/ic-certified-map) make the feature easily accessible for developers.

[![Watch youtube video](https://i.ytimg.com/vi/3mZHEfICi_U/maxresdefault.jpg)](https://www.youtube.com/watch?v=3mZHEfICi_U)

[How Internet Computer Responses Are Certified as Authentic](https://medium.com/dfinity/how-internet-computer-responses-are-certified-as-authentic-2ff1bb1ea659?)

[Certified Variables & Assets](https://assets.ctfassets.net/ywqk17d3hsnp/7AaD21HKM8kV3GguC8qWB4/5023bc305edb6fa3bd4aa593e72335c2/2021-06-10_Certified_variables_and_assets__1_.pdf)

-->
