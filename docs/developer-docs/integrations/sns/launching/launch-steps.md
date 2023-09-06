# SNSを立ち上げるまでのコマンドとアクション - レガシーフロー

:::caution
このページは、SNSの起動方法のレガシーフローを参照しています。これは現時点ではまだサポートされていますが、今後廃止される可能性があります。新しいSNS起動フローを使用することをお勧めします。
::：

## 概要

本番環境でSNSを立ち上げるための段階を、高レベルで説明[します](../launching/launch-summary.md)。

この記事では、開発者が SNS 立ち上げの段階を完了するために必要な技術的コマンドとアクションをリストアップしています。

低レベルでは、メインネット上のcanisters を対象としたコマンドという違いはありますが、[SNS ローカルテストリポジトリが](../testing/testing-locally.md)同じことを案内しています。

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

これらのパラメータは、SNSガバナンスcanister がインストールされる初期neurons も定義します。立ち上げステージ5と6では、立ち上げプロセス中、つまりSNSが完全に立ち上がる前に、SNSプロポーザルを提出し、採用する必要があります。いくつかのフロントエンド、例えばNNSフロントエンドdapp は、完全に立ち上げられていないSNSのneuronを表示しないことに注意してください。したがって、NNSフロントエンドのdapp プリンシパルによって制御されるneuronsは、起動に成功した後にのみ表示されます。neuron
 dapp canister (s)のアップグレードや、DAOのカスタムプロポーザルの登録など、立ち上げ時にすでに利用可能な他のSNSプロポーザルについても同様です。

:::

### 2\. Dapp 開発者は、SNS サブネットに展開できるように NNS 提案を提出します。

十分なステークを持つ NNSneuron を所有する人は誰でも、SNScanisters をデプロイできる[SNS-W](../introduction/sns-architecture.md#SNS-W)のプリンシパルウォレットをリストアップしたプロポーザル
を提出できます。

このようなプロポーザルを作成するには、`ic-admin` を使用して以下を実行するのが一般的です：

``` bash
ic-admin  \
   --nns-url "${NETWORK_URL}" propose-to-update-sns-deploy-whitelist  \
   --added-principals "${WALLET}"  \
   --proposal-title "Let me SNS!"  \
   --summary "This proposal whitelists developer's principal to deploy SNS"
```

- `WALLET` を問題のプリンシパルで置き換えることができます。
- `NETWORK_URL` を`https://nns.ic0.app` で置き換えることもできます。

### 3.(3つのうちの)提案\#1が可決されるか却下されるか

### 4\. Dapp 開発者が SNScanisters を SNS サブネット上に作成するトリガーとなります。

ウォレットcanister が SNS-W にリストされた後、
SNScanisters が SNS-W への手動呼び出しによって作成されます。

`sns CLI` SNSサブネット上のSNScanisters の作成をトリガーするコマンド：

``` bash
sns deploy --network "${NETWORK}" --init-config-file "${CONFIG}" --save-to "sns_canister_ids.json" 
```

### 5\. Dapp 開発者は、自分のdapp の制御を SNS に引き渡すために SNS 提案書を提出します。

このステップでは、開発者はdapp canister (複数) の制御を、新しく配置された SNS ルートcanister に引き渡します。SNSルートがdapp canister (複数可)を知っていることを確認するために、開発者はSNSプロポーザルによって、dapp をSNSに「登録」します。

この提案は、最初のSNSneuronsの1つによって提出される必要があることに注意してください。これは、開発者がコマンドラインツールquillで制御できるSNSneuron が少なくとも1つ必要であることを意味します。
もちろん、この提案には正しいパラメータを設定することが重要です。

#### Quillコマンド

``` bash
quill sns \
   --canister-ids-file ./sns_canister_ids.json \
   --pem-file "${PEM_FILE}" \
   make-proposal \
   --proposal \
      "(record { \
         title=\"${REGISTER_DAPP_PROPOSAL_TITLE}\"; \
         url=\"${REGISTER_DAPP_PROPOSAL_URL}\"; \
         summary=\"${REGISTER_DAPP_PROPOSAL_SUMMARY}\"; \
         action=opt variant {RegisterDappCanisters = record {canister_ids=${DAPP_CANISTER_IDS}}}})" \
   "${DEVELOPER_NEURON_ID}"
```

パラメータは

- `sns_canister_ids.json` はこのSNSのIDが書かれたファイルです。canisters

- `PEM_FILE`="$(dfx cache show)/../../${IDENTITY\_NAME}/identity.pem"

- `IDENTITY_NAME`=`<current-dapp-controller-identity-name>`

- `REGISTER_DAPP_PROPOSAL_TITLE`="登録テストdapp"

- `REGISTER_DAPP_PROPOSAL_URL`="https://example.com/"

- `REGISTER_DAPP_PROPOSAL_SUMMARY`="この提案は、テストdapp をSNSに登録します。"

- `DAPP_CANISTER_IDS`="vec {\
  プリンシパル"$CID1";\
  プリンシパル"$CID2";\
  プリンシパル"$CID3""\
  ここで、dapp’s canister プリンシパルは文字列としてエンコードされ、`CID2`,`CID2` という変数に設定されていると仮定します、`CID3`

- `DEVELOPER_NEURON_ID`=`<your-developer-neuron-ID>`

### 6.(3つのうちの)2番目の提案は可決または却下されます。

Dapp 開発者は自分の SNS の初期値で投票します。 の初期値によっては、 の初期値を持つ他の投票者も含まれます。neuron neuron neuron

この段階では、SNSの提案に投票するためにNNSのフロントエンドdapp を使用することはできないことに注意してください - NNS FEdapp は、SNSのneuronsと、完全に立ち上げられたSNSの提案のみを表示します。したがって、この提案を採用する方法は2つあります：

1.  議決権の3％以上を持つneuron が提案書を提出し、4日間の投票期間が終了するのを待つ方法。他のneuronの投票がなければ、この投票期間の後に提案が採択されます。

2.  初期のneurons は、クイルでコントロールされているneurons に議決権の過半数があるように設定されています。この場合、他のneuronsはこのステップで投票することができ、議決権の絶対過半数に達すれば、提案は早期に可決される可能性があります。

### 7.分散スワップ開始の提案

SNScanisters が配置され、dapp'の制御が SNS に引き渡された後、NNS の提案によってスワップが開始されます。

neuron
もちろん、この提案には適切なパラメータを設定することが重要です。

:::info

このようなプロポーザルはNNSに一度に1つしかないことに注意してください。
::：

``` bash
ic-admin   \
   --nns-url "${NETWORK_URL}" propose-to-open-sns-token-swap  \
   --min-participants 3  \
   --min-icp-e8s 5000000000  \
   --max-icp-e8s 50000000000  \
   --min-participant-icp-e8s 100000000  \
   --max-participant-icp-e8s 20000000000  \
   --swap-due-timestamp-seconds "${DEADLINE}"  \
   --sns-token-e8s 500000000000  \
   --target-swap-canister-id "${SNS_SWAP_ID}"  \
   --community-fund-investment-e8s 5000000000  \
   --neuron-basket-count 3  \
   --neuron-basket-dissolve-delay-interval-seconds 31536000  \
   --proposal-title "Decentralize this SNS"  \
   --summary "Decentralize this SNS"
```

### 8.第3号議案(3件中)の可決・不採択について

dapp 開発者がする技術的なことは何もありません。コミュニティ投票。

### 9.SNS参加者が分散化スワップに参加

dapp 開発者が行う技術的なことは何もありません。コミュニティがスワップに参加。

### 10.SNScanisters が SNS DAO になります。

スワップが期限に達したか、最大
ICP が収集されたかのどちらかによって終了する場合、その最終化は、誰でもできる SNS スワップへの手動コール
によってトリガーされなければなりません。
SNS ローカルテストリポジトリ
[こちらで](https://github.com/dfinity/sns-testing/blob/main/finalize_sns_sale.sh#L8)、より多くの文脈とともにこのコマンドを見つけることができます。

    dfx canister --network "${NETWORK}" call <SWAP_CANISTER_ID> finalize_swap '(record {})'

<!---
# Commands & actions to go through SNS launch - legacy flow

:::caution
This page refers to a legacy flow of how an SNS is launched. This is still supported at the moment but might be deprecated going forward. It is recommended to use the new SNS launch flow which can be found [here](./launch-steps-1proposal.md). Please find more context on this [here](./index.md).
:::


## Overview
At a high level, the stages for launching an SNS in production are explained [here](../launching/launch-summary.md).

This article lists the technical commands and actions that a developer needs to complete the stages for an SNS launch.

At a low level, the [SNS local testing repository](../testing/testing-locally.md) guides you through the same, with the difference that the commands target the canisters on the mainnet.

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

These parameters also define the initial neurons with which the SNS governance canister will be installed. The launch stages 5 and 6 require submitting and adopting an SNS proposal during the launch process, and thus before the SNS is fully launched. Note that some frontends, for example the NNS frontend dapp, do not show neurons of SNSs that are not fully launched. Therefore, neurons that are controlled by NNS frontend dapp principals will only be visible after a successful launch. Therefore, the initial neurons must be carefully setup in a way so that enough of them can be operated already during the launch process. 
This is also relevant for other SNS proposals that can already be used during the launch, for example to upgrade the dapp canister(s) or registering custom proposals for that DAO.

:::

### 2. Dapp developers submit NNS proposal so they can deploy to the SNS subnet

Anyone who owns an NNS neuron with enough stake can submit a proposal
that lists a principal wallet in [SNS-W](../introduction/sns-architecture.md#SNS-W) who can then deploy the SNS canisters.

To create such a proposal, a common path is to use `ic-admin` and run the following:

```bash 
ic-admin  \
   --nns-url "${NETWORK_URL}" propose-to-update-sns-deploy-whitelist  \
   --added-principals "${WALLET}"  \
   --proposal-title "Let me SNS!"  \
   --summary "This proposal whitelists developer's principal to deploy SNS"
``` 

* One can substitute `WALLET` with the principal in question.
* One can substitute `NETWORK_URL` with `https://nns.ic0.app`.

### 3. Proposal #1 (of 3) is passed or rejected

### 4. Dapp developers trigger the SNS canisters to be created on SNS subnet

After the wallet canister is listed in SNS-W, 
the SNS canisters are created triggered by a manual call to SNS-W.

The `sns CLI` command to trigger the creation of SNS canisters on the SNS subnet: 

```bash
sns deploy --network "${NETWORK}" --init-config-file "${CONFIG}" --save-to "sns_canister_ids.json" 
```

### 5. Dapp developers submit an SNS proposal to handover control of their dapp to the SNS

In this step, the developers will hand over the control of the dapp canister(s) to the newly deployed SNS root canister. To make sure that the SNS root is also aware of the dapp canister(s), the devs then also "register" the dapp in the SNS, by an SNS proposal.

Note that this proposal needs to be submitted by one of the initial SNS neurons. This means that the devs require at least one SNS neuron that they can control with the command line tool quill. They can then use the following command to submit the SNS proposal.
Of course it is crucial to set the right parameters in this proposal.

#### Quill command

```bash
quill sns \
   --canister-ids-file ./sns_canister_ids.json \
   --pem-file "${PEM_FILE}" \
   make-proposal \
   --proposal \
      "(record { \
         title=\"${REGISTER_DAPP_PROPOSAL_TITLE}\"; \
         url=\"${REGISTER_DAPP_PROPOSAL_URL}\"; \
         summary=\"${REGISTER_DAPP_PROPOSAL_SUMMARY}\"; \
         action=opt variant {RegisterDappCanisters = record {canister_ids=${DAPP_CANISTER_IDS}}}})" \
   "${DEVELOPER_NEURON_ID}"
```

Where the parameters are:

* `sns_canister_ids.json` is a file with the IDs of this SNS’s canisters

* `PEM_FILE`="$(dfx cache show)/../../${IDENTITY_NAME}/identity.pem"

* `IDENTITY_NAME`=`<current-dapp-controller-identity-name>`

* `REGISTER_DAPP_PROPOSAL_TITLE`="Register test dapp"

* `REGISTER_DAPP_PROPOSAL_URL`="https://example.com/"

* `REGISTER_DAPP_PROPOSAL_SUMMARY`="This proposal registers test dapp with SNS"

* `DAPP_CANISTER_IDS`="vec { \
 principal\"$CID1\"; \
 principal\"$CID2\"; \
 principal\"$CID3\" \
}" where we assume your dapp’s canister principals are encoded as strings and set into variables named `CID2`, `CID2`, `CID3`

* `DEVELOPER_NEURON_ID`=`<your-developer-neuron-ID>`

### 6. Proposal #2 (of 3) is passed or rejected

Dapp developers vote with their initial SNS neurons. Depending on the initial neuron distirbution, this can also include other voters that have initial neurons. 

Note that at this stage it is not possible to use the NNS frontend dapp to vote on SNS proposals - the NNS FE dapp will only show SNS neurons and proposals for SNSs that are fully launched. Therefore, there are two options how this proposal can be adopted:

1) The proposal is submitted by a neuron that has at least 3% of the voting power and one waits for the voting period of 4 days to be complete. If no other neurons vote, the proposal will then be adopted after this voting period.

2) The initial neurons are set up in such a way that the majority of the voting power is in neurons that are controlled via quill. In this case, other neurons can vote in this step and the proposal can potentially pass earlier if an absolute majority of the voting power is reached.

### 7. Proposal to start the decentralization swap

After the SNS canisters are deployed and the dapp's control is handed over to the SNS, an NNS proposal starts the swap.

Again, anyone who owns an NNS neuron with enough stake can submit this proposal.
Of course it is crucial to set the right parameters in this proposal.

:::info

Note that there can only be one such proposal at a time in the NNS. This means that the time when this proposal can be submitted might depend on other SNS' launch.
:::

```bash
ic-admin   \
   --nns-url "${NETWORK_URL}" propose-to-open-sns-token-swap  \
   --min-participants 3  \
   --min-icp-e8s 5000000000  \
   --max-icp-e8s 50000000000  \
   --min-participant-icp-e8s 100000000  \
   --max-participant-icp-e8s 20000000000  \
   --swap-due-timestamp-seconds "${DEADLINE}"  \
   --sns-token-e8s 500000000000  \
   --target-swap-canister-id "${SNS_SWAP_ID}"  \
   --community-fund-investment-e8s 5000000000  \
   --neuron-basket-count 3  \
   --neuron-basket-dissolve-delay-interval-seconds 31536000  \
   --proposal-title "Decentralize this SNS"  \
   --summary "Decentralize this SNS"
```

### 8. Proposal #3 (of 3) is passed or rejected

Nothing technical for dapp developers to do. Community votes.

### 9. SNS participants participate in the decentralization swap

Nothing technical for dapp developers to do. Community participates in the swap.

### 10. SNS canisters become SNS DAO

When the swap ends, either because its deadline is reached or because the maximum
ICP have been collected, its finalization has to be triggered by a manual call
to the SNS swap that can be done by anyone.
You can find this command with more context in the SNS local testing repository
[here](https://github.com/dfinity/sns-testing/blob/main/finalize_sns_sale.sh#L8).

```
dfx canister --network "${NETWORK}" call <SWAP_CANISTER_ID> finalize_swap '(record {})'
```

-->
