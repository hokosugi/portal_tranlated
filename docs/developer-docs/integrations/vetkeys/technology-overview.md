# vetKeys: テクノロジーの概要

## 概要

vetKeys on theInternet Computer は、開発者がIC上でdapps を構築する際に、暗号化、閾値復号化、署名をより簡単に実行できるようにします。**vetKey（Verifiably Encrypted Threshold Key Derivation**）と呼ばれるプロトコルが搭載されており、オンデマンドで復号鍵を導出することができます。

## オンデマンドでの鍵導出

ブロックチェーンのプライバシー機能はあまり知られていません。vetKeysの目標は、Internet Computer 。
デバイスのローカルで情報を暗号化し、ブロックチェーンに保存することは、秘密鍵の材料が常にローカルデバイスに残り、公開されないため簡単です。
プライバシーに配慮した方法で公開チャネルを越えて秘密鍵の材料を渡す簡単な方法がないため、ユーザーが別のデバイスから暗号化された情報を取得したり、別のユーザーと共有したりしたい場合に困難が生じます。

vetKeysは、ICのネイティブ署名スキームであるBLS署名が一意であり、したがって（適切な条件下で）暗号復号鍵として使用するのに理想的であるという事実を活用しています。BLS署名はIC上で分散方式で計算されるため、ユーザーのために鍵を導出する中央機関は存在しません。さらに、[IBE スキームの](https://internetcomputer.org/blog/features/vetkey-primer#identity-based-encryption-ibe)規格に従って、導出された鍵は暗号化された状態でユーザーに送信されます。そのため、ノードやネットワークがユーザーの鍵にアクセスすることはありません。

vetKeysを利用することで、以下のセクションで説明するものに限らず、一連のアプリケーションを実現することができます。

## エンドツーエンドの暗号化

主なユースケースは、ブロックチェーンが数百万人のユーザーと数十億の秘密鍵にスケールする方法で、単一の閾値共有秘密鍵を使用して、閾値暗号化されたデータをホストできるようにすることです。BLS署名は一意であるため、共通鍵としてすぐに役立ちます。

dapp例えばセキュアなファイルストレージdapp を考えてみましょう。
 dapp は、認証されたユーザのみがルートキーの回復を許可され、したがってファイルの復号化が許可されることを強制します。
ブロックチェーンのノードはユーザのルートキーの回復を支援しますが、そのキーやファイルの内容を見ることはありません。

## より洗練されたアクセスポリシーも表現可能

セキュアなメッセージングdapp では、2 人のユーザー間の会話は、そのペア ID の BLS 署名を使用して暗号化することができ、その BLS 署名にはdapp によってそのユーザーのみがアクセスできます。
セキュアな分散型ソーシャルネットワークでは、ユーザーが投稿に関連する鍵、例えば投稿の一意の識別子の BLS 署名を使用して投稿を暗号化できるようにすることができます。
その後、SocialFidapp は、投稿者と投稿が共有されるユーザーのみがその鍵にアクセスできるようにします。

## ブロックチェーン発行署名とクロスチェーンブリッジ

これは、dapps ステートメントに署名することを可能にする組み込みの認証機能を持たないブロックチェーンにとって特に有用です。
また、ブロックチェーンを効率的にブリッジするために使用することもできます。例えば、DeFiアプリケーションでアセットを交換するために使用できます。dapp 最初のブロックチェーン上の 、2番目のブロックチェーンによって発行された署名されたステートメントを検証することができ、その2番目のチェーンの完全なライトクライアントを実装する必要はありません。

## 検証可能なランダム性

BLS署名はその一意性から、検証可能なランダム関数（VRF）としても機能します。
信頼できる検証可能なランダム性は、信頼性のないオンライン宝くじやカジノ、公正な分散型ゲーム（GameFi）、非腐敗性トークン（NFT）のランダム機能の選択などのアプリケーションにとって重要です。

## "デッドマンズ・スイッチ"

ジャーナリストや内部告発者は、万が一自分が無能力になった場合、所持している危険な情報が自動的に公開されるようにすることができます。
彼らは情報をdapp に保存することができます。BLS 署名の下で暗号化され、dapp は所有者から認証された ping を受信した後、一定時間が経過すると自動的に公開された情報を復元します。

## 秘密入札オークションとMEV保護

vetKey を搭載したブロックチェーンは、多数の暗号文を同時に復号化する必要があるようなユースケースにも対応できます。
秘密入札オークションdapp では、ユーザはオークションの識別子の下で IBE 暗号化された入札を提出することができ、オークションの終了時にdapp は 1 回の vetKey 評価ですべての入札を復号化できます。同様の技術は、分散型取引所（DEX）において、マイナー抽出値（MEV）としても知られるフロントランニングを防止するために使用することができます。ユーザーは予測可能なバッチ識別子の下でIBE暗号化されたトランザクションを提出します。DEXはトランザクションを暗号化された形で発注し、特定のバッチの全トランザクションが発注されると、そのバッチの復号化キーの回収をトリガーし、復号化されたトランザクションを一定の順序で実行します。上記のすべての共通鍵暗号化のユースケースは、共通鍵暗号化の代わりに IBE を使用して暗号化するように変更することができ、それによって暗号化のための VetKey 派生を実行する必要がなくなることに注意。もちろん、復号化には vetKey の評価が必要です。

## タイムロック暗号化

タイム・ロック暗号化では、送信者が未来に向けてメッセージを暗号化し、ある時刻に復号化されることを保証します。既存のソリューションは、中央集権化された信頼できるパーティ、証人による暗号化（次の段落を参照）、またはパズルを解くことによる段階的な解除に依存しています。タイムロック暗号化は、中央集権機関が一定間隔で現在時刻に対応するIBE復号鍵を公開し、送信者が希望する復号時刻をIDとしてメッセージをIBE暗号化することで、IBEを介して実現できます。当局の機能は、vetKeyを搭載したブロックチェーン上のdapp 、信頼できる中央パーティが不要になります。

## 証人の暗号化

証人の関係(R)を持つ言語(L)のための証人暗号化スキームは、送信者がLのインスタンス(x)にメッセージを暗号化し、R(x,w)のような証人(w)を使用してのみ復号化できるようにします。現在の唯一の実装は、区別不能難読化に基づくもので、根拠のある仮定に基づくインスタンスはほとんど知られていません。証人の暗号化は、vetKey対応ブロックチェーン上で実装するのはほとんど些細なことです。誰でも、インスタンスxをアイデンティティとしてメッセージをIBE暗号化することができます。一方、証人の検証dapp 、xの有効な証人w（または、wが非公開のままであるべき場合は、wの知識に関する有効なゼロ知識証明）を提供する人は誰でも、xの復号鍵を取得することができます、例えば、株価があるレベルを上回ったり下回ったりすること、情報のエスクロー、ブレイク・ザ・グラス・ポリシーなどです。

## ワンタイムプログラム

一つの入力に対して一度だけ実行でき、計算結果以外にはプログラムについて何も漏らさないワンタイムプログラム。現在知られている唯一の例は、信頼できるハードウェアに依存するか、ブロックチェーン上の証人暗号化に依存しています。証人の暗号化がvetKey対応のブロックチェーン上で簡単に構築できることを考えると、ワンタイム・プログラムも同様に構築できることは驚くべきことではありません。プログラムの作成者は回路を文字化けさせ、入力されたワイヤー・キーをIBE暗号化し、ワイヤー・インデックスと値をIDとして使用します。dapp は、ユーザが入力に対応する IBE 復号化を復元するのを支援し、各ワイヤの単一の値のみが復元されるようにします。

## システムAPIの提案

現在進行中の vetKeys 機能の開発では、[インターフェース仕様を拡張するための](https://github.com/dfinity/interface-spec/pull/158) API 記述がこの[MR 草案に](https://github.com/dfinity/interface-spec/pull/158)あり、これには 2 つの新しいメソッドの API 記述が含まれています：

- vetkd\_public\_key'[(プレビュー](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-vetkd_public_key))
- vetkd\_encrypted\_key'[(](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-vetkd_encrypted_key)プレビュー)
  正確なAPIは[インターフェースの概要の](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-candid)"Threshold key derivation "のセクションにあります。

## 参考文献

- [フォーラムの投稿](https://forum.dfinity.org/t/threshold-key-derivation-privacy-on-the-ic/16560)
- [最初のコミュニティ会話](https://youtu.be/baM6jHnmMq8)
- [暗号の背景を理解するためのvetKeys入門](https://internetcomputer.org/blog/features/vetkey-primer)書。
- [研究論文](https://eprint.iacr.org/2023/616.pdf)。

<!---
# vetKeys: technology overview

## Overview
vetKeys on the Internet Computer allow developers to more easily perform encryption, threshold decryption, and signing when building dapps on the IC. It is powered by a protocol called **vetKey (Verifiably Encrypted Threshold Key Derivation)** that allows to derive decryption keys on demand.

## Key derivation on demand
Blockchains are not known for their privacy capabilities. The goal of vetKeys is to ease the burden of using security and privacy tools on the Internet Computer. 
Encrypting information locally on a device and storing it on a blockchain is easy as the secret key material always remains on the local device and is not exposed.
The difficulty arises when a user may want to retrieve the encrypted information from a different device, or share with a different user as there is no straightforward way to pass secret key material across public channels in a privacy-friendly way. 

vetKeys leverages the fact that BLS signatures, the native signature scheme on the IC, are unique, and therefore ideally suited (under the right conditions) to be used as cryptographic decryption keys. As BLS signatures are computed in a distributed way on the IC, there is no central authority deriving keys for users. Furthermore, following standard practices in [IBE schemes](https://internetcomputer.org/blog/features/vetkey-primer#identity-based-encryption-ibe) the derived key can be transported to the user in an encrypted manner. As such, nodes and the network never have access to a user’s keys.

The availability of vetKeys allows for a series of applications including but not limited to those covered in the following sections.

## End-to-end encryption

The main use case is to enable a blockchain to host threshold-encrypted data in a way that scales to millions of users and billions of secrets, using just a single threshold-shared secret key. BLS signatures are unique, making them immediately useful as symmetric keys.

Think for example of a secure file storage dapp: a user could use the BLS signature on their identity as the root secret under which they encrypt their files before storing them in the dapp.
The dapp enforces that only the authenticated user is allowed to recover the root key, and hence decrypt the files.
The nodes in the blockchain assist a user in recovering their root key, but never see that key or the content of the files.

## More sophisticated access policies can also be expressed
In a secure messaging dapp, the conversation between two users can be encrypted using the BLS signature on their pair of identities, to which only those users are given access by the dapp.
A secure decentralized social network could let users encrypt posts using a key that is related to the post, e.g., the BLS signature on a unique identifier for the post.
The SocialFi dapp then ensures that only the author and the users that the post is shared with get access to that key.

## Blockchain-issued signatures and cross-chain bridges
The key derivation of an IBE automatically yields a signature scheme, the resulting decryption keys can also be used as signatures issued by the blockchain.
This is especially useful for blockchains that don't have a built-in certification feature enabling dapps to sign statements.
It can also be used to efficiently bridge blockchains, e.g., to swap assets in DeFi application: a dapp on a first blockchain can verify signed statements issued by a second blockchain, without having to implement a complete light client of that second chain.

## Verifiable randomness
Because of their uniqueness, BLS signatures can also act as a verifiable random function (VRF). 
Trusted, verifiable randomness is important for applications such as trustless online lotteries and casinos, fair decentralized games (GameFi), and selecting random features for non-fungible tokens (NFTs). 

## "Dead man's switch"
Journalists or whistleblowers could ensure that compromising information in their possession is automatically published if they were to become incapacitated. 
They can store the information in a dapp, encrypted under a BLS signature that the dapp automatically and publicly recovers when a certain amount of time passes after it has received an authenticated ping from its owner.

## Secret-bid auctions and MEV protection
A vetKey-equipped blockchain can also cover use cases where many ciphertexts needs to be decrypted at the same time.
In a secret-bid auction dapp, users can submit bids that are IBE-encrypted under an identifier of the auction, so that at the end of the auction, the dapp can decrypt all bids with a single vetKey evaluation. A similar technique can be used to prevent front-running, also known as miner-extracted value (MEV), on a decentralized exchange (DEX). Users submit their transactions IBE-encrypted under a predictable batch identifier. The DeX orders the transactions in encrypted form and, when all transactions for a particular batch have been ordered, triggers the recovery of the decryption key for that batch and executes the decrypted transactions in the fixed order. Note that all of the symmetric-encryption use cases listed above can be modified to encrypt using an IBE instead of a symmetric-key encryption, thereby eliminating the need to perform a vetKey derivation for encryption. Decryption, of course, still requires a vetKey evaluation.

## Time-lock encryption
Time-lock encryption enables a sender to encrypt a message to the future, ensuring that it will get decrypted at a given time, but no earlier than that time. Existing solutions rely on centralized trusted parties, witness encryption (see next paragraph), or gradual release through puzzle solving. Time-lock encryption can be achieved via IBE by letting a centralized authority release IBE decryption keys corresponding to the current time at regular intervals, and letting the sender IBE-encrypt its message using the desired decryption time as identity.The authority's functionality can be run in a dapp on a vetKey-equipped blockchain, eliminating the need for a trusted central party.

## Witness encryption
A witness encryption scheme for a language (L) with witness relationship (R) lets a sender encrypt a message to an instance (x) in L that can only be decrypted using a witness (w) such that R(x,w). The only current implementations are based on indistinguishability obfuscation, of which few instantiations are known based on well-founded assumptions. Witness encryption is almost trivial to implement on a vetKey-enabled blockchain: anyone can IBE-encrypt their message using the instance x as identity, while a witness-verifying dapp lets anyone who provides a valid witness w for x (or a valid zero-knowledge proof of knowledge of w, if it should remain private) to obtain the decryption key for x. The primitive may sound rather theoretical at first, but it actually covers quite practical use cases as it enables one to encrypt to any verifiable future event, e.g., the price of a stock going above or below a certain level, information escrow, or break-the-glass policies.

## One-time programs
Another cryptographic primitive with few instantiations is one-time programs that can be executed only once on a single input, and that don't leak anything about the program other than the result of the computation. Their only currently known instances rely on trusted hardware or on witness encryption on a blockchain. Given that witness encryption is easy to build on a vetKey-enabled blockchain, it should not come as a surprise that one-time programs are as well. The creator of the program garbles the circuit and IBE-encrypts the input wire keys, using the wire index and the value as the identity. A dapp assists users in recovering the IBE decryption corresponding to their input, making sure that only a single value for each wire is ever recovered.

## Proposed system API
In ongoing development of the vetKeys feature, there is an API descriptions in this [draft MR to extend the Interface Spec](https://github.com/dfinity/interface-spec/pull/158), which contains API descriptions for the two new methods:
* `vetkd_public_key' ([preview](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-vetkd_public_key))
* `vetkd_encrypted_key' ([preview](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-vetkd_encrypted_key))
The exact API is then in the [Interface Overview](https://deploy-preview-158--ic-interface-spec.netlify.app/docs/#ic-candid) (in the section “Threshold key derivation”)

## References
- [Forum post](https://forum.dfinity.org/t/threshold-key-derivation-privacy-on-the-ic/16560).
- [The first Community Conversation](https://youtu.be/baM6jHnmMq8).
- [vetKeys primer to understand the crypto background](https://internetcomputer.org/blog/features/vetkey-primer).
- [Research paper](https://eprint.iacr.org/2023/616.pdf).

-->
