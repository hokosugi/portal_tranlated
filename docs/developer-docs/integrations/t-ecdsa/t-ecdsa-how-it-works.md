# 閾値ECDSA：技術概要

## 概要

IC 上の連鎖鍵 ECDSA 署名のハイレベルな概要を説明します。このセクションの情報の一部は、この機能を使用するために必要なものではありませんが、技術に関心のある読者にとっては、技術の背景情報を得るために興味深いかもしれません。ICは、GrothとShoupの[Eurocrypt 2022論文に](https://eprint.iacr.org/2021/1330)記載されている閾値ECDSAプロトコルを実装しています。GrothとShoupは、この分散署名プロトコルの包括的な[設計と分析も](https://eprint.iacr.org/2022/506)発表しています。

高レベルでは、IC上の閾値ECDSA実装は、次に概説するような複数のプロトコルを備えています。これは単なる閾値ECDSA署名の域をはるかに超えており、これが**チェーン鍵ECDSA署名の**ためのプロトコルスイートと呼ばれる理由です。

- **鍵生成：**このプロトコルは指定されたサブネット上で実行されます。このサブネットのレプリカ上で秘密鍵が共有されるように、新しい閾値ECDSA鍵を生成します。
- **XNet鍵の再共有**：このプロトコルは、ソースサブネットからターゲットサブネットにECDSA鍵を再共有します。その結果、同じ鍵がターゲット・サブネットのレプリカ上で別のランダムな秘密共有を使って秘密共有されます(ソース・サブネットでの共有とは異なる数のレプリカ上で共有される可能性があります)。
- **定期的な鍵の再共有：**このプロトコルは、秘密共有されているサブネット内で ECDSA 鍵を再共有します。これは、鍵の再共有が行われるたびに、以前に取得した鍵の共有が無価値になるため、時間の経過とともにレプリカを侵害しようとする適応的な攻撃者から保護するのに役立ちます。
- 事前署名の**計算、署名:**秘密分散された秘密鍵を使って署名を計算するプロトコル。事前署名、つまり実際の署名プロトコルで使用される4重署名を計算**するためのプロトコルは**、事前署名を計算するために署名リクエストに対して非同期に実行されます。この事前計算プロトコルは、閾値ECDSA署名を作成するステップの大部分を計算します。**署名プロトコルは**、canister の署名要求によって起動されます。 署名プロトコルは、閾値ECDSA署名を効率的に計算するために、事前計算された4倍を1つ消費します。
- **公開鍵の取得:** canister の公開鍵を取得することが可能。canister が提供する導出パスに 基づくBIP-32のような鍵導出の可能性も含まれる。

秘密鍵は、その生成時、サブネット内またはサブネット間における鍵の再共有時、 署名を計算する時など、その全生涯において、再構成された形では存在せず、 秘密が共有された形でのみ存在することに注意することが重要。

鍵管理、すなわち最初の鍵生成と鍵の再共有を行うために、様々なNNS提案が実装されてきました。これらの提案は、どのサブネットでECDSAマスター鍵を生成するか、どのサブネットで鍵を再共有するか、そしてどのサブネットで署名要求に答えられるようにするかを定義するために使用されます。

## ECDSA 鍵

ECDSAが有効なサブネットは、ICの選択されたサブネット上で鍵生成プロトコルで生成された、閾値ECDSA**マスター**鍵と呼ばれる鍵を保持します。マスターECDSA鍵とは、canister ECDSA鍵を導出できる鍵のことです。つまり、canister'のプリンシパルを入力とするBIP-32鍵導出メカニズムの拡張を使用して、IC上の各canister 、*canister ルート鍵*用のECDSA鍵を導出するには、与えられた楕円曲線用の単一のマスター鍵で十分です。鍵導出は、署名および公開鍵検索APIの一部として、プロトコルによって透過的に実行される。マスター鍵からcanister ルート鍵の導出については、下図のレベル0の鍵導出を参照。

canister ルート鍵から、BIP-32 鍵導出メカニズムの下位互換拡張を使用して、canister の ECDSA 鍵を無制限に導出することが可能。この拡張により、鍵導出関数の各レベルの入力として、32ビット整数だけでなく、 任意の長さのバイト配列を使用できるようになります。下図のレベル1以降を参照。canister ルート鍵に基づく、さらなるcanister 鍵の導出を説明するもの。この導出は、ECDSA API の`ecdsa_public_key` メソッドによってサポートされる。

canister ルート鍵からのさらなる ECDSA 鍵の導出は、特定のユースケースを容易にするため、IC を介さずに行うことも可能である。

![Threshold ECDSA Key derivation](../_attachments/key_derivation.png)

閾値ECDSAマスター鍵は、閾値ECDSA API（およびロールアウトを管理するNNSプロポーザル） では常に**鍵識別子を通して**参照される。鍵識別子は楕円曲線名と識別子から構成され、例えば鍵識別子の例は 2 タプルの`(secp256k1, test_key_1)` 。これらの鍵識別子は、システムが正しい鍵を参照するために使用されます。例えば、署名を計算する際に鍵共有を選択したり、APIコールやレスポンスを、対応する識別子を持つ鍵を保持するECDSA対応サブネットとの間でXNetルーティングする実装において使用されます。

現在、テスト用と本番用の 2 つのマスター鍵が配備されています。

- `(secp256k1, test_key_1)`テスト用鍵は 13 ノード・サブネット 1 つに配置。
- `(secp256k1, key_1)` 本番鍵は2つの高レプリケーション・サブネットに配置され、1つは署名用、もう1つは鍵の可用性を高めるためのバックアップ用です。

## デプロイメント

次に、Chromiumリリース（ベータ版）と一般公開リリースのデプロイの概要を説明します。

### Chromiumリリース（ベータ版）

Chromiumリリースの一部として、1つのサブネットにcurve`secp256k1` 、レプリケーションfactor 、13のテスト鍵のみが配備されています。この鍵はGAリリースの後、NNSの提案により削除される可能性があるため、 価値のあるものには使用しないでください。より具体的には、例えば、鍵が削除されるとビットコインが消失してしまうため、テスト鍵で本物のビットコインを保有しないことを強くお勧めします。テストキーはむしろ、ビットコインのスマートコントラクトの開発を促進し、GAリリースの準備としてテストネットのビットコインを保持することを目的としています。テストキーは、APIコールで参照するためのID`(secp256k1, test_key_1)` を持っています。

### 一般利用可能リリース

`secp256k1` 楕円曲線用の単一のスレッショルド ECDSA プロダクションキーが GA リリースとともにデプロイされました。id`(secp256k1, key_1)` の鍵は、actor (当初は約30ノード) の高レプリケーションで、2つの異なるサブネット上で秘密共有形式で管理されます。鍵は最初にレプリケーションの高いサブネットで生成され、そこで保持された後、再共有プロトコルを使用して新しいレプリケーションの高いサブネットに再共有されます。後者のサブネットは、この鍵のアクティブな署名サブネットとして動作するようにアクティブ化されます。このサブネットは、鍵の可用性を向上させるためのバックアップを目的として、 秘密共有形式で鍵を保持しますが、署名要求には応答しません。万が一、サブネットの1つが復旧不可能なほど破壊された場合、鍵の複製を行うことで、 鍵を別のサブネットに再共有することができ、鍵の可用性が向上します。

### さらなる側面

現在の Chromium デプロイメント（テスト鍵付き）と将来の GA リリース（本番鍵付き）の両方において、閾値 ECDSA API へのリクエストは常に XNet リクエストです。これは、ECDSA 対応のサブネットがユーザのcanisters をホストしていないためで、呼び出し元のcanister のサブネットから、それぞれの鍵を保持する閾値 ECDSA 対応のサブネットへの Xnet 通信と、応答を返す通信により、数秒の余分な待ち時間が発生します。

今後、さらなる楕円曲線と対応するマスター鍵のサポートが追加される可能性 があります。曲線`secp256r1` は、分散型認証局(CA)のようなユースケースをサポートするために興味深いものであり、前述のようなユースケースを容易にするために追加される最有力候補のグループです。

## API

次に、閾値ECDSAのAPIの概要を説明します。正式な仕様については、[Internet Computer インタフェース仕様の](/references/ic-interface-spec.md#ic-ecdsa_public_key)該当部分を参照してください。APIは、閾値ECDSA公開鍵を取得するための`ecdsa_public_key` 、秘密共有された閾値ECDSA秘密鍵を保持するサブネットから計算される閾値ECDSA署名を要求するための`sign_with_ecdsa` 、2つのメソッドから構成されます。

各 API 呼び出しは、上記のように曲線と鍵 ID の 2 つの部分からなる識別子によって、閾値 ECDSA マスター鍵を参照します。導出パスは、鍵導出階層においてcanister のルート鍵以下の鍵を参照するために使用される。マスター鍵からcanister ルート鍵への鍵の導出は、APIでは暗黙の了解となっています。

- `ecdsa_public_key`このメソッドは、指定された派生パスを使用して、指定されたcanister の SEC1 エンコードされた ECDSA 公開鍵を返します。`canister_id` が指定されていない場合は、呼び出し元のcanister id がデフォルトとなります。`derivation_path` は、可変長のバイト文字列のベクトル。`key_id` は、曲線と名前の両方を指定する構造体です。`key_id`<br/>
  カーブ`secp256k1` の場合、公開鍵はBIP32の一般化を使用して導出される (ia.cr/2021/1330、付録D参照)。(非強化)BIP-0032互換の公開鍵を導出するには、`derivation_path` の各バイト文字列(blob)は、2<sup>31</sup> 未満の符号なし整数を4バイトのビッグエンディアンでエンコードしたものでなければなりません。<br/>
  返される結果は、SEC1圧縮形式でエンコードされたECDSA`public_key` と、`public_key` の子鍵を決定論的に導出するために使用できる`chain_code` で構成される拡張公開鍵である。
  このコールでは、ECDSA機能が有効になっており、`canister_id` がcanister id の要件を満たしている必要がある。そうでない場合は拒否されます。
- `sign_with_ecdsa`このメソッドは、派生したECDSA公開鍵に対して個別に検証できる、指定された`message_hash` の新しいECDSA署名を返します。この公開鍵は、呼び出し元の`canister_id` と、ここで使用されているのと同じ`derivation_path` と`key_id` を使用して`ecdsa_public_key` を呼び出すことで得られる。<br/>
  署名は、`r` と`s` の2つの値のSEC1エンコーディングの連結としてエンコードされる。曲線`secp256k1` の場合、これは32バイトのビッグエンディアンエンコーディングに 相当する。<br/>
  この呼出しには、ECDSA機能が有効になっており、呼出し元がcanister であり、`message_hash` が32バイト長であることが必要。そうでない場合は拒否されます。

システム負荷が高い場合、ECDSA署名を計算するリクエストはタイムアウト する可能性があることに注意。この場合、canister 。

## API料金

ECDSA 署名 API の利用料は以下のとおりです。閾値ECDSAテスト鍵は通常サイズ（13ノード）のアプリケーションサブネット上に存在し、閾値ECDSA本番鍵は約30ノードサイズの受託サブネット上に存在します。閾値署名鍵が存在し、署名が計算されるサブネットのサブネットサイズが、結果として 生じるコストを定義します。canister のサブネットのサイズは料金には関係ありません。米ドルでのコストについては、2022年11月23日現在の米ドル/XDR為替レートが使用されています。

:::note
この機能を使用するcanister がブラックホールされることを意図している場合だけでなく、他のcanisters 、将来的に署名サブネットのサブネットサイズが大きくなっても、署名ごとの高いコストがカバーされるように、呼の広告コストよりも多くのcycles を呼で送信することを推奨します。通話で課金されなかったcycles は払い戻される。
::：

### t-ECDSAテスト鍵の料金

| トランザクション | 内容 | Cycles (テスト鍵) | 米ドル |
| --- | --- | --- | --- |
| 閾値ECDSA署名 | 1つの閾値ECDSA署名の計算用 (`sign_with_ecdsa`) | 10,000,000,000 | $0.0130886 |

### t-ECDSAプロダクション・キーの料金

| トランザクション | 内容 | Cycles (プロダクション・キー) | 米ドル |
| --- | --- | --- | --- |
| 閾値ECDSA署名 | 1つの閾値ECDSA署名の計算用 (`sign_with_ecdsa`) | 26,153,846,153 | $0.0342317 |

## 開発環境

canister の開発ライフ全体を通じて開発者を容易にするため、cycle IC 上では、ローカル開発およびテスト用の SDK と、canisters の本番前テストおよび本番運用用の IC の両方でこの機能を利用できます。

### SDK

canisters の開発は通常、開発者のローカル環境で行われ、SDK を使用することで容易になります。SDKは、閾値ECDSAの管理canister APIがローカルcanister 実行環境で利用できるように拡張されています。このため、閾値ECDSA APIを使用するcanisters 、開発およびテスト目的でローカルに実行することができます。この機能は SDK で常に有効になっているため、API を利用するためにユーザが追加操作を行う必要はありません。

SDK環境のレプリカが最初に起動されると、新しいECDSA鍵が生成されます。この鍵は不揮発性メモリに保存されるため、レプリカを再起動するたびに変更されることはありません。

技術的に興味のある読者のために、SDKはメインネットと全く同じ閾値ECDSAの実装を使用していますが、単一のレプリカしか実行していないことに注意してください。したがって、このプロトコルは単一のレプリカで動作しており、特殊なケースに縮退し、鍵の生成や署名などのオーバーヘッドがわずかしか発生しないため、SDK環境のパフォーマンスに顕著な影響を与えることなく、SDKのデフォルトで有効のままにしておくことができます。また、ローカルのSDK環境における署名のスループットと待ち時間は、IC上のスループットと待ち時間を代表するものではないことに注意してください。

### Internet Computer

IC のどのサブネット上のcanister でも、管理canister によって公開されたしきい値 ECDSA API を呼び出すことができます。呼び出しはXNet通信を介して、API呼び出しで参照される鍵を保持するECDSA対応サブネットにルーティングされます（テスト鍵を保持するこのような署名サブネットと本番鍵を保持する署名サブネットは現在1つしかありません）。このテスト鍵はレプリケーションfactor が13しかないサブネットにホストされており、将来削除される可能性があることに注意。主な目的は、Bitcoin testnetを使用したBitcoin-enableddApps の開発とテストを容易にすることです。

この機能の一般利用可能性（GA）リリースの一環として、`secp256k1` 楕円曲線上のプロダクション ECDSA キーが配備され、ビットコインメインネットやその他のユースケースとの統合に使用されます。

<!---
# Threshold ECDSA: technology overview

## Overview
We give a high-level outline of chain-key ECDSA signatures on the IC. Some of the information in this section is not required to use the feature, but may be of interest to the technically inclined reader for obtaining background information on the technology. The IC implements the threshold ECDSA protocol by Groth and Shoup as described in their [Eurocrypt 2022 paper](https://eprint.iacr.org/2021/1330). Groth and Shoup have also published a comprehensive [design and analysis](https://eprint.iacr.org/2022/506) of this distributed signing protocol.

At a high level, the threshold ECDSA implementation on the IC features multiple protocols as outlined next, all of which are crucial for a secure system setup and operation. Note that this goes far beyond just threshold ECDSA signing, which is the reason for calling this a protocol suite for **chain-key ECDSA signatures**.
-   **Key generation:** this protocol is executed on a specified subnet; it generates a new threshold ECDSA key such that the private key is secret shared over the replicas of this subnet.
-   **XNet key re-sharing:** this protocol re-shares an ECDSA key from a source subnet to a target subnet. It results in the same key being secret shared over the replicas of the target subnet using a different random secret sharing (potentially over a different number of replicas than the sharing in the source subnet uses).
-   **Periodic key re-sharing:** this protocol re-shares an ECDSA key within the subnet it is secret shared on. This helps protect against an adaptive attacker that attempts to compromise replicas over time as every key resharing makes the previously-obtained key shares worthless.
-   **Computing pre-signatures, signing:** these protocols compute signatures with the secret-shared private key. A **protocol for computing pre signatures**, i.e., quadruples that are used in the actual signing protocol, is run asynchronously to signing requests to precompute pre signatures. This precomputation protocol computes the vast majority of the steps of creating threshold ECDSA signatures. A **signing protocol** is triggered by a signing request of a canister. A signing protocol consumes one precomputed quadruple to efficiently compute a threshold ECDSA signature.
-   **Public key retrieval:** allows for retrieving a public key of a canister, including potential BIP-32-like key derivation based on a canister-provided derivation path.

It is crucial to note that the private key never exists in reconstructed, but only in secret-shared, form during its whole lifetime, be it during its generation, the re-sharing of the key within a subnet or from one subnet to another, and when computing signatures.

Various NNS proposals have been implemented to perform key management, i.e., initial key generation and key re-sharing. Those proposals are used to define on which subnet to generate an ECDSA master key, to which subnet to re-share the key to have it available for better availability, and which subnet to enable for answering signing requests.

## ECDSA keys

ECDSA-enabled subnets hold what we call threshold ECDSA **master keys**, generated with the key generation protocol on selected subnets of the IC. A master ECDSA key is a key from which canister ECDSA keys can be derived, i.e., a single master key for a given elliptic curve suffices for the derivation of an ECDSA key for each canister on the IC, the *canister root key*, using an extension of the BIP-32 key derivation mechanism with the canister's principal as input. The key derivation is executed transparently by the protocol as part of the signing and public key retrieval APIs. See the level-0 key derivation in the below figure for the derivation of canister root keys from a master key.

From a canister root key, an unlimited number of ECDSA keys can be derived for the canister using a backward-compatible extension of the BIP-32 key derivation mechanism. The extension allows not only 32-bit integers, but arbitrary-length byte arrays, to be used as input for each level of the key derivation function. See the levels 1 and greater in the below figure illustrating the derivation of further canister keys based on the canister root key. This derivation is supported by the ECDSA API through the `ecdsa_public_key` method.

The derivation of further ECDSA keys from a canister root key can be done without involvement of the IC as well to facilitate certain use cases.

![Threshold ECDSA Key derivation](../_attachments/key_derivation.png)

Threshold ECDSA master keys are always referred to through **key identifiers** in the threshold ECDSA API (as well as in the NNS proposals for managing the rollout). The key identifiers comprise an elliptic curve name and an identifier, e.g., an example key identifier is the 2-tuple `(secp256k1, test_key_1)`. Those key identifiers are used by the system to refer to the correct key, e.g., for selecting the key share when computing a signature or in the implementation of the XNet routing of API calls and responses to/from the ECDSA-enabled subnet holding the key with the corresponding identifier.

There are currently two master keys deployed, a test and a production key.

- `(secp256k1, test_key_1)`: the test key deployed on a single 13-node subnet.
- `(secp256k1, key_1)` the production key deployed on two high-replication subnets, one activated for signing, and the other one for backing up the key for better key availability.

## Deployment

We next outline the deployments for the Chromium (Beta) release and the general availability release.

### Chromium release (Beta)

As part of the Chromium release, only a test key for curve `secp256k1` has been deployed on one subnet with a replication factor of 13. This key may be deleted with an according NNS proposal some time after the GA release and therefore should not be used for anything that has value, but only for development and testing purposes. More concretely, it is, for example, strongly advised against holding real bitcoin with the test key as those Bitcoin would be lost when the key gets deleted. The test key is rather intended to facilitate development of Bitcoin smart contracts and hold Testnet bitcoin as preparation for the GA release. The test key has the id `(secp256k1, test_key_1)` for referring to it in API calls.

### General availability release

A single threshold ECDSA production key for the `secp256k1` elliptic curve has been deployed with the GA release. The key with id `(secp256k1, key_1)` will be maintained in secret-shared form on two different subnets with high replication factor (around 30 nodes initially). The key will be initially generated on a high-replication subnet and kept there and re-shared to a new high-replication subnet using the re-sharing protocol. The latter subnet will be activated to act as the active signing subnet for this key. The further will hold the key in secret-shared form for backup purposes for achieving better key availability, but will not respond to signing requests. In case of the unlikely event of one of the subnets getting destroyed beyond recoverability, the approach of key replication improves key availability by allowing for the key to be re-shared to a different subnet, should this be ever required in case of a disaster.

### Further Aspects

For both the current Chromium deployment with a test key and the future GA release with a production key, requests to the threshold ECDSA API are always XNet requests because the ECDSA-enabled subnets do not host user's canisters, thus some seconds of extra latency is incurred due to Xnet communications from the calling canister's subnet to the threshold-ECDSA-enabled subnet holding the respective key and communicating the response back.

Support for further elliptic curves and additional corresponding master keys may be added in the future. The curve `secp256r1` is interesting for supporting use cases such as decentralized certification authorities (CAs) and is the premier candidate group to be added to facilitate use cases like the mentioned one.

## API

We next give an overview of the API for threshold ECDSA. For the authoritative specification, the reader is referred to the corresponding part of the [Internet Computer interface specification](/references/ic-interface-spec.md#ic-ecdsa_public_key). The API comprises two methods, `ecdsa_public_key` for retrieving threshold ECDSA public keys, and `sign_with_ecdsa` for requesting threshold ECDSA signatures to be computed from the subnet holding the secret-shared private threshold ECDSA key.

Each API call refers to a threshold ECDSA master key by virtue of a 2-part identifier comprising a curve and a key id as outlined above. Derivation paths are used to refer to keys below a canister's root key in the key derivation hierarchy. The key derivation from the master key to the canister root key is implicit in the API.

-   `ecdsa_public_key`: this method returns a SEC1-encoded ECDSA public key for the given canister using the given derivation path. If the `canister_id` is unspecified, it will default to the canister id of the caller. The `derivation_path` is a vector of variable length byte strings. The `key_id` is a struct specifying both a curve and a name. The availability of a particular `key_id` depends on implementation.<br/>
For curve `secp256k1`, the public key is derived using a generalization of BIP32 (see ia.cr/2021/1330, Appendix D). To derive (non-hardened) BIP-0032-compatible public keys, each byte string (blob) in the `derivation_path` must be a 4-byte big-endian encoding of an unsigned integer less than 2<sup>31</sup>.<br/>
The return result is an extended public key consisting of an ECDSA `public_key`, encoded in SEC1 compressed form, and a `chain_code`, which can be used to deterministically derive child keys of the `public_key`.
This call requires that the ECDSA feature is enabled, and the `canister_id` meets the requirement of a canister id. Otherwise it will be rejected.
-   `sign_with_ecdsa`: this method returns a new ECDSA signature of the given `message_hash` that can be separately verified against a derived ECDSA public key. This public key can be obtained by calling `ecdsa_public_key` with the caller's `canister_id`, and the same `derivation_path` and `key_id` used here.<br/>
The signatures are encoded as the concatenation of the SEC1 encodings of the two values `r` and `s`. For curve `secp256k1`, this corresponds to 32-byte big-endian encoding.<br/>
This call requires that the ECDSA feature is enabled, the caller is a canister, and `message_hash` is 32 bytes long. Otherwise it will be rejected.

Note that in case of high system load, a request to compute an ECDSA signature may time out. In this case, the canister may want to back off and retry the request later.

## API Fees

The fees for the ECDSA signing API are as defined below. The threshold ECDSA test key resides on a regular-sized (13-node) application subnet, while the threshold ECDSA production key resides on an about 30-node-sized fiduciary subnet. The subnet size of the subnet where the threshold signature key resides and the signatures are computed define the resulting cost. The size of the subnet of the calling canister does not matter for the fees. For costs in USD, the USD/XDR exchange rate as of of November 23, 2022 has been used.

:::note
If a canister using this feature is intended to be blackholed, but also for other canisters, it is recommended to send more cycles with the call than the advertised cost of the call so that if the subnet size of the signing subnet increases in the future, the higher costs per signature are still covered. Any cycles not charged in a call are refunded.
:::

### Fees for the t-ECDSA Test Key

| Transaction                          | Description                                                                                                    | Cycles (test key)                     | USD                         |
|--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------------------------|-----------------------------|
| Threshold ECDSA signing              | For computing one threshold ECDSA signature (`sign_with_ecdsa`)                                                | 10,000,000,000              | $0.0130886                  |

### Fees for the t-ECDSA Production Key

| Transaction                          | Description                                                                                                    | Cycles (production key)                     | USD                         |
|--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------------------------|-----------------------------|
| Threshold ECDSA signing              | For computing one threshold ECDSA signature (`sign_with_ecdsa`)                                                | 26,153,846,153              | $0.0342317                  |

## Environments

In order to facilitate developers throughout the canister development life cycle on the IC, the feature is available in both the SDK for local development and testing as well as on the IC for pre-production testing and production operation of canisters.

### SDK

The development of canisters is typically done in the developer's local environment, facilitated by use of the SDK. The SDK has been extended such that the management canister API for threshold ECDSA is available in the local canister execution environment. Thus, canisters using the threshold ECDSA API can be run locally for development and testing purposes. The feature is always enabled in the SDK, so no further user action is required in order to make use of the API.

When the replica of the SDK environment is first started up, a new ECDSA key is generated. This key is then stored in non-volatile memory so that it does not change with every restart of the replica.

For the technically interested readers we want to note that the SDK uses the exact same implementation of threshold ECDSA as the mainnet, but only runs a single replica. Thus, the protocol is operating with a single replica, which means it degenerates to a special case and incurs only little overhead, e.g., for key generation and signing, and can thus remain enabled by default in the SDK without noticeably affecting performance of the SDK environment. Also note that the signing throughput and latency in the local SDK environment is not representative for the throughput and latency on the IC.

### Internet Computer

Any canister on any subnet of the IC can call the threshold ECDSA API exposed by the management canister. The calls are routed via XNet communication to the ECDSA-enabled subnet that holds the key referred to in the API call (only one such signing subnet holding a test key and one signing subnet holding the production key are available currently). Note that this test key is hosted on a subnet with a replication factor of only 13 and may be deleted in the future, thus it should not be used for anything of value, but rather solely for development and testing purposes. The main intended purpose is to facilitate the development and testing of Bitcoin-enabled dApps using Bitcoin testnet.

As part of the general availability (GA) release of the feature, a production ECDSA key on the `secp256k1` elliptic curve has been deployed to be used for integration with the Bitcoin Mainnet and other use cases of interest.

-->
