# Neuronファンド

## 概要

Neurons' Fund (NF) は、Internet Computer (IC) エコシステムのブートストラップを促進します。この記事では、Neurons' Fund (NF)の仕組みについて説明します。

## 背景と動機

ICの成功はIC上に構築されたサービスに依存します。イーサリアムと同様に、長期的には、IC上に構築されたサービスの価値は、集合的にIC自体よりも価値が高くなることを想定しています。
NFは、NNSが管理する国庫の概念を導入しています。この国庫はSNS DAOエコシステムの起動を支援することを目的としています。NNSは国庫のリソースがどのように割り当てられるかを決定します。NNSの国庫は、コミュニティが開発した追加のガバナンスと資金調達フレームワークをサポートするために将来拡張することができます。

この国庫を構築するために、NNSneuronは NF としてフラグを立て、SNS に参加する際に NF ファ ンドが取っているリスクに対してその成熟度を公開します。NFneuronの満期は、NFが参加を決定した時点で引き下げられます。neuron
ICPへの出資の代わりにNFneuronの満期を関与させることで、ICPトークノミクス全体への影響を最小限に抑えることができます。

誰もが分散スワップを通じてSNSに直接参加することもできます。

## Neurons'ファンドプロセス

Neurons' fund (NF)のプロセスは5つの段階に分けることができます：

1.  NFへの参加
2.  参加決定
3.  分散化スワップに参加するNFがSNSneuronsを受領。
4.  SNSガバナンスへの参加
5.  SNSneurons が解散する際の成熟度の向上。

以下では、これら5つのフェーズについて詳しく説明します。

![](../_attachments/neurons_fund_flow.png)

### 1: NFへの参加と脱退

NNSのフロントエンドdapp のチェックボックスを使用することで、neuron 、投票期間中のプロポーザルがある間でも、いつでもNFへの参加と脱退が可能です。

NFneuronは、次のステップで説明するように、SNSに参加する際にNFファンドが負うリスクにその満期をさらすことになります。

### 2: 参加に関する投票

NF の参加提案は、SNS を分散化する提案の一部です。これは基本的に「SNSを開始し、NFからX ICPをSNS分散化スワップに割り当てる」というものです。

その結果、すべてのNNSneuronは、dapp のためのSNSの創設と、NFがそのdapp に参加するかどうかの両方について投票します。

NFneuronがSNS提案の投票中にNFを脱退した場合、NFが提案したICP額は比例して減額されます。

SNS提案の投票受付中にneuronが参加する場合、NFがSNSに割り当てるICP額は変わりませんが、各NFneuron 、より少ない成熟度で参加することになります（比例して減少）。

### 3：分散スワップに参加するNF

SNS創設の提案が採択されると、SNSのイニシャルトークンを売却する分散スワップが開始されます。NFは分散化スワップに参加。

NFの満期neuronsは、提案で定義されたXに関連する金額だけ比例配分で減額されます。

分散化スワップが成功した場合、NNSはプロポーザルで指定されたX ICPを発行。

- SNSの国庫はX ICPを受領。
- NNS NF国庫はX ICPに対応するSNSneuronsを受け取ります。これは、参加する各NFのneuron 、さまざまな溶解遅延を持つneuronsのバスケットとして提供されます。

分散化スワップが成功しなかった場合、NFneuronsの満期は、先に減少した分だけ再び増加します。

### 4: SNSガバナンスへの参加

SNSの提案に対する投票への参加は、NNSが所有するSNSneuronsのホットキーを介して、NFneuronsに伝えられます。つまり、NFneuronはNNSによって所有されますが、NFneuronに成熟度を公開したプリンシパルには、SNSの提案に対する投票権が与えられます。

SNSneuronsの議決権は、公開された満期金額に比例します。

### 5: NFneuronsの満期増加

NNS NFの国庫は、SNSneuronsとトークンを国庫に保有し、その裁量で解散・売却します。

SNS の分散化スワップからneurons のセットが解散するとき、NNS は解散した SNSneurons の価値を決定します。後の段階では、これはDEXからデータを引き出すことによって行われる可能性があります。

SNS が管理するdapp への参加時に満期が短縮された NFneurons の満期は、前の段階で NNS が決定した量だけ増加します。最悪の場合、この金額はゼロになる可能性もあります。

NNSは後の時点でSNSからトークンを売却できます。NNSがSNSからトークンを売却した後、受け取ったICPはバーンされます。

<!---
# Neurons' fund

## Overview

The Neurons' Fund (NF) facilitates bootstrapping the Internet Computer (IC) ecosystem. In this article, we explain how the process of the Neurons' Fund (NF) works.

## Background and motivation

The success of the IC is dependent on the services built on the IC. Similar to Ethereum, we envisage that, in the long run, the value of services built on the IC will collectively be worth more than the IC itself.
The NF introduces the concept of an NNS controlled treasury. This treasury aims to assist in the bootstrapping of the SNS DAO ecosystem. The NNS will decide how the treasury resources are allocated. The NNS treasury can be extended in the future to support additional community-developed governance and fundraising frameworks.

To build this treasury, NNS neurons flagged as NF, will expose their maturity to the risks the NF fund is taking when participating in SNSes. Maturity of NF neurons will be reduced when the NF decides to participate. At a later date NF neurons whose maturity was reduced will be rewarded with maturity increases, dependent on the success of the NF's participation.
Involving the maturity of NF neurons instead of their stake in ICP minimizes the impact on the overall ICP tokenomics.

Please note that anyone can also directly participate in an SNS via the decentralization swap.

## Neurons' fund process

The Neurons' fund (NF) process can be split into five phases: 
1. Joining the NF.
2. Making a participation decision.
3. NF participating in the decentralization swap receiving SNS neurons.
4. Participation in SNS governance.
5. Increasing maturity when SNS neurons dissolve. 

In the following we describe these five phases in more detail.

![](../_attachments/neurons_fund_flow.png)

### 1: Joining and leaving the NF

Using a tick-box in the NNS front-end dapp, a neuron can join and leave the NF at any time, also while there are proposals with ongoing voting periods.

NF neurons will expose their maturity to the risks the NF fund is taking when participating in SNSes, as described in the following steps.

### 2: Voting on participation

The NF participation proposal is part of the proposal to decentralize an SNS. It is essentially a statement “Start the SNS and allocate X ICP from the NF to the SNS decentralization swap”.

As a consequence, all NNS neurons will vote on both the creation of an SNS for a dapp and on whether the NF participates in that dapp.

If NF neurons opt out of the NF whilst an SNS proposal is open for voting, then the ICP amount the NF was proposed to make is reduced proportionally.

If neurons opt in when an SNS proposal is open for voting, the ICP amount the NF allocates to an SNS remains the same, but each NF neuron will participate with less maturity (proportionally decreased).

### 3: NF participating in the decentralization swap

If a proposal to create an SNS is adopted, the decentralization swap, where initial tokens of the SNS are sold, starts. The NF participates in the decentralization swap.

The maturity of NF neurons is reduced pro rata by an amount related to X as defined in the proposal.

If the decentralization swap is successful, the NNS mints X ICP as specified in the proposal.
  * The SNS treasury receives the X ICP.
  * The NNS NF treasury receives SNS neurons corresponding to X ICP. This is provided as a basket of neurons with various dissolve delays for each participating NF neuron.

If the decentralization swap is not successful, the maturity of NF neurons is increased again by the amount it was decreased by earlier.

### 4: Participation in SNS governance

Participation in voting on the SNS proposals is passed through to the NF neurons via hotkeys on the SNS neurons owned by the NNS. This means, that NF neurons are owned by NNS but permission is given to the principals that exposed maturity to NF neurons to vote on SNS proposals.

The voting power of the SNS neurons is proportional to the amount of maturity exposed.

### 5: Increasing maturity of NF neurons

The NNS NF treasury holds SNS neurons & tokens in its treasury, to be dissolved and sold at its discretion.

When a set of neurons from a decentralization swap of an SNS dissolves, the NNS determines the value of the dissolved SNS neurons. In the first stage, this is done by a proposal. In a later stage this could be done by pulling data from a DEX.

The maturity of NF neurons whose maturity was reduced when a participation in a SNS-controlled dapp was made is increased by the amount determined by the NNS in the previous step. In the worst case this amount could also be zero.

The NNS can sell the tokens from an SNS at a later point in time. After the NNS sells tokens from an SNS, the received ICP will be burned.

-->
