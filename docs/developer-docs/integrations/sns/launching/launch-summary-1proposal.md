# SNS立ち上げの段階

## 概要

dapp SNSの立ち上げでは、dapp がNNSに引き渡され、NNSは、
SNScanisters を作成し、SNSと
を分散化するための分散化スワップを開始します（[こちらを](../introduction/sns-launch.md)参照）。

## SNSの立ち上げ段階

dapp'の制御を新しく作成されたSNSに引き渡すことは、次のような高い
レベルのステージで進行します。
 dapp 'の開発者
チームとNNSコミュニティからの手動アクションを必要とするのは、最初の2つのステージだけであることに注意してください。
NNSコミュニティの承認が必要なのは第3段階です。

### 1\. Dapp 開発者は、SNSの初期パラメータを選択します。dapp

これらのパラメータは、SNSのすべての初期パラメータを定義します。

- トークン名、トークン・シンボル、元帳取引手数料
- トークノミクスとガバナンスの仕組み
- 初期トークン分配
- 分散化スワップの条件（スワップ開始日、
  ICP トークンを最低何枚、最大何枚集めるべきかなど

したがって、これらのパラメータを定義するには、通常、すでに多くの準備と
コミュニティの関与が必要です（
詳細は[こちら](../tokenomics/sns-checklist.md)）。

:::info

これらのパラメータはまた、SNSガバナンスcanister がインストールされる初期neurons を定義します。
完全にローンチされる前、SNSガバナンスcanister はプレ分散スワップ・モードにあり、少数のプロポーザルのみが許可されます（ステップ7を参照）。
しかし、ローンチが進行している間にdapp canister (s)をアップグレードしたり、
そのDAOのためのカスタムプロポーザルを登録したりするなど、SNSプロポーザルのいくつかはこの間にすでに使用されるかもしれません。
このような操作は、SNSが完全に起動する前に、起動プロセス中にSNSプロポーザルを提出し、採用する必要があることに注意してください。
いくつかのフロントエンド、例えばNNSのフロントエンドdapp は、完全にローンチされていないSNSのneuronsを表示しません。したがって、NNSの
フロントエンドdapp プリンシパルによって制御されるneuronsは、ローンチが成功した後にのみ表示されます。
したがって、最初のneuronsは、ローンチプロセス中に十分な数がすでに操作できるように、慎重に設定する必要があります：

この段階で私たちが持っているもの

#### 表1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal</td>
    <td>operational</td>
  </tr>
</table>

### 2\. Dapp の共同コントローラとしてNNSルートを追加。dapp

ステップ 3 の少し前に、dapp 開発者はdapp canister (s) の追加コントローラとして NNS ルートcanister を追加することでdapp を NNS に引き渡します。
これは、残りのステップが自動的に動作するために必要です。
資格のある NNSneuron は誰でもステージ 3 で提案を提出できるので、これは
重要なステップです。dapp 開発者はdapp を DAO に引き渡す意思を明確に表明します。

成功した場合、ステージの終了時に以下のように変更されます：

#### 表 1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td class="light-orange-text">dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
</table>

### 3.SNS を作成するための NNS 提案の提出

また、どのdapp canister (s) を SNS に引き渡すかを定義します。

:::info

このような提案は、NNSには一度に一つしか存在しないことに注意してください。
:::info このようなプロポーザルはNNSに一度に一つしか存在しないことに注意してください。つまり、このプロポーザルを提出できる時期は、他のSNSの立ち上げに依存する可能性があります：

成功すると、この段階が終了した時点で、以下のように変化しています：

#### 表1Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
</table>

#### 表2：NNS提案

 <table>
  <tr>
    <th>NNS Proposal</th>
    <th>State</th>
  </tr>
  <tr>
    <td class="light-green-text">SNS creation proposal</td>
    <td class="light-green-text">Open</td>
  </tr>
</table>

### 4.NNS提案の決定

ステージ3からのNNS提案が採用された場合、
、NNSは自動的に残りのステージをトリガーし、その結果、
SNSの作成が開始されます。

提案が却下された場合、dapp canister (s)の制御は、元のdapp 開発者に排他的に
返されます。

提案の実行が成功するためには、以下の条件も満たす必要があります：

- 分散化されるdapp の共同コントローラーとして NNS root が追加されていること（ステージ 2
  が成功裏に実行されたこと）。
- SNS-Wcanister に十分なcycles があること。

これらの条件が満たされない場合、提案は即座に失敗し、
コントロールは元のdapp 開発者にのみ返されます。

成功すれば、この段階の最後に提案が採用され、条件が満たされます。
つまり、次のような状況になります：

#### 表2：NNSの提案

 <table>
  <tr>
    <th>NNS Proposal</th>
    <th>State</th>
  </tr>
  <tr>
    <td>SNS creation proposal</td>
    <td class="light-orange-text">Adopted</td>
  </tr>
</table>

### 5\. (自動的に）SNS-WがSNSを展開canisters

[SNS-W](../introduction/sns-architecture.md#SNS-W)
は、自動的に発動する
SNS 生成の第一段階として、SNS サブネット上に SNScanisters を生成します。
この段階では、SNScanisters はまだ意味のある値で初期化されていません。

この結果、以下のような状況になります：

#### 表1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
  <tr>
    <td class="light-green-text">SNS swap</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">NNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS governance</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS ledger</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS governance</td>
    <td class="light-green-text">not initialized</td>
  </tr>
</table>

### 6.(自動的に) SNS-Wは、SNSの唯一のコントローラとしてSNSルートを設定します。dapp

SNScanisters が展開されると、[SNS-W](../introduction/sns-architecture.md#SNS-W)
は SNS ルートをdapp canister (s) の唯一のコントローラとして設定します。

技術的な理由から、canister NNSルートは、dapp
 、ステージ2の共同コントローラーとして追加されました。
したがって、SNS-Wは、
適切な変更を行うために、NNSルートを含む必要な更新を編成します。

詳細には、これには2つのステップが含まれます：

- まず、SNS-W は
  dapp canister (s)の制御から元のdapp 開発者を削除します。
  次に、SNS-W はdapp canister (s)
  の制御者として、新しく作成された SNS ルートcanister を追加します。
  これは、dapp
   canister (s)の共同制御者であり、したがって必要なパーミッションを持つ NNS ルートへの呼び出しを介して行われます。
- この移行が成功した場合、SNS-WはNNSルートcanister に、
  自身をコントローラとして削除するように要求します。

ステージが成功すると、次のようになります：

#### 表 1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td class="light-orange-text">SNS root</td>
    <td>operational</td>
  </tr>
  <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>not initialized</td>
  </tr>
</table>

### 7\. (自動的に) SNS-W はステップ 1 の設定に従って SNScanisters を初期化します。

次に、SNS-Wは、ステージ3で提案され
、ステージ4でNNSコミュニティによって承認されたように、適切な初期ペイロードでSNScanisters を初期化します。

**SNScanisters は、分散化スワップ前のモードで初期化されます。**

SNScanister の作成後、canisters は存在しますが、まだ完全には機能していません。

- SNSは**分散化スワップ**前モードです。

この時点で、SNSの元帳には2つの勘定科目があります：

- SNS ガバナンスcanister が所有し、将来的に SNS コミュニティの希望
  に従って使用できる**国庫**。
- 分散化スワップで使用される、事前に割り当てられたトークン。

誰かがトークンを移転して配布したり、トークン市場
を時期尚早に開始したりできないように、残りの初期トークンはすべてneurons にロックされます。
さらに、分散化スワップ前のモードでは、初期neurons は
SNS を変更したり、財務トークンを移転したりできません。

成功した場合、ステージ終了時に以下のように変更されます：

#### 表 1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Waiting to be started</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
</table>

### 8.(自動）SNS スワップ開始

この開始時間に達すると、
スワップが自動的に開始され、参加受付が開始されます。

エンドユーザーは ICP トークンを
スワップcanister に送金することで、分散スワップに参加できます。

つまり、以下のような状況になります：

#### 表1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Open</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>pre-decentralization swap mode</td>
  </tr>
</table>

### 9\. (自動的に）SNSスワップが終了

この時刻に達すると、スワップは自動的に終了します。
終了時刻
より前にICPの最大参加人数に達した場合、スワップはより早く終了することもできます。

つまり、以下のような状況になります：

#### 表1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Ended</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>pre-decentralization swap mode</td>
  </tr>
</table>

### 10.(自動的に）SNSスワップが終了

分散スワップが終了すると、まず
が成功したかどうか、例えば十分な ICP が集まったかどうかが確認されます。
スワップが成功した場合、
交換レートが決定され、すべての SNS トークンが
neuron s のスワップ参加者に渡されます。

すべてのneurons が作成されると、SNS は分散制御
の下に置かれ、分散化前のスワップモードに戻ります。
このようにして、ガバナンスcanister は完全に機能するように設定されます。

スワップが成功しなかった場合、分散化の試みは失敗し、dapp’s コントロール
がdapp の元の開発者に引き渡され、
収集された ICP がスワップ参加者に返金されるなど、すべて
が SNS 立ち上げ試行前のステートへ戻されます。

成功した場合、ステージの終わりには、次のようになります：

#### 表1：Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Finalized</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
</table>

<!---
# Stages of an SNS launch
## Overview
During an SNS launch, a dapp is handed over to the NNS and the NNS both
creates the SNS canisters and starts a decentralization swap to decentralize the SNS and
thereby the dapp (see [here](../introduction/sns-launch.md)).

## SNS launch Stages
Handing over a dapp's control to a newly created SNS proceeds in the following high
level stages.
Note that only the first two stages require manual action from the dapp's developer
team and the NNS community. All other stages are exectued fully automatically.
The NNS community's approval is relevant in Stage 3. 

### 1. Dapp developers choose the initial parameters of the SNS for a dapp
  These parameters define all the initial parameters of the SNS, including
  * the token name, token symbol, ledger transaction fee
  * the tokenomics and how governance will work
  * the initial token distribution
  * the conditions for the decentralization swap, including the swap's start date and
    how many ICP tokens should at least and at most be collected
    
  Therefore defining these parameters usually requires a lot of preparation and
  community engagement already (see [here](../tokenomics/sns-checklist.md) for
  more information).

  :::info 
  
These parameters also define the initial neurons with which the SNS governance canister will be installed.
  Before being fully launched, the SNS governance canister is in a pre-decentralization-swap mode and only few proposals are allowed (see Step 7).
  However, some SNS proposals might already be used during this time, for example upgrades to the dapp canister(s) while the launch is ongoing or
  registering custom proposals for that DAO.
  Note that such operations require submitting and adopting an SNS proposal during the launch process, and thus before the SNS is fully launched. 
  Some frontends, for example the NNS frontend dapp, do not show neurons of SNSs that are not fully launched and thus neurons controlled by NNS 
  frontend dapp principals will only be visible after a successful launch. 
  Therefore, the initial neurons must be carefully setup in a way so that enough of them can be operated already during the launch process. :::

  What we have at this stage:

#### Table 1: Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal</td>
    <td>operational</td>
  </tr>
</table>


### 2. Dapp developers add NNS root as co-controller of dapp
Shortly before Step 3, the dapp developers hand over the dapp to the NNS by adding the NNS root canister as an additional controller for the dapp canister(s).
This is necessary in order for the rest of the steps to work automatically.
As any eligible NNS neuron can submit the proposal in Stage 3, this is an important step
where the dapp developers explicity express their intent to hand over their dapp to a DAO.

If successful, at the end of stage, the following has changed:

#### Table 1: Canisters
<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td class="light-orange-text">dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
</table>

### 3. Submit NNS proposal to create SNS

This proposal presents all the initial parameters for the SNS as defined in the first Stage to the NNS community. 
It also defines which dapp canister(s) should be handed over to the SNS.

:::info

Note that there can only be one such proposal at a time in the NNS. This means that the time when this proposal can be submitted might depend on other SNS' launch.
:::

If successful, at the end of this stage, the following has changed:

#### Table 1: Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
</table>

#### Table 2: NNS Proposals
 <table>
  <tr>
    <th>NNS Proposal</th>
    <th>State</th>
  </tr>
  <tr>
    <td class="light-green-text">SNS creation proposal</td>
    <td class="light-green-text">Open</td>
  </tr>
</table>


### 4. The NNS proposal is decided
If the NNS proposal from Stage 3 is adopted, 
then the NNS automatically triggers the remaining stages and thus the creation of
the SNS.

If the proposal is rejected, then the dapp canister(s)' control is given back exclusively
to the original dapp developers.

For the proposal's execution to be successful, the following conditions must also be met:
* NNS root has been added as a co-controller of the dapp to be decentralized (Stage 2
was executed succesfully)
* the SNS-W canister has sufficient cycles.

If these conditions are not met, the proposal will fail immediately and the control is 
given back exclusively to the original dapp developers.

If successful, at the end of this stage the proposal is adopted and the conditions are met.
This means that we are in the following situation:

#### Table 2: NNS Proposals
 <table>
  <tr>
    <th>NNS Proposal</th>
    <th>State</th>
  </tr>
  <tr>
    <td>SNS creation proposal</td>
    <td class="light-orange-text">Adopted</td>
  </tr>
</table>


### 5. (Automatically) SNS-W deploys SNS canisters
As a first step of the automatically triggered
SNS creation, [SNS-W](../introduction/sns-architecture.md#SNS-W)
creates SNS canisters on the SNS subnet.
At this stage the SNS canisters are not yet initialized with any meaningful values.

This results in the following situation:

#### Table 1: Canisters
<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>dapp developer principal, NNS root</td>
    <td>operational</td>
  </tr>
  <tr>
    <td class="light-green-text">SNS swap</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">NNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS governance</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS ledger</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">not initialized</td>
  </tr>
 <tr>
    <td class="light-green-text">SNS root</td>
    <td class="light-green-text">SNS subnet</td>
    <td class="light-green-text">SNS governance</td>
    <td class="light-green-text">not initialized</td>
  </tr>
</table>


### 6. (Automatically) SNS-W sets SNS root as sole controller of dapp
Once the SNS canisters are deployed, [SNS-W](../introduction/sns-architecture.md#SNS-W)
sets the SNS root as the sole controller of the dapp canister(s).

For technical reasons, the NNS root canister was added as the co-controller of the dapp
in Stage 2.
Therefore, the SNS-W orchestrates the necessary updates involving NNS root for 
making the appropriate changes.

In detail, this includes two steps:
* First, SNS-W removes the original dapp developers from controlling the
  dapp canister(s). 
  Next, SNS-W adds the newly created SNS root canister as the dapp canister(s)
  controller.
  This is done via a call to NNS root who is the co-controller of the dapp
  canister(s) and thus has the necessary permissions.
* If this transition worked successfully, SNS-W asks the NNS root canister to 
  remove itself as controller.
  
A succesful stage results in the following situation:

#### Table 1: Canisters
<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td class="light-orange-text">SNS root</td>
    <td>operational</td>
  </tr>
  <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>not initialized</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>not initialized</td>
  </tr>
</table>

### 7. (Automatically) SNS-W initializes SNS canisters according to settings from Step 1
Next, SNS-W initializes the SNS canisters with the appropriate initial payloads as proposed
in Stage 3 and approved by the NNS community in Stage 4. 

**The SNS canisters are initialized in pre-decentralization-swap mode.**

After the SNS canister creation, the canisters exist but are not yet fully functional 
- the SNS is in **pre-decentralization-swap mode**.

At this point, the SNS ledger has two accounts:

* The **treasury** that is owned by the SNS governance canister and which can be used in the future according
  to the SNS community's wishes.
* Some pre-allocated tokens to be used in the decentralization swap.

To ensure that no one can transfer tokens and distribute them, or start token markets 
prematurely, all remaining initial tokens are locked in neurons.
Moreover, in pre-decentralization-swap mode, the initial neurons cannot modify the 
SNS or transfer the treasury tokens.

If successful, at the end of stage, the following has changed:
#### Table 1: Canisters
<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Waiting to be started</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td class="light-orange-text">pre-decentralization swap mode</td>
  </tr>
</table>

### 8. (Automatically) SNS swap starts
The swap was initialized with a defined start time. 
Once this start time is reached,
the swap will automatically be started and is open for participations.

End users can participate in the decentralization swap by transferring ICP tokens to the
swap canister.

This means, we will have the following situation:
#### Table 1: Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Open</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>pre-decentralization swap mode</td>
  </tr>
</table>

### 9. (Automatically) SNS swap ends
The swap was also initialized with a defined end time.
When this time is reached, the swap automatically ends.
The swap can also end earlier if the maximum ICP participation is reached before the end 
time.

This means, we will have the following situation:
#### Table 1: Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Ended</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td>pre-decentralization swap mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td>pre-decentralization swap mode</td>
  </tr>
</table>


### 10. (Automatically) SNS swap finalizes
When the decentralization swap ends, it is first established whether
it was successful, e.g., enough ICP have been collected. 
If the swap was successful,
the exchange rate is determined and all SNS tokens are given to the swap participants in
neurons.

Once all neurons are created, the SNS should be under decentralized control
and the pre-decentralization-swap mode is reverted.
Thus, the governance canister is set to be fully functional.

If the swap is not successful, the decentralization attempt failed and everything
is reverted to the state before the SNS launch attempt, including that the dapp’s control
is handed back to the original developers of the dapp, and the
collected ICP are refunded to the swap participants.

If successful, at the end of stage, we have:

#### Table 1: Canisters

<table>
  <tr>
    <th>Canisters</th>
    <th>Subnet</th>
    <th>Controlled by</th>
    <th>State</th>
  </tr>
  <tr>
    <td>dapp canister(s)</td>
    <td>application subnet</td>
    <td>SNS root</td>
    <td>operational</td>
  </tr>
 <tr>
    <td>SNS swap</td>
    <td>SNS subnet</td>
    <td>NNS root</td>
    <td class="light-orange-text">Finalized</td>
  </tr>
 <tr>
    <td>SNS governance</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
 <tr>
    <td>SNS ledger</td>
    <td>SNS subnet</td>
    <td>SNS root</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
 <tr>
    <td>SNS root</td>
    <td>SNS subnet</td>
    <td>SNS governance</td>
    <td class="light-orange-text">normal mode</td>
  </tr>
</table>

-->
