# クイックスタート

## 概要

Internet Computer エコシステムの中で、Internet Computer Protocol トークン（ICP トークン）はネイティブなユーティリティ・トークンです。ICP トークンはInternet Computer のガバナンスと経済の両面で重要な役割を果たします。

このセルフ・カストディ・クイックスタート・シナリオでは、以下を想定しています：

- あなたは新しいICPトークン保有者です。

- ICP トークンで何ができるかを理解したい場合。

- SDK コマンドラインインターフェース`dfx` を使用して、ICP トークンを変換、転送、ロックする方法を知りたい場合。

まだトークン保有者でない場合は、ICPトークンを取引所から購入するか、トークンの付与を受けてから預かる必要があります。ICPトークンの入手方法とカストディ・オプションの概要については、[ICPトークンの入手方法と](/concepts/tokens-cycles.md#get-cycles) [デジタル資産の自己カストディの](custody-options-intro)選択を参照してください。

[Network Nervous System (NNS)アプリケーションや](https://nns.ic0.app)ハードウェアウォレットが提供するユーザーインターフェースなど、ICPトークンとやり取りするために別のアプリケーションを使用している場合は、そのアプリケーションのドキュメントを参照してください。

このセルフ・カストディ・クイック・スタートでは、SDK コマンドライン・インターフェイス`dfx` を使用して ICP トークンと対話することだけに焦点を当てます。

## ICP トークンの使用方法

以下の図に、トークンを使用できる最も一般的な 3 つの方法の概要を簡略化して示します。

![developers guide:icp tokens how to use](../_attachments/icp-tokens-how-to-use.svg)

この図が示すように、ICP トークンをどのように使用するかは、主にトークンを取得する目的によって異なります。

**開発**者や**起業**家であれば、ICP トークンを次のように変換できます。 **cycles**Cycles を使用してアプリケーションを構築、デプロイし、製品やサービスを市場に提供することができます。

あなたが**ガバナンスに参加**し、Internet Computer の方向性に影響を与えることに興味があるコミュニティのメンバーであれば、neuronと呼ばれるステークにICPトークンをロックすることができます。

## 前提条件

始めるには、以下を確認してください：

- \[x\] インターネットに接続し、ローカルの**Intel ベースの macOS**または**Linux**コンピュータでシェル端末にアクセスできること。

- \[x\] ローカルコンピュータで新しいターミナルシェルを開き、ターミナルで基本的なコマンドラインプログラムを実行する方法を知っていること。

- \[x\] ICPトークンをセルフ・カストディ・ウォレットに保管していること。

- \[x\] ローカルコンピュータのターミナルで以下のコマンドを実行し、SDKをダウンロードしてインストールしました：
  
  ``` bash
  sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
  ```

- \[x\] セルフ・カストディに使用する ID の公開鍵/秘密鍵のバックアップ・コピーを作成していること。
  
  たとえば、SDK`dfx` コマンドラインインターフェイスを使用して作成されたデフォルトの開発者 ID を使用している場合は、安全な場所に`~/.config/dfx/identity/default/identity.pem` ファイルのバックアップを保存しておく必要があります。

- \[x\] ICP トークンを含む操作を実行するための安全な環境があります。

セキュリティのベスト・プラクティスとして、ICP トークンの転送を伴う操作には、最小限のハードウェアとソフト ウェアを備えたエア・ギャップのあるコンピュータと、ネットワークに接続されたコンピュータの両方が必要です。実際には、2 台のコンピュータ間でファイルを移動し、リスクを最小限に抑えるためのその他の予防措置を講じる必要があります。

このページでは簡単のため、ネットワークに接続された1台のコンピュータを使って主要なタスクを完了する方法を説明します。

## 台帳への接続とアカウント識別子の取得

すべての ICP トークンのトランザクションは、Internet Computer 上で実行されている台帳canister に記録されます。この説明では、`dfx` が作成した`default` 開発者 ID を使用していることを前提としています。

この ID は、**プリンシパルデータ**型と、プリンシパルのテキスト表現（しばしば**プリンシパル識別子と**呼ばれま す）によって表されます。この ID の表現は、ビットコインやイーサリアムのアドレスに似ています。

しかし、開発者 ID に関連付けられたプリンシパルは、通常、元帳の**口座識別子とは**異なります。プリンシパル識別子とアカウント識別子は関連しており、どちらもあなたの ID をテキストで表しますが、フォーマットは異なります。

台帳に接続してアカウント情報を取得するには、次の手順を実行します：

- #### ステップ 1：次のコマンドを実行して、カレント ディレクトリに空の`dfx.json` ファイルを作成します：

<!-- end list -->

``` bash
echo '{}' > dfx.json
```

- #### ステップ 2：次のコマンドを実行して、Internet Computer ネットワークの現在のステータスと、それに接続できるかどうかを確認します：

<!-- end list -->

``` bash
dfx ping ic
```

以下のような出力が表示されるはずです：

    {
        "ic_api_version": "0.18.0"  "impl_hash": "d639545e0f38e075ad240fd4ec45d4eeeb11e1f67a52cdd449cd664d825e7fec"  "impl_version": "8dc1a28b4fb9605558c03121811c9af9701a6142"  "replica_health_status": "healthy"  "root_key": [48, 129, 130, 48, 29, 6, 13, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 1, 2, 1, 6, 12, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 2, 1, 3, 97, 0, 129, 76, 14, 110, 199, 31, 171, 88, 59, 8, 189, 129, 55, 60, 37, 92, 60, 55, 27, 46, 132, 134, 60, 152, 164, 241, 224, 139, 116, 35, 93, 20, 251, 93, 156, 12, 213, 70, 217, 104, 95, 145, 58, 12, 11, 44, 197, 52, 21, 131, 191, 75, 67, 146, 228, 103, 219, 150, 214, 91, 155, 180, 203, 113, 113, 18, 248, 71, 46, 13, 90, 77, 20, 80, 95, 253, 116, 132, 176, 18, 145, 9, 28, 95, 135, 185, 136, 131, 70, 63, 152, 9, 26, 11, 170, 174]
    }

- #### ステップ 3: (オプション) 以下のコマンドを実行して、現在使用している開発者 ID を確認します：

<!-- end list -->

``` bash
dfx identity whoami
```

ほとんどの場合、現在`default` の開発者 ID を使用していることがわかります。例えば、以下のようになります：

    default

- #### ステップ 4: (オプション) 次のコマンドを実行して、現在の ID のプリンシパルのテキスト表示を表示します：

<!-- end list -->

``` bash
dfx identity get-principal
```

このコマンドは、以下のような出力を表示します：

    tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe

- #### ステップ 5：以下のコマンドを実行して、開発者 ID のアカウント識別子を取得します：

<!-- end list -->

``` bash
dfx ledger account-id
```

このコマンドは、開発者 ID に関連付けられた元帳のアカウント識別子を表示します。たとえば、次のような出力が表示されます：

    03e3d86f29a069c6f2c5c48e01bc084e4ea18ad02b0eec8fccadf4487183c223

- #### ステップ 6: 以下のコマンドを実行して、アカウントの残高を確認します：

<!-- end list -->

``` bash
dfx ledger --network ic balance
```

このコマンドは、元帳アカウントの ICP トークンの残高を表示します。たとえば、次のような出力が表示されます：

    10.00000000 ICP

## ICPトークンをcycles

ICP トークンを元帳アカウントで使用してアプリケーションを開発する場合は、まずトークンをcycles に変換し、cycles をcanister に転送してcycles ウォレットとする必要があります。

ICP トークンをcycles に変換するには：

- #### ステップ 1: 以下のようなコマンドを実行して元帳アカウントから ICP トークンを転送し、cycles で新しいcanister を作成します：

<!-- end list -->

``` bash
dfx ledger --network ic create-canister <controller-principal-identifier> --amount <icp-tokens>
```

このコマンドは、`--amount` 引数に指定した ICP トークンの数をcycles に変換し、cycles を指定したプリンシパルによって管理される新しいcanister 識別子に関連付けます。

たとえば、以下のコマンドは、1.25 ICP トークンをcycles に変換し、新しいcanister のコントローラとしてデフォルト ID のプリンシパル識別子を指定します：

    dfx ledger --network ic create-canister tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe --amount 1.25

トランザクションが成功すると、台帳にイベントが記録され、次のような出力が表示されます：

    Transfer sent at BlockHeight: 20
    Canister created with id: "53zcu-tiaaa-aaaaa-qaaba-cai"

- #### ステップ 2: 以下のようなコマンドを実行して、新しく作成したcanister プレースホルダにcycles ウォレットコードをインストールします：

<!-- end list -->

``` bash
dfx identity --network ic deploy-wallet <canister-identifer>
```

たとえば、次のようなコマンドを実行します：

    dfx identity --network ic deploy-wallet 53zcu-tiaaa-aaaaa-qaaba-cai

このコマンドを実行すると、次のような出力が表示されます：

    Creating a wallet canister on the IC network.
    The wallet canister on the "ic" network for user "default" is "53zcu-tiaaa-aaaaa-qaaba-cai"

## ICP トークンの別のアカウントへの転送

ICP トークンを元帳の別のアカウントに転送するには、転送先のアカウントの識別子が必要です。

ICP トークンを別の口座に転送するには、次の手順を実行します：

- #### ステップ1： 次のコマンドを実行して、元帳のアカウントを管理するIDを使用していることを確認します：

<!-- end list -->

``` bash
dfx identity whoami
```

- #### ステップ 2：次のコマンドを実行して、ID に関連付けられた元帳口座の現在の残高を確認します：

<!-- end list -->

``` bash
dfx ledger --network ic balance
```

- #### ステップ 3：次のようなコマンドを実行して、ICP トークンを別のアカウントに転送します：

<!-- end list -->

``` bash
dfx ledger --network ic transfer <destination-ledger-account-id> --icp <ICP-amount> --memo <numeric-memo>
```

たとえば、次のようになります：

    dfx ledger --network ic transfer ae6e1a76da5725bbbf0c5c035aaf0525b791e0f0f7cce27d8e27826389871406 --icp 5 --memo 12345

:::info
この例では、`--icp` コマンドラインオプションで整数を使用して、指定した口座に ICP トークンを転送する方法を示しています。

- また、`--e8s` オプションを**使用して、e8s**と呼ばれる ICP トークンの端数単位を指定することもできます（単独で指定することも、`--icp` オプションと併用することもできます）。
- また、`--amount` を使用して、転送する ICP トークンの数を小数点以下 8 桁までの端数単位で指定することもできます（`5.00000025`.
  :: など）：

転送先アドレスは、Internet Computer ネットワーク上で動作する台帳canister のアドレス、[Network Nervous System アプリケーションを](https://nns.ic0.app)使用して追加したアカウント、または取引所にあるウォレットのアドレスを指定できます。

ICP トークンを[Network Nervous System アプリケーション](https://nns.ic0.app)のアカウントに転送すると、トランザクションが反映されるのを確認するためにブラウザを更新する必要がある場合があります。

`dfx ledger` コマンドラインオプションの使用方法の詳細については、[dfx ledger](/references/cli-reference/dfx-ledger.md) を参照してください。

## ICP トークンをロックするにはneuron

ガバナンスに参加して報酬を得るために ICP トークンをロックする場合は、[Network Nervous System (NNS) アプリケーション](https://nns.ic0.app)または`dfx canister call` コマンドを使用する必要があります。

[Network Nervous System (NNS) アプリケーションを](https://nns.ic0.app)使用する場合よりも、SDK コマンドラインインタフェースを使用する場合の方が、ICP トークンをロックアップしてステーキングneuronを作成するプロセスが複雑であるため、このガイドにはその手順を記載していません。

Network Nervous System については、[ Internet ComputerのNetwork Nervous System](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8) 、[ neuron](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8) の[、および ICP ユーティリティ・トークンの理解を](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8)参照してください。

neuron のロック期間およびディゾルブ遅延の設定については、[ Internet Computer Network Nervous System アプリケーションおよびウォレットを](https://medium.com/dfinity/getting-started-on-the-internet-computers-network-nervous-system-app-wallet-61ecf111ea11)参照してください。

<!---
# Self-custody quick start

## Overview
Within the Internet Computer ecosystem, Internet Computer Protocol tokens (ICP tokens) are a native utility token. ICP tokens play a key role in both the governance and economics of the Internet Computer.

This self-custody quick start scenario assumes:

-   You are a new ICP token holder.

-   You want to understand what you can do with your ICP tokens.

-   You want to know how to convert, transfer, or lock your ICP tokens using the SDK command-line interface `dfx`.

If you aren’t yet a token holder, you’ll need to purchase ICP tokens from an exchange or receive a token grant before you can take custody. For an overview of how to get ICP tokens and custody options, see [how you can get ICP tokens](/concepts/tokens-cycles.md#get-cycles) and [choosing self-custody for digital assets](custody-options-intro).

If you are using another application—such as the [Network Nervous System (NNS) application](https://nns.ic0.app) or the user interface provided by a hardware wallet—to interact with your ICP tokens, you should refer to the documentation for that application.

This self-custody quick start focuses solely on interacting with ICP tokens using the SDK command-line interface `dfx`.

## How you can use ICP tokens

The following diagram provides a simplified overview of the three most common ways you can use tokens.

![developers guide:icp tokens how to use](../_attachments/icp-tokens-how-to-use.svg)

As this diagram suggests, how you use ICP tokens depends primarily on your goals in acquiring them.

If you are a **developer** or **entrepreneur**, ICP tokens can be converted to **cycles**. Cycles can then be used to build and deploy applications to deliver products and services to the market.

If you are a member of the community interested in **participating in governance** and influencing the direction of the Internet Computer, you can lock up ICP tokens in a stake—called a neuron—so that you can submit and vote on proposals.

## Prerequisites

To get started, verify the following:
-   [x] You have an internet connection and access to a shell terminal on your local **Intel-based macOS** or **Linux** computer.
-   [x] You know how to open a new terminal shell on your local computer and how to run basic command-line programs in the terminal.
-   [x] You hold ICP tokens in a self-custody wallet.
-   [x] You have downloaded and installed the SDK by running the following command in a terminal on your local computer:

    ``` bash
    sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
    ```

-   [x] You have created a backup copy of the public/private key for the identity you are using for self-custody.

    For example, if you are using the default developer identity created using the SDK `dfx` command-line interface, you should have a backup of the `~/.config/dfx/identity/default/identity.pem` file stored in a secure location.

-   [x] You have a secure environment in which to perform operations involving ICP tokens.

As a security best practice, any operations that involve the transfer of ICP tokens would require both an air-gapped computer with minimal hardware and software and a computer connected to the network. In practice, this requires moving files between two computers and taking other precautions to minimize risks.

For simplicity, this page describes how to complete key tasks using a single computer connected to the network.

## Connect to a ledger and get your account identifier

All ICP token transactions are recorded in a ledger canister running on the Internet Computer. These instructions assume that you are using the `default` developer identity that `dfx` has created for you.

This identity is represented by a **principal** data type and a textual representation of the principal often referred to as your **principal identifier**. This representation of your identity is similar to a Bitcoin or Ethereum address.

However, the principal associated with your developer identity is typically not the same as your **account identifier** in the ledger. The principal identifier and the account identifier are related; both provide a textual representation of your identity, but they use different formats.

To connect to the ledger and get account information:

- #### Step 1:  Create an empty `dfx.json` file in your current directory by running the following command:

```bash
echo '{}' > dfx.json
```

- #### Step 2:  Check the current status of the Internet Computer network and your ability to connect to it by running the following command:

```bash
dfx ping ic
```

You should see output similar to the following:

```
{
    "ic_api_version": "0.18.0"  "impl_hash": "d639545e0f38e075ad240fd4ec45d4eeeb11e1f67a52cdd449cd664d825e7fec"  "impl_version": "8dc1a28b4fb9605558c03121811c9af9701a6142"  "replica_health_status": "healthy"  "root_key": [48, 129, 130, 48, 29, 6, 13, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 1, 2, 1, 6, 12, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 2, 1, 3, 97, 0, 129, 76, 14, 110, 199, 31, 171, 88, 59, 8, 189, 129, 55, 60, 37, 92, 60, 55, 27, 46, 132, 134, 60, 152, 164, 241, 224, 139, 116, 35, 93, 20, 251, 93, 156, 12, 213, 70, 217, 104, 95, 145, 58, 12, 11, 44, 197, 52, 21, 131, 191, 75, 67, 146, 228, 103, 219, 150, 214, 91, 155, 180, 203, 113, 113, 18, 248, 71, 46, 13, 90, 77, 20, 80, 95, 253, 116, 132, 176, 18, 145, 9, 28, 95, 135, 185, 136, 131, 70, 63, 152, 9, 26, 11, 170, 174]
}
```

- #### Step 3:  (Optional) Confirm the developer identity you are currently using by running the following command:

``` bash
dfx identity whoami
```

In most cases, you should see that you are currently using your `default` developer identity. For example:

```
default
```

- #### Step 4:  (Optional) View the textual representation of the principal for your current identity by running the following command:

``` bash
dfx identity get-principal
```

This command displays output similar to the following:

```
tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe
```

- #### Step 5:  Get the account identifier for your developer identity by running the following command:

``` bash
dfx ledger account-id
```

This command displays the ledger account identifier associated with your developer identity. For example, you should see output similar to the following:

```
03e3d86f29a069c6f2c5c48e01bc084e4ea18ad02b0eec8fccadf4487183c223
```

- #### Step 6:  Check your account balance by running the following command:

``` bash
dfx ledger --network ic balance
```

This command displays the ICP token balance from the ledger account. For example, you should see output similar to the following:

```
10.00000000 ICP
```

## Convert ICP tokens to cycles

If you want to use your ICP tokens in your ledger account to power application development, you first must convert them to cycles and transfer the cycles to a canister that will be your cycles wallet.

To convert ICP tokens to cycles:

- #### Step 1:  Create a new canister with cycles by transferring ICP tokens from your ledger account by running a command similar to the following:

``` bash
dfx ledger --network ic create-canister <controller-principal-identifier> --amount <icp-tokens>
```

This command converts the number of ICP tokens you specify for the `--amount` argument into cycles, and associates the cycles with a new canister identifier controlled by the principal you specify.

For example, the following command converts 1.25 ICP tokens into cycles and specifies the principal identifier for the default identity as the controller of the new canister:

```
dfx ledger --network ic create-canister tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe --amount 1.25
```

If the transaction is successful, the ledger records the event and you should see output similar to the following:

```
Transfer sent at BlockHeight: 20
Canister created with id: "53zcu-tiaaa-aaaaa-qaaba-cai"
```

- #### Step 2:  Install the cycles wallet code in the newly-created canister placeholder by running a command similar to the following:

``` bash
dfx identity --network ic deploy-wallet <canister-identifer>
```

For example:

```
dfx identity --network ic deploy-wallet 53zcu-tiaaa-aaaaa-qaaba-cai
```

This command displays output similar to the following:

```
Creating a wallet canister on the IC network.
The wallet canister on the "ic" network for user "default" is "53zcu-tiaaa-aaaaa-qaaba-cai"
```

## Transfer ICP tokens to another account

If you want to transfer ICP tokens to another account in the ledger, you need to know the account identifier for the destination account.

To transfer ICP tokens to another account:

- #### Step 1:  Check that you are using an identity with control over the ledger account by running the following command:

``` bash
dfx identity whoami
```

- #### Step 2:  Check the current balance in the ledger account associated with your identity by running the following command:

``` bash
dfx ledger --network ic balance
```

- #### Step 3:  Transfer ICP tokens to another account by running a command similar to the following:

``` bash
dfx ledger --network ic transfer <destination-ledger-account-id> --icp <ICP-amount> --memo <numeric-memo>
```

For example:

```
dfx ledger --network ic transfer ae6e1a76da5725bbbf0c5c035aaf0525b791e0f0f7cce27d8e27826389871406 --icp 5 --memo 12345
```

:::info
This example illustrates how to transfer ICP tokens to the specified account using a whole number with the `--icp` command-line option.

-   You can also specify fractional units of ICP tokens—called **e8s**—using the `--e8s` option, either on its own or in conjunction with the `--icp` option.
-   Alternatively, you can use the `--amount` to specify the number of ICP tokens to transfer with fractional units up to 8 decimal places, for example, as `5.00000025`.
:::

The destination address can be an address in the ledger canister running on the Internet Computer network, an account you have added using the [Network Nervous System application](https://nns.ic0.app), or the address for a wallet you have on an exchange.

If you transfer the ICP tokens to an account in the [Network Nervous System application](https://nns.ic0.app), you might need to refresh the browser to see the transaction reflected.

For more information about using the `dfx ledger` command-line options, see [dfx ledger](/references/cli-reference/dfx-ledger.md).

## Lock ICP tokens by staking them in a neuron

If you want to lock up ICP tokens to participate in governance and earn rewards, you must use the [Network Nervous System (NNS) application](https://nns.ic0.app) or `dfx canister call` commands.

Because locking up ICP tokens to create staked neurons is a more complex process when using the SDK command-line interface than it is when using the [Network Nervous System (NNS) application](https://nns.ic0.app), the steps aren’t included in this guide.

For information about the Network Nervous System, see [understanding the Internet Computer’s Network Nervous System, neurons, and ICP utility tokens](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8).

For additional details about setting the locked period and dissolve delay for a neuron, see [the Internet Computer Network Nervous System application and wallet](https://medium.com/dfinity/getting-started-on-the-internet-computers-network-nervous-system-app-wallet-61ecf111ea11).

-->
