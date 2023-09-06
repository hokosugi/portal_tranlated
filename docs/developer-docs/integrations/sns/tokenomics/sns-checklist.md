---

sidebar_position: 2
---
# SNS準備チェックリスト

:::caution
本ガイドラインは、情報提供のみを目的としており、時間の経過とともに
適切に変更される可能性があります。
SNSの立ち上げを計画しているプロジェクトに一般的なガイダンスを提供することを目的としていますが、そのような立ち上げの準備として
、取るべき行動項目の決定的なリストとして解釈されるべきではありません。このガイドに従うことは、SNS
の立ち上げの成功を保証するものではありません。最終的には、このガイドに従うこと、および/または
SNSを立ち上げることは、自己責任で行ってください。
::：

:::info
NNSは、1つの提案だけでSNSを立ち上げることができる新しいフローを承認しました。
旧フローでは、2つのNNSと1つのSNSの提案が必要でした。
これが現在推奨されているSNSの立ち上げフローであるため、このページや他のページでは、新しい「1つの提案」のフローに焦点を当てています。
とはいえ、旧フローで開始したプロジェクトが、すでにテストを開始しているなどの理由で、この経路を継続したい場合は、旧レガシーフローもまだ利用可能です。このようなプロジェクトは、旧フローに従った詳細な立ち上げステージと、これらのステージを通過するために取るべき具体的なアクションを、[こちらで](../launching/launch-steps.md)ご覧いただけます。
::：

# 1.ドキュメンテーション/準備

## 1.1.トークノミクス仕様

### 1.1.1.トークン・ユーティリティ

DAO のトークンが、ガバナンスへの参加、DAO がガバナンスするdapp が提供するサービスへの積極的な参加への報酬、dapp と DAO の成長への貢献への報酬のために、どのようなユースケースで使われるかを簡潔に定義します。

### 1.1.2.初期トークン割り当て

最初のトークン割り当て、つまりどのグループ/アカウントがどれだけのトークンを受け取るべきかを定義するために、開発者は以下の主要なブロックを考慮することができます：DAO treasury、decentralization swap、seed funders、funding development team。トークンの割り当てに関する詳細は、[tokenomics intro](./tokenomics-intro.md) を参照してください。開発者とシードチームの権利確定期間に関する情報も含めてください。

### 1.1.3.議決権

設立時の議決権の分配方法、潜在的な攻撃ベクトル、および議決権が時間の経過とともにどのように変化するかについての情報を提供してください。

発生時にスワップ参加者が議決権の過半数を持つことがベストプラクティスと考えられています。もし開発者とシード投資家が一緒に過半数を持つのであれば、なぜこの2者が独立し ているのかを明確に示すべき。

### 1.1.4.資金調達目標

分散化スワップについて、最低・最大の資金調達目標が定義されていること。資金使途（チームの増強計画など）についての情報も追加してください。

### 1.1.5.SNSトークノミクスツール

[SNS tokenomics ツールを](https://docs.google.com/spreadsheets/u/0/d/1eSxkJl94jPt63CdOXH6ROy-WSkacW6P4qcAKMLrfBPc/edit)使用して、選択したトークンの量、トークンの初期分配、溶解遅延などを分析し、文書化します。ツールとトレーニングデッキは、[このウィキページに](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration)あります。

## 1.2.技術アーキテクチャ/ホワイトペーパー/プロジェクトロードマップ

### 1.2.1.技術アーキテクチャ

テクニカルアーキテクチャは、dapp の概要を示し、dapp がどのように機能するかを説明するものです。dapps の中には、チームが開発・保守するオフチェーンコンポーネントや、サードパーティコンポーネントに依存するものがあります。DAOによって制御されないコンポーネントの使用も、依存関係を示すために含まれるべきです。

### 1.2.2.ホワイトペーパー

ホワイトペーパーは、dapp についての情報、アーキテクチャ、SNS の立ち上げによるゴール、トークノミクス、分散化提案に投票するコミュニティに関連するその他の情報を提供する必要があります。dapp が SNS の立ち上げから何を得られるかを明確にする必要があります。ヒントとして、OpenChatのホワイトペーパーは[こちら](https://oc.app/whitepaper)、Hot or Notのホワイト[ペーパーは](https://hotornot.notion.site/hotornot/Hot-or-Not-Whitepaper-c539179e51f44867979f4372e4635f59)こちらをご覧ください。

### 1.2.3.プロジェクトのロードマップ

ロードマップは、dapp 、SNS立ち上げ後の計画を示すものです。ロードマップは、dapp の背後にいるチーム/開発者がdapp のために持っているビジョンと、彼らが SNS のローンチ後も取り組みたいことを示すべきです。[OpenChatのロードマップを](https://oc.app/roadmap)参考にしてください。

## 1.3.依存関係の開示

SNS 立ち上げ時にdapp を完全に分散化することは不可能かもしれません。dapp はオフチェーンサービスやSMS/テキストゲートウェイのようなサードパーティサービスプロバイダに依存している可能性があります。

もしdapp が DAO によって管理できない何らかの依存関係を持つのであれば、コミュニティが潜在的なリスクを認識できるように、それらの依存関係は文書化されるべきです。可能であれば、これらのサービスをどのようにオンチェーン化し、DAOの管理下に置くかの計画も必要です。

## 1.4.SNS設定ファイルの作成

SNSは、初期パラメータを定義するNNSプロポーザルを使用して初期化されます。したがって、初期設定はNNS DAOによって承認されます。
プロポーザルは.yaml設定ファイルから作成することができ、トークン名、トークン供給、トークン分配、取引手数料、ディゾルブ遅延などを定義します。

初期パラメータについては[こちらで](./preparation.md)詳しく説明しています。
定義が必要なすべてのパラメータを含むテンプレートは[こちらで](https://github.com/dfinity/ic/blob/master/rs/sns/cli/sns_init_template.yaml)、記入例は[こちらで](https://github.com/dfinity/sns-testing/blob/main/example_sns_init.yaml)ご覧いただけます。
透明性と読みやすさのために、パラメータとトークンの割り当てを説明するコメントを設定ファイルに追加してください。

restricted\_countries "パラメータは、次のような国コードの配列を取ります:`restricted_countries: [ "CH" ]` 。

NNSによって承認されたパラメータとして定義された確認テキストは、ユーザーが分散スワップに参加する前に表示することができます。この確認テキストは次のように設定できます：`confirmation_text: "Please confirm that..."`

NNSプロポーザルを提出する前に、ローカルで広範囲に設定をテストしてください。テストの詳細についてはセクション2を参照してください。

## 1.5.NNSプロポーザルの作成

<!-- OLD: As a part of the SNS launch process, two proposals must be created. The first proposal is for allowing a principle to create the SNS canisters, see Hot or Not’s [command](https://forum.dfinity.org/t/help-submit-the-sns-w-wallet-principal-id-to-be-whitelisted/20322/20) to create the first proposal.-->

SNSの立ち上げプロセスの一環として、SNSの作成と分散スワップの開始をNNSに依頼するために、NNSプロポーザルを作成する必要があります。プロポーザルの作成方法については、[こちらを](../launching/launch-summary-1proposal.md)ご覧ください。

トークノミクス（トークン配布、ガバナンス、分散スワップ）、dapp の詳細（オープンソース・コードへのリンク）、ホワイトペーパー、フォーラムでの議論へのリンク（ステップ 3.2 を参照）、その他コミュニティがdapp のために SNS を立ち上げるべきかどうかを判断するために関連するあらゆる情報を含めてください。

準備プロセスの詳細については[ドキュメントを](./preparation.md)、立ち上げの仕組みの詳細については[このドキュメントを](../launching/index.md)参照してください。
ドキュメントのページ「[SNS preeployment considerations](./predeployment-considerations.md)」には、ホワイトペーパー/提案書でカバーすべきトピックのリストがあります。DFINITYの投票については、この[フォーラムの投稿を](https://forum.dfinity.org/t/dfinitys-voting-on-upcoming-sns-launch-proposals/19543)参照してください。

# 2.技術的な準備とテスト

## 2.1.セキュリティレビュー

一般に、リスクのある発見事項の修正を含むセキュリティレビューを実施することは、ベストプラクティスと考えられています。セキュリティのベストプラクティスに関するガイダンスは[こちら](../../../security/index.md)。セキュリティレビューがどの程度dapp に関連しているのか、また、該当する場合には、dapp に対してどのようなセキュリティレビューが実施されているのかを説明する必要があります。

## 2.2.オープンソース

dapp がまだオープンソース化されていない場合は、SNS の立ち上げ前、実際には分散化提案が作成される前にオープンソース化されるべきです。ソースコードがコミュニティと共有されていなければ、dapp は真の分散型とは言えません。コードをオープンソース化することで、コミュニティはSNSのローンチ前、そしてアップグレードが投票によって決定されるローンチ後に、dapp を評価する機会を得ることができます。コードを完全に可視化することなく、有意義な投票決定を下すことは困難です。また、可視化しなければ、投票前にコードを検証し、それがもたらす影響を評価することもできません。

## 2.3.再現可能なビルドの作成

コードをオープンソース化するのと同じ理由で、オープンソース化されたコードから再現可能なビルドを作成することが可能であるべきです。ビルドとデプロイの手順を提供することで、コミュニティはSNSの立ち上げ提案に投票する前にdapp 、コードを評価することができます。また、アップグレードに投票する必要がある分散化後は、投票前にコミュニティによってアップグレードを検証し、テストすることができます。

ビルドとデプロイの手順をソースコードと一緒に提供します。理想的には、説明書はコードリポジトリのREADMEファイルの一部であり、そうでない場合は、説明書へのリンクをREADMEファイルで利用できるようにする必要があります。再現可能なビルドを作成するためには、ビルド環境が再現可能である必要があります。Dockerを使って再現可能なビルドを作成する方法については、[こちらの](../../../backend/reproducible-builds.md)ドキュメントを参照してください。

## 2.4.SNS Testflightを使用して、メインネット上のSNSでdapp 。

dapp 本番環境で SNS の起動を要求する前に、開発者はデプロイされたdapp’s の操作（例えば、dapp’s canisters のアップグレード）を SNS プロポーザル経由でテストすることを強くお勧めします。

SNSプロポーザルによるcanisters のアップグレード、SNSプロポーザルによるアセットcanister コンテンツの更新テスト、その他の典型的なアップグレードおよび保守運用を必ずテストしてください。また、[cycles の管理戦略を](../managing/cycles-usage.md)確立し、canisters が枯渇しないようにしましょう。cycles テスト期間は長ければ長いほどよく、理想的には数週間です。

開発者は、testflight SNS に登録されたdapp’s canisters を直接管理することができます。
testflight は、ローカルのテスト環境でも、メインネット上のライブdapp でも行うことができます。メインネットに展開する場合、testflight SNSは、専用のSNSサブネットではなく、通常のアプリケーションサブネットに展開されます。

メインネット上で SNS testflight を使用するには、`–network ic` パラメータを deploy コマンドに渡します。テストフライトを実行するための[ドキュメントは](../testing/testing-on-mainnet.md)、追加されたパラメータ（これもドキュメントで説明されています）を除いて、ローカルに展開する場合と同じです。

セットアップ手順など、[メインネット上でのSNS Testflightの](../testing/testing-on-mainnet.md)テストに関する詳細については、ドキュメントを参照してください。

## 2.5.SNSフロントエンドをdapp

開発者はdapp に SNS 機能のフロントエンドを統合することができます。これにより、neuronの参加者がdapp のフロントエンドで直接提案に投票できるようになります。統合は、SNSを立ち上げる前に、SNS TestflightまたはローカルのSNSテストで十分にテストしてください。

## 2.6.SNSの立ち上げをローカルでテスト

SNSを使用したすべてのdapp （2.4節で説明）操作の包括的なテストに加え、SNSのローンチプロセスのローカルテストを実施することをお勧めします。そうすることで、NNSフロントエンドdapp を介して、ユーザーの視点からもSNSの初期トークンスワッププロセスを完全にシミュレートすることができます。テスト用にSNSをローカルにセットアップする方法の詳細は、こちらをご覧ください[1](https://github.com/dfinity/sns-testing).

# 3.コミュニティ協議

## 3.1.トークノミクス／ホワイトペーパー／ロードマップ／アーキテクチャの公開

セクション1.1および1.2で作成された文書は、SNSのローンチに先駆けて公開されるべきです。これにより、dapp 、dapp の将来計画、技術アーキテクチャ、トークノミクスに関する透明性が提供されます。この情報は、dapp’s ウェブサイト、GitHub、または意味のある場所で共有することができます。

## 3.2.コミュニティでの議論

開発者は分散化計画についてコミュニティとフォーラムで議論することを強く推奨します。NNSの提案が作成される少なくとも2、3週間前にスレッドを立てることをお勧めします。そうすることで、コミュニティはあなたの計画について知り、質問し、SNSの立ち上げに対する信頼を築くことができます。十分な数の NNSneuronが提案に賛成票を投じない限り、SNS の初期化と分散化スワップは開始されません。

共有することをお勧めします：

- \[x\] 使用したinitファイル。[ここで](https://github.com/dfinity/ic/blob/master/rs/sns/cli/sns_init_template.yaml)テンプレートを見つけることができます。
- \[x\] 分散化とトークノミクスの完全な説明を含むホワイトペーパーを提供してください。
- \[x\]dapp アーキテクチャの技術的な分解をcanisters 、ソースコード、ドキュメントとして提供し、コミュニティがdapp がスワップ後に実際に分散化されたアプリケーションになることを検証できるようにします。
- \[x\]dapp について、どの程度までセキュリティレビューが関連すると考えられていたのか、また、dapp について、どのようなセキュリティレビューが実施されたのかを説明してください。

このアイデアは、コミュニティに情報を提供することで、コミュニティが分散化の観点からサポートしているものを検証できるようにすることです。

この[フォーラムの投稿は](https://forum.dfinity.org/t/dfinitys-voting-on-upcoming-sns-launch-proposals/19543)、DFINITY 、投票する際の観点からの最初の考えを示しています。

## 4.SNS立ち上げワークフロー

SNS 立ち上げに含まれるすべてのステージと、これらのステージを有効にするために必要なアクションのより詳細な[説明を](../launching/launch-summary-1proposal.md)ご覧ください[。](../launching/launch-steps-1proposal.md)

## 4.1.Dapp コントロール・ハンドオーバー

SNS立ち上げのためのNNS提案の提出とともに、dapp の開発者は、canister の追加コントローラとしてNNSルートdapp を設定することにより、dapp をNNSに引き渡します。

プリンシパルが変更されるため、引き渡しがdapp 全体に対してどのような影響を与えるかを検討します。例えば、資産canisters のアクセス権を変更する必要がありますか？SNS を立ち上げる前に、dapp のすべての機能を徹底的にテストする必要があります。SNS testflightによるテストについては、前述を参照してください。

## 4.2.分散化提案の提出

この提案書により、SNSの立ち上げが開始されます。手順1.4で作成した提案内容を用いて、NNS提案書を作成します。その方法は、[立ち上げステージに必要なアクションのドキュメントを](../launching/launch-steps-1proposal.md)参照してください。

## 4.3.カスタムSNS関数の設定

SNSプロポーザルを介してSNS管理canisters でコードを実行するには、canisters 、2つのパブリック関数（
ドキュメントではジェネリック関数とも呼ばれる）を公開する必要があります。最初の関数は、プロポーザルのペイロードを検証してレンダリングする検証関数（
）であり、2番目の関数は、プロポーザルのペイロードを指定してアクションを実行する実行関数（
）です。

ジェネリック関数を使用するには、まずSNSプロポーザルを提出し、これらの関数をSNSガバナンスに登録する必要があります。プロポーザルが採用されると、指定されたバイナリペイロードでそれらを実行するプロポーザルを提出することができます。
プロポーザルの詳細については、[SNSの管理方法のドキュメントに](../managing/making-proposals.md)記載されています。

<!---


# SNS preparation checklist

:::caution
This guide is intended for informational purposes only and may be modified over time as 
appropriate. It is aimed to provide general guidance for projects that are planning to launch 
an SNS but should not be construed as the definitive list of action items that need to be taken
in preparation of any such launch. Following this guide is not a guarantee of a successful SNS
launch – that is a decision by the NNS DAO. Ultimately, following this guide and/or launching
an SNS is done at your own risk.
:::

:::info
The NNS has approved a new flow for how SNSs can be launched that only require one proposal.
The old flow required two NNS and one SNS proposal.
Since this is now the recommended flow how to launch an SNS, this page and others focus on the new "one-proposal" flow.
Nevertheless, the old, legacy flow is still available for those projects who started with the old flow and want to continue with this path, for example because they already started testing. These project can find the detailed launch stages according to the old flow [here](../launching/launch-summary.md) and the concrete actions to take to run through these stages [here](../launching/launch-steps.md). 
:::

# 1. Documentation / Preparation

## 1.1. Tokenomics specification

### 1.1.1. Token utility
Define concisely for which use cases the token of the DAO will be used for participation in governance, rewarding active participation in services offered by the dapp that is governed by the DAO and rewarding contributions to the growth of the dapp and the DAO.

### 1.1.2. Initial token allocation
For the initial token allocation, i.e., defining which groups/accounts should receive how many tokens, developers could consider the following main blocks: DAO treasury, decentralization swap, seed funders and funding development team. See more information about token allocation in the [tokenomics intro](./tokenomics-intro.md). Include information about vesting periods for developers and the seed team.

### 1.1.3. Voting power
Provide information about how voting power is distributed at genesis, potential attack vectors and how the voting power might evolve over time.

It is considered to be best practice that swap participants have the majority of voting power at genesis. If the developers and seed investors have the majority together, then it should be clearly articulated why these two parties are independent.

### 1.1.4. Funding target
The minimum and maximum funding target must be defined for the decentralization swap. Add information about the planned usage of the funds, e.g. plans of ramping up the team.

### 1.1.5. SNS tokenomics tool
Use the [SNS tokenomics tool](https://docs.google.com/spreadsheets/u/0/d/1eSxkJl94jPt63CdOXH6ROy-WSkacW6P4qcAKMLrfBPc/edit) to analyze and document the chosen amount of tokens, initial distribution of tokens, dissolve delays etc. Both the tool and a training deck can be found in [this wiki page](https://wiki.internetcomputer.org/wiki/How-To:_SNS_tokenomics_configuration).

## 1.2. Technical architecture / whitepaper / project roadmap

### 1.2.1. Technical architecture
The technical architecture should give an overview of the dapp and illustrate how the dapp works. Some dapps rely on off-chain components, either developed and maintained by the team, or 3rd party components. The use of components that will not be controlled by the DAO should also be included to show the dependencies.

### 1.2.2. Whitepaper
The whitepaper should provide information about the dapp, the architecture, the goal with launching the SNS, tokenomics and other information relevant to the community to vote on the decentralization proposal. It should be clear what the dapp will gain from the SNS launch. For inspiration,  see OpenChat’s whitepaper it [here](https://oc.app/whitepaper), and Hot or Not’s whitepaper [here](https://hotornot.notion.site/hotornot/Hot-or-Not-Whitepaper-c539179e51f44867979f4372e4635f59).

### 1.2.3. Project roadmap
The roadmap should show what the plan is with the dapp, also past the SNS launch. The roadmap should show the vision the team/developers behind the dapp have for the dapp, and what they would like to work on, also after the SNS launch. See [OpenChat’s roadmap](https://oc.app/roadmap) for inspiration.

## 1.3. Disclosure of dependencies
It may not be possible to fully decentralize the dapp at the time of the SNS launch. The dapp may rely on off-chain services or 3rd party service providers like SMS/text gateways.

If the dapp does have dependencies of some kind that cannot be managed by the DAO, then those dependencies should be documented so the community is aware of potential risks. When possible, there should also be a plan for how to bring these services on-chain and under control of the DAO.

## 1.4. Create SNS configuration file
The SNS is initialized using an NNS proposal that defines the initial parameters, thus the initial configuration is approved by the NNS DAO.
The proposal can be created from a .yaml configuration file and defines things like the token name, token supply, token distribution, transaction fees, dissolve delays and more. 

The initial parameters are explained more [here](./preparation.md).
You can find a template with all parameters that need to be defined [here](https://github.com/dfinity/ic/blob/master/rs/sns/cli/sns_init_template.yaml) and an example of a filled in file [here](https://github.com/dfinity/sns-testing/blob/main/example_sns_init.yaml).
Add comments, explaining the parameters and token allocation, in the configuration file for transparency and ease of reading.

Geo-restriction can be used to exclude users with IPs in specific countries with the “restricted_countries” parameter, which takes an array of country codes like this: `restricted_countries: [ "CH" ]`. 

A confirmation text, defined as a parameter that is approved by the NNS, can be shown before the user can participate in the decentralization swap. This confirmation text can be set like this: `confirmation_text: "Please confirm that..."` 

Test the configuration extensively locally before submitting the NNS proposal. See section 2 for more information about testing.

## 1.5. Create NNS proposals
<!-- OLD: As a part of the SNS launch process, two proposals must be created. The first proposal is for allowing a principle to create the SNS canisters, see Hot or Not’s [command](https://forum.dfinity.org/t/help-submit-the-sns-w-wallet-principal-id-to-be-whitelisted/20322/20) to create the first proposal.-!->

 As a part of the SNS launch process, an NNS proposal must be created to ask the NNS to create an SNS and start a decentralization swap. You can find the details about how to create such a proposal [here](../launching/launch-summary-1proposal.md).

Include relevant information like tokenomics (token distribution, governance, decentralization swap), details about the dapp (link to the open sourced code), whitepaper, a link to the forum discussion (see Step 3.2), and anything else relevant for the community to make a decisions if an SNS should be launched for the dapp. 

For details about the preparation process, see the [documentation](./preparation.md) and for details about how the launch works, see [this documentation](../launching/index.md). 
The documentation page [SNS predeployment considerations](./predeployment-considerations.md) has a list of topics that should be covered in the whitepaper/proposal. See this [forum post](https://forum.dfinity.org/t/dfinitys-voting-on-upcoming-sns-launch-proposals/19543) for information about DFINITY’s voting.

# 2. Technical Prep & Testing

## 2.1. Security review
In general, it is considered best practice to conduct a security review including the fixing of risky findings. Guidance on security best practices is [here](../../../security/index.md). It should be explained to which extent security reviews are relevant for the dapp and what kind of security reviews have been conducted for the dapp if applicable.

## 2.2. Open Sourcing
If the dapp is not already open sourced, it should be open sourced before the SNS launch - actually before the decentralization proposals are created. A dapp is not truly decentralized if the source code is not shared with the community. Open sourcing the code gives the community an opportunity to evaluate the dapp before the SNS launch, and after the launch, where upgrades must be voted on. It’s hard to make a meaningful voting decision without having full visibility into the code, and without the visibility it will also not be possible to verify the code and assess the impact it will have, before voting.

## 2.3. Create reproducible build
It should be possible to create a reproducible build from the open sourced code, for the same reasons as open sourcing the code. Providing the build and deploy instructions enables the community to evaluate the dapp and the code before voting on the SNS launch proposal, and after the decentralization, where upgrades must be voted on, the upgrades can be verified and tested by the community before voting.

Provide the build and deploy instructions with the source code. Ideally the instructions are a part of the code repository README file, and if that’s not the case, a link to the instructions should be available in the README file. In order to be able to create a reproducible build, the build environment needs to be reproducible. The documentation [here](../../../backend/reproducible-builds.md) provides more information how reproducible builds can be created using Docker.

## 2.4. Test dapp operations under SNS on mainnet with SNS Testflight
Before requesting an SNS launch in production, developers are strongly encouraged to test their deployed dapp’s operation (e.g., upgrading the dapp’s canisters) via SNS proposals, as if the live version of the dapp was managed by SNS.

Make sure to test upgrading canisters through SNS proposals, test updating asset canister content through SNS proposals, and other typical upgrade and maintenance operations. Also establish a [cycles management strategy](../managing/cycles-usage.md), so canisters never run out of cycles. The longer the test runs, the better, ideally several weeks.

The developer can keep direct control over the dapp’s canisters registered with testflight SNS.
The testflight can be done in a local test environment or with the live dapp on the mainnet. When deployed on the mainnet, the testflight SNS is deployed to a regular application subnet instead of a dedicated SNS subnet.

To use the SNS testflight on the mainnet, pass the `–network ic` parameter to the deploy command. The [documentation](../testing/testing-on-mainnet.md) for running the testflight is the same as for deploying it locally - except for the added parameter (which is also covered in the documentation).

See the documentation for more information about testing [SNS Testflight on mainnet](../testing/testing-on-mainnet.md), including setup instructions.

## 2.5. Integrate an SNS frontend into the dapp
Developers can choose to integrate a frontend for the SNS functionality in the dapp. A good example of a useful integration is SNS proposal voting. This allows neurons to vote on proposals directly in the dapp frontend. Integrations should be tested thoroughly with the SNS Testflight or the local SNS test before the SNS launch.

## 2.6. Test the SNS launch locally
In addition to performing comprehensive testing of all dapp operations using the SNS (as explained in section 2.4), it is recommended to conduct a local test of the SNS launch process. By doing so, you can simulate the complete SNS initial token swap process also from the user’s perspective via the NNS frontend dapp. Detailed instructions on how to set up SNS locally for testing are available [here 1](https://github.com/dfinity/sns-testing).

# 3. Community Consultation

## 3.1. Publish tokenomics / whitepaper / roadmap / architecture
The documentation prepared in section 1.1 and 1.2 should be made publicly available ahead of the SNS launch. This provides transparency about the dapp, future plans with the dapp, the technical architecture and the tokenomics. This information can be shared on the dapp’s website, GitHub or where it would make sense.

## 3.2. Community discussion
It’s strongly recommended that developers have a discussion in the forum with the community about the decentralization plans. It’s suggested to start a thread at least a couple of weeks before the NNS proposal is created. This will allow the community to learn about your plans, ask questions and build trust in the SNS launch. The SNS initialization and decentralization swap will not start unless enough NNS neurons vote in favor of the proposal.

It is recommended to share:

- [x] The init file that you used. You can find the template [here](https://github.com/dfinity/ic/blob/master/rs/sns/cli/sns_init_template.yaml).
- [x] Provide whitepaper with a full description of the decentralization and tokenomics.
- [x] Provide a technical decomposition of the dapp architecture in terms of canisters, source code and documentation so that the community can validate that the dapp will actually be a decentralized application after the swap.
- [x] Explain to which extent security reviews were considered relevant for the dapp and what kind of security reviews have been conducted for the dapp.

The idea is to provide the community with information so they can verify what they are supporting from a decentralization standpoint.

This [forum post](https://forum.dfinity.org/t/dfinitys-voting-on-upcoming-sns-launch-proposals/19543) provides some initial thoughts from the perspective of DFINITY when voting. 

## 4. SNS Launch Workflow
Please find all stages included in an SNS launch [here](../launching/launch-summary-1proposal.md) and a more detailed descriptions of the actions needed to enable these stages [here](../launching/launch-steps-1proposal.md).

## 4.1. Dapp control handover
Together with the submission of the NNS proposal to launch an SNS, the dapp developers hand over their dapp to the NNS by setting the NNS root canister as an additional controller of the dapp. 

Consider what the impact of the handover will have for the entire dapp, since principals will change. Is it necessary to change access rights to e.g. asset canisters? All features of the dapp should be thoroughly tested before the SNS launch. See previous mention of testing with the SNS testflight.

## 4.2. Submit decentralization proposal
This proposal will initiate the SNS launch. Use the proposal content created in Step 1.4 and create the NNS proposal. You can learn how to do so in the [documentation about the required actions to go through the launch stages](../launching/launch-steps-1proposal.md).

## 4.3. Setup custom SNS functions
To execute code on SNS managed canisters via SNS proposals, the canisters must expose two public functions,
also referred to as generic functions in the documentation. The first function is a validation function 
to validate and render the proposal payload, and the second function is an execution function to perform
an action given the proposal payload.

To use generic functions, you must first submit an SNS proposal to register these functions with SNS governance. Once the proposal is adopted, you can submit proposals to execute them with a given binary payload.
You can find more details on proposals in the [documentation how to manage SNSs](../managing/making-proposals.md).

-->
