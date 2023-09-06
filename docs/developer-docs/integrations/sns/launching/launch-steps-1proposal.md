# SNSを起動するためのコマンドとアクション

## 概要

[ここでは](../launching/launch-summary-1proposal.md)、本番環境での SNS 立ち上げの段階について説明します。

この記事では、開発者が SNS 立ち上げの段階を完了するために必要な技術的なコマンドと手順を示します。

低レベルでは、[SNS のローカルテストリポジトリが](../testing/testing-locally.md)
を案内しています。ここでのコマンドはメインネット上のcanisters を対象としている点が異なります。

## 必要条件

### 1.IC SDK

IC SDKの[インストールを](../../../setup/install)参照してください。

### 2\.`ic-admin`

[ `ic-admin`](../../../setup/ic-admin.md) の[インストールを](../../../setup/ic-admin.md)参照してください。

### 3\. `sns` CLI

:::note
お使いの dfx バージョンにバンドルされている sns CLI のバージョンは、使用法のセクションで説明されている最新のコマンドを備えていない可能性があります。
::：

``` bash
git clone git@github.com:dfinity/ic.git
cd ic
bazel build //rs/sns/cli:sns
ls bazel-bin/rs/sns/cli/sns 
```

## ステージ

### 1\. Dapp のSNSの初期パラメータを選択します。dapp

通常、dapp の開発者は、後続のプロポーザルで使用される初期パラメータを選択します。

:::info

これらのパラメータは、SNSガバナンスcanister がインストールされる初期neurons も定義します。SNS ガバナンスcanister は、完全に起動する前は、分散化スワップ前モードであり、少数のプロポーザルのみが許可されます (ステップ 7 を参照)。しかし、ローンチが進行している間にdapp canister (s)をアップグレードしたり、そのDAOのためのカスタムプロポーザルを登録するなど、この間にすでにいくつかのSNSプロポーザルが利用されるかもしれません。このような操作では、SNSが完全に起動する前に、起動プロセス中にSNSプロポーザルを提出し、採用する必要があります。いくつかのフロントエンド、例えばNNSフロントエンドdapp は、完全にローンチされていないSNSのneuronsを表示しません。したがって、NNSフロントエンドdapp プリンシパルによって制御されるneuronsは、ローンチが成功した後にのみ表示されます。したがって、初期neuronは、立ち上げプロセス中にすでに十分な数が操作できるように、注意深く設定する必要があります。
::：

### 2\. Dapp の共同コントローラとして NNS root を追加します。dapp

次のコマンドを実行することで可能です：

    dfx sns prepare-canisters add-nns-root $CANISTER_ID

### 3.SNS を作成するための NNS 提案を提出

neuron 対象となる NNS を十分なステークで所有している人なら誰でも、指定されたdapp に対して SNS を作成する NNS 提案を提出することができます。
もちろん、この提案に適切なパラメータを設定することは非常に重要です。
このコマンドの使用例は[こちらにも](https://github.com/dfinity/sns-testing/blob/main/propose_sns.sh)あります。

:::info

このようなプロポーザルはNNSに一度に一つしかないことに注意してください。
::：

このような提案を作成するには、`sns-cli` ：

    dfx sns propose --network ic --neuron $NEURON_ID sns_init.yaml

### 4.NNS提案決定

dapp 開発者が行う技術的なことは何もありません。コミュニティの投票

### 5\. (自動化された) SNS-W による SNS の展開canisters

dapp 開発者が行う技術的なことは何もありません。これは、ステージ 4 で採択された提案の結果
として自動的にトリガーされます。

### 6.(自動化) SNS-W が SNS の唯一のコントローラーとして SNS ルートを設定します。dapp

dapp 開発者が行う技術的なことは何もありません。これは、ステージ 4 で採用された提案の結果
として自動的にトリガされます。

### 7\. (自動化) SNS-W はステップ 1 の設定に従って SNScanisters を初期化。

dapp 開発者が行う技術的なことはありません。これは、ステージ 4 で採択された提案の結果
として自動的にトリガされます。

### 8.(自動化）SNSスワップ開始

dapp 開発者が行う技術的なことは何もありません。これは、ステージ 4 で採択された提案の結果
として自動的にトリガーされます。

### 9\. (自動化）SNS スワップ終了

dapp 開発者が行う技術的なことは何もありません。これは、ステージ 4 で採択された提案の結果
として自動的にトリガーされます。

### 10.(自動化) SNS スワップの確定

dapp 開発者が行う技術的なことは何もありません。これはステージ 4 で採択された提案の結果
として自動的にトリガーされます。

<!---
# Commands & actions to go through SNS launch

## Overview
At a high level, the stages for launching an SNS in production are explained [here](../launching/launch-summary-1proposal.md).

This article lists the technical commands and steps a developer needs to complete the stages for an SNS launch.

At a low level, the [SNS local testing repository](../testing/testing-locally.md) guides you
through the same, with the difference that the commands here target the canisters on the mainnet.

## Requirements

### 1. IC SDK

See: [installing the IC SDK](../../../setup/install).

### 2. `ic-admin`

See: [installing the `ic-admin`](../../../setup/ic-admin.md).

### 3. `sns` CLI

:::note
The version of the sns CLI that is bundled with your dfx version may not have the latest commands described in the Usage section. If needed, it is recommended to build and use the sns CLI tool yourself.
:::

```bash
git clone git@github.com:dfinity/ic.git
cd ic
bazel build //rs/sns/cli:sns
ls bazel-bin/rs/sns/cli/sns 
```
## Stages

### 1. Dapp developers choose the initial parameters of the SNS for a dapp

Typically, dapp developers choose initial parameters that will be used in subsequent proposals.

:::info 

These parameters also define the initial neurons with which the SNS governance canister will be installed. Before being fully launched, the SNS governance canister is in a pre-decentralization-swap mode and only few proposals are allowed (see Step 7). However, some SNS proposals might already be used during this time, for example upgrades to the dapp canister(s) while the launch is ongoing or registering custom proposals for that DAO. Such operations require submitting and adopting an SNS proposal during the launch process, and thus before the SNS is fully launched. Some frontends, for example the NNS frontend dapp, do not show neurons of SNSs that are not fully launched and thus neurons controlled by NNS frontend dapp principals will only be visible after a successful launch. Therefore, the initial neurons must be carefully setup in a way so that enough of them can be operated already during the launch process. 
:::

### 2. Dapp developers add NNS root as co-controller of dapp

They can do so by running the following command:
```
dfx sns prepare-canisters add-nns-root $CANISTER_ID
```

### 3. Submit NNS proposal to create SNS

Anyone who owns an eligible NNS neuron with enough stake can submit an NNS proposal to create an SNS for a given dapp.
Of course it is crucial to set the right parameters in this proposal.
You can also find an example of how this command is used [here](https://github.com/dfinity/sns-testing/blob/main/propose_sns.sh).


:::info

Note that there can only be one such proposal at a time in the NNS. This means that the time when this proposal can be submitted might depend on other SNS' launch.
:::


To create such a proposal, a common path is to use `sns-cli` and run the following:
```
dfx sns propose --network ic --neuron $NEURON_ID sns_init.yaml
```

### 4. The NNS proposal is decided
Nothing technical for dapp developers to do. Community votes.


### 5. (Automated) SNS-W deploys SNS canisters
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

### 6. (Automated) SNS-W sets SNS root as sole controller of dapp
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

### 7. (Automated) SNS-W initializes SNS canisters according to settings from Step 1
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

### 8. (Automated) SNS swap starts
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

### 9. (Automated) SNS swap ends
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

### 10. (Automated) SNS swap finalizes
Nothing technical for dapp developers to do. This is triggered automatically as a result
of an adopted proposal in Stage 4.

-->
