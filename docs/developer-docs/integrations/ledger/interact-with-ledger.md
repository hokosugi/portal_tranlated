# ICP元帳との対話

## 概要

## コマンドラインからのICP台帳との対話

`dfx` は、ICP ledger と関連機能を操作するための便利なコマンドです。canister [ここに](https://internetcomputer.org/docs/current/references/cli-reference/dfx-ledger.md)ドキュメントを見つけるか、次のコマンドをコンソールに入力するだけです：

``` bash
dfx ledger --help
```

サブコマンドの`--help` フラグもチェックする価値があります。

現在、`dfx` は、ICP ledger 機能のサブセット、つまり`balance` と`transfer` のみを公開しています。
両コマンドには、ledgercanister id (`--ledger-canister-id`) を指定するフラグがあります。これにより、ローカル台帳デプロイメントや、同じインターフェイスを提供する他のトークンとのやり取りが簡単になります。

### 残高

特定のアカウントのICP残高を取得します：

``` bash
dfx ledger --network ic balance <account-id>
```

`<account-id>` は 16 進文字列としてエンコードされます。を実行すると、現在の`dfx` ID のアカウント ID を表示できます：

``` bash
dfx ledger account-id
```

多くの場合、特定のプリンシパルのメイン口座残高を確認したいものです。`--of-principal` 引数を指定しながら、`balance` コマンドと`account-id` コマンドを組み合わせると、この便利なコマンドを実行できます：

``` bash
dfx ledger --network ic balance $(dfx ledger account-id --of-principal <principal-id>)
```

### 振替

Transfer機能は、ICPを自分の口座から他の口座に移すために使用できます。

``` bash
dfx ledger --network ic transfer --amount <amount> --memo <memo> <receiver-account-id>
```

<!-- ## Interact with ICP using Candid UI -->

## ウェブアプリケーションからICP元帳と対話

JavaScriptアプリケーションからICP ledgerを簡単に操作するために、[nns-jsライブラリを](https://github.com/dfinity/nns-js)使用することができます。

## アプリケーションからICPと対話canister

canisterこの[例では](/docs/current/samples/token-transfer)、Motoko と Rust での`balance` と`transfer` の使い方を紹介しています。

### ICPの受信

canister canister がICPで支払いを受け取るには、 が支払いについて知っていることを確認する必要があります。canister 

これを実現するには、現在主に2つのパターンがあります。さらに、台帳とトークン化に関する[ワーキンググループが](https://forum.dfinity.org/t/announcing-technical-working-groups/11781)あり、標準的な台帳/トークン・インターフェースと決済フローを定義することに注力しています。

#### 送信者による直接通知

このパターンでは、送信者が受信者に支払いを通知します。ただし、受信者は台帳の[`query_blocks` インタフェースを](/docs/current/references/ledger#_getting_ledger_blocks)使用して支払いを検証する必要があります。
次の図は、このパターンを簡略化したものです：

``` plantuml
    participant Sender
    participant "ICP Ledger"
    participant Receiver
    
    Sender -> "ICP Ledger": transfer()
    "ICP Ledger" --> Sender: blockNumber
    Sender -> Receiver: notify(blockNumber)
    Receiver -> "ICP Ledger": query_blocks(blockNumber)
    "ICP Ledger" --> Receiver: block
    Receiver -> Receiver: verify payment
```

#### ICP台帳による通知（現在は無効）

このパターンでは、台帳自体が受信者に通知します。これにより、受信者は通知を即座に信頼できます。ただし、受信側への呼び出しがまだ一方向呼び出しとして実装されていないため、このフローは現在無効になっています。

``` plantuml
    participant Sender
    participant "ICP Ledger"
    participant Receiver
    
    Sender -> "ICP Ledger": transfer()
    "ICP Ledger" --> Sender: blockNumber
    Sender -> "ICP Ledger": notify(blockNumber, receiver)
    "ICP Ledger" -> "Receiver": transaction_notification(details)
```

<!---
# Interact with the ICP ledger

## Overview

## Interact with ICP ledger from the command line

`dfx` provides a convenience command to interact with the ICP ledger canister and related functionality. You can find the documentation [here](https://internetcomputer.org/docs/current/references/cli-reference/dfx-ledger.md) or just enter the following command into your console:

``` bash
dfx ledger --help
```

It's worth checking out the `--help` flag of the subcommands as well.

Currently, `dfx` exposes only a subset of the ICP ledger functionality, namely `balance` and `transfer`.
Both commands provide a flag to specify a ledger canister id (`--ledger-canister-id`). This simplifies interacting with a local ledger deployment or other tokens that provide the same interface.

### Balance

Get the ICP balance of a specific account:

``` bash
dfx ledger --network ic balance <account-id>
```

The `<account-id>` is encoded as a hex string. You can print the account id of the current `dfx` identity by running:

```bash
dfx ledger account-id
```

In many cases you want to check the main account balance of a specific principal. You can combine the `balance` command with the `account-id` command, while specifying an `--of-principal` argument to yield this helpful command:

``` bash
dfx ledger --network ic balance $(dfx ledger account-id --of-principal <principal-id>)
```

### Transfer

The transfer function can be used to transfer ICP from your account to another. 

``` bash
dfx ledger --network ic transfer --amount <amount> --memo <memo> <receiver-account-id>
```

<!-- ## Interact with ICP using Candid UI -!->

## Interact with ICP ledger from your web application

In order to simplify working with ICP ledger from JavaScript applications, you can use the [nns-js library](https://github.com/dfinity/nns-js).

## Interact with ICP from a canister

The [ICP transfer example](/docs/current/samples/token-transfer) provides a good starting point for interacting with ICP  ledger from a canister. The example showcases the usage of `balance` and `transfer` in Motoko and Rust.

### Receiving ICP

If you want a canister to receive payment in ICP you need to make sure that the canister knows about the payment, because a transfer only involves the sender and the ledger canister.

There are currently two main patterns to achieve this. Furthermore, there is a [chartered working group](https://forum.dfinity.org/t/announcing-technical-working-groups/11781) on Ledger & Tokenization which is focused on defining a standard ledger/token interface as well payment flows.

#### Direct notification by sender

In this pattern the sender notifies the receiver about the payment. However, the receiver needs to verify the payment by using the [`query_blocks` interface](/docs/current/references/ledger#_getting_ledger_blocks) of the ledger.
The following diagram shows a simplified illustration of this pattern:

```plantuml
    participant Sender
    participant "ICP Ledger"
    participant Receiver
    
    Sender -> "ICP Ledger": transfer()
    "ICP Ledger" -!-> Sender: blockNumber
    Sender -> Receiver: notify(blockNumber)
    Receiver -> "ICP Ledger": query_blocks(blockNumber)
    "ICP Ledger" -!-> Receiver: block
    Receiver -> Receiver: verify payment
```


#### Notification by ICP ledger (currently disabled)

In this pattern the ledger itself notifies the receiver. Thereby, the receiver can trust the notification immediately. However, this flow is currently disabled because the call to the receiver is not yet implemented as a one-way call. 

```plantuml
    participant Sender
    participant "ICP Ledger"
    participant Receiver
    
    Sender -> "ICP Ledger": transfer()
    "ICP Ledger" -!-> Sender: blockNumber
    Sender -> "ICP Ledger": notify(blockNumber, receiver)
    "ICP Ledger" -> "Receiver": transaction_notification(details)
```

-->
