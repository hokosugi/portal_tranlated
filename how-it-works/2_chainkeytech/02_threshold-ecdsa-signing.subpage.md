---

title: Chain-key signatures
abstract:
shareImage: /img/how-it-works/chain-key-signature.jpg
slug: threshold-ecdsa-signing
---
# チェーンキー署名

連鎖鍵*署名は*連鎖鍵技術を拡張し、他のブロックチェーンを対象とした取引を、Internet Computer Protocol を使って完全にオンチェーンで計算できるようにします。
連鎖鍵署名を使うことで、ICは完全にトラストレスな方法で他のブロックチェーンと統合することができます。
実際、連鎖鍵署名を使うことは、2つのブロックチェーン以外に追加の信頼前提が必要なく、特に署名鍵やそのシェアを管理する追加の当事者も必要ないため、ブロックチェーンを統合する最も強力で分散化された方法です。

連鎖鍵技術と同様に、連鎖鍵署名の重要な構成要素は閾値暗号です。
連鎖鍵暗号を実装するために[使用される閾値署名スキームは](/how-it-works/chain-key-technology/)BLS署名に基づいています。BLS署名には明確な利点がありますが、他のブロックチェーンとの互換性がありません。
他のブロックチェーンと連携するためには、ICは他のブロックチェーンのデジタル署名方式と互換性のある閾値署名を使用する必要があります。
（ビットコインやイーサリアムを含む）他のブロックチェーンで使用されている署名方式で最も一般的に使用されているのは、[ECDSA署名方式](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)です。
このため、現在ICでは*閾値ECDSA*署名がサポートされており、他の閾値署名方式の実装は計画段階にあります。

ECDSA署名はブロックチェーン業界で広く使用されています。この機能により、canister スマートコントラクトがECDSA公開鍵を持ち、それに関して署名できるようになります。対応する秘密鍵は、canister スマート・コントラクトを保持するサブネットのノード間で閾値共有されます。これは、Internet Computer とビットコインおよびイーサリアムとの直接統合のための前提条件です。

ECDSA用の安全で効率的な閾値署名プロトコルの実装は、BLS署名よりもはるかに困難です。近年、[閾値ECDSAに関する研究が](https://eprint.iacr.org/2020/1390)盛んに行われていますが、これらのプロトコルのどれもが、Internet Computer の厳しい要件を満たしていません。それらはすべて、*同期ネットワークを*前提としているか（メッセージが予期せず遅延した場合、プロトコルが失敗するか、安全でなくなることを意味します）、*堅牢性を*提供していないか（*1つの*ノードがクラッシュした場合、署名を生成する能力が完全に失われることを意味します）、または*その両方*です。ICではこれらの仮定はどちらも受け入れられません。多くの欠陥ノードを持つ*非同期ネットワーク*であっても、セキュリティと有効性は維持されなければなりません。

DFINITY は、*非同期ネットワーク*上で動作し、非常に*ロバスト*（サブネット内のノードの3分の1までがクラッシュまたは破損しても署名を生成する）*で*ありながら、許容可能なパフォーマンスを提供する新しい閾値ECDSA署名プロトコルを設計、分析、実装しました。 の研究者によって書かれた論文では、DFINITY[プロトコルの詳細が説明](https://eprint.iacr.org/2022/506)され、[そのセキュリティの重要な要素が証明されて](https://eprint.iacr.org/2021/1330)います。

[ECDSAホワイトペーパー](https://eprint.iacr.org/2021/1330)

[ECDSA GitHub](https://github.com/ic-association/nns-proposals/blob/main/proposals/governance/20210920T1500Z.md)

[動議提案21340](https://dashboard.internetcomputer.org/proposal/21340)

[ Internet Computer コミュニティが閾値ECDSA署名を採択 動議案](https://medium.com/dfinity/the-internet-computer-community-approves-threshold-ecdsa-signatures-motion-proposal-65a0a3463492?source=friends_link&sk=db265995e31dac5ea751cd91e7b0a3b0)

[![Watch youtube video](https://i.ytimg.com/vi/MulbKPwv6_s/maxresdefault.jpg)](https://www.youtube.com/watch?v=MulbKPwv6_s)

<!---


# Chain-key signatures

_Chain-key signatures_ extend chain-key technology to allow transactions targeted at other blockchains to be computed fully on-chain using the Internet Computer Protocol.
Using chain-key signatures, the IC can integrate with other blockchains in a completely trustless manner.
Indeed, using chain-key signatures is the strongest, most decentralized way of integrating blockchains as no additional trust assumptions besides that of the two blockchains are required, particularly no additional parties that manage signature keys or their shares.

Just like chain-key technology, a key component of chain-key signatures is threshold cryptography.
The [threshold signature scheme](/how-it-works/chain-key-technology/)used to implement chain-key cryptography is based on BLS signatures. While BLS signatures have distinct advantages, they are simply not compatible with other blockchains.
In order to work with other blockchains, the IC must use threshold signatures that are compatible with the digital signature schemes of those other blockchains.
By far, the most commonly used signature scheme used on other blockchains (including Bitcoin and Ethereum) is the [ECDSA signature scheme](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm).
Because of this, _threshold ECDSA_ signatures are currently supported on the IC, with implementations of other threshold signature schemes in the planning stages.

ECDSA signatures are widely used in the blockchain industry. This feature will enable canister smart contracts to have an ECDSA public key and to sign with regard to it. The corresponding secret key is threshold-shared among the nodes of the subnet holding the canister smart contract. This is a prerequisite for the direct integration between the Internet Computer and Bitcoin and Ethereum.

Implementing a secure and efficient threshold signing protocol for ECDSA is much more challenging than for BLS signatures. While there has been a flurry of [research on threshold ECDSA in recent years](https://eprint.iacr.org/2020/1390), none of these protocols meet the demanding requirements of the Internet Computer: they all either assume a _synchronous network_ (meaning that the protocols will fail or become insecure if messages are unexpectedly delayed) or provide _no robustness_ (meaning that the ability to produce signatures is completely lost if a _single_ node should crash) or _both_. Neither of these assumptions are acceptable on the IC: security and liveness must hold even an an _asynchronous network_ with many faulty nodes.

DFINITY has designed, analyzed, and implemented a new threshold ECDSA signing protocol that works over an _asynchronous network_ and is quite _robust_ (it will still produce signatures if up to a third of the nodes in a subnet are crashed or corrupt) while still delivering acceptable performance. Papers written by DFINITY's researchers [describe the protocol in detail](https://eprint.iacr.org/2022/506) and [prove the key elements of its security](https://eprint.iacr.org/2021/1330).

[ECDSA White Paper](https://eprint.iacr.org/2021/1330)

[ECDSA GitHub](https://github.com/ic-association/nns-proposals/blob/main/proposals/governance/20210920T1500Z.md)

[Motion Proposal 21340](https://dashboard.internetcomputer.org/proposal/21340)

[The Internet Computer Community Adopts Threshold ECDSA Signatures Motion Proposal](https://medium.com/dfinity/the-internet-computer-community-approves-threshold-ecdsa-signatures-motion-proposal-65a0a3463492?source=friends_link&sk=db265995e31dac5ea751cd91e7b0a3b0)

[![Watch youtube video](https://i.ytimg.com/vi/MulbKPwv6_s/maxresdefault.jpg)](https://www.youtube.com/watch?v=MulbKPwv6_s)

-->
