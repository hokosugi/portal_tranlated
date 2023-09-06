# 最初のビットコインの展開dapp

## 概要

このチュートリアルでは、Internet Computer で**ビットコインを送受信できる** [canister スマートコントラクトの](https://wiki.internetcomputer.org/wiki/Canister_smart_contract)サンプルをデプロイする方法を説明します。

## アーキテクチャ

内部的に
の[ECDSA API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-ecdsa_public_key)とInternet Computer の[Bitcoin API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin-api)を活用しています[。](https://github.com/dfinity/examples/)

ICP \< \> BTCの統合に関するより深い理解については、[Bitcoinの統合に関する](https://wiki.internetcomputer.org/wiki/Bitcoin_Integration)IC wikiの記事を参照してください。

## 前提条件

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールします。

## ステップ1：サンプルコードのビルドとデプロイ

### スマートコントラクトのクローン

このチュートリアルでは、**同じスマートコントラクトを**異なるプログラミング言語で記述しています。 [Motoko](../developer-docs/backend/motoko/index.md)と[Rust](../developer-docs/backend/rust/index.md)です。

どちらも同じように機能するので、どちらをクローンしてデプロイしてもかまいません。

- **オプション1**: スマートコントラクトをクローンして **Motoko**:

<!-- end list -->

``` bash
git clone https://github.com/dfinity/examples
cd examples/motoko/basic_bitcoin
git submodule update --init --recursive
```

- **オプション2：** **Rustで**スマートコントラクトをクローンして構築します：

<!-- end list -->

``` bash
git clone https://github.com/dfinity/examples
cd examples/rust/basic_bitcoin
git submodule update --init --recursive
```

:::info
Rustを選択し、MacOSを使用している場合は、Homebrewをインストールし、サンプルをコンパイルできるように`brew install llvm` 。
::：

### デプロイするためにcycles を取得します。

Internet Computer にデプロイするには [cycles](../developer-docs/setup/cycles)(他のブロックチェーンでは「gas」に相当します）。[cycles の蛇口から](/developer-docs/setup/cycles/cycles-faucet.md) cycles を無料で入手できます。

### にスマートコントラクトをデプロイします。Internet Computer

``` bash
dfx deploy --network=ic basic_bitcoin --argument '(variant { Testnet })'
```

#### これは何をするかというと

- `dfx deploy` コマンドラインインターフェースにスマートコントラクトを 。`deploy` 
- `--network=ic` スマートコントラクトをメインネットのICPブロックチェーンにデプロイするようコマンドラインに指示します。
- `--argument '(variant { Testnet })'` 引数 を渡してスマートコントラクトを初期化し、ビットコインテストネットに接続するよう指示します。`Testnet` 

:::info
 canister を`variant { Testnet }` で初期化し、canister が[ビットコインテストネットに](https://en.bitcoin.it/wiki/Testnet)接続するようにしています。具体的には、ビットコインコミュニティが現在使用しているビットコインテストネットワークである`Testnet3` に接続します。

**ビットコインのメインネット**に接続するには、`variant { Mainnet }`
:: を使用します：

成功すると、次のような出力が表示されます：

``` bash
Deploying: basic_bitcoin
Building canisters...
...
Deployed canisters.
URLs:
Candid:
    basic_bitcoin: https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=<YOUR-CANISTER-ID>
```

canister はライブで使用可能です！コマンドライン、または上記の出力に表示されているリンクである Candid のウェブ UI を使用して対話できます。

上記の出力では、ビットコインのCandidウェブUIcanister を見るには、URL`https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=<YOUR-CANISTER-ID>` を使用します。

![Candid web UI for bitcoin canister](_attachments/candid-web-ui-bitcoin-canister.webp)

## ステップ2：ビットコインアドレスの生成

ビットコインには様々なタイプのアドレスがあります（例：P2PKH、P2SH）。これらの
アドレスのほとんどは、ECDSA 公開鍵から生成できます。サンプルコード
は、canister が[ecdsa\_public\_key](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-ecdsa_public_key)API を使用して[P2PKH アドレスを](https://en.bitcoin.it/wiki/Transaction#Pay-to-PubkeyHash)生成する方法を示しています。

canister の Candid UI で、`get_p2pkh_address` の下にある「Call」ボタンをクリックして、
P2PKH Bitcoin アドレスを生成します：

![Generating a P2PKH Bitcoin Address](_attachments/generate-ecdsa-key.png)

または、コマンドラインを好む場合：

    dfx canister --network=ic call basic_bitcoin get_p2pkh_address

:::info

- canister が取得する
  ECDSA 公開鍵は一意であるため、表示されるビットコインアドレスは上記のものとは異なります。

- 
  私たちはビットコインテストネットアドレスを生成しています。このアドレスは、ビットコインテストネットでビットコインを送受信するためにのみ使用できます。
  ::：

## ステップ 3: ビットコインの受信

canister がデプロイされ、ビットコインアドレスが手に入ったので、次は
テストネットのビットコインを受け取る番です。[coinfaucet.eu](https://coinfaucet.eu)（
）などのビットコイン・コックを使用して、ビットコインを受け取ることができます。

アドレスを入力し、「Send testnet Bitcoins」をクリックします。以下の例では、ビットコインアドレス`n31eU1K11m1r58aJMgTyxGonu7wSMoUYe7` を使用しますが、自分のアドレスを使用します。canister はビットコインテストネットで 0.011 テスト BTC を受け取ります。

![Bitcoin Testnet Faucet](_attachments/bitcoin-testnet-faucet.png)

このようなものが表示されるはずです：

![Bitcoin Testnet Faucet](_attachments/bitcoin-testnet-faucet-received.png)

トランザクションが少なくとも 1 回確認されると、
数分かかりますが、canister'の残高で確認できるようになります。

## ステップ 4: ビットコイン残高の確認

ビットコインアドレスの残高は、canister の`get_balance` エンドポイントを使用して確認できます。

Candid UI でcanister のアドレスを貼り付け、「Call」をクリックします：

![Checking Bitcoin Balance](_attachments/bitcoin-received-funds.png)

または、コマンドラインを使用して呼び出します。必ず`mheyfRsAQ1XrjtzjfU1cCH2B6G1KmNarNL` をあなた自身の生成した P2PKH アドレスに置き換えてください：

    dfx canister --network=ic call basic_bitcoin get_balance '("mheyfRsAQ1XrjtzjfU1cCH2B6G1KmNarNL")'

ビットコインアドレスの残高を確認するには、[bitcoin\_get\_balance](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_balance)API を使用します。

## ステップ 5: ビットコインの送信

canister の`send` エンドポイントを使用してビットコインを送信できます。

Candid UI で、送信先アドレスと送信金額を追加します。以下の例
では、4'321 Satoshi (0.00004321 BTC) を testnet のコックに送り返します。

![Sending Bitcoin](_attachments/bitcoin-send-transaction.png)

コマンドライン経由では、同じ呼び出しは次のようになります：

    dfx canister --network=ic call basic_bitcoin send '(record { destination_address = "tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt"; amount_in_satoshi = 4321; })'

`send` エンドポイントは、次のようにしてビットコインを送信できます：

1.  [bitcoin\_get\_current\_fee\_percentiles APIを](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_current_fee_percentiles)使用して、ビットコインネットワーク上の最新の手数料のパーセンタイルを取得します。
2.  [bitcoin\_get\_utxos](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_utxos) API を使用して、未使用のトランザクション出力（UTXO）を取得します。
3.  ステップ 2 の UTXO の一部を入力として使用し、送信先アドレスと金額を出力として使用してトランザクショ ンを構築します。
    ステップ 1 で取得した手数料パーセンタイルを使用して、適切な手数料を設定します。
4.  [sign\_with\_ecdsa API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-sign_with_ecdsa) を使ってトランザクションの入力に署名。
5.  [bitcoin\_send\_transaction API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_send_transaction) を使用して、署名されたトランザクションをビットコインネットワークに送信します。

`send` エンドポイントは、ネットワークに送信したトランザクションの ID を返します。
ブロックエクスプローラーを使用して、このトランザクションのステータスを追跡できます。
トランザクションが少なくとも 1 回確認されると、
現在の残高に反映されていることを確認できるはずです。

## まとめ

このチュートリアルでは、以下のことができました：

- ビットコインを受信および送信できるcanister スマートコントラクトを ICP ブロックチェーン上にデプロイします。
- cycles 蛇口を使用して、canister をメインネット上の ICP ブロックチェーンに無料でデプロイします。
- canister をビットコインのテストネットに接続します。
- canister にテストネットの BTC を送ります。
- canister のテストネットBTC残高を確認します。
- canister を使用して、テストネット BTC を別の BTC アドレスに送信します。

<!---
# Deploying your first Bitcoin dapp

## Overview 
This tutorial will walk you through how to deploy a sample [canister smart contract](https://wiki.internetcomputer.org/wiki/Canister_smart_contract) **that can send and receive Bitcoin** on the Internet Computer.

## Architecture

We will be relying on the "Basic Bitcoin" example in the [examples repository](https://github.com/dfinity/examples/),
which internally leverages the [ECDSA API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-ecdsa_public_key)
and [Bitcoin API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin-api) of the Internet Computer.

For deeper understanding of the ICP < > BTC integration, see the IC wiki article on [Bitcoin integration](https://wiki.internetcomputer.org/wiki/Bitcoin_Integration).

## Prerequisites

* [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).

## Step 1: Building and deploying sample code

### Clone the smart contract

This tutorial has the **same smart contract** written in different programming languages: in [Motoko](../developer-docs/backend/motoko/index.md) and [Rust](../developer-docs/backend/rust/index.md).

You can clone and deploy either one, as they both function in the same way.

- **Option 1:** clone and build the smart contract in **Motoko**:

```bash
git clone https://github.com/dfinity/examples
cd examples/motoko/basic_bitcoin
git submodule update --init --recursive
```

- **Option 2:** clone and build the smart contract in **Rust**:

```bash
git clone https://github.com/dfinity/examples
cd examples/rust/basic_bitcoin
git submodule update --init --recursive
```

:::info
If you choose Rust and are using MacOS, you'll need to install Homebrew and run `brew install llvm` to be able to compile the example.
:::

### Acquire cycles to deploy

Deploying to the Internet Computer requires [cycles](../developer-docs/setup/cycles) (the equivalent of "gas" in other blockchains). You can get free cycles from the [cycles faucet](/developer-docs/setup/cycles/cycles-faucet.md).

### Deploy the smart contract to the Internet Computer

```bash
dfx deploy --network=ic basic_bitcoin --argument '(variant { Testnet })'
```

#### What this does
- `dfx deploy` tells the command line interface to `deploy` the smart contract
- `--network=ic` tells the command line to deploy the smart contract to the mainnet ICP blockchain
- `--argument '(variant { Testnet })'` passes the argument `Testnet` to initialize the smart contract, telling it to connect to the Bitcoin testnet

:::info
We're initializing the canister with `variant { Testnet }`, so that the canister connects to the the [Bitcoin testnet](https://en.bitcoin.it/wiki/Testnet). To be specific, this connects to `Testnet3`, which is the current Bitcoin test network used by the Bitcoin community. 

To connect to the **Bitcoin mainnet**, one should use `variant { Mainnet }`
:::


If successful, you should see an output that looks like this:

```bash
Deploying: basic_bitcoin
Building canisters...
...
Deployed canisters.
URLs:
Candid:
    basic_bitcoin: https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=<YOUR-CANISTER-ID>
```

Your canister is live and ready to use! You can interact with it using either the command line, or using the Candid web UI, which is the link you see in the output above.

In the output above, to see the Candid web UI for your bitcoin canister, you would use the URL `https://a4gq6-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=<YOUR-CANISTER-ID>`. 

![Candid web UI for bitcoin canister](_attachments/candid-web-ui-bitcoin-canister.webp)

## Step 2: Generating a Bitcoin address

Bitcoin has different types of addresses (e.g. P2PKH, P2SH). Most of these
addresses can be generated from an ECDSA public key. The example code
showcases how your canister can generate a [P2PKH address](https://en.bitcoin.it/wiki/Transaction#Pay-to-PubkeyHash) using the [ecdsa_public_key](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-ecdsa_public_key) API.

On the Candid UI of your canister, click the "Call" button under `get_p2pkh_address` to
generate a P2PKH Bitcoin address:

![Generating a P2PKH Bitcoin Address](_attachments/generate-ecdsa-key.png)

Or, if you prefer the command line:

    dfx canister --network=ic call basic_bitcoin get_p2pkh_address

:::info

* The Bitcoin address you see will be different from the one above, because the
  ECDSA public key your canister retrieves is unique.

* We are generating a Bitcoin testnet address, which can only be
used for sending/receiving Bitcoin on the Bitcoin testnet.
:::

## Step 3: Receiving Bitcoin

Now that the canister is deployed and you have a Bitcoin address, it's time to receive
some testnet Bitcoin. You can use one of the Bitcoin faucets, such as [coinfaucet.eu](https://coinfaucet.eu),
to receive some bitcoin.

Enter your address and click on "Send testnet Bitcoins". In the example below we will use bitcoin address `n31eU1K11m1r58aJMgTyxGonu7wSMoUYe7`, but you would use your own address. The canister will be receiving 0.011 test BTC on the Bitcoin Testnet.

![Bitcoin Testnet Faucet](_attachments/bitcoin-testnet-faucet.png)

You should see something similar to this:

![Bitcoin Testnet Faucet](_attachments/bitcoin-testnet-faucet-received.png)


Once the transaction has at least one confirmation, which can take a few minutes,
you'll be able to see it in your canister's balance.

## Step 4: Checking your Bitcoin balance

You can check a Bitcoin address's balance by using the `get_balance` endpoint on your canister.

In the Candid UI, paste in your canister's address, and click on "Call":

![Checking Bitcoin Balance](_attachments/bitcoin-received-funds.png)

Alternatively, make the call using the command line. Be sure to replace `mheyfRsAQ1XrjtzjfU1cCH2B6G1KmNarNL` with your own generated P2PKH address:

```
dfx canister --network=ic call basic_bitcoin get_balance '("mheyfRsAQ1XrjtzjfU1cCH2B6G1KmNarNL")'
```

Checking the balance of a Bitcoin address relies on the [bitcoin_get_balance](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_balance) API.

## Step 5: Sending Bitcoin

You can send Bitcoin using the `send` endpoint on your canister.

In the Candid UI, add a destination address and an amount to send. In the example
below, we're sending 4'321 Satoshi (0.00004321 BTC) back to the testnet faucet.

![Sending Bitcoin](_attachments/bitcoin-send-transaction.png)

Via command line, the same call would look like this:

```
dfx canister --network=ic call basic_bitcoin send '(record { destination_address = "tb1ql7w62elx9ucw4pj5lgw4l028hmuw80sndtntxt"; amount_in_satoshi = 4321; })'
```

The `send` endpoint is able to send Bitcoin by:

1. Getting the percentiles of the most recent fees on the Bitcoin network using the [bitcoin_get_current_fee_percentiles API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_current_fee_percentiles).
2. Fetching your unspent transaction outputs (UTXOs), using the [bitcoin_get_utxos API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_get_utxos).
3. Building a transaction, using some of the UTXOs from step 2 as input and the destination address and amount to send as output.
   The fee percentiles obtained from step 1 is used to set an appropriate fee.
4. Signing the inputs of the transaction using the [sign_with_ecdsa API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-sign_with_ecdsa).
5. Sending the signed transaction to the Bitcoin network using the [bitcoin_send_transaction API](https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-bitcoin_send_transaction).

The `send` endpoint returns the ID of the transaction it sent to the network.
You can track the status of this transaction using a block explorer. Once the
transaction has at least one confirmation, you should be able to see it
reflected in your current balance.

## Conclusion

In this tutorial, you were able to:

* Deploy a canister smart contract on the ICP blockchain that can receive & send Bitcoin.
* Use a cycles faucet to deploy the canister to ICP blockchain on the mainnet for free.
* Connect the canister to the Bitcoin testnet.
* Send the canister some testnet BTC.
* Check the testnet BTC balance of the canister.
* Use the canister to send testnet BTC to another BTC address. 

-->
