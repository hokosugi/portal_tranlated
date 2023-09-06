---

title: Chain-Key Cryptography
abstract:
shareImage: /img/how-it-works/chain-key-cryptography.jpg
slug: chain-key-technology
---
# チェーン・キー暗号

## 署名

*電子署名スキームは*、非常に伝統的な公開鍵暗号方式の一種です。（署名者のみが保持する）秘密鍵を使用してメッセージに対する電子署名を生成し、（誰もが利用可能な）公開鍵を使用してメッセージに対する電子署名を効率的に検証することができます。このようなスキームで達成される基本的なセキュリティ特性は、対応する秘密鍵で署名アルゴリズムを明示的に呼び出さなければ、メッセージに有効な署名を作成できないということです。

*閾値署名スキームは*、秘密の署名鍵が一箇所に保存されることのない（単一障害点となる）デジタル署名スキームです。むしろ、秘密鍵は効果的に*秘密共有に*分割され、各秘密共有は異なるマシンに保存されます。メッセージに署名するには、これらのマシンがメッセージに署名することに同意し、分散方式で電子署名を生成するために互いに調整する必要があります（重要なことは、一箇所に秘密署名鍵を再構築することなく）。

## チェーンキー技術

閾値署名方式は古くからある技術ですが、ICはこの技術を設計の中核に完全に統合した最初のブロックチェーンです。ブロックチェーン出力の効率的な検証、ICトポロジーの自律的進化、予測不可能で偏りのない擬似乱数のソースcanisters 。

IC内の各サブネットは、このような閾値署名スキームの公開検証鍵と関連付けられています。

1.  最も重要なことは、この公開鍵は、外部ユーザーからのイングレス・メッセージに対する応答や、あるcanister から別のユーザーへのメッセージなど、ICの出力を検証するために使用される可能性があるということです。これはICと他のブロックチェーンとの基本的な違いの1つです。他のブロックチェーンでは、生成ブロックからプロトコル全体を実行することでしかステートを検証できませんが、ICでは1つのデジタル署名を検証するだけで検証できます。そのため、これはInternet Computer で前例のないスケーラビリティを可能にする重要な技術の1つです。
2.  この公開鍵は、サブネットのステート全体を定期的に検証するためにも使用されます。これにより、サブネットに新しいノードを追加したり、クラッシュしたノードがすぐに他のノードに追いつけるようにするなど、さまざまな機能が可能になります。これにより、ICのスケーラビリティが向上し、NNSによって調整されたICのトポロジーが時間とともに自律的に進化することが可能になります。

さらに、これらの閾値署名は、*予測不可能な擬似乱数の*ソースを作成する方法として使用されます：

1.  どのスマートコントラクトでも利用可能な、予測不可能で偏りのない擬似乱数のソースとして。これはブロックチェーンの世界では全くユニークな機能であり、他のブロックチェーンでは実装不可能なアプリケーション（例えばNFTくじ）を可能にします；
2.  ICコンセンサスプロトコルのリーダーを擬似乱数で選択するメカニズムとして、コンセンサスの効率性と公平性を高めます。

## 実装

ICが実装する閾値署名スキームは、よく知られた[BLS署名スキームの](https://en.wikipedia.org/wiki/BLS_digital_signature)閾値バージョンです。BLS署名方式を使用する理由の一つは、非常にシンプルで効率的な閾値署名プロトコルを実現できる唯一の方式だからです。実際、秘密署名鍵のシェアを持つマシンは、非常に簡単にメッセージに対する署名のシェアを生成することができ、これらの署名シェアを組み合わせてメッセージに対するBLS署名を形成することができます。

BLS 署名方式を使用するもう一つの理由は、署名が*一意で*あることです。つまり、ある公開鍵とメッセージに対して、そのメッセージに有効な署名は一つしかないということです。スマートコントラクトが擬似乱数を要求した後（その前ではありません！）、特別なメッセージに対する署名が生成され、この署名がハッシュ関数に渡され、必要な擬似乱数が生成されるシードが導出されます。署名スキームのセキュリティ特性により、このシードも導出された擬似乱数も予測されたり偏ったりすることはありません。

閾値BLSによる署名は非常に簡単ですが、秘密署名鍵の共有を生成・配布するための安全で分散化されたプロトコル、つまりDKG（分散鍵生成プロトコル）を設計することは依然として課題です。DKGの設計に関する研究はかなり行われていますが、文献にあるDKGプロトコルの大部分は、Internet Computer の厳しい要件を満たしていません。その理由は、*同期ネットワークを*仮定しているか（メッセージが予期せず遅延した場合、プロトコルが失敗するか、安全でなくなることを意味します）、*ロバスト性を*提供していないか（*1つの*ノードがクラッシュした場合、署名を生成する能力が完全に失われることを意味します）、または*その両方*です。ICではこれらの仮定はどちらも受け入れられません。多くの欠陥ノードを持つ*非同期ネットワーク*であっても、セキュリティと有効性は維持されなければなりません。

DFINITY は、*非同期ネットワーク*上で動作する[新しいDKGプロトコルを](https://eprint.iacr.org/2021/339)設計、分析、実装しました。このプロトコルは非常に*堅牢*であり（サブネット内のノードの3分の1までがクラッシュまたは破損しても成功します）、許容可能なパフォーマンスを提供します。新しい鍵を生成するだけでなく、このプロトコルを使って既存の鍵を再共有することもできます。この機能は、時間の経過とともにサブネットのメンバーシップが変化した場合に、ICトポロジーの自律的な進化を可能にするために不可欠です。

[チェーン・キー暗号：の背後にある科学的ブレークスルーInternet Computer](https://medium.com/dfinity/chain-key-technology-one-public-key-for-the-internet-computer-6a3644901e28)

[ Internet Computer とビットコイン・ネットワークの統合](https://www.youtube.com/watch?v=TtVo3krjARI)

[非対話型分散鍵生成に関するビデオ](https://www.youtube.com/watch?v=gKUi-2T7tdc)

[応用暗号：非対話型分散鍵生成の紹介](https://medium.com/dfinity/applied-crypto-one-public-key-for-the-internet-computer-ni-dkg-4af800db869d)

[NIDKGホワイトペーパー](https://eprint.iacr.org/2021/339)

[![Watch youtube video](https://i.ytimg.com/vi/vUcDRFC09J0/maxresdefault.jpg)](https://www.youtube.com/watch?v=vUcDRFC09J0)

<!---


# Chain-Key Cryptography

## Signatures

A _digital signature scheme_ is a very traditional type of public-key cryptosystem, in which a secret key (held only by the signer) is used to generate a digital signature on a message, and a public key (available to everyone) may be used to efficiently verify a digital signature on a message. The basic security property achieved by such a scheme is that a valid signature on a message cannot be created without explicitly invoking the signing algorithm with the corresponding secret key.

A _threshold signature scheme_ is a digital signature scheme where the secret signing key is never stored in one location (which would become a single point of failure). Rather, the secret key is effectively split up into _secret shares_, and each secret share is stored on a different machine. To sign a message, these machines must agree to sign the message and coordinate with one another to generate a digital signature in a distributed fashion (importantly, without ever reconstructing the secret signing key in one location).

## Chain-Key Technology

While threshold signature schemes is a technology that has been around for a long time, the IC is the first blockchain to fully integrate this technology in the core of its design. As described above, the technology enables _chain-key cryptography_ and all of its benefits -- Efficient verification of blockchain outputs, autonomous evolution of the IC topology, a source of unpredictable and unbiasable pseudo-random numbers for canisters.

Each subnet in the IC is associated with the public verification key of such a threshold signature scheme.

1. Most importantly, this public key may be used to verify the outputs of the IC, including responses to ingress messages from external users, as well as messages from one canister to another. This is one of the fundamental differences between the IC and other blockchains: the state of other blockchains can only be validated by running the entire protocol from the genesis block, whereas on the IC, it can be validated just by verifying a single digital signature. As such, this is one of the key technologies that enables unprecedented scalability on the Internet Computer.
2. This public key is also used to verify the entire state of a subnet at regular intervals, which enables a number of functions, such as adding new nodes to a subnet and allowing crashed nodes to quickly catch up to the rest. This enhances both the scalability of the IC and is crucial to enable the topology of the IC to autonomously evolve over time as orchestrated by the NNS.

In addition, these threshold signatures are used as a way to create a source of _unpredictable pseudo-random numbers_, which is used in two ways:

1. as a source of unpredictable and unbiasable pseudo-random numbers available to any smart contract, which is a totally unique feature in the blockchain world that enables applications that would be impossible to implement on other blockchains (for example, an NFT raffle);
2. as a mechanism for pseudo-randomly selecting the leader in the IC consensus protocol, which enhances the efficiency and fairness properties of consensus.

## Implementation

The threshold signature scheme implemented by the IC is a threshold version of the well-known [BLS signature scheme](https://en.wikipedia.org/wiki/BLS_digital_signature). One reason for using the BLS signature scheme is that it is the only one that yields a threshold signing protocol that is very simple and efficient. Indeed, a machine holding a share of the secret signing key can very easily generate a share of a signature on a message, and these signature shares can be combined to form a BLS signature on a message – no further interaction between these machines is required.

Another reason for using the BLS signature scheme is that signatures are _unique_, meaning that for a given public key and message, there is only one valid signature on that message. This unique-signature property is essential for the application to generating unpredictable and unbiased pseudo-random numbers for smart contracts: after a smart contract requests a pseudo-random number (and not before!), a signature on a special message is generated, and this signature is passed through a hash function to derive a seed from which the required pseudo-random numbers are generated. By the security property of the signature scheme, neither this seed nor the derived pseudo-random numbers can be predicted or biased.

While signing with threshold BLS is quite straightforward, designing a secure, decentralized protocol for generating and distribution the shares of the secret signing key – that is, a DKG, or Distributed Key Generation protocol – remains a challenge. While there has been quite a bit of research on DKG design, the vast majority of DKG protocols in the literature do not meet the demanding requirements of the Internet Computer, in that they either assume a _synchronous network_ (meaning that the protocols will fail or become insecure if messages are unexpectedly delayed) or provide _no robustness_ (meaning that the ability to produce signatures is completely lost if a _single_ node should crash) or _both_. Neither of these assumptions are acceptable on the IC: security and liveness must hold even an an _asynchronous network_ with many faulty nodes.

DFINITY has designed, analyzed, and implemented [a new DKG protocol](https://eprint.iacr.org/2021/339) that works over an _asynchronous network_ and is quite _robust_ (it will still succeed if up to a third of the nodes in a subnet are crashed or corrupt) while still delivering acceptable performance. In addition to generating a new key, this protocol can also be used to reshare an existing key. This functionality is essential to enable autonomous evolution of the IC topology as subnet membership changes over time.

[Chain Key Cryptography: The Scientific Breakthrough Behind the Internet Computer](https://medium.com/dfinity/chain-key-technology-one-public-key-for-the-internet-computer-6a3644901e28)

[Integrating The Internet Computer and Bitcoin Networks](https://www.youtube.com/watch?v=TtVo3krjARI)

[Video on non-interactive distributed key generation](https://www.youtube.com/watch?v=gKUi-2T7tdc)

[Applied Crypto: Introducing Noninteractive Distributed Key Generation](https://medium.com/dfinity/applied-crypto-one-public-key-for-the-internet-computer-ni-dkg-4af800db869d)

[NIDKG White Paper](https://eprint.iacr.org/2021/339)

[![Watch youtube video](https://i.ytimg.com/vi/vUcDRFC09J0/maxresdefault.jpg)](https://www.youtube.com/watch?v=vUcDRFC09J0)

-->
