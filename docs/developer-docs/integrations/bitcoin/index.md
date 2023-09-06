# ビットコインの統合

## 概要

Internet Computer はビットコインネットワークと直接統合されています。これにより、Internet Computer 上のcanisters は、ビットコインの受信、保有、送信をすべてビットコインネットワーク上のトランザクションで直接行うことができます。Canisters は、ビットコインネットワーク上でビットコインを保有する通常のユーザーとまったく同じように行動できます。これらはすべて、以下の方法で可能になります：

- Internet Computer はプロトコルレベルでビットコインと統合しています。
- Canisters 閾値ECDSAに基づく連鎖鍵署名のための新しいプロトコルによって、ECDSA鍵を安全に保持（および使用）できること。

Internet Computer は、このような他のブロックチェーンとの直接統合を行う最初のブロックチェーンネットワークの一つであり、この目的のために新しい技術基盤を構築しました。

## 使用例

この統合により、多くの新しいユースケースが可能になります：

- **ビットコイン・スマートコントラクト：** canister はビットコイン・ネットワーク上のビットコインを直接保持することができ、エンジニアはcanisters を使用して強力なビットコイン・スマートコントラクトを実装することができます。どのようなcanister スマートコントラクトでも、ビットコイン・スマートコントラクト機能を提供できるようになりました。例えば、ユーザーが秘密鍵を管理する必要のないバイオメトリクス認証のオンチェーンビットコインウォレットや、ユーザーがソーシャルdApps を使用してピアツーピアのビットコイン取引を行うことができるソーシャルファイなど。
- **ビットコインの取引：** Internet Computer 上の分散型取引所において、第三者による資産の保管を必要とせずにビットコインを直接取引。
- **分散化スワップ：**SNSを利用したDAOがIC上のサービスを分散化する際に、ビットコインを利用してトークンを購入する分散化スワップ。
- **Chain Key Bitcoin (ckBTC):**ビットコインで1:1裏付けされたInternet Computer 上のビットコイン類似品。ビットコインのメインネットがビットコインの機能をリリースした直後に IC 上で利用可能になる予定。ckBTC は IC 上でビットコインを扱う最も簡単な方法。

## 例

これらは、ビットコイン統合機能の使用方法のほんの一例に過ぎません。この機能によって無限の可能性が広がるのは、あなたの想像力だけです。このドキュメントでは、あなた自身のdApps でこの機能を使用する方法を説明します。また、ビットコイン統合APIはUTXOとビットコイントランザクションレベルで動作する低レベルAPIであり、使用することは自明ではないことに注意してください。

ビットコイン機能のビットコインメインネットリリース（一般利用可能リリース）後、**チェーンキービットコイン（ckBTC）** canister が利用可能になります。ckBTCcanister は IC 上でオンチェーンビットコインを提供します。これはラップされたビットコインのように見えますが、分散型アーキテクチャであり、ブリッジの代わりにスレッショルド ECDSA を使用しているため、基本的な信頼モデルははるかに強力です。

私たちは、いくつかの明確な利点があるため、多くの人々がプロジェクトに私たちのネイティブ統合の代わりにckBTCを使用することを好むと想定しています：

- **統合が簡単:**低レベルのBitcoin統合APIを使用する代わりに、Internet Computer 上の他の台帳と同じようにckBTC台帳にアクセスできます。ckBTC台帳は、ICRC-1トークン規格に準拠し、カジブルトークンを使用します。
- **より速く、より安価な送金:**ckBTC は、Internet Computer の低い確定時間（数秒以内）で、ビットコインネットワーク上のビットコイン送金の数分の一のコストで送金できます。このスキームを使用すると、ビットコインネットワークとの決済転送のみをビットコインネットワーク上で行う必要があり、大部分の転送はIC上で直接高速かつ低コストで行うことができます。

## リソース

- ビットコイン統合の仕組みについてさらに深く知りたい方は、[仕組みの](bitcoin-how-it-works.md)ページをご覧ください。プロトコルレベルのビットコイン統合の概要、閾値ECDSAプロトコルに基づく新しいチェーンキーECDSAの実装、APIなどをご覧いただけます。
- さらに深く知りたい場合は、チェーンキー[ECDSA のドキュメントを](https://internetcomputer.org/docs/current/developer-docs/integrations/t-ecdsa)ご覧ください。
- [Bitcoin integration Wiki](https://wiki.internetcomputer.org/wiki/Bitcoin_integration).
- ローカルで Bitcoin 統合の実験に興味がある場合は、[ローカル開発ガイドを](local-development.md)参照してください。
- [サンプルレポには](https://github.com/dfinity/examples) [Rust](https://github.com/dfinity/examples/tree/master/rust/basic_bitcoin)と [Motoko](https://github.com/dfinity/examples/tree/master/motoko/basic_bitcoin)サンプル[コードのウォークスルーに従って](../../../samples/deploying-your-first-bitcoin-dapp.md)ビルドを開始できます。

<!---
# Bitcoin integration

## Overview

The Internet Computer integrates directly with the Bitcoin network. This allows canisters on the Internet Computer to receive, hold, and send Bitcoin, all directly with transactions on the Bitcoin network. Canisters can act exactly like regular users holding bitcoin on the Bitcoin network. All of this is made possible by:
-  The Internet Computer integrating with Bitcoin at the protocol level 
-  Canisters being able to securely hold (and use) ECDSA keys by means of a novel protocol for chain-key signatures, based on threshold ECDSA. 

The Internet Computer is among the first blockchain networks performing such direct integration with other blockchains and has built a novel technology foundation for this purpose.

## Use cases
This integration allows for a plethora of novel use cases:

-   **Bitcoin smart contracts:** a canister can directly hold Bitcoin on the Bitcoin network, which allows engineers to implement powerful Bitcoin smart contracts using canisters. Any canister smart contract can now offer Bitcoin smart contract functionality. For example on-chain Bitcoin wallets with biometric authentication without the user being required to manage the private key, or Social-Fi, where users can do peer-to-peer Bitcoin transactions using social dApps.
-   **Trading Bitcoin:** trade Bitcoin directly on decentralized exchanges on the Internet Computer, without requiring any third-party custody of the assets.
-   **Decentralization swap:** using Bitcoin to buy tokens in a decentralization swap when an SNS-powered DAO decentralizes a service on the IC.
-   **Chain Key Bitcoin (ckBTC):** a Bitcoin analogue on the Internet Computer that is backed 1:1 by bitcoin, will be available on the IC shortly after the Bitcoin mainnet release of the Bitcoin feature. ckBTC will be the easiest way to handle bitcoin on the IC.

## Examples
These are only a few examples of how one can use the Bitcoin integration feature. Your imagination is the only limit to the endless range of possibilities being opened up by this feature. This documentation explains how to use this feature in your own dApps. Please also note that the Bitcoin integration API is a low-level API that operates on the level of UTXOs and Bitcoin transactions and is non-trivial to use.

After the Bitcoin mainnet release (general availability release) of the Bitcoin feature, a **Chain Key Bitcoin (ckBTC)** canister will be made available. The ckBTC canister will provide on-chain bitcoin on the IC, which looks and feels like wrapped bitcoin but has a much stronger underlying trust model because of its decentralized architecture and using threshold ECDSA instead of bridges. 

We envision that many people will prefer to use ckBTC instead of our native integration for their projects because of some distinct advantages:
-   **Easier to integrate:** instead of using the low-level Bitcoin integration API, one can simply access the ckBTC ledger like any other ledger on the Internet Computer. The ckBTC ledger will adhere to the ICRC-1 token standard for fungible tokens.
-   **Faster and cheaper transfers:** ckBTC can be transferred with the low finality time of the Internet Computer (within seconds) and for a fraction of the cost of a Bitcoin transfer on the Bitcoin network. Using this scheme, only the settlement transfers with the Bitcoin network need to be done on the Bitcoin network, the majority of transfers can be done at high speed and low cost directly on the IC.

## Resources

- If you want to dive deeper into how Bitcoin integration works, see the [how it works](bitcoin-how-it-works.md) page to get an overview of the protocol-level Bitcoin integration, our novel chain-key ECDSA implementation based on a threshold ECDSA protocol, the API, and more. 
- If you want to go even deeper, have a look at the [chain-key ECDSA documentation](https://internetcomputer.org/docs/current/developer-docs/integrations/t-ecdsa).
- [Bitcoin integration Wiki](https://wiki.internetcomputer.org/wiki/Bitcoin_integration).
- If you are interested in experimenting with Bitcoin integration locally, see the [local development guide](local-development.md).
- In the [examples repo](https://github.com/dfinity/examples) you can find sample code in [Rust](https://github.com/dfinity/examples/tree/master/rust/basic_bitcoin) and [Motoko](https://github.com/dfinity/examples/tree/master/motoko/basic_bitcoin) from which you can start to build following the [sample code walk-through](../../../samples/deploying-your-first-bitcoin-dapp.md).

-->
