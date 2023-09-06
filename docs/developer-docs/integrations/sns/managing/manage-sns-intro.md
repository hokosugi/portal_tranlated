# SNSの管理

## 概要

SNS は IC 上でシステムから提供される DAO であり、
NNS コミュニティが SNS の新しいアップグレードcanister コードを承認するなどして、ある程度保守します。
このため、SNS コミュニティから保守の負担の多くを取り除くことができます。
しかし、
SNSコミュニティが行わなければならない保守作業もあります。例えば、SNSを新しいバージョンにアップグレードする*タイミングを*決定し投票すること、必要に応じてSNSのパラメータを調整すること、SNSのcanisters
 、cycles 。

## SNSコミュニティ

[](../testing/testing-before-launch.md) SNSが[立ち上げられる](../testing/testing-before-launch.md)と、
SNSがコントロールするdapp も、SNScanisters
 も、開発者のような一個人のコントロール下に置かれることはありません。
つまり、dapp と SNS
canisters 、およびそれらにどのような変更を加えることができるかは、
**SNSコミュニティによってコントロールさ**れます。
SNSneuron を持っている人なら誰でも、dapp の変更を提案したり、SNS
のガバナンスのパラメータを変更したりする提案をすることができ、大多数が同意すれば、その提案は
自動的にオンチェーンで実行されます。
このため、以下のページは開発者だけでなく、SNSの運営に積極的に協力したいSNSコミュニティ
のメンバーにも関係します。

## SNSの管理

このページでは、
SNSの**管理に**関連するいくつかの側面を紹介します。
これらのさまざまな
トピックについて、より詳細なページが完成したら、ここにリンクします。

- 
  SNSを立ち上げた後は、どのソフトウェアもそうですが、**メンテナンスが**必要です。
  これには、SNScanisters が
  最新バージョンで動作しているか、また、運用を継続するのに十分な
  cycles があるかなどを確認することが含まれます。
  <!--how SNS canisters can be  upgraded to new versions on this page (./upgradeSNS.md)
  and about-->
   cycle の管理については、[こちらのページで](./cycles-usage.md)詳しく説明しています。
  特に強調したいのは以下の点です：

::注意
SNSコミュニティは、SNSが管理するすべてのdapp canisters だけでなく、
すべてのSNScanisters のcycles を個別にトッピングする責任があります。
 cycles が自動的に作成されるcanisters についても監視するよう、特に注意する必要があります。特に、台帳canister によって自動的に生成されるアーカイブcanisters
 がこれに含まれます。
**アーカイブcanisters に十分なcycles が提供されていない場合**、**台帳ブロック
の履歴が失われる可能性があります。**
::：

- システム機能として提供される SNS は、NNS が承認したcanister バージョンにのみアップグレードできますが、各 SNS コミュニティは
  必要に応じて SNS を構成できます。
  、新しい状況に対応したり、
  異なるガバナンスやトークノミクス・モデルを試したりするためです。
  したがって、SNSを管理するということは、コミュニティが
  、SNSの**神経系パラメータによって**定義される
  、これらの構成を定期的に検討し、調整することも意味します。

<!--We describe on
this page(./nervous-system-parameters.md)
which parameters can be chosen and how they can be adjusted 
by proposal.-->

- dapp dapp[このページは](./making-proposals.md)、プロポーザルがどのように使用され、SNSコミュニティがどのように独自のプロポーザル
  を定義できるかについて、いくつかのガイドライン
  を提供します。

- dapp は、IC上に配置されたcanister から静的アセットを保存および取得する、関連する[アセットcanister](https://github.com/dfinity/sdk/tree/master/src/canisters/frontend/ic-frontend-canister)を持つかもしれません。dapp が SNS DAO の制御下に置かれると、SNS DAO は関連するアセットcanister も制御できなければなりません。この仕組みの詳細は[このページを](./sns-asset-canister.md)参照してください。

<!---
# Manage an SNS

## Overview
SNSs are system-provided DAOs on the IC that are to some extent maintained by the
NNS community, who for example approve new upgrades of SNS canister code.
This eliminates much of the maintenance burden from the SNS communities.
However, there are still some maintenance tasks that have to be performed by an
SNS community, such as deciding and voting on _when_ an SNS should be upgraded
to a new version, adjusting the SNS parameters when needed, and making
sure that the SNS canisters do not run out of cycles.


## SNS community

Once an
[SNS is launched](../testing/testing-before-launch.md),
neither the dapp that the SNS controls, nor the SNS canisters
are under the control of a singe entity such as a developer.
This means that the dapp and to the SNS
canisters, as well as which changes can be made to them,
are **controlled by the SNS community**.
Anyone with an SNS neuron can make a proposal to suggest
a change in the dapp or to modify a parameter in the SNS
governance and, if the majority agrees, the proposal is
automatically executed on-chain.
For this reason, the following pages are not only relevant
for developers but for any member of an SNS community
who would like to actively help managing the SNS.

## Managing an SNS

On this page we will introduce a 
few aspects that are relevant for **managing** an SNS.
As we complete more detailed pages for these different 
topics, we will link to them here.

* As every piece of software, after an
SNS is launched is has to be **maintained**.
This includes ensuring that the SNS canisters are
running the latest version and that they have enough
cycles to continue operating.
You can find more information on<!--how SNS canisters can be  upgraded to new versions on this page (./upgradeSNS.md)
and about-!-> 
cycle management [on this page](./cycles-usage.md).
  We especially want to emphasize the following:

:::caution
The SNS communities are responsible for individually topping up the cycles of
all SNS canisters as well as all dapp canisters that are controlled by the SNS.
Special care must be taken that cycles are also monitored for canisters that
are automatically created. In particular, this includes the archive canisters
that are automatically spawned by the ledger canister.
**If the archive canisters are not provided with sufficient cycles, the ledger block
history may be lost.**
:::

* While SNSs that are provided as a system function can only be upgraded to canister versions approved by the NNS, each SNS community can configure their SNS according
to their needs. Most chosen configurations can also be adjusted over time,
for example to adopt to new situations or to experiment with
different governance and tokenomic models.
Therefore, managing an SNS also means that the community
should regularly consider and adjust these configurations,
which are defined by the SNS's **nervous system parameters**.


<!--We describe on
this page(./nervous-system-parameters.md)
which parameters can be chosen and how they can be adjusted 
by proposal.-!->

* An SNS community needs proposals, some of which are specific to a given dapp, to govern the DAO with its properties and the dapp. [This page](./making-proposals.md) provides some guidelines 
regarding how proposals can be used and how an SNS community can define
their own kinds of proposals.
  
* A dapp might have an associated The [asset canister](https://github.com/dfinity/sdk/tree/master/src/canisters/frontend/ic-frontend-canister) that stores and retrieves static assets from a canister deployed on the IC. Once a dapp is under SNS DAO control, the SNS DAO must also be able to control the associated asset canister. More details on how this works can be found on [this page](./sns-asset-canister.md).
-->
