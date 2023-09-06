# SNSアセットcanisters

## 概要

[アセットcanister](https://github.com/dfinity/sdk/tree/master/src/canisters/frontend/ic-frontend-canister)は、IC 上に配置されたcanister から静的アセットを保存したり取得したりする方法をユーザに提供します。一般的に、アセットcanisters は HTML、CSS、または JavaScript アセットを提供するために使用され、これらは通常dapp のフロントエンドの一部です。このため、アセットcanister はフロントエンドcanister とも呼ばれます。このガイドでは、アセットcanister と呼ぶことにします。

SNS のコンテキストでは、dapp'の関連アセットcanister はdapp に関連するフロントエンドアセットを提供し、SNS DAO のフロントエンドを含むこともあります。

アセットcanister の内容はSNSの立ち上げ前に設定されなければならず、その後の変更は`Prepare` パーミッションを持つプリンシパルによって行われなければなりません。この`Prepare` パーミッションを持つプリンシパルは、アセットcanister に一括で変更を加え、それらの変更を「ロック」することができます。これらの変更を適用するには、提案書を提出する必要があります。ロック」された変更に対するプロポーザルは誰でも提出できます。変更が提案されると、SNS DAOによって投票されます。承認された場合、SNSガバナンスcanister は承認された変更をコミットできる唯一の存在です。この構成により、アセットcanister への変更が承認された提案によってのみ行われることが保証されます。これらの変更は、このドキュメントの残りの部分では、アセットcanister の「更新」と呼ばれます。

このセクションは、プロジェクトにアセットcanister が含まれている場合に関連し、アセットcanister の制御を SNS に引き渡すテスト方法について説明します。

:::info

この文書では、**アップグレードという**用語は、canister のWebAssembly モジュールのアップグレード版をデプロイすることを指します。

**更新という**用語は、アセットcanister 内に保存されているアセットを変更または更新することを指します。
::：

## アセットのデプロイcanister

アセットcanister の制御を SNS に引き渡すには、まずアセットをデプロイする必要があります。dapp のコントロールが SNS に引き渡されると、関連するアセットcanister も同じようになります。

SNS の起動中にアセットcanister を展開する概要は次のとおりです：

- まず、アセットcanister を[dfx 0.14.1+](https://github.com/dfinity/sdk/blob/release-0.14.1/src/distributed/assetstorage.wasm.gz) の Wasm ファイルで作成するか、アップグレードする必要があります。
- 次に、dapp は、以下のパーミッションを設定することで、アセットcanister のコントロールを SNS に渡します：
  - SNS のガバナンスcanister には`Commit` のパーミッションが与えられます。これは、以前の開発者が`grant_permissions` コマンドを使用して行います (以下のパーミッションの付与セクションを参照)。そうでなければ、SNS がコントローラになった時点でこのパーミッションを付与する必要があります。
  - `Prepare` パーミッションを持つプリンシパルのホワイトリストを作成し、特定の個人にアセットへの変更をアップロードするパーミッションを与えますcanister 。変更がアセットに適用される前に、提案によって承認される必要がありますcanister 。
  - SNS を作成するユーザーまたは開発者は、自分の個人的な権限を削除する必要があります。
- 最後に、設定をコミットするために SNS の機能を登録する必要があります。

アセットcanister をデプロイするには、`dfx.json` ファイル内のcanister を`"type": "assets"` に設定します。canister がビルドされると、dfx は assets.wasm.gz ファイルや candid/assets.did ファイルなどの必要なファイルを自動的に生成します。

`dfx.json` ファイル内でアセットcanister を設定する例を以下に示します：

```
    "assets": {
      "source": [],
      "type": "assets"
    }
```

### テストのためのローカルへのデプロイ

テスト目的でアセットcanister をローカルにデプロイするには、以下のコマンドを使用します：

    dfx deploy assets --network "local" --no-wallet

dapp を SNS に渡す前に、アセットcanister をデプロイし、パーミッションを設定しておく必要があります。

### メインネットへのデプロイ

:::info
メインネットにデプロイするには、cycles を含むウォレットが必要です。cycles ウォレットの詳細については、[こちらを](../../../setup/cycles/cycles-wallet.md)参照してください。
::：

アセットcanister をメインネットにデプロイするには、以下のコマンドを使用します：

    dfx deploy assets --network "ic" --wallet <principal>

dapp が SNS に引き渡される前に、アセットcanister がデプロイされ、パーミッションが設定されている必要があります。

## アセットcanister のパーミッションの設定

アセットcanister を設定する場合、プリンシパルのホワイトリストを含むパーミッションのセットを作成する必要があります。このホワイトリストには、アセットcanister 内のアセットを更新する変更の提出を許可される人の詳細が記載されています。このホワイトリストは、SNSを立ち上げる前に設定する必要があります。変更の提出を許可されたプリンシパルには、`Prepare` パーミッションが与えられます。

`Prepare` 権限を持つプリンシパルがアセットcanister に変更を提出すると、これらの変更は「ロック」ステートに設定されます。その後、誰でも「ロック」された変更を適用する提案を提出できます。この提案を提出するために必要なパーミッションはありません。

SNS立ち上げ時には、アセットcanister のコントロールをSNSに引き渡す必要があります。すべてのdapp canisters と同様にcanister の制御を SNS ルートに割り当てる以外に、SNS のガバナンスcanister を`Commit` 権限を持つプリンシパルとしてホワイトリストに追加する必要があります。`Commit` 権限を持つプリンシパルのみが、提案された変更を適用できます。

アセットcanister が SNS に引き渡されたら、ガバナンスcanister のみが`Commit` 権限を持ち、ホワイトリストのプリンシパルは`Prepare` 権限を持つべきです。SNSを設定し、デプロイした開発者は、SNSの立ち上げ時に権限を削除してください。

SNSは、カスタム提案タイプとして追加する必要がある提案を介して`take_ownership` 。これにより、すべてのパーミッションがクリアされ、SNSガバナンスcanister `Commit` のパーミッションのみが与えられます。SNS が`take_ownership` を呼び出さない場合、ユーザーはアセットへのすべての変更が SNS プロポーザルで承認されたことを確認できません。

### パーミッションの付与

アセット内のプリンシパルにパーミッションを付与するにはcanister 、次のコマンドを使用します：

    dfx canister call --network ic <canister-id> grant_permission <principal>

### パーミッションの取り消し

アセット内のプリンシパルのパーミッションを取り消すにはcanister 、次のコマンドを使用します：

    dfx canister call --network ic <canister-id>  revoke_permission <principal>

### 権限の一覧表示

アセットcanister の権限を一覧表示するには、次のコマンドを使用します：

    dfx canister call --network ic <canister-id> list_permitted '(record {permission = variant {<Permission>}})'

たとえば、`Commit` パーミッションを持つすべてのプリンシパルを一覧表示するには、次のコマンドを使用します：

    dfx canister call --network ic oa7fk-maaaa-aaaam-abgka-cai list_permitted '(record {permission = variant {Commit}})'

たとえば、`Prepare` パーミッションを持つすべてのプリンシパルを一覧表示するには、次のコマンドを使用します：

    dfx canister call --network ic oa7fk-maaaa-aaaam-abgka-cai list_permitted '(record {permission = variant {Commit}})'

アセットcanister がデプロイされ、パーミッションが設定されると、SNS 分散スワップを開始できます。SNSの起動については、[こちらを](../launching/launch-summary.md)参照してください。

## SNS GenericNervousSystemFunctions（ジェネリック神経系機能

汎用プロポーザルは、各SNSがカスタムプロポーザルを定義するための方法です。アセットcanister に変更をコミットするには、このような汎用プロポーザルを使用します。

汎用プロポーザルを使用するには、まずこの新しいプロポーザルタイプをSNSに「登録」する必要があります。そのためには、`AddGenericNervousSystemFunction` プロポーザルを使用します。

`commit_proposed_batch` API をサポートする新しい`AddGenericNervousSystemFunction` SNS 課題を提出するには、canister id をアセットcanister (これは以下の更新ステップで更新されます) とし、`commit_proposed_batch` 関数をターゲットとします。検証関数は`validate_commit_proposed_batch` とします。

`ExecuteNervousSystemFunction` SNSプロポーザルは、新しく登録されたプロポーザルを実行するために使用されます。`dfx deploy <frontend canister name> --network ic --by-proposal` の出力を用いて`ExecuteNervousSystemFunction` SNS 課題を申請するには、以下の更新手順を参照してください。

## SNS 課題の提出と資産のアップグレードcanister

アセット（canister ）が SNS の管理下に置かれると、変更は SNS プロポーザルで行う必要があります。

アセットcanister を更新するために SNS 提案を提出する前に、アセットcanister が[dfx 0.14.1+](https://github.com/dfinity/sdk/blob/release-0.14.1/src/distributed/assetstorage.wasm.gz) にバンドルされている Wasm を使用するように（提案によって）アップグレードされていることを確認してください。

- #### ステップ 1: SNS 提案を提出するには、まず`Prepare` 権限を持つプリンシパルに実行してもらいます：

<!-- end list -->

    dfx deploy <frontend canister name> --network ic --by-proposal

出力は以下のようになります：

    Proposed commit of batch 2 with evidence e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855. Either commit it by proposal, or delete it.

- #### ステップ2: バッチ番号とエビデンス値を保存し、アセットcanister APIで使用します。

- #### ステップ 3: 他の人が提案のエビデンスを検証できるように、他の人にdapp リポジトリをクローンしてもらい、実行してもらいます：

<!-- end list -->

    dfx deploy <frontend canister name> --network ic --compute-evidence

計算されたエビデンスはステップ2のエビデンスと一致するはずです。

- #### ステップ 4: 以下の API を使用して、バッチをコミットする新しい提案を提出します：

これらのアセットcanister API をプロポーザルで使用してください：

```
   type CommitProposedBatchArguments = record {
   batch_id: BatchId;
   evidence: blob;
  };
  type ValidationResult = variant { Ok : text; Err : text };


    validate_commit_proposed_batch: (CommitProposedBatchArguments) -> (ValidationResult);
  commit_proposed_batch: (CommitProposedBatchArguments) -> ();

```

提案が却下された場合、作成者はこの新しいアセットcanister API を使用する必要があります：

```
  type DeleteBatchArguments = record {
    batch_id: BatchId;
  };
  delete_batch: (DeleteBatchArguments) -> ();
```

## アセットcanister の例

SNS アセットcanister の例として、canister `sqbzf-5aaaa-aaaam-aavya-cai` があります。これは[DragginzDapp SNS](https://dashboard.internetcomputer.org/canister/sqbzf-5aaaa-aaaam-aavya-cai) のアセットcanister の一部です。

<!---
# SNS asset canisters

## Overview

The [asset canister](https://github.com/dfinity/sdk/tree/master/src/canisters/frontend/ic-frontend-canister) provides users with a way to store and retrieve static assets from a canister deployed on the IC. Generally, asset canisters are used to serve HTML, CSS, or JavaScript assets, which are typically part of a dapp's frontend. For this reason, the asset canister is also referred to as the frontend canister. For purposes of this guide, we'll refer to it as the asset canister. 

In the context of the SNS, a dapp's associated asset canister serves the frontend assets related to dapp and may also include a frontend to the SNS DAO, e.g., through which users can vote on governance proposals.  

The contents of the asset canister must be configured prior to the launch of the SNS, and any changes afterwards must be made by a principal with the `Prepare` permission. Principals with this `Prepare` permission can make a batch of changes to the asset canister and then 'lock' those changes. To have those changes applied, a proposal must be submitted. Anyone can submit the proposal for the 'locked' changes. Once changes are proposed, they can be voted on by the SNS DAO. If approved, the SNS governance canister is the only one that can commit the approved changes. This configuration assures that changes to the asset canister are only made by approved proposals. These changes are referred to as 'updates' to the asset canister in the remainder of this document. 

This section is relevant if your project contains an asset canister and describes how you can test handing over control of an asset canister to an SNS.

:::info 

In this document, the term **upgrade** refers to deploying an upgraded version of a canister's WebAssembly module.

The term **update** refers to changing or updating the assets stored within an asset canister.
:::

## Deploying an asset canister

An asset canister must first be deployed before control of it can be handed over to an SNS. When a dapp's control is handed over to an SNS, this is also true for the associated asset canister.

The general overview of deploying an asset canister during an SNS launch is as follows:
- First, the asset canister must be created with or upgraded to a Wasm file from [dfx 0.14.1+](https://github.com/dfinity/sdk/blob/release-0.14.1/src/distributed/assetstorage.wasm.gz).
- Then, the dapp should hand control of the asset canister over to the SNS by setting the following permissions:
    - The SNS governance canister is given `Commit` permissions. This is done by the previous developer using the `grant_permissions` command (see the granting permissions section below), otherwise the SNS must grant this permission once it is a controller.
    - A whitelist of principals with `Prepare` permissions is created to give certain individuals the permission to upload changes to the asset canister. Changes must be approved through a proposal before they are applied to the asset canister. 
    - The user or developer creating the SNS should remove their own personal permissions. 
- Lastly, the SNS's function should be registered to commit the configuration.

To deploy an asset canister, any canister in the `dfx.json` file can be set as `"type": "assets"`, and dfx will automatically generate the required files, such as an assets.wasm.gz file and a candid/assets.did file once the canister has been built. 

An example of configuring an asset canister within the `dfx.json` file can be found below:

```
    "assets": {
      "source": [],
      "type": "assets"
    }
```


### Deploying locally for testing

To deploy your asset canister locally for testing purposes, the following command can be used:

```
dfx deploy assets --network "local" --no-wallet
```

The asset canister should be deployed and have permissions configured before the dapp is handed over to an SNS. 

### Deploying on the mainnet

:::info 
To deploy to the mainnet, you will need a wallet that contains cycles. For more information on cycles wallets, please see [here](../../../setup/cycles/cycles-wallet.md).
:::

To deploy your asset canister to the mainnet, the following command can be used:

```
dfx deploy assets --network "ic" --wallet <principal>
```

The asset canister should be deployed and have permissions configured before the dapp is handed over to an SNS. 

## Configuring an asset canister's permissions

When configuring an asset canister, a set of permissions that contains a whitelist of principals must be created. This whitelist details who is allowed to submit changes that update assets in the asset canister. This whitelist must be configured prior to the SNS launch. Principals that are allowed to submit changes are given the `Prepare` permission.

Once a principal with `Prepare` permissions submits changes to the asset canister, these changes are set in a 'locked' state. Then, anyone can submit a proposal that proposes the 'locked' changes be applied; there is no permission necessary to submit this proposal. 

During the SNS launch, control of the asset canister must be handed over to the SNS. Apart from assigning canister control to SNS root as with all dapp canisters, the SNS' governance canister should be added to the whitelist as a principal with `Commit` rights. Only principals with `Commit` rights may apply proposed changes. 

Once the asset canister has been handed over to the SNS, only the governance canister should have `Commit` rights, and principals in the whitelist should have `Prepare` rights. The developer who configured and deployed the SNS should have their permissions removed during the SNS launch. 

It is recommended that the SNS calls `take_ownership` via a proposal, which must be added as a custom proposal type. This will clear all permissions, and give only the SNS governance canister `Commit` permissions. If the SNS does not call `take_ownership`, a user cannot verify that all changes to the assets have been approved by an SNS proposal. 

### Granting permissions

To grant a principal permission within an asset canister, the following command can be used:

```
dfx canister call --network ic <canister-id> grant_permission <principal>
```

### Revoking permissions

To revoke a principal's permission within an asset canister, the following command can be used:

```
dfx canister call --network ic <canister-id>  revoke_permission <principal>
```

### Listing permissions

To list permissions for an asset canister, the following command can be used:

```
dfx canister call --network ic <canister-id> list_permitted '(record {permission = variant {<Permission>}})'
```

For example, to list all principals with the `Commit` permission:

```
dfx canister call --network ic oa7fk-maaaa-aaaam-abgka-cai list_permitted '(record {permission = variant {Commit}})'
```

To list all principals with the `Prepare` permission:

```
dfx canister call --network ic oa7fk-maaaa-aaaam-abgka-cai list_permitted '(record {permission = variant {Commit}})'
```

Once the asset canister has been deployed and permissions have been configured, the SNS decentralization swap can be started. You can learn more about launching an SNS [here](../launching/launch-summary.md)

## SNS GenericNervousSystemFunctions

Generic proposals are a way for each SNS to define custom proposals. To commit changes to the asset canister, such generic proposals are used.

To use a generic proposal, first this new proposal type has to be "registered" in the SNS. To do so, one uses a `AddGenericNervousSystemFunction` proposal.

To submit a new `AddGenericNervousSystemFunction` SNS Proposal to support the `commit_proposed_batch` API, the target canister id should be the asset canister (that is updated in the update steps below) and the target function is `commit_proposed_batch`. The validate function should be `validate_commit_proposed_batch`. 

The `ExecuteNervousSystemFunction` SNS proposal is used to execute the newly registered proposal. To submit an `ExecuteNervousSystemFunction` SNS Proposal with the output from `dfx deploy <frontend canister name> --network ic --by-proposal` see update steps below.

## Submitting an SNS proposal and upgrading an asset canister

Once an asset canister is under the control of the SNS, any changes must be done via an SNS proposal. 

Before submitting an SNS proposal to update an asset canister, assure that the asset canister has been upgraded (by proposal) to use the Wasm bundled with [dfx 0.14.1+](https://github.com/dfinity/sdk/blob/release-0.14.1/src/distributed/assetstorage.wasm.gz).

- #### Step 1: To submit an SNS proposal, first have a principal with `Prepare` permission run:

```
dfx deploy <frontend canister name> --network ic --by-proposal
```

The output will contain something like this:

```
Proposed commit of batch 2 with evidence e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855. Either commit it by proposal, or delete it.
```

- #### Step 2: Save the batch number and the evidence value for use with the asset canister API.

- #### Step 3: To ensure that others would be able to verify the evidence in the proposal, have someone else clone the dapp repo and run:

```
dfx deploy <frontend canister name> --network ic --compute-evidence
```

The computed evidence should match the evidence from step 2.

- #### Step 4: Submit a new proposal to commit the batch, using the APIs below:

Use these asset canister APIs in the proposal:

```
   type CommitProposedBatchArguments = record {
   batch_id: BatchId;
   evidence: blob;
  };
  type ValidationResult = variant { Ok : text; Err : text };


    validate_commit_proposed_batch: (CommitProposedBatchArguments) -> (ValidationResult);
  commit_proposed_batch: (CommitProposedBatchArguments) -> ();

```

If the proposal is rejected, the preparer should use this new asset canister API:

```
  type DeleteBatchArguments = record {
    batch_id: BatchId;
  };
  delete_batch: (DeleteBatchArguments) -> ();
```


## Asset canister example

An example of an SNS asset canister is canister `sqbzf-5aaaa-aaaam-aavya-cai`, which is an asset canister part of the [Dragginz Dapp SNS](https://dashboard.internetcomputer.org/canister/sqbzf-5aaaa-aaaam-aavya-cai).


-->
