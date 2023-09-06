---

sidebar_position: 5
---
# SNSの報酬

各 SNS は、特に
どのように報酬を使用して、ガバナンス参加者
とdapp ユーザーに特定の行動を奨励するかを定義するパラメータで個別に設定できます。

## 報酬の概要

この記事の目的は、SNSの報酬スキームの設計を説明することです。

トークン化の可能性は、トークン化されたオープン・ガバナンス・システム（
）によって解き放たれます。トークンを賭けて投票に参加することができます。ステークされたトークン
を持つ人は誰でも、dapp のガバナンス提案を提出し、投票することができます。

ガバナンスに参加することで、開発者、ユーザー、その他の投資家は
SNS が管理するdapp に実装されるべき新機能を一括して決定することができます。
トークンがステークされると、トークンの将来の価値
とdapp を考慮して投票するインセンティブが与えられます。 これは
SNS の報酬に関する初のシンプルなスキームです。開発者とコミュニティからの収集された経験に基づいて、この
は将来的に強化することができます。

報酬には2つのカテゴリがあります：

- ユーザーが SNS ガバナンスに参加するインセンティブを与える**投票**報酬。

- SNS が管理するdapp のアーリーアダプターやアクティブユーザーになるよう、dapp ユーザーにインセンティブを与えるための**ユーザー報酬**。

報酬スキームは、
[Network Nervous System （NNS](https://medium.com/dfinity/the-network-nervous-system-governing-the-internet-computer-1d176605d66a#:~:text=Network%20Nervous%20System%20overview,how%20to%20update%20this%20information.)）で使用されている投票報酬に基づいています。
ただし、各SNSによって柔軟に設定することができます。

## NNSの投票報酬のまとめ

Internet Computer
[NNS](/tokenomics/nns/nns-intro.md)内では、投票報酬は定期的に（現在は毎日）支払われます。 その期間の全体的な報酬プールに基づいています。 各 は、 が投票した議決権 と、 が参加したプロポーザルの数に応じて、そのプールの比例配分額を受け取ります。より正確には、 これは以下のように機能します：
 neuron neuron
 neuron

- 総報酬プールの決定：
  
  - G（発生時間）とG+8yの間の時間tについて、総供給量に占める年率換算された報酬の割合は、R(t) = 5% + 5% \[(G + 8y - t)/8y\]² です。
  - G+8yの後の時間tでは、R(t) = 5%となります。
  - ある日の投票報酬の総プールは、ICP供給量（その日のICPトークンの総供給量）\* R(t) / 365.25として計算されます。

- neuronsの投票権：
  
  - 溶解遅延が6ヶ月以上のneuronsのみが投票対象となります。解散遅延の上限は8年。
  - neuron の議決権は以下のように計算されます。`neuron_stake * dissolve_delay_bonus * age_bonus`
  - 特に溶解遅延ボーナスと年齢ボーナスは累積されます。
  - 解散遅延ボーナス(ddb)は、ddb<sub>min</sub> =1 と ddb<sub>max</sub>=2 の間の値で、解散遅延の一次関数です(上限は8年)。
  - 年齢ボーナス(ab)は、ab<sub>min</sub>=1とab<sub>max</sub>=1.25の間の値で、neuron (上限は4年)の年齢の一次関数です。neuron は、ロックステートに入るとエージングを開始。neuron が解散ステートに入ると老化は 0 にリセットされます。
  - 投票権は投票時ではなく、提案が行われたときに計算されます。

- neurons への報酬プールの割り当て：
  
  - 報酬プールは、この日に決済された提案の投票力に、該当する提案カテゴリの報酬ウェイトを乗じた値に比例して割り当てられます。
    - この報酬期間(通常は1日)に含まれるプロポーザルの集合を決定します: これらは、投票報酬に関してまだ決着しておらず、もはや投票のために開かれていないプロポーザルです。
    - 投票資格のあるneurons による総投票力が合計されます。
    - 各neuron には、これらのプロポーザルに貢献した投票力に、該当するプロポーザルのカテゴリの報酬ウェイトを掛けたものに比例して報酬が与えられます。
  - neuron が投票に対して報酬を受けると、この報酬はneuron の満期と呼ばれる属性に記録されます。この属性は取引可能な資産ではありません。neuron 成熟度から収入を得たい場合、ユーザは成熟度をバーンして新しいICPを作成する必要があります。

## SNS報酬の設計

### 投票報酬

上記の背景のセクションで強調したように、SNSはNNSの投票報酬スキーム
、スキームを柔軟に構成することができます。
特にステートメントがない限り、アプローチと方式は NNS と同じです。
NNS と同様に、SNS ガバナンス提案によって SNS の構成を変更することが可能です。

#### 総報酬プールの決定

- 報酬関数のパラメータを変更した場合の影響は、この[ツールで](https://docs.google.com/spreadsheets/d/1cTqgjGcG5rEQ5kRGprpdLvBL7ZdTqUDCuCi0QjClbgk/edit#gid=0)シミュレーションできます。

![](./_attachments/graph_rewards_total_supply.png)

- 報酬最小値 r<sub>min</sub>: 0 以上の有理数値。デフォルト値：0.00。
- 報酬最大値 r<sub>max</sub>: r<sub>min</sub> 以上の有理数値。デフォルト値：0.00。
- 報酬の支払い開始時間 t<sub>start</sub>: SNSの発生時間以上のタイムスタンプ。報酬計算がオンになると、開始時刻は現在の時刻に設定されます。
- 時間長 t<sub>delta</sub>: 0以上で、r<sub>max</sub> と r<sub>min</sub> の間の時間遷移の長さを決定します。デフォルト値：0年。
- t<sub>start</sub> と t<sub>start</sub>+t<sub>delta</sub> の間の時間 t について、総供給量に対する年率化された報酬のパーセンテージは、R(t) = r<sub>min</sub>+ (r<sub>max</sub>-r<sub>min</sub>) \[ (t<sub>start</sub>+ t<sub>delta</sub> - t) / t<sub>delta</sub> \]² です。
- t<sub>start</sub>+t<sub>delta</sub> の後の時間 t については、R(t) = r<sub>min となります。</sub>
- 特別な場合 r<sub>max</sub> = r<sub>min</sub> 報酬関数は一定、すなわちR(t)=r<sub>min。</sub>
- ある日の投票報酬の総プールは、SNS 供給量 (SNS トークンの総供給量) \* R(t) / 365.25 として計算されます。
- 投票報酬は発行されます。つまり、満期がSNSトークンに変換されると、新たな供給が発生します。SNSがt<sub>start</sub>+t<sub>delta</sub> の後にトークン供給の増加を止めたい場合、SNSはr<sub>min</sub>=0を設定する必要があります。

#### neuronの投票力 s

- 投票に必要な最小溶解遅延 dd<sub>min</sub>: 0 以上の整数値。デフォルト値：6ヶ月。
- 最大解散遅延 dd<sub>max</sub>: dd<sub>min</sub> 以上の整数値。デフォルト値：8年。
- 最大ディゾルブ遅延ボーナス：
  - ddb<sub>max</sub> 1以上の有理数値。デフォルト値：2。
  - 特別な場合 ddb<sub>max</sub>=1 ディゾルブ遅延ボーナスは発生しません。
- 最大年齢 a<sub>max</sub>: 0 以上の整数値。 デフォルト値：4歳。
- 最大年齢ボーナス
  - ab<sub>max</sub> 1 以上の有理数値。デフォルト値：1.25。
  - 特殊ケース ab<sub>max</sub>=1 の場合、年齢ボーナスはありません。

#### 報酬プールの配分

- 報酬プールは、その日に決着した議案の議決権に比例して配分されます (NNS と同じ)。
- 特定の日に提案が提出されなかった場合、報酬は翌日に持ち越されます。
- NNSでは、提案の種類に応じて報酬のウェイトが設定されています。SNS報酬スキームの最初のバージョンでは、この機能はまだ利用できません。

投票報酬の計算と分配を有効にするフラグがあります。SNSは、投票報酬なしで、あるいは投票報酬なしで、立ち上がり期間を過ごすことを選択するかもしれません。

### 投票報酬パラメータの設定

投票報酬パラメータは、SNSガバナンス（canister ）で定義され、提案によって変更することができます。実装で使用するデータ構造**VotingRewardsParametersの**詳細は[こちら](https://github.com/dfinity/ic/blob/master/rs/sns/governance/proto/ic_sns_governance/pb/v1/governance.proto#L726)。

以下の表では、**VotingRewardsParametersの**関連するすべてのパラメータの概要を、この記事の表記と実装で使用される正式名称をリンクして示します。

| パラメータ | *VotingRewardsParameters*における正式名称 |
| --- | --- |
| r<sub>min</sub> | *初期報酬ポイント数* |
| r<sub>最大</sub> | *最終的な報酬率* |
| t<sub>start</sub> | *開始タイムスタンプ秒数* |
| t<sub>デルタ</sub> | *報酬レート遷移時間秒数* |

**VotingRewardsParametersが**入力されていない場合、投票報酬は無効になります。

以下に、投票権の決定に関連するパラメータの概要を示します。

| パラメータ | *VotingRewardsParameters*内の正式名称 |
| --- | --- |
| dd<sub>min</sub> | *neuron最小投票遅延時間(\_minimum\_dissolve\_delay\_to\_vote\_seconds)* |
| dd<sub>max</sub> | *最大ディゾルブ遅延秒数* |
| ddb<sub>最大</sub> | 実装後、追加予定 |
| a<sub>最大</sub> | *最大\_neuron\_age\_for\_age\_bonus* |
| ab<sub>最大</sub> | 実施後、追加予定。 |

### ユーザー報酬

- ユーザー報酬の目的は、SNSの早期導入と積極的な利用を促進することです。利用状況やそれに応じたユーザー報酬の意味は、個々の SNS によって大きく異なる可能性があるため、最初は非常にシンプルな設定にしています。
- トークンの一部（ユーザー報酬用に予約）は、SNS が管理するアカウント（canister ）に保管できます。このcanister は、報酬がいつ誰に支払われるかを成文化できます。
- このソリューションでは、（新規発行ではなく）既存のトークンを支払うことができます。ユーザーの報酬が発行のトリガーになることが必要な場合は、後のフェーズで追加できます。

<!---

# SNS rewards

Each SNS can be individually configured with parameters that define, among other things,
how and SNS uses rewards to incentivice certain behavior for the governance participants
and the dapp users.

## Rewards Overview

The goal of this article is to explain the design of the SNS reward scheme.

The full potential of tokenization can be unlocked by a tokenized open governance system,
where tokens can be staked to participate in voting. Anyone with staked tokens 
can submit and vote on governance proposals for the dapp.

By participating in governance, developers, users, and other investors can collectively 
decide what new features should be implemented in the dapp that is governed by the SNS.
As their tokens are stacked, they will be incentivized to vote taking into consideration
the future value of the tokens and the dapp. This represents the first simple scheme for
SNS rewards. Based on the collected experience from developers and the community, this
can be enhanced in the future.

We consider two categories of rewards:
  * **Voting rewards** to incentivize users to take part in SNS governance.

  * **User rewards** to incentivize dapp users to become early adopters and active users of the dapp that is governed by the SNS.

The reward scheme is based on the voting rewards used in the
[Network Nervous System (NNS)](https://medium.com/dfinity/the-network-nervous-system-governing-the-internet-computer-1d176605d66a#:~:text=Network%20Nervous%20System%20overview,how%20to%20update%20this%20information.),
which however can be flexibly configured by each SNS.

## Recap on NNS voting rewards

The [NNS](/tokenomics/nns/nns-intro.md) is the DAO that governs the Internet Computer.
Within the NNS, voting rewards are paid out on a regular basis (currently daily),
based on an overall reward pool for that time period. 
Each neuron receives a pro-rata amount of that pool according to the voting power with 
which the neuron voted and in how many proposals the neuron participated. More precisely,
this works as follows:

* Determination of the total reward pool:
  * For a time t between G (genesis time) and G + 8y the annualized reward as a percentage of total supply is R(t) = 5% + 5% [(G + 8y – t)/8y]²
  * For a time t after G+8y, we have R(t) = 5%.
  * The total pool of voting rewards for a given day is calculated as ICP supply (total supply of ICP tokens on that day) * R(t) / 365.25.
* Voting power of neurons:
  * Only neurons with a dissolve delay of more than 6 months are eligible for voting. The maximum dissolve delay is 8 years.
  * The voting power of a neuron is computed as `neuron_stake * dissolve_delay_bonus * age_bonus`
  * In particular the dissolve delay bonus and the age bonus are cumulative.
  * The dissolve delay bonus (ddb) is a value between ddb<sub>min</sub> =1 and ddb<sub>max</sub>=2 and a linear function of the dissolve delay (capped at eight years).
  * The age bonus (ab) is a value between ab<sub>min</sub>=1 and ab<sub>max</sub>=1.25 and a linear function of the age of the neuron (capped at four years). A neuron starts aging when it enters a locked state. Aging is reset to 0 when a neuron enters a dissolving state.
  * The voting power is calculated when the proposal is made, not when the ballot is cast.

* Allocation of reward pool to neurons:
  * The reward pool is allocated in proportion to the voting power of proposals that are settled on this day multiplied by the reward weight of the according proposal category.
    * Determine the set of proposals that are included in this reward period (typically a day): these are the proposals that are not yet settled with respect to voting rewards, and no longer open for voting.
    * The total voting power by neurons who were eligible for voting is added up.
    * Each neuron is rewarded in proportion to the voting power it contributed to these proposals multiplied by the reward weight of the according proposal category.
  * When a neuron is rewarded for voting, these rewards are recorded in an attribute of the neuron that is called maturity which is not a tradable asset. If a user wants to generate income from maturity, he/she needs to burn maturity to create new ICP via spawning a neuron which is a non-deterministic process described [here](https://wiki.internetcomputer.org/wiki/Maturity_modulation).

## Design of SNS rewards

### Voting rewards

As highlighted in the background section above, the SNSs leverage the NNS voting reward scheme 
and allow for flexibility to configure the scheme. Hence, in the following we go through the
features of the NNS and describe how it is adapted and made configurable for the SNS. 
Unless otherwise stated, the approach and formula are the same as for the NNS. 
As for the NNS, it is possible to change the SNS configuration by an SNS governance proposal.

#### Determination of the total reward pool
  * The impact of changing the parameters of the reward function can be simulated in this [tool](https://docs.google.com/spreadsheets/d/1cTqgjGcG5rEQ5kRGprpdLvBL7ZdTqUDCuCi0QjClbgk/edit#gid=0). 
  
  ![](./_attachments/graph_rewards_total_supply.png)
* Reward minimum r<sub>min</sub>: rational value greater than or equal to 0. Default value: 0.00.
* Reward maximum r<sub>max</sub>: rational value greater than or equal to r<sub>min</sub>. Default value: 0.00.
* Start time for paying out rewards t<sub>start</sub>: timestamp greater than or equal to genesis time of the SNS. The start time is set to the current time once the reward calculation is switched on.
* Time length t<sub>delta</sub> which is greater than or equal to 0 and which determines the time transition length between r<sub>max</sub> and r<sub>min</sub>. Default value: 0 years.
* For a time t between t<sub>start</sub> and t<sub>start</sub>+t<sub>delta</sub> the annualized reward as a percentage of total supply is R(t) = r<sub>min</sub>+ (r<sub>max</sub>-r<sub>min</sub>) [ (t<sub>start</sub>+ t<sub>delta</sub> – t) / t<sub>delta</sub> ]²
* For a time t after t<sub>start</sub>+t<sub>delta</sub>, we have R(t) = r<sub>min</sub>
* For the special case r<sub>max</sub> = r<sub>min</sub> the reward function is constant, namely R(t)=r<sub>min</sub>
* The total pool of voting rewards for a given day is calculated as SNS supply (total supply of SNS tokens) * R(t) / 365.25.
* Voting rewards are minted, i.e. generating new supply once the according maturity is converted to the SNS token. In case that the SNS would like to stop a token supply increase after t<sub>start</sub>+t<sub>delta</sub> the SNS should set r<sub>min</sub>=0.

#### Voting power of neurons
  * Required minimum dissolve delay for voting dd<sub>min</sub>: integer value greater than or equal to zero. Default value: 6 months.
  * Maximum dissolve delay dd<sub>max</sub>: integer value greater than or equal to dd<sub>min</sub>. Default value: 8 years.
  * Maximum dissolve delay bonus:
    * ddb<sub>max</sub> rational value greater than or equal to 1. Default value: 2.
    * The special case ddb<sub>max</sub>=1 results in no dissolve delay bonus.
  * Maximum age a<sub>max</sub>: integer value greater than or equal to 0. Default value: 4 years.
  * Maximum age bonus
    * ab<sub>max</sub> rational value greater than or equal to 1. Default value: 1.25.
    * The special case ab<sub>max</sub>=1 results in no age bonus.
#### Allocation of reward pool
  * The reward pool is allocated in proportion to the voting power of proposals that are settled on this day (same as for the NNS).
  * If on a particular day no proposal was submitted then rewards will be carried over to the next day.
  * NNS has reward weights for different proposal types. For the first version of the SNS reward scheme this functionality is not yet available.

There is a flag which activates the calculation and distribution of voting rewards, as an SNS might choose to go through a ramp-up period without voting rewards, or with no voting rewards at all.

### Setting voting reward parameters

Voting reward parameters are defined in the SNS governance canister and can be changed by proposal. Details on the data structure **VotingRewardsParameters** used in the implementation are [here](https://github.com/dfinity/ic/blob/master/rs/sns/governance/proto/ic_sns_governance/pb/v1/governance.proto#L726).

In the following table we provide an overview of all relevant parameters of **VotingRewardsParameters**, linking the notation of this article to full names used in the implementation.

|Parameter|Full name in *VotingRewardsParameters*|
| --- | --- |
|r<sub>min</sub>|*initial_reward_rate_basis_points*|
|r<sub>max</sub>|*final_reward_rate_basis_points*|
|t<sub>start</sub>|*start_timestamp_seconds*|
|t<sub>delta</sub>|*reward_rate_transition_duration_seconds*|

When **VotingRewardsParameters** is not populated, voting rewards are disabled.

In the following we provide an overview of the relevant parameters for the determination of voting power.

|Parameter|Full name in *VotingRewardsParameters*|
| --- | --- |
|dd<sub>min</sub>|*neuron_minimum_dissolve_delay_to_vote_seconds*|
|dd<sub>max</sub>|*max_dissolve_delay_seconds*|
|ddb<sub>max</sub>|To be added, once implemented.|
|a<sub>max</sub>|*max_neuron_age_for_age_bonus*|
|ab<sub>max</sub>|To be added, once implemented.|

### User rewards

* The purpose of user rewards is to foster early adoption and active usage of the SNS. Given that the meaning of usage and the according user rewards can vary greatly across individual SNSs we have a very simple set-up at start.
* Some tokens (reserved for user rewards) can be held in an account that is owned by an SNS-controlled canister. This canister can then codify when the rewards are paid out and to whom.
* This solution allows paying out existing (not newly minted) tokens. If it is required that user rewards trigger minting, this could be added in a later phase.

-->
