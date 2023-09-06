---

title: "Tokenomics of the Internet Computer"
abstract: 
shareImage: /img/how-it-works/tokenomics.600.jpg
slug: tokenomics
---
# のトークノミクスInternet Computer

### ICPユーティリティ・トークン

Internet Computer (IC)はICPと呼ばれるユーティリティトークンを使用しています。このトークンは、プラットフォーム上で以下の機能に使用されます：第一に、ICP保有者はICPをステークしてICのガバナンスに参加し、提案に投票し、投票報酬を得ることができます。第二に、ICPをcycles に変換することができ、これは計算の燃料として使用されます。第三に、ICPトークンは、ノード・マシンを運用することで計算能力を提供する事業体への支払いに使用されます。最後に、ICPはIC上の分散型自律組織（DAO）のトークンスワップに参加するために使用できます。これら4つのユースケースについては、以下で詳しく説明します。前述したプラットフォームのユースケースに加え、ICPはNFTやサブスクリプションなどの商品やサービスの支払いなど、交換媒体としても使用できます。

### ガバナンスと投票報酬

neuron Neuron の保有者は、ICをどのように変更すべきかについての提案であるプロポーザルに投票することができます。 sの議決権は、ICPのステーク数に比例します。しかし、議決権は他のいくつかの の特性、特にトークンのステーク期間にも依存します。たとえば、 は、ステークされている期間を最大8年に設定することで、議決権を100%高めることができます。ステーク期間が長い s の議決権は増加し、ステークされた ICP の価値を長期的に最大化する意思決定を目的とした議案に投票するインセンティブが生まれます。neuron neuron neuron neuron

ガバナンスへの参加に対して、neuronは新たに発行される ICP に変換できる投票報酬を受け取ります。ICは毎日、スケジュールに従って投票報酬のポットを計算し、それを相対的な投票力に従って、資格のあるすべてのneuronsに分配します。投票報酬のスケジュールは、早期導入にインセンティブを与えるように設計されています：創設当初は、ICPの総供給量の10%が年率ベースで投票報酬として分配されます。8年間でこの数字は5%に減少します。

日々の報酬額はシステム内のICPの全体的なステーク量に依存しないため、参加者は全体的なステーク量とガバナンスへの参加が減少した場合、より大きな報酬を受け取ることができます。この仕組みにより、ICPをステークし、ガバナンスに参加する自然なインセンティブが生まれます。2022年11月現在、2億6,600万ICPがステークされており、これは総供給量の54%に相当します。ステークスされたICPの大部分、すなわち1億2,300万ICP（すなわち46％）は、ステークス者の長期的なコミットメントを表明する最長8年間ステークスされています。

Neuron自動的に投票するNeuronは、IC コミュニティが安全かつ迅速に意思決定に到達できるよう、他のneuronの投票に従って自動的に投票するよう設定できます。

以下のグラフは、2022年11月1日時点の年換算投票報酬をステーク時間の関数として表したものです。現在の年間投票報酬の見積もりについては、ICダッシュボードの[ガバナンス・ページを](https://dashboard.internetcomputer.org/governance)ご参照ください。

![](/img/how-it-works/voting_rewards.png)

### Cycles 計算燃料として

ICPはICの利用料金の支払いに使用できます。より具体的には、ICP トークンはcycles に変換（つまりバーン）することができ、これらのcycles は、canisters が使用するリソース（ストレージ、CPU、帯域幅）に対して、開発者が「canisters」と呼ばれるスマートコントラクトを IC にインストールするための支払いに使用されます。cycle 価格は不換紙幣のバスケットに固定されているため、ICPからcycle への変換レートはICPの市場価格によって変動します。したがって、開発者がアプリケーションを実行するための燃料を入手するコストは予測可能です。

ICの「逆ガスモデル」では、開発者はcanisters に計算cycles をロードすることでコストを前払いします。その結果、ユーザーはトークンで支払うことなく、分散型アプリケーション（dapp ）と対話することができます。cycles はコストが安定しているため、開発者は計算とストレージにいくら必要かを事前に知ることができます。

### ノードプロバイダへの報酬

ICP トークンはノードプロバイダー（IC をホストするコンピューティングノードを所有・運営する事業体）への支払いに使用されます。ノードプロバイダーへの報酬は新しく発行されたICPを通じて支払われ、各ノードごとに毎月計算されます。ノードあたりの金額は2つのパラメータに依存します：1つ目は、ホスティング価格が場所によって異なるため、ノードの場所です。2つ目は、ノードのタイプ、つまりハードウェアと接続の仕様です。

ノードの投資とランニングコストをカバーするため、ノードのプロバイダー報酬はXDRで指定され、過去30日間の平均為替レートに基づいてICPに換算されます。

### ICエコシステムへの投資

ICは、開発者が自分のdapps のコントロールを分散型自律組織（DAO）に移行し、資金を調達するためのプラグ＆プレイソリューションを提供します。

いわゆる分散化スワップの一環として、ユーザーはICPの一部を新しいDAOにコミットすることができます。その見返りとして、分散化スワップが完了すると、これらのユーザーはDAOのトークンを受け取り、全員が同じ価格を支払います。開発者はICPを集める期間と最小・最大の資金調達目標を指定することができ、それによって売却がいつ終わるかが決まります。

分散化スワップによって集められたICP資金は、dapp またはサービスのオリジナル開発者に転送されるのではなく、完全に自律化されたDAOの準備金内に保持されます。これらの資金は、dapp の将来の計算ニーズの支払いや、dapp の将来の機能拡張のためのコード報奨金の支払いに使用することができます。

DAOの分散スワップによる投資は、ICエコシステムのロケット燃料のような役割を果たします。それはエキサイティングなWeb3プロジェクトへの簡単で透明なアクセスを提供し、プラットフォームの生産的な使用への資金を流します。

### 総供給の発展

ICにはインフレとデフレのメカニズムがあります。ガバナンスの参加者は、投票報酬を新しく発行されたICPに変換できます。また、ノード提供者は新しく鋳造されたICPトークンの形で報酬を受け取ります。一方、ICPは計算とストレージの代金を支払うためにcycles （つまりバーン）に変換されます。これは以下の図に示されています。

![](/img/how-it-works/deflation_inflation.png)

<!---


# Tokenomics of the Internet Computer


### The ICP utility token

The Internet Computer (IC) makes use of a utility token called ICP. This token is used for the following functions on the platform: First, any ICP holder can participate in the governance of the IC by staking ICP in order to vote on proposals and earn voting rewards. Second, you can transform ICP into cycles, which are used as fuel for computation. Third, ICP tokens are used to pay the entities who provide compute capacity by operating node machines. Last but not least, ICP can be used in order to participate in token swaps of decentralized autonomous organizations (DAOs) on the IC. We will elaborate on these four use cases in the following. In addition to the aforementioned platform use cases, ICP can also be used as a medium of exchange, i.e., to pay for goods and services like NFTs, subscriptions, etc.

### Governance and voting rewards

Anyone can participate in the governance of the IC by staking ICP tokens in so-called neurons. Neuron holders can vote on proposals, which are suggestions on how the IC should be changed. The neurons’ voting power for decision making is proportional to the number of ICP staked inside. However, the voting power also depends on some other neuron characteristics, in particular, for how long tokens are staked. For example, a neuron can boost its voting power by 100% by setting the time staked to the maximum of 8 years. The increased voting power for neurons with longer staking time creates an incentive to vote on proposals with the aim of driving decisions that maximize the value of their staked ICP over the long term.

For participation in governance, neurons receive voting rewards which can be converted into newly minted ICP. Every day, the IC calculates a voting reward pot according to a schedule, which it then divides among all eligible neurons according to their relative voting power. The schedule for voting rewards is designed to incentivize early adoption: Initially at genesis, 10% of the total supply of ICP is distributed in voting rewards on an annualized basis. Over the course of eight years, this number falls to 5%.

As the daily reward amount is independent of the overall amount of staked ICP in the system, participants receive larger rewards if overall staking and participation in governance decreases. This mechanism creates a natural incentive to stake ICP and participate in governance. As of November 2022, 266M ICP is staked, corresponding to 54% of the total supply. A significant part of staked ICP, namely 123M ICP (i.e., 46%), is staked for the maximum time of 8 years expressing the long-term commitment of these stakers.

Neurons can be configured to vote automatically by following the votes of other neurons, in an advanced form of “liquid democracy.” Neurons that vote automatically still receive their full share of the voting reward, as they enable the IC community to reach decisions securely and quickly.

The following graph depicts annualized voting rewards as a function of the staking time as of November 1, 2022. For current estimates of annualized voting rewards, refer to the IC Dashboard’s [Governance page](https://dashboard.internetcomputer.org/governance).

![](/img/how-it-works/voting_rewards.png)

### Cycles as fuel for computation

ICP can be used to pay for the usage of the IC. More specifically, ICP tokens can be converted to cycles (i.e., burned), and these cycles are used by developers to pay for installing smart contracts, called “canisters” on the IC, for the resources that canisters use (storage, CPU, and bandwidth). The cycle price is pegged to a basket of fiat currencies, so the conversion rate ICP to cycle fluctuates with the market price of ICP. Hence the cost to developers of acquiring fuel to run their application is predictable.

In the "reverse gas model" of the IC, developers pre-pay costs by loading canisters with computation cycles. As a consequence, users can interact with a decentralized application (dapp) without having to pay in tokens. Since cycles are stable in cost, developers know in advance how much they will need to spend on computation & storage.

### Node provider rewards

ICP tokens are used to pay the node providers—these are the entities that own and operate the computing nodes that host the IC. Node provider rewards are paid via newly minted ICP and computed on a monthly basis for each node individually. The amount per node depends on two parameters: First, the location of the node, as hosting prices differ between locations. Second, the type of the node, i.e., the hardware and connectivity specifications.

To cover the investment & running cost of nodes, which occur in fiat currency terms, node provider rewards are specified in XDR, and are converted into ICP based on the average exchange rate over the last 30 days.

### Investing in the IC ecosystem

The IC provides a plug & play solution for developers to transfer control of their dapps over to a Decentralized Autonomous Organization (DAO) and raise funds.

As part of a so-called decentralization swap, users can commit some ICP to a new DAO. In return, when the decentralization swap is complete, these users will receive tokens of the DAO with everyone paying the same price. Developers can specify a time period and minimum & maximum funding target of ICP to be collected, which determines when the sale is over.

The ICP funds raised by the decentralization swap are retained within the reserves of the fully autonomous DAO, rather than being forwarded to the original developers of the dapp or service. These funds can be used to pay for future computation needs of the dapp and also to pay code bounties for future dapp enhancements.

Investments via decentralization swaps in DAOs act like rocket fuel for the IC ecosystem. It provides easy and transparent access to exciting Web3 projects and channels funds to productive usage of the platform.

### Development of total supply

The IC has inflationary and deflationary mechanisms. Governance participants can convert voting rewards to newly minted ICP. Also, node providers receive rewards in the form of newly minted ICP tokens. On the other hand, ICP is converted to cycles (i.e., burned) in order to pay for computation and storage. This is depicted in the following picture.

![](/img/how-it-works/deflation_inflation.png)

-->
