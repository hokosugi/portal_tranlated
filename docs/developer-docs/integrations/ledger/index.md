# ICP台帳

## 概要

Internet Computer Protocol (ICP) は、ユーティリティ・トークン (ティッカー「ICP」) の管理を、**台帳canister** と呼ばれる専用のcanister を使って実装しています。単一の台帳canister があり、Internet Computer の特別なサブネット（NNS サブネット）上で他のcanisters とともに実行されます。台帳canister は**アカウントと** **トランザクションを**保持するスマートコントラクトです。これらのトランザクションは、アカウント用に**ICPトークンを発行**するか、あるアカウントから別のアカウントに**ICPトークンを移転**するか、**ICPトークンをバーンして**消滅させます（例えばICPトークンをcycles に変換する場合など）。台帳canister は、発生ステート（初期状態）から始まるすべてのトランザクションの追跡可能な履歴を保持します。

このセクションの構成は以下のとおりです：

- このページでは、ICPと台帳の基本について紹介します。元帳の完全なパブリック・インターフェース（canister ）の詳細については、[参考文献セクションの](/docs/current/references/) [仕様を](/docs/current/references/ledger)ご覧ください。
- [Interact with the ICP ledger](interact-with-ledger.md)では、コマンドライン、JavaScript アプリケーション、canisters から ICP ledger と対話するためのチュートリアルを提供します。
- [Ledger local setup](./ledger-local-setup.md)」では、開発用にローカル・レプリカに ledgercanister をデプロイする手順を説明します。
- [新しいトークンのデプロイでは](./deploy-new-token.md)、カスタム元帳canister をデプロイして独自のトークンを作成し、[Rosetta API を](../rosetta/)介して取引所で利用できるようにする方法を説明します。
- [アカウントのトリミングでは](./collecting-dust.md)、元帳からダストのアカウントをトリミングするメカニズムについて説明します。

## アーキテクチャ

### アカウント

アカウントは、Internet Computer (IC) プリンシパルでなければならないアカウント所有者に属し、その所有者によって管理されます。複数の IC プリンシパルがアカウントを所有することはできません (「共同アカウント」はありません)。ただし、プリンシパルは外部ユーザーを指すことも、canister を指すこともできるので、共同アカウントはcanisters として実装することができます。

アカウント所有者は複数のアカウントを管理してもよい。この場合、各アカウントはペア(account\_owner, sub\_account)に対応します。サブアカウントはオプションのビット文字列で、同じ所有者の異なるサブアカウントを区別するのに役立ちます。

元帳上のアカウントは、プリンシパルIDとサブアカウント識別子から得られるアドレスによって識別されます。

この文脈では、プリンシパル識別子は、ビットコインやイーサリアムのユーザーの公開鍵のハッシュと大まかに同等であると考えることができます。対応する秘密鍵を使用してメッセージに署名することで、元帳canister を認証し、プリンシパルのアカウントを操作します。Canisters は、元帳canister にアカウントを持つこともできます。この場合、アドレスはcanisterのプリンシパルから派生します。

この場合、アドレスはcanister のプリンシパルから派生します。元帳 は、Internet Computer 内部の管理操作を使用して初期化されます。初期化プロセスの一環として、canister は、アカウントと関連する ICP トークン残高のセットで作成されます。

:::info 元帳がプリンシパルIDだけでなくアカウントIDを使用するのはなぜですか？

アカウントを導入した主な理由は、プリンシパルが複数のアカウントを管理できるようにするためです。ウォレットソフトウェアを利用することで、このような事態を避けることができますが、canisters
 ::：

### トランザクションの種類

台帳の内部ステートメントを変更できる操作は3つありますcanister ：

- アカウントの**ICP トークンの**発行。

- アカウント間での ICP**トークンの移動**。

- ICP トークンの**バーン**。

すべての操作は、元帳canister のトランザクションとして記録されます。

台帳はハッシュ化されたブロックチェーン、つまりcanister スマートコントラクト（台帳canister ）内で実行されるブロックチェーンとしてトランザクションを管理し、そのブロックチェーンはNNSサブネットのブロックチェーン上で実行されます。

ステートの変更が記録されると、それぞれの新しいトランザクションがブロックに配置され、一意のインデックスが割り当てられます。チェーン全体は、最新のチェーンリンクに署名することで定期的に認証されます。チェーンの認証に使用される署名は、Internet Computer のルート公開鍵にアクセスできる第三者であれば誰でも検証できます。特定のトランザクションは、台帳に問い合わせることで取得できます。

### ICP ユーティリティ・トークンの基本特性

ICP トークンは、Bitcoin のような分散型ネットワークを管理するユーティリティ・トークンに似ていますが、重要な点でも異なります。

ICPトークンは以下の点でビットコインと似ています：

- 各 ICP トークンは 10^8 回分割可能です。

- すべてのトランザクションは、ジェネシスの初期ステートから始まる台帳に保存されます。

- トークンは完全に交換可能です。

- アカウント識別子は32バイトで、公開鍵のハッシュとほぼ同じです。

ICPトークンは以下の点でビットコインと異なります：

- プルーフ・オブ・ワークを使用するのではなく、ステークされた参加ノードはチェーンの有効なステートについて合意するために閾値BLS署名の変種を使用します。

- このメモフィールドはRosetta APIによってトランザクションを区別するnonceを格納するために使用されます。しかし、このフィールドには他の用途も考えられます。

<!---
# The ICP ledger

## Overview

The Internet Computer Protocol (ICP) implements management of its utility token (ticker "ICP") using a specialized canister, called the **ledger canister**. There is a single ledger canister which runs alongside other canisters on a special subnet of the Internet Computer - the NNS subnet. The ledger canister is a smart contract that holds **accounts** and **transactions**. These transactions either **mint ICP tokens** for accounts, **transfer ICP tokens** from one account to another, or **burn ICP tokens**, eliminating them from existence, e.g. while converting ICP tokens to cycles. The ledger canister maintains a traceable history of all transactions starting from its genesis state (initial state). 

This section is structured as follows:

- This page provides an introduction to the basics of ICP and the ledger. For a more detailed explanation of the complete public interface of the ledger canister head over to the [specification](/docs/current/references/ledger) in the [references section](/docs/current/references/).
- [Interact with the ICP ledger](interact-with-ledger.md) provides hands on tutorials to interact with the ICP ledger from the command line, JavaScript applications and from canisters.
- [Ledger local setup](./ledger-local-setup.md) walks you through the steps to deploy a ledger canister to your local replica for development.
- [Deploy new token](./deploy-new-token.md) explains how you can create your own token by deploying a custom ledger canister and make it available to exchanges via the [Rosetta API](../rosetta/).
- [Account trimming](./collecting-dust.md) explains the mechanism to trim the dust accounts from the ledger.

## Architecture

### Accounts

An account belongs to and is controlled by the account owner who must be an Internet Computer (IC) principal. No account can be owned by two or more IC principals (no "joint accounts"). However, since a principal can refer to an external user as well as to a canister, joint accounts can be implemented as canisters.

An account owner may control more than one account. In this case, each account corresponds to a pair (account_owner, sub_account). The sub-account is an optional bitstring which helps distinguish between the different sub-accounts of the same owner.

An account on the ledger is identified by its address, which is derived from the principal ID and sub-account identifier.

In this context, you can think of principal identifiers as a rough equivalent to the hash of a user’s public key for Bitcoin or Ethereum. You use the corresponding secret key to sign messages and therefore authenticate to the ledger canister and operate on the principal’s account. Canisters can also have accounts in the ledger canister, in which case the address is derived from the canister’s principal.

The ledger canister is initialized using administrative operations that are internal to the Internet Computer. As part of the initialization process, the canister is created with the set of accounts and associated ICP token balances.

:::info Why does the ledger use Account IDs and not just Principal IDs?

The main reason for introducing accounts was to allow a principal to control multiple accounts. While this could be abstracted away for a user by a the wallet software, this is not possible for canisters.
:::

### Transaction types

There are three operations that can change the internal state of the ledger canister:

-   **Minting ICP tokens** for accounts.

-   **Transferring ICP tokens** between accounts.

-   **Burning ICP tokens**.

All operations are recorded as transactions in the ledger canister.

The ledger maintains the transactions as a hashed blockchain, i.e., a blockchain running inside a canister smart contract (the ledger canister), which in turn is running on the NNS subnet blockchain.

As state changes are recorded, each new transaction is placed in a block and assigned a unique index. The entire chain is regularly authenticated by signing the latest chain link. The signature used to authenticate the chain can be verified by any third party who has access to the root public key of the Internet Computer. Specific transactions can be retrieved by querying the ledger.


### Basic properties for ICP utility tokens

The ICP token is similar to utility tokens governing decentralized networks such as Bitcoin, but also differs in important ways.

The ICP token is similar to Bitcoin in the following ways:

-   Each ICP token is divisible 10^8 times.

-   All transactions are stored in the ledger starting with the genesis initial state.

-   Tokens are entirely fungible.

-   Account identifiers are 32 bytes and are roughly the equivalent of the hash of a public key, optionally together with some additional sub-account specifier.

The ICP token differs from Bitcoin in the following ways:

-   Rather than using proof of work, staked participant nodes use a variant of threshold BLS signatures to agree on a valid state of the chain.

-   Any transaction can store an 8-byte memo — this memo field is used by the Rosetta API to store the nonce that distinguishes between transactions. However, other uses for the field are possible.

-->
