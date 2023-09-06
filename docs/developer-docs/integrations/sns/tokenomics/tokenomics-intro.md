---

sidebar_position: 4
---
# SNSのトークノミクス

各 SNS は、特に SNS のトークノミクス
と、それが管理するdapp を定義するパラメータで個別に設定することができます。
したがって、SNS を立ち上げたり管理したりする際には、トークノミクスを理解することが重要です。
説明した概念は、
[他の DAO](../introduction/dao-alternatives.md) にも関連します。

## トークノミクスの概要

### トークノミクスとは？

トークンとはブロックチェーン上のデジタル資産です。トークノミクスはブロックチェーン上のトークンシステムの経済性を説明します。新しいインセンティブシステムやユースケースの導入が可能になるため、Web 2.0インフラストラクチャ上で動作する従来のアプリと比較して、分散型自律組織（DAO）のゲームチェンジャーとなります。DAOをトークン化することで、例えば、世界中の誰もがトークンを購入することができ、それによってDAOの初期資金に貢献することができます。さらに、トークンをアーリーアダプターやアクティブユーザーに支払うことで、ユーザーを引き付けることができます。

トークノミクスは以下のような幅広いトピックをカバーしています。

- 時間の経過に伴うトークンの需要と供給の発展。これには、新しいトークンの発行（ミンティング）とトークンの破棄（バーン）が含まれます。
- トークンの使用方法。
- DAO の参加者へのトークンの割り当て。
- アーリーアダプターへのトークン提供などのインセンティブメカニズム。

### 需要と供給

トークンの供給とは、トークン保有者が一定の価格で売却する意思のある量と定義します。同様に、トークンの需要とは、トークンの購入者が所定の価格で購入する意思のあるトークンの量です。次のグラフは、米ドル建てのトークンを例に、需要と供給の典型的な関係を表しています。

![](./_attachments/graph_supply_demand.png)
(画像出典：epthinktank.eu)

通常、供給は価格の上昇とともに増加します。例えば、ビットコインの価格が上昇した場合、通常、より多くのビットコイン保有者が市場でより高い価格で売却することを望むでしょう。一方、需要は通常、価格の上昇とともに減少します。この2つの曲線の交点がいわゆる均衡価格を決定し、図では P<sub>2</sub> と呼ばれています。

均衡価格はどのようにして決まるのでしょうか？

- P<sub>1</sub> \> P<sub>2</sub>: 余剰、すなわち需要より供給が多い状態です。これは価格に下落圧力をもたらします。
- P<sub>3</sub> \< P<sub>2</sub>: 不足、すなわち需要が供給を上回っています。これは価格に上昇圧力をもたらします。

### 時間経過に伴うトークン排出

トークン発行スケジュールは、時間の経過とともに新しいトークンが発行される割合を定義します。トークン排出スケジュールの設計はDAOの成功にとって極めて重要です。

一方では、トークンの排出はトークンの**流動**性を生み出します。人々がDAOの活動に参加できるように、最初から十分な量のトークンが利用可能であることが保証されなければなりません。

一方、トークンの排出はトークンの供給に貢献し、トークン価格に影響を与えます。したがって、トークンの排出スケジュールを制限することは、トークン価格にプラスの影響を与える可能性があります。

その結果、排出スケジュールは通常以下のように設計されます：トークン・エコノミーをスタートさせ、早期参加を促すために、当初は大量のトークンを発行します。時間が経つにつれて、トークン価格への影響を抑えるためにトークン供給量の限界増加が減少し、希少性、すなわち利用可能性が制限されます。

### トークンの使用例

トークンは、さまざまな（重複する可能性のある）ユースケースをカバーすることができます。例えば

- 
  長期的な思考とコミットメントを奨励するために、システムはしばしばトークンのステーキングを要求します。ステーキングとは、トークン保有者が一定期間トークンの一部をロックアップすることを意味します。その代わり、ステークホルダーは報酬を得ることができます。
- **通貨**：デジタルマネーの一形態で、交換の媒体、口座の単位、価値の保管庫として使用されます。
- **オペレーション**：ブロックチェーン上でのオペレーションを促進。例えば、情報の保存や取引の実行のための手数料の支払いなど。
- **分散型金融（DeFi）**：ブロックチェーン上の金融機能（貸出、貯蓄、取引など）。
  DeFiトークンは、流動性の提供など、これらの機能を促進するインセンティブをユーザーに与えます。
- **ソーシャルファイナンス（SoFi）**：ソーシャルネットワークを支えるトークン。これには人気と評判のトークン化が含まれます。例えば、参加者はフォロワーやビューが多い場合にトークンを受け取ることができます。

## DAO設立時に考慮すべきトークノミクスの側面

DAO を設立する際には、（少なくとも）以下の側面を考慮するとよいでしょう：

### トークンのユーティリティ

DAO のトークン（または複数のトークン）がどのようなユースケースで使われるかを簡潔に定義してください（ユースケースの前のセクションを参照）。  特に、トークンが以下の用途にどのように使用されるかを検討する必要があります。

- ガバナンスへの参加。
- DAOが提供するサービスへの積極的な参加への報酬。
- DAOの成長への貢献への報酬

### 初期トークンの割り当て

最初のトークン割り当て、つまりどのグループ/アカウントがどれだけのトークンを受け取るかを定義するために、以下の主要なブロックを考慮することができます。

- **DAO treasury**: DAO が管理するトークンです。DAOのプロトコルで定義された事前定義されたルールに従って使用されるか、投票に従ってアドホックに配布されます。例えば、コミュニティの賞金やユーザーへの報酬に使用されます。
- **分散化スワップ**: 初回またはその後の分散化スワップによるコミュニティへの分配。
- **シード資金提供者**: DAO立ち上げ前にプロジェクトに投資した資金提供者(選択した場合)への分配。
- **ファンディング開発チーム**: DAOの初期バージョンを作成した開発者。

最初からDAOの分散化を強調するために、開発者は以下の方法で強いシグナルを送ることができます：

- トークンの大部分をDAOの金庫に割り当て、金庫が長期にわたってユーザーにインセンティブを与え、報酬を与えることを可能にします。
- 分散化スワップに割り当てられるトークンの量が、シード資金提供者や資金調達開発チームに割り当てられる量よりも多くなるようにします。

### 議決権と分散化

議決権は、DAOがどのように発展するかを単独で決定できる単一または少数のエンティティが存在しないように、多くの独立したエンティティに分散されるべきです。

前述したように、ガバナンスへの参加は通常、一定期間のトークンのステークを必要とします。長期的な思考とコミットメントにインセンティブを与えるために、DAOは、より長い期間ステークするトークン保有者により多くの議決権を提供することができます。議決権の構成は、最初から分散化を確実にするために、トークンの（最初の）配分も考慮すべきです。例えば、資金調達開発チームの議決権が総議決権の50％以下になるようにする必要があります。

この[ページに](rewards.md)投票報酬の設定に関する詳細情報があります。

## 追加文書とツール

この[ページでは](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration)、チームが
SNS DAO のためにトークノミクスのセットアップを選択することを可能にするさらなる資料を見つけることができます。
SNSトークノミクスのキーコンセプトと SNS
トークノミクスツールへのドキュメントリンクを提供します。

<!---

# SNS tokenomics

Each SNS can be individually configured with parameters that define, among other things,
the tokenomics of an SNS and the dapp that it governs. 
Therefore when launching or maintaining an SNS, it is important to understand tokenomics.
The described concepts are also relevant for 
[other DAOs](../introduction/dao-alternatives.md).

## Tokenomics overview
### What is tokenomics?
A token is a digital asset on a blockchain. Tokenomics describes the economics of a token system on a blockchain. It is a game changer for decentralized autonomous organization (DAOs) compared to traditional apps running on a Web 2.0 infrastructure, because it enables the introduction of new incentive systems and use cases. Tokenizing a DAO allows, for instance, that anyone in the world can purchase tokens and thereby contribute to initial funding for the DAO. Moreover, tokens can be paid to early adopters and active users, which will help attract users.

Tokenomics covers a wide range of topics, such as 
* Development of token supply & demand over time. This includes creating new tokens (minting) and destroying tokens (burning).
* How tokens are used.
* Allocation of tokens to participants of the DAO.
* Incentive mechanisms, e.g., providing tokens to early adopters.   

### Supply and demand
We define the supply of a token as the amount which token holders are willing to sell at a given price. Likewise, token demand is the amount of tokens, token buyers are willing to buy for a given price. The following graph depicts the typical relationship between the supply & demand and price for an example good, which in our case could be a token priced in USD. 

![](./_attachments/graph_supply_demand.png)
(Image source: epthinktank.eu)

Typically supply increases with increasing prices. For example, if the price of Bitcoin increases, typically more Bitcoin holders will be willing to sell at the higher price on the market. On the other hand, demand typically decreases with increasing prices. The intersection of the two curves determines the so-called equilibrium price, called P<sub>2</sub> in the picture.

How do we end up at the equilibrium price ?
* If P<sub>1</sub> > P<sub>2</sub>: We have a surplus, i.e., more supply than demand. This creates downward pressure on the price. 
* If P<sub>3</sub> < P<sub>2</sub>: We have a shortage, i.e., more demand than supply. This creates upward pressure on the price. 
### Token emission over time
A token emission schedule defines the rate at which new tokens are minted over time. The design of a token emission schedule is crucial for the success of a DAO. 

On the one hand, token emissions generate **liquidity** of tokens. It should be ensured that sufficient amounts of tokens are available from the start so that people can participate in activities on the DAO. 

On the other hand, token emissions contribute to the token supply and hence influence the token price. Therefore, limiting the token emission schedule can have a positive impact on the token price. 

As a consequence, emission schedules are typically designed as follows: Initially, high amounts of tokens are issued to kick start the token economy and to incentivize early participation. Over time, the marginal increase of the token supply goes down to limit the impact on the token price and to create scarcity, i.e., limited availability.    

### Token use cases
Tokens can cover many different (potentially overlapping) use cases. For example 
* **Governance**: those tokens give holders the right to vote on proposed changes of a DAO. 
To incentivise long-term thinking and commitment, systems often require staking of tokens. Staking means that token holders lock up a portion of tokens for a period of time. In exchange, stakers can earn rewards.
* **Currency**: form of digital money with usage as medium of exchange, unit of account, store of value. 
* **Operations**: facilitate operations on the blockchain. For example paying fees to store information and execute transactions.
* **Decentralized Finance (DeFi)**: financial functions (e.g. lending, saving, trading) on a blockchain. 
DeFi tokens incentivise users to facilitate these functions, e.g. providing liquidity.
* **Social Finance (SoFi)**: tokens underpinning social networks. This includes tokenization of popularity & reputation. For example participants could receive tokens if they have a lot of followers or views.


## Tokenomics aspects to consider when establishing a DAO
When establishing a DAO you might want to consider (at least) the following aspects: 

### Token utility
Define concisely for which use cases the token (or several tokens) of the DAO will be used (see prior section on use cases).  In particular, it should be considered how the token(s) could be used for 
* Participation in governance.
* Rewarding active participation in services offered by the DAO.
* Rewarding contributions to the growth of the DAO.

### Initial token allocation
For the initial token allocation, i.e., defining which groups/accounts should receive how many tokens, you could consider the following main blocks

* **DAO treasury**: these are tokens which are at the disposition of the DAO. They can be used according to predefined rules defined in the protocol of the DAO or distributed ad-hoc subject to voting. For example, they might be used for community bounties & user rewards.
* **Decentralization swap**: distribution to the community via an initial or subsequent decentralization swap. 
* **Seed funders**: distribution to funders (if you choose to have them) who invested in the project prior to the launch of the DAO.  
* **Funding development team**: developers who created the initial version of the DAO. 

To emphasize the decentralization of the DAO from the start, developers can send a strong signal by:
* Allocating a significant part of the tokens to the DAO treasury, allowing the treasury to incentivize and reward users over time. 
* Ensure that the amount of tokens allocated to the decentralization swap is bigger than the amount allocated to seed funders and the funding development team.  

### Voting power and decentralization
The voting power should be distributed over many, independent entities such that there is not one single or a few entities that can decide by themselves how the DAO evolves.

As mentioned above, participation in governance typically requires the staking of tokens for a certain amount of time. To incentivise long-term thinking and commitment, DAOs can provide more voting power to those token holders who stake for a longer time period. The configuration of the voting power should also consider the (initial) allocation of tokens, to ensure decentralization from the start. For example, it should be ensured that the voting power of the funding dev team is below 50% of the total voting power. 

On this [page](rewards.md) there is more information on the configuration of voting rewards. 

## Additional documentation and tools
On this [page](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration)
you will find further material enabling teams to choose a tokenomics set-up for their
SNS DAO.
It provides documentation links to SNS tokenomics key concepts as well as a SNS
tokenomics tool.



-->
