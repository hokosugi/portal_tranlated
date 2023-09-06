# NNSのステークと投票報酬

## 概要

ステークホルダーはICPトークンをステークすることで投票権を獲得し、投票報酬を得ることができます。

Internet Computer は分散型プラットフォームであり、その進化は投票を通じてステークホルダーによって決定されます。これは、Internet Computer の将来に影響を与える決定が、その結果に既得権を持つ人々によって行われることを意味します。ガバナンスへの参加の見返りとして、Internet Computer は投票報酬を与えます。投票者は積極的に投票することもできますし、Internet Computer のリキッド・デモクラシーを利用して他の投票者を自動的にフォローすることもできます。

## 主要概念

### Neurons

権利確定され、投票権を得るためには、ICPトークンはまず
にステークされ、6
ヶ月から長くて 8 年を超える期間ロックされる必要があります。

トークンがユーザーのアカウントに保管されるのと同様に、ステークも「neuron 」と呼ばれる特別な
アカウントに保管されます。各neuron は独自の識別子を持ち、
そのステークに関連するいくつかの属性を持ちます。これらには以下が含まれます：

- ロックされている期間（「ディゾルブ遅延」）。
- 現在解散中かどうか。
- プロポーザルに投票した結果、どれだけの報酬が発生したか（「満期」）。

neuron が6ヶ月以上ロックされると、提案の提出とそれに対する投票の両方（
）の能力が得られます。neuron がどれだけ積極的に提案に投票したかによって、
投票報酬が発生します。
公開されているすべての提案に投票すると、最大の報酬を得られます。

neuron は他のneuronを「フォロー」することもできます。この場合、
は自動的に、
がフォローしているneuronの大多数と同じ投票を行います。

### 投票力

ロックされたneuron の投票力は、いくつかの factorによって決定されます：

- プリンシパル。1ICP=1票の投票権。
- 次に、そのロックアップ期間、つまり解散の遅れ。6ヶ月の場合は1.06倍、8年の場合は2倍の投票力ボーナスが付与されます。それ以外の期間は、その間に直線的に拡大されます。
- 最後に、年齢、つまり解散せずにロックされていた期間。4年間は1.25倍のボーナスが与えられ、他のボーナスと乗算されます。0秒から4年の間の他のすべての持続時間は、その間で直線的に変化します。

つまり、最大の投票力である、1ICPにつき2.5票のステーク
は、neuron を8年間ロックし、そのロックしたステートで
を4年間放置することによってのみ達成可能です。その時点で、あなたはコミットされたステークに対して最大の
議決権を持つことになります。

### 成熟度

成熟度は、neuron に蓄積された投票報酬を表します。毎日
は、
提案が行われた時点での投票力と、投票した提案の数に基づいて、neuron に投票したすべての参加者に報酬総額の
の一部を割り当てることで、参加者に報酬を与えます。

議決権行使報酬の課税状況については、税務当局によって見解が異なる可能性がありま すのでご注意ください。Neuron 、議決権行使報酬を受け取り、それをICPに変換するオーナーは、 適切な専門家にご相談ください。

neuron の議決権を複利的に利用したい場合、最も
自然な活動は、定期的に「満期を賭ける」ことです。
neuron から獲得した報酬を清算し、ICPに変換したい場合は、満期を報酬neuron に「スポーン」することができます。

## ステーキングが重要な理由

ステーキングは、Internet Computer をサポートする人々が、
プラットフォームで次に起こることを決定できるようにする方法です。

Internet Computer の立ち上げ当初は、すべての提案の可決には
の多数決が必要でした。しかし、これは徐々に変わりつつあります。
アップデート後、総投票力の3%のうち
の過半数だけで提案を通過させることが可能になりました。つまり、大規模な事業体が棄権し、
ネットワークの大多数が投票しない場合でも、
提案にチャンスがあるということです。

## 投票報酬

投票報酬はneuronの重要な側面であり、複利で総投票力を増やすことができます。そこで、
ステーキングと報酬をよりよく理解するために、
ステーキングを2つの視点から見ることが役に立つかもしれません：

### 長期：何年にもわたる投票報酬

投票報酬関数はこの曲線で描かれます: https://dashboard.internetcomputer.org/circulation

最初の年、NNSは総供給量の10%を割り当て、
投票報酬を生み出します。
報酬は産卵され、それに応じた報酬neuron が
払い出されるまで鋳造されないためです。この配分率は、発生から8年目までに5%に達するまで2次関数的に低下します。
最初の8年間の総供給量に対する年率化された報酬の割合の計算式は`R(t) = 5% + 5% [(G + 8y – t)/8y]²` です。

NNSのすべてのパラメータと同様に、発行率は
NNSの提案によって変更できますが、これは現在のレートスケジュールです。

ICPの総供給量はデフレと
インフレを伴うダイナミックなシステムであるため、
与えられた日や年の投票報酬が将来どうなるかを予測することは不可能です。
数カ月後の
割当率を予測するのは比較的簡単ですが、
割当率が変更される可能性があること、および利害関係者が
その満期を迎える頻度が高いことから、総供給量を予測するのははるかに困難です。

### 短期：毎日投票する報酬

毎日、ネットワークから各投票neuron に報酬が付与されます。各neuron が受け取るこれらの報酬の
パーセンテージは、
以下の factorに依存します：

- 賭けられたICPと満期の量。
- 溶解遅延の長さ。
- neuron の「年齢」(非溶解ステートで過ごした時間)。
- neuron が投票した適格な提案の数。

これらの値を組み合わせて、neuron の総投票力を計算：

- neuronのうち、溶解遅延が6ヶ月を超えるものだけが投票対象となります。最大解散遅延は8年。
- neuron の議決権は`neuron_stake * dissolve_delay_bonus * age_bonus` のように計算されます。
- 特に溶解遅延ボーナスと年齢ボーナスは累積されます。
- neuron の賭け金は、賭け ICP と賭け満期の合計です。
- 溶解遅延ボーナス (ddb) は、ddb<sub>min</sub> = 1 と ddb<sub>max</sub> = 2 の間の値で、溶解遅延の一次関数です (上限は 8 年)。
- 年齢ボーナス(ab)は、ab<sub>min</sub>=1とab<sub>max</sub>=1.25の間の値で、neuron (上限は4年)の年齢の一次関数です。neuron は、ロックステートに入るとエージングを開始。neuron が解散ステートに入ると老化は 0 にリセットされます。
- 投票権は投票時ではなく、提案がなされたときに計算されます。

たとえば、neuron のステークが60 ICP、ステークされた満期が40である場合、そのステークを合計すると100になります。
次に、解散遅延を8年と仮定します。これにより、解散遅延ボーナスが2になります。
最後に、neuron の年齢を2年と仮定します。これにより、年齢ボーナスは1.125となります。
すべてを合わせると、このneuron の投票力は`100 * 2 * 1.125 = 225` となります。

ある日の投票報酬プールの合計は、`ICP supply (total supply of ICP tokens on that day) * R(t) / 365.25` のように計算されます。
報酬プールは、その日に決着した提案の投票力に、該当する提案カテゴリの報酬ウェイトを掛けた比率で配分されます。

たとえば、ある日に NNS が
報酬の合計で 1000 満期を生み出し (この計算方法については後述)、
のプロポーザルが 10 件提出され、neuronの 2 件のみが投票権を有していたとします：

- Neuron A の投票権は 20 で、10 の提案すべてに投票しました。
- Neuron B の議決権は 80 で、10 の提案すべてに投票しました。

Bは80の議決権を持っており、10議案すべてに投票しました。すると、1000の満期金は、
比例した議決権によって、この2つのneuronに分けられます：

- Neuron 20の議決権を持つAは、全体の20% = 200の満期を得ます。
- Neuron 投票力80のBは、全体の80％＝800満期を獲得。

もしneuron のどちらかがこれら10件の提案のうちX%にしか投票しなかった場合（該当する提案カテゴリの報酬ウェイトで加重）、
、その報酬は最大資格のX%に減少します。

たとえば、ある日に10件の提案があったとして、neuron がそのうちの5件にしか投票しなかった場合、
そのneuron は、その日に受け取ることができる報酬の50%しか受け取ることができません。
 neuron が投票した5件の提案の報酬の重みが2だった場合、
それは`weight_of_proposal_votes = 5 * 2` 、一方`weight_of_all_proposals = 5 * 2 + 5 * 1` 、
したがってそれは、その日に受け取ることができる報酬の`(5 * 2) / (5 * 1 + 5 * 2) = 66%` を受け取ることになります。

### インフレとデフレのメカニズム

デフレ・メカニズム：

- cycles を発行してコンピュートとストレージの代金を支払い、ICP をバーンしてcycles を作成。
- 取引手数料のバーン。
- neuronsのプロポーザルに失敗した場合の手数料のバーン。これはneuronsの払い出しまたはマージ時にのみ発生するため、蓄積された手数料は最終的にデフレに貢献する前にしばらくの間持続する可能性があることに注意。

インフレメカニズム：

- ノードプロバイダーはICPを発行することで報酬を得ます。
- 投票報酬は、一旦スポーンしてICPに変換されます。

<!---
# Staking and voting rewards on the NNS

## Overview
Stakeholders gain voting power and can earn voting rewards by staking their ICP tokens. 

The Internet Computer is a decentralized platform whose evolution is decided by its stakeholders through voting. This means decision impacting the future of the Internet Computer are made by people vested in the outcome. In return for participation in governance, the Internet Computer gives out voting rewards. Voters can vote actively, or they can use the liquid democracy on the Internet Computer to automatically follow other voters.

## Key concepts

### Neurons

In order to become vested and obtain voting power, ICP tokens must first
be staked, and then locked up for a length of time greater than 6
months to, at most, 8 years.

Just as tokens are held in a user's account, stake is held in a special
account called a "neuron". Each neuron has its own identifier, and
several attributes relating to its stake. These include:

* The length of time it is locked for (the "dissolve delay").
* Whether it is currently dissolving.
* How much reward it has accrued as a result of voting on proposals (the "maturity").

Once a neuron is locked for more than six months, it gains the ability
both to submit proposals and to vote on them. Voting in turn generates
voting rewards, based on how active a neuron is in voting on proposals.
If you vote on every open proposal, you gain the maximum reward.

A neuron can also "follow" other neurons, which causes it to
automatically vote the same as the majority of the neurons that it
follows. 

### Voting power

The voting power of a locked neuron is determined by several factors:

* Principally, by its stake. 1 ICP = the power of 1 vote.
* Next, by its lock up duration, or dissolve delay. 6 months grants a 1.06x voting power bonus, and 8 years grants 2x. All other durations scale linearly between.
* Lastly, by its age, or length of time spent locked up without dissolving. 4 years grants a 1.25x bonus, multiplicative with any other bonuses. All other durations between 0 seconds and 4 years scale linearly between.

This means that the maximum voting power, of 2.5 votes per ICP staked,
is only achievable by locking up your neuron for 8 years, and leaving it
in that locked up state for 4 years. At that time you will have the most
voting power for the stake committed.

### Maturity

Maturity represents the voting rewards accumulated in a neuron. Each day
the network rewards participants by allocating to every voting neuron a
portion of the total reward, based both on its voting power at the time
proposals were made, and the number of proposals it voted on.

Please note that different tax authorities may take different views on the taxation status of the voting rewards. Neuron owners who receive voting rewards and convert them to ICP should consult appropriate professionals.

For those who wish to compound the voting power in their neuron, the most
natural activity is to "stake maturity" on a regular basis. If you wish to liquidate rewards you earn from the
neuron and convert them to ICP, you can "spawn" maturity into a reward neuron.

## Why staking matters

Staking is a way of allowing those who support the Internet Computer to
decide what happens next with the platform.

When the Internet Computer first launched, all proposals required a
majority vote to pass. Gradually, however, this is changing. After an
update it is now possible for proposals to pass with only a
majority among 3% of the total voting power, meaning that proposals
stand a chance even if large entities abstain and the majority of the
network does not vote.

## Voting rewards

Voting rewards are an important aspect of neurons and can be compounded to increase your total voting power. So
to better understand staking and reward, it may be helpful to look at
staking from two perspectives:

### Long-term: voting rewards over years

The voting reward function is depicted in this curve: https://dashboard.internetcomputer.org/circulation

In the first year, the NNS allocates 10% of the total supply to generate
voting rewards. Note the term "allocates" rather than "mints", because
rewards are not minted until they are spawned and the according reward neuron is
disbursed. This allocation rate drops quadratically until it reaches 5% by year 8 after genesis.
The formula for the annualized rewards as a percentage of total supply for the first 8 years is `R(t) = 5% + 5% [(G + 8y – t)/8y]²`.

Like all parameters in the NNS, the minting rate can be changed via
NNS proposals, but this is the current rate schedule.

Because the total supply of ICP is a dynamic system with deflation and
inflation, it is impossible to predict what voting rewards will be on any
given day or year in the future. It is relatively easy to predict what
the percentage allocation rate will be months from now, but it is much
harder to predict what the total supply will be both because of
potential changes to the rate, and how often stakeholders will spawn
their maturity.

### Short-term: voting rewards each day

Every day, rewards are granted by the network to each voting neuron. The
percentage of those rewards received by each neuron depend on the
following factors:

* Amount of ICP and maturity staked.
* Length of dissolve delay.
* "Age" of the neuron (time spent in a non-dissolving state).
* Number of eligible proposals the neuron has voted on.

These values are combined to calculate the total voting power of a neuron. It is computed as follows:
* Only neurons with a dissolve delay of more than 6 months are eligible for voting. The maximum dissolve delay is 8 years.
* The voting power of a neuron is computed as `neuron_stake * dissolve_delay_bonus * age_bonus`.
* In particular the dissolve delay bonus and the age bonus are cumulative.
* The neuron stake is the sum of staked ICP and staked maturity.
* The dissolve delay bonus (ddb) is a value between ddb<sub>min</sub> = 1 and ddb<sub>max</sub> = 2 and a linear function of the dissolve delay (capped at eight years).
* The age bonus (ab) is a value between ab<sub>min</sub>=1 and ab<sub>max</sub>=1.25 and a linear function of the age of the neuron (capped at four years). A neuron starts aging when it enters a locked state. Aging is reset to 0 when a neuron enters a dissolving state.
* The voting power is calculated when the proposal is made, not when the ballot is cast.

For example, if a neuron has a stake of 60 ICP and 40 staked maturity, it has a combined stake of 100.
Then, let's assume a dissolve delay of 8 years, which gives it a dissolve delay bonus of 2.
Finally, assume a neuron age of 2 years. This gives it an age bonus of 1.125.
All together, this neuron then has a voting power of `100 * 2 * 1.125 = 225`.

The total pool of voting rewards for a given day is calculated as `ICP supply (total supply of ICP tokens on that day) * R(t) / 365.25`.
The reward pool is then allocated in proportion to the voting power of proposals that are settled on this day multiplied by the reward weight of the according proposal category.

For example, if on a single day the NNS has generated 1000 maturity in total
rewards (see below for more on how this is computed), and there were 10
proposals submitted for which only two neurons were eligible to vote on, and:

* Neuron A has a voting power of 20, and voted on all 10 proposals.
* Neuron B has a voting power of 80, and voted on all 10 proposals.

Then the 1000 maturity would be divided between these two neurons by their
proportional voting power:

* Neuron A with voting power of 20, gets 20% of the total = 200 maturity.
* Neuron B with voting power of 80, gets 80% of the total = 800 maturity.

If either neuron had only voted for X% of those 10 proposals (weighted by the reward weight of the according proposal category),
it's reward would be decreased to X% of its maximum eligibility.

For example, if on a single day there were 10 proposals, but a neuron only voted for five of them,
that neuron would only receive 50% of its rewards for which it is eligible that day.
If the five proposals the neuron voted on had a reward weight of two,
it would have a `weight_of_proposal_votes = 5 * 2`, while the `weight_of_all_proposals = 5 * 2 + 5 * 1`,
therefore it would receive `(5 * 2) / (5 * 1 + 5 * 2) = 66%` of the rewards for which it is eligible that day.

### Inflationary and deflationary mechanisms

Deflationary mechanisms:

* Minting cycles to pay for compute and storage burns ICP to create cycles.
* Burning of transaction fees.
* Burning of the fee for failed proposals of neurons; note that this only happens at disbursement or merging of neurons, so accumulated fees can persist for a while before finally contributing to deflation.

Inflationary mechanisms:

* Node providers are paid by minting ICP.
* Voting rewards, once spawned and converted to ICP.

-->
