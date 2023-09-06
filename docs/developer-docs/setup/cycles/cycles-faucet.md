# 無料で始めるcycles

## 概要

このガイドでは、dapps をメインネットにデプロイするために使用できる、最初の 20T 無料cycles を取得するために**cycles 蛇口を**使用する方法について説明します。

## 前提条件

- \[x\][このガイドに従って](/developer-docs/setup/install/index.mdx) Internet Computer SDK をインストールします。

## ステップ1：クーポンを取得します。

まず、[https://faucet](https://faucet.dfinity.org) に移動する必要があります[。dfinity.org](https://faucet.dfinity.org).
[DFINITY dev officialDiscord](https://discord.gg/jnjVVQaE2C)サーバーにcycles のリクエストを入れる必要があります。Discord サーバーに参加するには、蛇口ページの**REQUESTCYCLES**ボタンをクリックします。

![Getting Coupon](_attachments/faucet_step_1.png)

## ステップ 2:Discord サーバーに入ったら、`#cycles-faucet` チャンネルに移動します。

![Cycles-faucet](./_attachments/cycles-faucet.png)

## ステップ 3: このチャンネルで、次のようなメッセージを送信します：

> こんにちは、cycles クーポンをリクエストしたいです。ありがとうございます。

## ステップ 4: このメッセージを送信すると、DFINITY チームメンバーからDiscord ダイレクトメッセージでご連絡いたします。

:::caution
 Discord 設定が他のユーザーからのダイレクトメッセージを許可するように設定されていることを確認してください。
::：

## ステップ 5:DFINITY チームからのダイレクトメッセージには、アンケートが記載されています。このアンケートに回答してください。

![Survey](_attachments/faucet_step_2.png)

## ステップ 6: アンケートに回答したら、ダイレクトメッセージに返信し、アンケートに回答したことをチームメンバーに知らせます。その後、クーポンが送られてきます。

## ステップ 7: https:[//faucet.dfinity.org](https://faucet.dfinity.org)のウェブページに戻ります。

**次のステップを**クリックして次に進みます。

## ステップ 8: クーポンを利用します。

クーポンコードを入手したら、蛇口UIにクーポンコードを入力します。

![Enter Coupon](_attachments/faucet_step_3.png)

**NEXT STEPを**クリックして次に進みます。

## ステップ9：IC SDKをセットアップします。

次に、お使いのコンピューターに`dfx` がインストールされていることを確認します。このコマンドを実行して、コンピューター上の`dfx` のバージョンを確認します：

    dfx --version

dfxのバージョンが0.12.0未満の場合は、このコマンドを実行してください：

    sudo dfx upgrade

![Setup SDK](_attachments/faucet_step_4.png)

**NEXT STEPを**クリックして次に進みます。

## ステップ10：cycles を請求するために新しい ID を作成します。

新しいIDを作成するには、次のコマンドを使用します：

    dfx identity new MyNewIdentity

あなたのIDのシードフレーズが返されます。これを安全な場所に保存してください。

次に、この ID をデフォルトで使用するように設定します：

    dfx identity use MyNewIdentity

## ステップ11:cycles を請求します。

このコマンドを実行して、無料のcycles を要求する必要があります：

    dfx wallet --network ic redeem-faucet-coupon <your-coupon-code>

![Claim Cycles](_attachments/faucet_step_5.png)

**次のステップを**クリックして進みます。

## ステップ 12: ウォレットの確認canister.

最後のステップは、`dfx wallet --network ic balance` コマンドを使用して残高をチェックすることで、ウォレットが正しくセットアップされていることを確認することです：

![Verify Wallet Canister](_attachments/faucet_step_6.png)

## 結論

これで IC 上でウェブサイトをホストする準備ができました。またはdapp のチュートリアルに従いましょう[。](../../../tutorials/index.mdx)

<!---
# Getting started with free cycles
 
## Overview

This guide explains how to use **cycles faucet** to acquire your first amount of 20T free cycles that could be used to deploy your dapps on the mainnet.

## Prerequisites

- [x] Install Internet Computer SDK following [this guide](/developer-docs/setup/install/index.mdx).

## Step 1: Get a coupon.

First, you will need to navigate to <https://faucet.dfinity.org>. You
will need to put in a request for cycles on the [DFINITY dev official Discord](https://discord.gg/jnjVVQaE2C) server. You can click on the **REQUEST CYCLES** button on the faucet page to join the Discord server.

![Getting Coupon](_attachments/faucet_step_1.png)

## Step 2: Once inside the Discord server, navigate into the `#cycles-faucet` channel. 

![Cycles-faucet](./_attachments/cycles-faucet.png)

## Step 3: In this channel, send a message such as:

> Hello, I'd like to request a cycles coupon. Thank you.

## Step 4: After you send this message, a member of the DFINITY team will reach out to you through a Discord direct message. 

:::caution
Please ensure that your Discord settings are set to allow direct messages from other users.
:::

## Step 5: In the direct message from the DFINITY team, there will be a survey. You must complete this survey. 

![Survey](_attachments/faucet_step_2.png)

## Step 6: Once completed, reply to the direct message to inform the team member that you've completed the survey. Then, they will send you a coupon.

## Step 7: Head back to the <https://faucet.dfinity.org> webpage. 

Now, click **NEXT STEP** to continue.

## Step 8: Redeem the coupon.

Now that you have a coupon code, enter your coupon code within the faucet UI.

![Enter Coupon](_attachments/faucet_step_3.png)

Click **NEXT STEP** to continue.

## Step 9: Setup the IC SDK.

Next, confirm your computer has `dfx` installed. Run this command to check the version of `dfx` on your computer:

    dfx --version

If your dfx version is below 0.12.0, please run this command:

    sudo dfx upgrade

![Setup SDK](_attachments/faucet_step_4.png)

Click **NEXT STEP** to continue.

## Step 10: Create a new identity to claim your cycles.

To create a new identity, use the command:

```
dfx identity new MyNewIdentity
```

Your identity's seed phrase will be returned. Be sure to save this in a secure location.

Then, set this identity to be used by default:

```
dfx identity use MyNewIdentity
```

## Step 11: Now, claim your cycles. 

You will need to claim your free cycles by running this command:

```
dfx wallet --network ic redeem-faucet-coupon <your-coupon-code>
```

![Claim Cycles](_attachments/faucet_step_5.png)

Click **NEXT STEP** to continue.

## Step 12: Verify wallet canister.

The last step is to verify the wallet is setup correctly, by checking its balance using the `dfx wallet --network ic balance` command:

![Verify Wallet Canister](_attachments/faucet_step_6.png)

## Conclusion

Now you are ready to host a website on the IC or follow one of our dapp tutorials, which can be found [here.](../../../tutorials/index.mdx)


-->
