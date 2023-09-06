---

sidebar_position: 3
---
# SNS開設

## 概要

SNS ローンチは SNS を作成するだけでなく、その主な目的の 1 つは
SNS のコントロールを分散化し、それによって SNS が統治するdapp のコントロールを分散化することです。

これを達成するためには、
議決権の適切な分散化を保証するために、新しいトークンを大規模なコミュニティに配布する必要があります。もちろん、その方法はたくさんあります。

開発者は自分のdapp
 を NNS に渡し、SNS の作成とそのための分散化スワップの開始を NNS に依頼することができます。
分散化スワップは参加者から ICP を集め、ICP を（ステークされた）SNS トークンと交換することで、SNS の議決権
を参加者間で分散します。

次に、この重要な概念である分散化スワップについて詳しく説明し、SNS立ち上げの詳細な技術的段階については、[このセクション（](../launching/launch-summary.md)
[](../launching/launch-summary.md)）を参照してください。

## 分散化スワップ

各 SNS のローンチには、IC が所有し運営する
**分散スワップcanister**が含まれます。
より詳細には、NNS のルートcanister によって制御されます。

- スワップはcanister 開始時に設定され、定義された量の SNS トークンが
  公開配布されます。

- 分散スワップの間、参加者はスワップcanister
   にICPを送り、dapp’s の資金調達に貢献することができます。

- スワップが終了すると、集められたICPはSNSトークンと「スワップ」されます。
  参加者はSNSトークンをSNSneuronsの形でステークされ、SNSは集められたICP
  をSNSが管理する宝庫で受け取ります。各スワップ参加者は、拠出されたICPの全体数のシェアによって日割り計算されたSNSトークンのプールの一部を受け取ります。
  例えば、スワップcanister が当初1000 SNSトークンを保有し、分散化スワップ中に500 ICPトークン
  が収集された場合、交換レートは2:1
  となり、各参加者は拠出したICPトークンごとに2 SNSトークンを受け取ります。

分散化スワップが成功すると、SNSトークンが所有され、SNSは大規模なコミュニティによって
統治されます。
収集されたICPはSNS所有の国庫に保管されます。

<!---

# SNS launch

## Overview
The SNS launch not only creates the SNS, but one of its main purposes is to
decentralize the control of an SNS and thereby of the dapp that the SNS governs.

To achieve this, new tokens must be distributed to a large community to ensure
proper decentralization of the voting power. There are of course many ways to do so.

The SNS provides one simple way to achieve this: a developer can hand over their dapp
to the NNS and ask it to create an SNS and start a decentralization swap for it.
The decentralization swap collects ICP from participants and distributes the voting
power of the SNS among participants by swapping the ICP for (staked) SNS tokens.

We next explain this key concept, the decentralization swap, in more detail and refer to
[this section](../launching/launch-summary.md) for the detailed, technical stages of an SNS launch.

## Decentralization swap

The launch of each SNS includes a separate **decentralization swap canister** that
is owned and run by the IC.
In more detail, it is controlled by the NNS root canister.

* The swap canister is set up at the start with a defined amount of SNS tokens to be
  distributed publicly.

* During the decentralization swap, participants can send ICP to the swap canister
  to contribute to the dapp’s funding.

* At the swap's end the collected ICP are “swapped” for the SNS tokens; the
  participants get staked SNS tokens in the form of SNS neurons and the SNS gets the
  collected ICP in an SNS controlled treasury. Each swap participant will receive their portion of the pool of SNS tokens, pro-rated by their share of the overall number of ICP contributed.
  For example, if the swap canister initially held 1000 SNS tokens and 500 ICP tokens
  were collected during the decentralization swap, then the exchange rate would be 2:1
  and each participant would get 2 SNS tokens for each ICP token they contributed.

After a successful decentralization swap, SNS tokens are owned and the SNS is governed
by a large community.
The ICP that were collected are in an SNS-owned treasury.

-->
