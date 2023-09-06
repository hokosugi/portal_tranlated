# SNScycle 管理

## 概要

::注意
SNS コミュニティは、すべての SNS とdapp canisters 
 が十分なcycles を確保できるように、
 必要なときに** canister それぞれの SNS** に手動でcycles を送る必要があります。

- **手動とは**、誰かが
  canisters のcycles を監視し、canisters'cycles の残高が少なくなったときに
  にcycles を追加で送るよう積極的に呼びかけることです。

- このプロセスは、**各個人のSNScanister**
   について、また**各dapp canister について繰り返さなければなりません。**

dapp
とはいえ、SNS のコンテキスト において、この重要性を強調したいと思います。
**SNS のガバナンスや SNS 台帳 の canister cycles
 が** **不足し、削除された場合、ユーザは の液体トークンとステークされたトークンを失い、  は を管理できなくなるため、これはもちろん致命的
 dapp canisters
**です。また、SNS はコミュニティによって所有されているため、 ある個人がこれらのステップに責任を感じるように、 調整が必要かもしれません。
 canisters

:::

## Cycles アーカイブcanisters

SNS コミュニティは、ほとんどの SNS と関連するdapp
 canisters は、SNS が開始されたときにインストールされるか、提案によって
SNS に明示的に追加されることを認識しておく必要があります。
例外として、台帳の**アーカイブcanisters** canister があります。
ストレージには限りがあるため、台帳はすべてのブロックを永久に保持することはできません。
したがって、そのために別のアーカイブcanisters を生成します。
台帳が新しいアーカイブを生成すると、cycles 以下のように影響します。
台帳は、現在の
cycle 残高からcycles の事前定義数*X*を取り出し、この*X* cycles を初期残高
としてアーカイブを作成します。*X*は、SNS 台帳canister が
最初に初期化されるときに設定されます。

これは特に、SNSコミュニティが
以下のことに注意すべきことを意味します:
:::caution
SNS台帳canister は自動的に新しいアーカイブ
canisters を生成します。
このとき、これらの新しいcanisters のcycles 残高を
管理しなければなりません。新しいアーカイブが生まれると、台帳のcycles 残高は
かなり減少します。
アーカイブcanister のcycles がなくなると、
台帳のブロックが失われる可能性があります。
::：

SNS コミュニティがcycles を管理しやすくするために、SNS は次のように開始されます：

- SNScanisters は、すでに多数のcycles,
  すなわち 180T で開始されます。
- SNS の台帳は 60T (2\*30T)cycles で開始されます。このような
  は、最初のアーカイブが生成されたとき、
  ともにおよそ 30Tcycles のはずです（他の SNS
  canisters がインストールされたときと同様です）。

## canisters' を観察・管理する方法cycles

次に、SNS の
cycles とdapp canisters を監視・管理する方法を詳しく説明します。

### ステップ 1: すべての SNS とガバメントdapp canisters とそのcycle バランスを見つけます。

SNS root はこれらすべてのcanisters とそのcycles について知っています。
SNS rootcanister'のメソッド
`get_sns_canisters_summary` を使用することで、すべての SNS と関連するdapp canisters ' ステータス、cycles を含む
を取得できます。

<!-- dfx, dashboard?-->

### ステップ 2: 与えられたcanister に新しいcycles を送信します。

最初のステップで、SNS またはdapp canisters の 1 つが
 cycles に不足していることが判明した場合、canister'のcycles をトップアップすることができます。

これを行うには、十分なcycles を持つウォレットが必要です。
SNS では、この方法でcycles を寄付してくれる人を探すことができます。
ウォレットからcycles を他のカンジタ（dapp または SNScanister ）に転送する方法は、[ここで](https://internetcomputer.org/docs/current/developer-docs/production/topping-up-canister/#option-2-if-you-have-cycles-on-your-cycles-wallet)見つけることができます。

 cycles
このような提案をする方法は[こちらを](./making-proposals.md)ご覧ください。

<!--## Helpful community tools
- Is referring to community tools sth that we do? (think it would be nice)
Ask authors of tools for permission
-->

## cycles 管理の（可能な）将来

このページが示すように、cycles の現在の管理は
かなり大変です。
そのため、コミュニティは
将来追加されるかもしれない機能拡張について考えています。いくつか例を挙げます：

- **Canister **
  g**roups**: groupsは、 個々の のためではなく、 のグループのための を管理することを可能にします。すべての SNS と SNS が管理する   が同じグループになれば、 SNS の管理がかなり簡単になるかもしれません。canister
   canisters canisters cycles dapp
  canister
   cycles 

- ** cycle 管理をある程度自動化するためのSNSの機能強化**：
  SNSの国庫からICPを取り出し、
   cycles に変換し、
  SNScanisters にcycles を送る作業を、少なくともある程度自動化することが考えられます。

<!---
# SNS cycle management

## Overview
:::caution
An SNS community must ensure that all SNS and dapp canisters
have sufficient cycles by manually sending cycles when necessary to
**each individual SNS canister**.


- **Manually** means that someone has to monitor the cycles of the
   canisters and actively make calls to send additional cycles to them
   when the canisters' cycles balance get low.
   
- This process has to be repeated for **each indiviudal SNS canister** 
and also for **each dapp canister**.


   
At least the latter is not new to developers who built a dapp on the IC.
Nevertheless, we want to underline the importance of this in the context
of an SNS: **if the SNS governance or SNS ledger canister run out of cycles
and get deleted, this is of course critical as users lose their
liquid and staked tokens and as the dapp canisters cannot be governed
anymore.**
Also, as the SNS canisters are owned by a community, some
coordination might be needed to ensure that some individuals
feel responsible for these steps.

:::

## Cycles in the archive canisters
The SNS community should be aware of most SNS and associated dapp 
canisters are either installed when the SNS is initiated or they are
explicitly added to the SNS by a proposal.
One exception are the **archive canisters** of the ledger canister.
Due to limited storage, the ledger cannot keep all blocks forever and
thus it spawns separate archive canisters to do so.
The ledger spawning a new archive affects the cycles as follows:
The ledger takes a predefined number *X* of cycles from its current
cycle balance and creates the archive with these *X* cycles as the
initial balance. The number *X* is set when the SNS ledger canister is 
first initialized.

This specifically means that SNS communities should be aware of the
following:
:::caution
The SNS ledger canister automatically spawns new archive
canisters.
When this happens, the cycles balance of these new canisters has
to be managed. The ledger's cycles balance decreases by a 
significat amount if a new archive is spawned.
If an archive canister runs out of cycles,
ledger blocks might be lost.
:::

To help SNS communities manage cycles, the SNS is initiated as follows:
* SNS canisters are started with already a large number of cycles,
  namely 180T
* the SNS ledger is started with 60T (2*30T) cycles such
  that when the first archive is spawn,
  both should have roughly 30T cycles (as have all other SNS
  canisters when they are installed).


## How to observe and manage the canisters' cycles
We next describe in more detail how you can monitor and manage the
cycles of the SNS and dapp canisters.

### Step 1: Find all SNS and governed dapp canisters and their cycle balance.
SNS root knows about all these canisters and their cycles. 
You can get all the SNS and associated dapp canisters' status,
which includes the cycles, by using the SNS root canister's method
`get_sns_canisters_summary`.
<!-- dfx, dashboard?-!->

### Step 2: Send new cycles to a given canister.
If the first step shows that one of SNS or dapp canisters runs 
low on cycles, you can top up the canister's cycles.

To do this, you need a wallet with sufficient cycles.
In an SNS, can look for people who donate cycles in this way.
You can find [here](https://internetcomputer.org/docs/current/developer-docs/production/topping-up-canister/#option-2-if-you-have-cycles-on-your-cycles-wallet) how to transfer cycles from a wallet to any other cansiter (which can be a dapp or SNS canister).

Otherwise, you can make a proposal that transfers some ICP
from the SNS treasury to an individual who is trusted to convert these to cycles and make such payments.
You can find [here](./making-proposals.md) how to make such a proposal.

<!--## Helpful community tools
- Is referring to community tools sth that we do? (think it would be nice)
Ask authors of tools for permission
-!->

## The (possible) future of cycles management
As this page shows, the current management of cycles is 
quite some work.
Therefore the community is thinking about some enhancements
might be added in the future. Some examples are:

- **Canister groups**: canister groups would allow to manage
   the cycles for groups of canisters rather than for
   individual canisters. If all SNS and SNS-governed dapp
   canister could be in the same group, this might considerably
   simplify SNS cycles management.  
   
- **SNS enhancements to automate some cycle management**: it is 
conceivable that the task of taking ICP from the SNS's treasury,
   converting them to cycles, and sending those cycles to the 
   SNS canisters can be automated at least to some extent.

-->
