# 閾値ECDSA：連鎖鍵署名

## 概要

Internet Computer は、連鎖鍵署名ツールボックスの一部として、新しい閾値ECDSAプロトコルを実装しています。このプロトコルでは、ECDSAの秘密鍵は複数の当事者、すなわちIC上の閾値ECDSA対応サブネットのレプリカによって秘密共有され、秘密鍵が再構成されることなく、それらの秘密共有を使って署名が計算されます。このようなサブネットの各レプリカは、それ自体では鍵に関する情報を提供しない鍵共有を保持します。少なくとも3分の1のレプリカが、それぞれの鍵共有を使って閾値署名を生成する必要があります。実際の閾値署名プロトコルの他に、連鎖鍵ECDSAは安全な鍵の分散生成と定期的な鍵の再共有を行うプロトコルから構成されており、これらはプロトコルの重要な部分です。このため、チェーン鍵ECDSA署名は、既製の閾値ECDSAプロトコルよりもはるかに強力です。

Internet Computer の任意のサブネット上の各canister は、一意の ECDSA 鍵ペアを制御し、対応する秘密鍵で計算される署名を要求できます。署名はcanister 、すなわちECDSA鍵の正当な保有者にのみ発行されます。各canister は、そのECDSA鍵に対してのみ署名を取得することができる。canisters 自身はECDSAの秘密鍵や鍵共有を保持せず、ICの特定の閾値ECDSA対応サブネットに 委託することに注意。閾値暗号は、ブロックチェーンのトラストモデルにおいて、従来の暗号だけでは実現不可能な機能を実現するのに役立ちます。

ブロックチェーン上の閾値ECDSAの実装は、秘密鍵を安全に保管し、適格なエンティティの要求に応じて、そのエンティティにのみ署名を発行するハードウェア・セキュリティ・モジュール（HSM）のオンチェーン対応物と見なすことができます。

閾値ECDSAが利用可能になることで、例えば以下のような多くの重要なユースケースが可能になります：

- Canisters ビットコインのネイティブな保有
- イーサリアムとの統合。例えば、イーサリアムのERC-20トークンをICに取り込んだり、イーサリアムの取引に署名したり；
- トランザクションの署名にECDSAを使用する他のブロックチェーンとの統合；
- 証明書が閾値ECDSAを使用して発行される分散型認証局（CA）の実現（これには、現在実装されている曲線`secp256k1` 、すなわち`secp256r1` ）とは異なる楕円曲線が必要です。

これらは、私たちの閾値ECDSAプロトコルのユースケースのほんの一例にすぎません。創造的なエンジニアや起業家は、さらに多くのエキサイティングなユースケースを思いつくことでしょう。

## リソース

- 連鎖鍵ECDSA署名についてもっと知りたい方は、[仕組みの](./t-ecdsa-how-it-works.md)ページをご覧ください。
- さらに深く知りたい方は、GrothとShoupの[Eurocrypt 2022の論文を](https://eprint.iacr.org/2021/1330)ご覧ください。
- `threshold-ecdsa` のサンプルコードは[examples リポジトリの](https://github.com/dfinity/examples)[`motoko`](https://github.com/dfinity/examples/tree/master/motoko/threshold-ecdsa)または [`rust`](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa)サブディレクトリにあります。
- 対応するコード・ウォークスルーは[サンプル・ドキュメントに](../../../samples/t-ecdsa-sample.md)あります。

<!---
# Threshold ECDSA: chain-key signatures

## Overview
The Internet Computer implements a novel threshold ECDSA protocol as part of its chain-key signatures toolbox. In this protocol, the private ECDSA key is held in a secret-shared manner by multiple parties, namely the replicas of a threshold-ECDSA-enabled subnet on the IC, and signatures are computed using those secret shares without the private key ever being reconstructed. Each replica of such subnet holds a key share that provides no information about the key on its own. At least one third of the replicas are required to generate a threshold signature using their respective key shares. Besides the actual threshold signing protocol, chain-key ECDSA also comprises protocols for secure key distributed key generation and periodic key resharing, which are crucial parts of the protocol. This makes chain-key ECDSA signatures much more powerful than any off-the-shelf threshold ECDSA protocol.

Each canister on any subnet of the Internet Computer has control over a unique ECDSA key pair and can request signatures with the corresponding private key to be computed. A signature is only issued to the eligible canister, i.e., the legitimate holder of the ECDSA key. Each canister can obtain signatures only for its ECDSA keys. Note that canisters do not hold any private ECDSA keys or key shares themselves, but delegate this to specific threshold-ECDSA-enabled subnets of the IC. Threshold cryptography can help enable functionality in the trust model of a blockchain that would be impossible to achieve with conventional cryptography alone.

A threshold ECDSA implementation on a blockchain can be viewed as the on-chain counterpart to a hardware security module (HSM) that stores private keys securely and issues signatures on request of the eligible entities, and only to those.

The availability of threshold ECDSA allows for a multitude of important use cases, as for example:
-   Canisters natively holding Bitcoin;
-   Integration with Ethereum, e.g., getting the ERC-20 tokens of Ethereum into the IC or signing Ethereum transactions;
-   Integrations with other blockchains that use ECDSA as signature scheme for signing transactions;
-   Realizing a decentralized certification authority (CA), where certificates are issued using threshold ECDSA (this requires a different elliptic curve to the currently implemented curve `secp256k1`, namely `secp256r1`).

Those are only a few examples for use cases of our threshold ECDSA protocol. Creative engineers and entrepreneurs will likely come up with a large number of further exciting use cases.

## Resources
- If you want to learn more about chain-key ECDSA signatures, check the [how it works](./t-ecdsa-how-it-works.md) page to see more. 
- If you want to take an even deeper dive see Groth and Shoup's [Eurocrypt 2022 paper](https://eprint.iacr.org/2021/1330).
- Sample code for `threshold-ecdsa` is provided in the [examples repository](https://github.com/dfinity/examples), under either [`motoko`](https://github.com/dfinity/examples/tree/master/motoko/threshold-ecdsa) or [`rust`](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa) sub-directories. 
- You can find the corresponding code-walkthrough in the [samples documentation](../../../samples/t-ecdsa-sample.md).

-->
