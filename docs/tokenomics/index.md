---
 
hide_table_of_contents: true
---
# DAOとトークノミクス

canister スマートコントラクト開発を[始める](/tutorials/index.mdx)方法、\[進捗\]/developer-docs/build/index.md)、\[デプロイ\]/developer-docs/production/index.md)、アプリの更新と管理方法。cycles 、計算におけるその使い方、そしてICの\[様々な機能の統合\]/developer-docs/integrations/index.md)についての発言もありました。しかし、なぜかまだドキュメントに何かが欠けているように感じました。IC上での構築はコードだけではありません。

分散型アプリケーションは本質的に、新しい考え方やコーディングの方法だけでなく、相互作用、制御、資金調達、計画の方法にも心を開く必要があります。このドキュメントのカテゴリーは、よりオープンなインターネットへの移行において生じるであろう、より*起業家的な*考察を捉えることを目的としています。dappsこれは、開発者、構築者、創設者だけでなく、dapps の利用者によってもなされるべき考慮事項を記すことを通して行われます。

DAOドキュメントへようこそ。もし、ここに特に見たいものがあれば、オープンですので、遠慮なく投稿してください。

## Network Nervous System (NNS)

NNSはICをコントロールするオープントークナイズされたDAOです。分散化が進むにつれ、コミュニティ・ガバナンスを促進するICの構成要素について学ぶことは非常に重要です。NNSは、ユーザーがトークンをステークし、投票する（または投票を委任する - リキッド民主主義ftw）ことによってICのガバナンスに参加し、時間の経過とともに報酬を得ることを可能にします。

- NNSの詳細については、[こちらを](./nns/nns-intro.md)ご覧ください。
- 提案、投票、[報酬については](./nns/nns-staking-voting-rewards.md)、ダッシュボードの[ガバナンスセクションを](https://dashboard.internetcomputer.org/governance)ご覧ください。
- neurons' fundについては[こちらを](./nns/neurons-fund.md)ご覧ください。
- NNSアプリへの参加は、[NNSイントロダクションを](token-holders/nns-app-quickstart.md)ご覧ください。

## サービス・ナーバス・システム（SNS）

NNSがICであるように、SNSはIC上で動作するサービスです。dapp のガバナンスをトークン化し、分散化したい開発者や創設者は、ここから始めましょう。サービスやdapp のガバナンスは、SNS を取得することで分散化された方法で運用することができます。

- [SNSとDAOに飛び込みましょう](/developer-docs/integrations/sns/tokenomics/index.md)
- [DAOのトークノミクスを](/developer-docs/integrations/sns/tokenomics/tokenomics-intro.md)見る
- SNS を取得する場合、[考慮すべき](/developer-docs/integrations/sns/tokenomics//predeployment-considerations.md)数多くの事項について考えてみましょう。

## アイデンティティと認証

Internet Computer 上でアプリを構築する利点の 1 つは、ユーザーがトークンを保持する必要なく対話および認証できることです。これは、Internet Identityによって容易になります。

- [インターネットアイデンティティとは](https://internetcomputer.org/internet-identity)
- [Internet Identityの始め方](https://internetidentity.zendesk.com/hc/en-us/articles/15430677359124-How-Do-I-Create-an-Internet-Identity-on-My-Mobile-Device-)

## トークン保有者

トークンを取得するには、取引所経由で購入する、ノードプロバイダとして稼ぐ、友人から受け取る、またはその他の方法があります。トークンを保有する方法の選択肢と、自己管理を設定する方法について説明します。クイックスタートに従ってトークンを管理してください。

- [カストディのオプション](token-holders/custody-options-intro.md)
- [自己カストディのクイックスタート](token-holders/self-custody-quickstart.md)

<!---


# DAOs and Tokenomics

When building our documentation we tried to include the natural guides for developers; how to [get started](/tutorials/index.mdx) with canister smart contract development, how to [make progress]/developer-docs/build/index.md), how to [deploy]/developer-docs/production/index.md), update and manage apps. There were some remarks about cycles and their use in computation and about [integrating various features]/developer-docs/integrations/index.md) of the IC. Yet, somehow it still felt like something was lacking in the documentation. Building on the IC is about more than just the code. 

Decentralized applications inherently require us to open our minds to new ways of thinking and coding, but also of interacting, controlling, funding and planning. This category of documentation aims to capture the more *entrepreneurial* considerations that will likely arise in a move towards a more open internet. This is done both via noting considerations that should be made by developers, builders and founders, but also by users of dapps, as, after all, in the read-write-own model of web 3, users play a key role in the development of dapps. 

Welcome to our DAO docs. If there's anything in particular you'd like to see here, it's open, feel free to contribute.

## Network Nervous System (NNS)
The NNS is the open tokenized DAO that controls the IC. As we move to more and more decentralization, it is crucial to learn more about the IC components that facilitate community governance. The NNS allows users to participate the governance of the IC by staking tokens and voting (or delegating votes - liquid democracy ftw) and to earn rewards over time. 
- A more detailed introduction to the NNS can be [found here](./nns/nns-intro.md)
- Proposals, voting and rewards are [explained here](./nns/nns-staking-voting-rewards.md) can be explored in the [Governance section](https://dashboard.internetcomputer.org/governance) on the dashboard
- You can read about the neurons' fund [here](./nns/neurons-fund.md)
- Start participating in the NNS app by checking out the [NNS Intro](token-holders/nns-app-quickstart.md)

## Service Nervous System (SNS)
As the NNS is to the IC, an SNS is to services running on the IC. If you are a developer or founder who would like to tokenize and decentralize the governance of your dapp, this is the place to start. Governance of a service or dapp can operate in a decentralized manner by getting an SNS. 
- [Dive into the SNS and DAOs](/developer-docs/integrations/sns/tokenomics/index.md)
- See the [tokenomics of a DAO](/developer-docs/integrations/sns/tokenomics/tokenomics-intro.md)
- Think about the numerous [considerations](/developer-docs/integrations/sns/tokenomics//predeployment-considerations.md) that should be made if you plan to get an SNS


## Identity & Authentication
One of the advantages of building apps on the Internet Computer is that users can interact and authenticate without the need for holding tokens. This is facilitated by Internet Identity. 

- [What is Internet Identity](https://internetcomputer.org/internet-identity)
- [How to get started with Internet Identity](https://internetidentity.zendesk.com/hc/en-us/articles/15430677359124-How-Do-I-Create-an-Internet-Identity-on-My-Mobile-Device-)

## Token Holders
There are multiple ways to obtain token; purchasing via an exchange, earning as a node provider, receiving from a friend, or otherwise. Learn about some options for how to hold tokens, and how you can set up self-custody. Follow the quickstart to take control of your tokens. 
- [Custody Options](token-holders/custody-options-intro.md)
- [Self Custody Quickstart](token-holders/self-custody-quickstart.md)
-->
