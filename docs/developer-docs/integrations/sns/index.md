# サービス・ナーバス・システム（SNS）

## 概要

このページでは、
dapp の制御を Service Nervous System (SNS) に渡すこと、または SNS と統合することを検討する際に必要な手順を紹介します。
SNS について初めて耳にする場合は、高レベルの[SNS](/sns)
と[FAQ](/sns/faq)ページを参照し、ここで説明する内容の概要を把握することをお勧めします。

このページでは、SNSの開発者向けドキュメントがどのように構成されているかの概要を説明し、関連する他のSNSドキュメントへの参照も示します。

## SNS入門

このセクションでは、アーキテクチャやSNSの起動方法など、SNSのライフサイクルcycle の概要について説明します。

- [SNSの](./introduction/sns-intro-high-level.md)概要
- [SNSアーキテクチャ](./introduction/sns-architecture.md)SNSがどのように展開され、どのようにアップグレードされるのか、canisters 。
- [SNSの立ち上げ](./launching/launch-summary.md)SNSがどのように立ち上げられるかを高いレベルで説明しています。
- [代替DAO](./introduction/dao-alternatives.md)DAOを取得する代替方法を提示します。

## SNS立ち上げの準備

このセクションでは、トークノミクスの計画のような技術的な側面だけでなく、技術的にどのように SNS で異なる設定を行うことができるかといった技術的な側面も含め、DAO の設立を検討する際に必要なアイデアやツールを紹介します。

このドキュメントでは

- [SNSの準備](./tokenomics/index.md)
- [SNSを](./tokenomics/sns-checklist.md)立ち[上げる](./tokenomics/sns-checklist.md)際に考慮すべきことをまとめた[SNS立ち上げチェックリスト](./tokenomics/sns-checklist.md)。
- SNSの立ち上げを計画する際に考慮すべき非技術的な事項を紹介する「[展開前の考慮事項](./tokenomics/predeployment-considerations.md)」。
- [SNSトークノミクス](./tokenomics/tokenomics-intro.md)SNSのトークノミクスを計画する際に考慮すべきトークノミクスについて紹介します。
- [SNSの報酬](./tokenomics/rewards.md)SNSのトークノミクスを計画する際に考慮すべきSNSの報酬について紹介します。
- 設定を[SNSのパラメータに](./tokenomics/preparation.md)変換する方法を（技術的に）紹介します。

## SNSとの統合

このセクションでは、SNS で分散化したいdapp を持っている開発者だけでなく、ウォレットdapps や分散型取引所のような SNS と統合するサービスを構築したい開発者も対象としています。

内容は以下の通りです。

- [SNSとの統合についての紹介](./integrating/index.md)。<!--Guidelines how to integrate a frontend (integrate-sns/frontend-integration.md)-->
- [台帳との統合方法のガイドラインcanister](./integrating/ledger-integration.md) 。
- [インデックスとの](./integrating/index-integration.md)統合方法のガイドライン[ canister.](./integrating/index-integration.md)

## SNSのテスト

SNS の立ち上げ準備、SNS との統合、SNS の管理で重要なのは、テストです。
このセクションでは、以下を提供します。

- [SNSテスト入門](./testing/testing-before-launch.md)
- SNSの起動を含め、[ローカルでSNSをテストする方法のガイドライン](./testing/testing-locally.md)。
- メインネット上を含む、[SNS管理下のdapp ](./testing/testing-on-mainnet.md) 。

## SNSの起動

このセクションでは、SNSの起動について詳しく説明します。
内容は以下の通りです：

- 現在、SNSを立ち上げる方法として2つの方法がサポートされているため、[立ち上げページの読み方の紹介](./launching/index.md)。
- 2023年8月にNNSによって採用され、1つの提案のみを含む推奨される打ち上げフローの[SNS打ち上げに含まれるすべての段階の](./launching/launch-summary-1proposal.md)詳細な説明。
- 1つの提案のみを含むこの推奨打上げフローのSNS[打上げステージを完了するために必要な技術的アクション](./launching/launch-steps-1proposal.md)。
- このフローでテストを開始した人のためにまだ利用可能ですが、ある時点で廃止される可能性がある古いレガシー発射フローの[SNS発射に含まれるすべてのステージの](./launching/launch-summary.md)詳細な説明。
- [この](./launching/launch-steps.md)フローでテストを開始した人はまだ利用可能ですが、ある時点で廃止される可能性があります。

## SNSの管理

SNSが立ち上げられた後、SNSコミュニティはそれを管理する必要があります。これには、canisters に十分なcycles 、dapp を管理し、SNSのcanister アップグレードを管理することが含まれます。
このセクションには以下が含まれます。

- [SNS管理入](./managing/manage-sns-intro.md)門
- [SNSプロポーザルの](./managing/making-proposals.md)紹介。
- [ canisters](./managing/cycles-usage.md) の[ cycles 管理に関するヒント](./managing/cycles-usage.md)。
- SNS で[管理されたアセットcanister dapp の](./managing/sns-asset-canister.md)[使用方法の](./managing/sns-asset-canister.md)紹介。

<!-- Information on nervous system parameters that can be configured in each SNS (managing-sns/nervous-system-parameters.md); Information on how SNS are upgraded (managing-sns/upgradeSNS.md)-->

## ユーザー向けの説明とガイド

最後に、ウェブサイトとウィキには、SNSの利用者に関連する情報が掲載されています。

**ウェブサイトでは**、**以下の説明があります：**

- [SNSの概要ページ](https://internetcomputer.org/sns)
- [SNSのFAQ](https://internetcomputer.org/sns/faq)：
  - SNS[分散化スワップへの参加方法ガイド](/sns/faq#participate)。

**ウィキには**

- [DAO](https://wiki.internetcomputer.org/wiki/DAO)
- [Service Nervous System (SNS)](https://wiki.internetcomputer.org/wiki/Service_Nervous_System_\(SNS\)).
- [NeuronS' Fund](https://wiki.internetcomputer.org/wiki/Neurons_Fund).
- [SNSの報酬](https://wiki.internetcomputer.org/wiki/SNS_Rewards)。
- [SNSトークン化の考察](https://wiki.internetcomputer.org/wiki/SNS_Tokenization_Considerations)。
- [SNS分散化スワップトラスト](https://wiki.internetcomputer.org/wiki/SNS_decentralization_sale_trust)。

Wikiには以下のトピックのガイドもあります：

- [How to: get a DAO on the IC (IC上でDAOを取得する方法](https://wiki.internetcomputer.org/wiki/How_to_get_a_DAO_on_the_IC)) 誰かがIC上でDAOを取得する方法の要約を提供しますが、これは主にここの開発者ドキュメントに包含されます。
- [ハウツーSNS のトークノミクス設定](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration)チームが SNS DAO のトークノミクス設定を選択するための資料を提供します。
- [How to: verify SNS decentralization swap proposal SNSの分散](https://wiki.internetcomputer.org/wiki/How-to:_Verify_SNS_decentralization_sale_proposal)スワップを開始するNNSの提案を検証するガイドです。
- [How to: SNScanisters](https://wiki.internetcomputer.org/wiki/How-to:_Interact_with_SNS_canisters)SNScanisters との交流方法についてのガイドです。
- SNS[分散化スワップへの参加方法：quill](https://wiki.internetcomputer.org/wiki/How-To:_Participate_in_the_SNS_decentralization_sale_via_quill)（canisters とやりとりするためのコマンドラインツール）を使ってSNS[分散化スワップに](https://wiki.internetcomputer.org/wiki/How-To:_Participate_in_the_SNS_decentralization_sale_via_quill)参加します。
- [コミュニティファンドへの](https://wiki.internetcomputer.org/wiki/How-To:_Join_the_Community_fund_via_quill)参加方法: quill（コマンドラインツール）canisters.

<!---
# Service Nervous System (SNS)

## Overview
These pages introduce instructions needed when considering handing over control of a 
dapp to a Service Nervous System (SNS) or integrating with an SNS.
If this is the first time you hear about the SNS, we recommend to take a look at the high level [SNS](/sns)
and [FAQ](/sns/faq) pages to get an overview of what is discussed in more detail here.

This page provides an overview of how the SNS developer documentation is organized and also lists references to other relevant SNS documentation.

## Introduction to the SNS
This section gives a high level overview of the SNS lifecycle, including the architecture and how an SNS is launched.
You will find 
* [SNS introduction](./introduction/sns-intro-high-level.md) giving a quick introduction.
* [SNS architecture](./introduction/sns-architecture.md) explaining how SNSs are deployed and upgraded and what canisters are involved.
* [SNS launch](./launching/launch-summary.md) explaining on a high level how and SNS is launched.
* [Alternative DAOs](./introduction/dao-alternatives.md) presenting alternative ways how to get a DAO.

## Preparing an SNS launch
This section introduces the ideas and tools needed when considering to form a DAO, including less technical aspects, such as planning the tokenomics, as well as more technical aspects, such as how different configuration choices can technically be set in the SNS.

In this documentation you will find
* [An introduction to SNS preparation](./tokenomics/index.md).
* [The SNS launch checklist](./tokenomics/sns-checklist.md) providing a summary of what to consider when launching an SNS.
* [Pre-deployment considerations](./tokenomics/predeployment-considerations.md) introducing some non-technical considerations to take into account when planning an SNS launch.
* [SNS tokenomics](./tokenomics/tokenomics-intro.md) providing and introduction to tokenomics that can be considered when planning an SNS's tokenomics.
* [SNS rewards](./tokenomics/rewards.md) providing and introduction to SNS rewards that can be considered when planning an SNS's tokenomics.
* A (technical) introduction how to convert the configurations into [SNS parameters](./tokenomics/preparation.md).

## Integrating with an SNS
This section not only targets developers that have a dapp that they would like to decentralize with an SNS, but also developers that want to build services that integrate with SNSs, such as wallet dapps or decentralized exchanges.

It includes
* [An introduction to SNS integration](./integrating/index.md). <!--Guidelines how to integrate a frontend (integrate-sns/frontend-integration.md)-!->
* [Guidelines how to integrate with the ledger canister](./integrating/ledger-integration.md).
* [Guidelines how to integrate with the index canister](./integrating/index-integration.md).

## Testing an SNS
An important part of preparing an SNS launch, integrating with an SNS, and managing an SNS, is testing.
This section provides 
* [An introduction to SNS testing](./testing/testing-before-launch.md).
* [Guidelines how to test an SNS locally](./testing/testing-locally.md), including the SNS launch.
* [Guidelines how to test the operation of the dapp under SNS control](./testing/testing-on-mainnet.md), including on the mainnet.

## Launching an SNS
This section of documentation explains the SNS launch in detail.
It contains:
* An [introduction of how to read the launch pages](./launching/index.md) as there are currently two supported methods how to launch an SNS.
* A detailed [description of all stages included in an SNS launch](./launching/launch-summary-1proposal.md) for the recommended launch flow that was adopted by the NNS in August 2023 and only includes one proposal.
* [The technical actions that are needed to complete the SNS launch stages](./launching/launch-steps-1proposal.md) for this recommended launch flow that only includes one proposal. 
* A detailed [description of all stages included in an SNS launch](./launching/launch-summary.md) for the old, legacy launch flow that is still available for those who started testing with this flow but might be deprecated at some point.
 * [The technical actions that are needed to complete the SNS launch stages](./launching/launch-steps.md) for the old, legacy launch flow that is still available for those who started testing with this flow but might be deprecated at some point but.

## Managing an SNS
After an SNS is launched, the SNS community needs to manage it, including ensuring that the canisters have enough cycles, govern the dapp, and manage SNS canister upgrades.
This section includes
* [An introduction to managing an SNS](./managing/manage-sns-intro.md).
* [An introduction to SNS proposals](./managing/making-proposals.md).
* [Tips regarding cycles management for the canisters](./managing/cycles-usage.md).
* [An intorduction to how to use the asset canister with an SNS-controlled dapp](./managing/sns-asset-canister.md).


<!-- Information on nervous system parameters that can be configured in each SNS (managing-sns/nervous-system-parameters.md); Information on how SNS are upgraded (managing-sns/upgradeSNS.md)-!->

## Explanations and guides for users
Finally, the website and Wiki contain information relevant for users of the SNS.

On the **website**, you will find **explanations on:**
* [SNS overview page](https://internetcomputer.org/sns).
* [SNS FAQ](https://internetcomputer.org/sns/faq) including, for example:
  * [A guide how to participate in the SNS decentralization swap](/sns/faq#participate).

The **Wiki** contains more information about
* [DAOs](https://wiki.internetcomputer.org/wiki/DAO).
* [Service Nervous System (SNS)](https://wiki.internetcomputer.org/wiki/Service_Nervous_System_(SNS)).
* [Neurons' Fund](https://wiki.internetcomputer.org/wiki/Neurons_Fund).
* [SNS Rewards](https://wiki.internetcomputer.org/wiki/SNS_Rewards).
* [SNS Tokenization Considerations](https://wiki.internetcomputer.org/wiki/SNS_Tokenization_Considerations).
* [SNS decentralization swap trust](https://wiki.internetcomputer.org/wiki/SNS_decentralization_sale_trust).

The Wiki also contains guides for the following topics:
* [How to: get a DAO on the IC](https://wiki.internetcomputer.org/wiki/How_to_get_a_DAO_on_the_IC) providing a summary of how someone can get a DAO on the IC, but this is largely subsumed by the developer documentation here.
* [How to: SNS tokenomics configuration](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration) providing material enabling teams to choose a tokenomics set-up for their SNS DAO.
* [How to: verify SNS decentralization swap proposal](https://wiki.internetcomputer.org/wiki/How-to:_Verify_SNS_decentralization_sale_proposal) which is a guide how to verify the NNS proposal that starts a SNS decentralization swap.
* [How to: interact with SNS canisters](https://wiki.internetcomputer.org/wiki/How-to:_Interact_with_SNS_canisters) which is a guide on how to interact with SNS canisters.
* [How to: participate in the SNS decentralization swap via quill](https://wiki.internetcomputer.org/wiki/How-To:_Participate_in_the_SNS_decentralization_sale_via_quill), which is a command line tool for interacting with canisters.
* [How to: join the community fund via quill](https://wiki.internetcomputer.org/wiki/How-To:_Join_the_Community_fund_via_quill), which is a command line tool for interacting with canisters.

-->
