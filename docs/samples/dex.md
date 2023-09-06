# 分散型取引所（DEX）のサンプル

## 概要

IC上でDeFiアプリケーションを実現するには、canisters 、トークンcanisters および台帳canister とやり取りする必要があります。このサンプルdapp は、これらのやり取りを促進する方法を示しています。[YouTubeで](https://youtu.be/fLbaOmH24Gs)簡単に紹介されています。

このサンプルは [Motoko](https://github.com/dfinity/examples/tree/master/motoko/defi)と[Rustで](https://github.com/dfinity/examples/tree/master/rust/defi)実装されており、[IC上で実行されている](https://gzz56-daaaa-aaaal-qai2a-cai.ic0.app/)のを見ることができます。

## アーキテクチャ

ICの設計は、より複雑なオンチェーン計算を可能にします。安価なストレージと組み合わせることで、オンチェーンでのオーダーブックが可能になります。このサンプル・コードはこれらの機能を利用し、ユーザーの残高と注文を取引所canister 内に保存します。取引所のサンプル機能は以下のステップに集約できます：

- 取引所が資金を預かります（トークンと ICP では仕組みが異なります。）

- 取引所が内部残高帳簿を更新。

- ユーザーは取引所で取引を行い、取引所は内部残高帳簿を更新します。

- 取引所から資金を引き出すと、保管はユーザーに戻ります。

### インターフェース

取引所からユーザー固有の元帳口座識別子を要求します。この一意の口座識別子は、取引所の元帳口座のユーザー固有のサブ口座を表し、ユーザーの預金を区別できるようにします。

    getDepositAddress: () -> (blob);

取引所へのユーザー預金を開始します。ユーザーが ICP を入金したい場合、取引所はユーザー固有の入金アドレスからそのデフォルトのサブアド レスへ資金を移動し、DEX 上のユーザーの ICP 残高を調整します。ユーザーが DIP トークンの入金を希望する場合、取引所は承認された資金をトークン口座に移動し、ユーザーの残高を調整しようとします。

    deposit: (Token) -> (DepositReceipt);

取引所への引き出し要求。ユーザーが十分な残高を持っている場合、取引所はユーザーに資金を送り返します。

    withdraw: (Token, nat, principal) -> (WithdrawReceipt);

取引所に新規注文を出します。注文が既存の注文と一致した場合、その注文は執行されます。

    placeOrder: (Token, nat, Token, nat) -> (OrderPlacementReceipt);

ユーザーは発注した注文をキャンセルできます。

    cancelOrder: (OrderId) -> (CancelOrderReceipt);

特定のトークンの取引所におけるユーザー残高を要求します。

    getBalance: (Token) -> (nat) query;

### 手数料

取引から手数料を差し引くのは取引所の責任です。これは、取引所が引き出しと内部転送の手数料を支払う必要があるため重要です。

## トークン取引所のウォークスルー

このセクションには、中核となる取引所の機能の詳細なウォークスルーが含まれています。ほとんどのやり取りは複数のステップを必要とし、提供されるフロントエンドを使用することで簡素化されます。取引所canister の機能は公開されているため、上級ユーザーは`dfx` を使用して取引所とやり取りすることができます。

### ICP の入金

元帳canister は独自のインターフェースを提供しているため、ICP とのやり取りは個別に解決する必要があります。

- ユーザーは`getDepositAddress` 関数を呼び出します。応答には、取引所が管理するユーザー固有のサブアカウントを表す一意のアカウント識別子が含まれます。取引所は、このアドレスを通じて入金担当ユーザーを識別できます。

- ユーザーは、取得した入金アドレスに ICP を転送し、転送が完了するのを待ちます。

- 取引所に通知するために、ユーザーは ICP トークンのプリンシパルで`deposit` に電話をかけます。取引所はユーザーのサブアカウントを調べ、取引所におけるユーザーの残高を調整します。第二段階として、取引所はユーザーのサブアカウントからデフォルトのサブアカウントに資金を移します。

### トークンの入金

開発中のトークン規格は多数あります（IS20、DFT、DRC20 など）。

- ユーザーはトークンcanister の`approve` 関数を呼び出します。これにより、取引所はユーザーに代わって自分自身に資金を送金できるようになります。

- ICP 入金と同様に、ユーザーは取引所の`deposit` 関数を呼び出します。その後、取引所は承認されたトークンの資金を自分自身に送金し、ユーザーの取引所残高を調整します。

### 注文の発注

取引所に資金を入金した後、ユーザーは注文を出すことができます。注文は`from: (Token1, amount1)` と`to: (Token2, amount2)` の 2 つのタプルで構成されます。これらの注文は取引所に追加されます。これらの注文に何が起こるかは、取引所の実装に固有です。このサンプルでは、完全に一致する注文のみを実行する単純な取引所を提供します。これは単なるおもちゃの取引所であり、取引所の機能は完全性のためのものであることに注意してください。

### 資金の引き出し

資金の預け入れに比べ、資金の引き出しは簡単です。取引所は資金を保管しているため、`withdraw` の要求に応じて取引所は資金をユーザーに送り返します。取引所の内部残高はそれに応じて調整されます。

## 前提条件

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。
- \[[cmake](https://cmake.org/) をダウンロードします。
- \[x\][npm](https://nodejs.org/en/download/) をダウンロードします。
- \[x\] Rustバージョンをデプロイする場合は、ターゲットとしてWasmを追加してください：
  `rustup target add wasm32-unknown-unknown`

### ステップ 1: プロジェクトの GitHub リポジトリをダウンロードし、依存関係をインストールします：

    git clone --recurse-submodules --shallow-submodules https://github.com/dfinity/examples.git
    # for the rust implementation examples/rust/defi
    cd examples/motoko/defi
    make install

インストールスクリプトは、取引所のフロントエンドにアクセスするためのURLを出力します：

    ===== VISIT DEFI FRONTEND =====
    http://127.0.0.1:4943?canisterId=by6od-j4aaa-aaaaa-qaadq-cai
    ===== VISIT DEFI FRONTEND =====

または、URL "http://127.0.0.1:4943?canisterId=$(dfxcanister id frontend) "を再生成することもできます。この URL をウェブブラウザで開きます。

### ステップ 2：取引所とやり取りするには、ログインボタンをクリックして、ローカルのインターネット ID を作成します。

:::caution
このサンプルプロジェクトでは、ローカルのテストバージョンの Internet Identity を使用します。メインネットの Internet Identity を使用**しないで**ください。このテストネットの Internet Identity はメインネットでは機能しません。
::：

![DEX II Login](./_attachments/dex-ii.png)

### ステップ 3: プロンプトが表示されたら、「**Create Internet Identity**」を選択します。

![II Step 1](./_attachments/II1.png)

### ステップ4: 次に「**Create Passkey**」を選択します。

![II Step 2](./_attachments/II2.png)

### ステップ 5: CAPTCHAを完了します。

![II Step 3](./_attachments/II3.png)

### ステップ6： II番号を保存し、「**I saved it, continue**」をクリックします。

![II Step 4](./_attachments/II4.png)

### ステップ7： 取引所のフロントエンドウェブページにリダイレクトされます。

![II Step 5](./_attachments/II5.png)

### ステップ8： フロントエンドからコピーできるIIプリンシパルで初期化スクリプトを実行することで、トークンとICPを自分に付与できます。

![II Principal](./_attachments/II-principal.png)

### ステップ 9: 次に、以下のコマンドを実行します：

`make init-local II_PRINCIPAL=<YOUR II PRINCIPAL>`

### ステップ 10ウェブブラウザを更新して、トークンが入金されたことを確認します。

![II Deposit](./_attachments/II-deposit.png)

自分自身とトークンを取引するには、2つ目のシークレットブラウザウィンドウを開きます。

## よくある間違いとトラブルシューティング

- 同時実行：canister 関数に`await` ステートメントがある場合、実行がインターリーブされる可能性があります。バグを回避するためには、データ構造の更新の配置を注意深く検討し、ダブルスペンド攻撃を防ぐ必要があります。

- 浮動小数点: より高度な交換では、浮動小数点に注意し、小数を制限する必要があります。

- 待機後にパニックを発生させない：パニックが発生すると、ステートがロールバックされます。これは、交換の正しさに問題を引き起こす可能性があります。

<!---
# Decentralized exchange (DEX) sample

## Overview

To enable DeFi applications on the IC, canisters need to interact with token canisters and the ledger canister. This sample dapp illustrates how to facilitate these interactions. You can see a quick introduction on [YouTube](https://youtu.be/fLbaOmH24Gs).

The sample exchange is implemented in [Motoko](https://github.com/dfinity/examples/tree/master/motoko/defi) and [Rust](https://github.com/dfinity/examples/tree/master/rust/defi) and can be seen [running on the IC](https://gzz56-daaaa-aaaal-qai2a-cai.ic0.app/).

## Architecture

The design of the IC allows for more complex on-chain computation. In combination with cheap storage, it is possible to have on-chain order books. This sample code takes advantage of these features and stores user balances and orders inside the exchange canister. The sample exchange functionality can be condensed into the following steps:

-   Exchange takes custody of funds (different mechanism for tokens and ICP, see below).

-   Exchange updates internal balance book.

-   Users trade on exchange causing the exchange to update its internal balance book.

-   Withdrawing funds from the exchange gives custody back to the user.

### Interface

Request user-specific ledger account identifier from the exchange. This unique account identifier represents a user-specific subaccount in the exchange’s ledger account, allowing it to differentiate between user deposits.

    getDepositAddress: () -> (blob);

Initiate user deposit to exchange. If the user wants to deposit ICP, the exchange moves the funds from the user-specific deposit address to its default subaddress and adjusts the user’s ICP balance on the DEX. If the user wants to deposit a DIP token, the exchange tries to move the approved funds to its token account and adjusts the user’s balance.

    deposit: (Token) -> (DepositReceipt);

Withdraw request to the exchange. The exchange will send funds back to the user if the user has a sufficient balance.

    withdraw: (Token, nat, principal) -> (WithdrawReceipt);

Place new order to exchange. If the order matches an existing order, it will get executed.

    placeOrder: (Token, nat, Token, nat) -> (OrderPlacementReceipt);

Allows the user to cancel submitted orders.

    cancelOrder: (OrderId) -> (CancelOrderReceipt);

Request user’s balance on exchange for a specific token.

    getBalance: (Token) -> (nat) query;

### Fee

It is the responsibility of the exchange to subtract fees from the trades. This is important because the exchange must pay fees for withdrawals and internal transfers.

## Token exchange walkthrough

This section contains a detailed walkthrough of the core exchange functionalities. Most interactions require multiple steps and are simplified by using the provided frontend. Since the exchange canister functions are public, advanced users can use `dfx` to interact with the exchange.

### Depositing ICP

The ledger canister provides a unique interface so that interactions with ICP need to be resolved separately.

-   The user calls the `getDepositAddress` function. The response contains a unique account identifier representing a user-specific subaccount controlled by the exchange. The exchange can identify the user responsible for deposits through this address.

-   User transfers ICP to the fetched deposit address and waits for the transfer to complete.

-   To notify the exchange, the user calls `deposit` with the ICP token principal. The exchange will look into the user’s subaccount and adjust the user’s balance on the exchange. In a second step, the exchange will transfer the funds from the user subaccount to its default subaccount, where the exchange keeps all of its ICP.

### Depositing tokens

There are a number of token standards in development (e.g. IS20, DFT, and DRC20); This sample uses DIP20.

-   The user calls the `approve` function of the token canister. This gives the exchange the ability to transfer funds to itself on behalf of the user.

-   Similar to the ICP depositing, the user calls the `deposit` function of the exchange. The exchange then transfers the approved token funds to itself and adjusts the user’s exchange balance.

### Placing orders

After depositing funds to the exchange, the user can place orders. An order consists of two tuples. `from: (Token1, amount1)` and `to: (Token2, amount2)`. These orders get added to the exchange. What happens to these orders is specific to the exchange implementation. This sample provides a simple exchange that only executes exactly matching orders. Be aware this is just a toy exchange, and the exchange functionality is just for completeness. 

### Withdrawing funds

Compared to depositing funds, withdrawing funds is simpler. Since the exchange has custody of the funds, the exchange will send funds back to the user on `withdraw` requests. The internal exchange balances are adjusted accordingly.

## Prerequisites
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download [cmake](https://cmake.org/).
- [x] Download [npm](https://nodejs.org/en/download/).
- [x] If you want to deploy the Rust version, make sure you add Wasm as a target:
    `rustup target add wasm32-unknown-unknown`


### Step 1: Download the project's GitHub repo and install the dependencies:

```
git clone --recurse-submodules --shallow-submodules https://github.com/dfinity/examples.git
# for the rust implementation examples/rust/defi
cd examples/motoko/defi
make install
```

The install scripts output the URL to visit the exchange frontend:

```
===== VISIT DEFI FRONTEND =====
http://127.0.0.1:4943?canisterId=by6od-j4aaa-aaaaa-qaadq-cai
===== VISIT DEFI FRONTEND =====
```

or you can regenerate the URL "http://127.0.0.1:4943?canisterId=$(dfx canister id frontend)". Open this URL in a web browser.

### Step 2: To interact with the exchange, you can create a local Internet Identity by clicking the login button.

:::caution
This sample project uses a local test version of Internet Identity. **Do not** use your mainnet Internet Identity, and this testnet Internet Identity will not work on the mainnet.
:::

![DEX II Login](./_attachments/dex-ii.png)

### Step 3: When prompted, select **Create Internet Identity**.

![II Step 1](./_attachments/II1.png)

### Step 4: Then select **Create Passkey**.

![II Step 2](./_attachments/II2.png)

### Step 5: Complete the CAPTCHA.

![II Step 3](./_attachments/II3.png)

### Step 6: Save the II number and click **I saved it, continue**.

![II Step 4](./_attachments/II4.png)

### Step 7: You will be redirected to the exchange's frontend webpage.

![II Step 5](./_attachments/II5.png)

### Step 8: You can give yourself some tokens and ICP by running an initialization script with your II principal that you can copy from the frontend.

![II Principal](./_attachments/II-principal.png)

### Step 9: Then run the following command:

`make init-local II_PRINCIPAL=<YOUR II PRINCIPAL>`

### Step 10: Refresh the web browser to verify that your tokens were deposited. 

![II Deposit](./_attachments/II-deposit.png)

To trade tokens with yourself, you can open a second incognito browser window.

## Common mistakes and troubleshooting

-   Concurrent execution: if canister functions have `await` statements, it is possible that execution is interleaved. To avoid bugs, it is necessary to carefully consider the placement of data structure updates to prevent double-spend attacks.

-   Floating points: more advanced exchanges should take care of floating points and make sure to limit decimals.

-   No panics after await: when a panic happens, the state gets rolled back. This can cause issues with the correctness of the exchange.

-->
