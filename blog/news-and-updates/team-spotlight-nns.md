---

title: Team spotlight - Network Nervous System
description: For the first installment of this series, we sat down with the Network Nervous System team to learn more about the NNS, the SNS initiative, and the current goals the team is focused on.
tags: [Team spotlight]
image: /img/blog/dev-update-blog-nns-1.jpeg
---
![NNS Team spotlight](../../static/img/blog/dev-update-blog-nns-1.jpeg)

開発者の皆さん、こんにちは。新しいブログ記事シリーズ、チーム・スポットライトへようこそ！

2週間に1度、DFINITY チームの1つを紹介し、彼らが取り組んでいることを知り、そのチームが IC のエコシステムにどのようにフィットしているかを深く掘り下げていきます。この新しいシリーズを通して、開発者はSDKとMotoko チーム以外のチームの最新情報と洞察を得ることができます。SDKと チームは、このブログの「Developer weekly update」シリーズで定期的に取り上げています。

このシリーズの第1回目として、Network Nervous System チームと対談し、NNS、SNS イニシアチブ、チームが現在注力している目標について詳しく伺いました。

**まずはじめに、NNSチームの焦点と目的についてお聞かせください。NNSチームの使命は何でしょうか？**

*NNSはNetwork Nervous System の略です。NNSはDAO（分散型自律組織）で、Internet Computer Protocol 。*例えば、*ノードがどのようにサブネットに割り当てられるか、Internet Computer protocol がどのように新しいバージョンに更新されるかを調整します。*
*ですから、その名前が示すように、NNSチームの主な任務は、NNS DAOの設計、実装、メンテナンスに貢献*することです。*これに加えてNNSチームは、ICプラットフォームが提供するDAOフレームワークであり、個々のアプリケーションがガバナンスを分散できるようにするService Nervous System (SNS)の設計と実装の主要な貢献者です。*

**NNSチームは、管理・貢献する責任が非常に重く、エキサイティングなチームです！NNSチームの構成は？**

*スイスと米国にまたがる6人のエンジニア、1人のエンジニアリング・マネージャー、2人の研究者で構成されています。*

**NNSチームが最も力を入れているICの製品や機能は何ですか？**

*NNSチームが取り組んでいる主な製品は2つあります：*

*- NNS DAO*

*- SNS DAOとSNSを立ち上げるためのSNS launchpadからなるSNS DAOフレームワークです。*

**NNS DAOの概要はすでにお聞きしましたので、SNSの取り組みについてもう少しお話ししましょう。SNSの取り組みについて、NNSチームが担当していることを簡単に教えてください。**

*NNSチームはSNSイニシアチブの主要な貢献者の1つです。私たちはSNSスワップ（canister ）の構築を支援し、その維持に貢献しています。このスワップは初期資金を集め、立ち上げ時にSNSを分散化する役割を担っています。また、私たちはメンテナンスの主要責任チームでもあります：*

*- SNS Governancecanister DAOの意思決定を担当。*

*- SNS Rootcanister は、他のcanisters のアップグレードを担当します。*

*- SNS Wasm modulescanister" と呼ばれる NNScanister は、デプロイを担当し、SNScanisters のアップグレードに関与します。*

**NNS DAOは、SNSイニシアティブや新しいSNSの立ち上げに果たす役割はありますか？**

*NNS DAOは、新しいSNSの立ち上げを承認し、開始します。つまり、誰かが新しいSNSにdapp’s 、このプロセスを承認する必要があります。このプロセスの詳細は[こちらを](https://internetcomputer.org/docs/current/developer-docs/integrations/sns/launching/launch-summary)ご覧ください。*

**チームのロードマップについて話しましょう。NNSチームが現在取り組んでいる、あるいは注力している主要プロジェクトは何ですか？**

*私たちは通常、異なるプロジェクトに並行して取り組んでいます（NNSとSNSの機能を組み合わせて）。現在取り組んでいるプロジェクトの1つは、SNSの立ち上げプロセスをより使いやすくすることです。現在、SNSを立ち上げるには、2つのNNSの提案と、新しいSNSに彼らのdapp を引き渡す開発者チームからのかなりの手作業が必要です。*

*その名が示すように、「1プロポーザル」機能が実装されれば、この全プロセスは1つのNNSプロポーザルで行われます。つまり、SNSを立ち上げるべきかどうかをICコミュニティが決定するNNS提案が1つあり、dapp 、この提案が採用されれば、SNSの完全な立ち上げが自動的にトリガーされ、実行されるということです。*

**SNSの立ち上げプロセスが劇的に改善されそうですね！この目標を達成するために、どのようなコンポーネントが開発されているのでしょうか？**

*このプロセスを変更するには、新しい提案を導入するための NNS ガバナンスや SNScanisters の展開を調整する SNS Wasm モジュールcanister など、複数のコンポーネントを変更する必要があります。*

**このプロジェクトの利点について簡単に触れましたが、「1プロポーザル」機能を導入することで追加的な利点はありますか？**

* dapp を SNS に渡したい開発者にとっては、SNS 立ち上げのフローが飛躍的に簡素化されます。さらに、SNSを立ち上げるべきかどうかを判断するために、1つのSNS提案を理解し検証するだけでよいNNSコミュニティにとっても、プロセスが簡素化されます。*

**現在、NNSチームが計画している主なロードマップは他にありますか？**

*私たちが活動している環境は少しダイナミックなので、具体的な機能について話すのは難しいです。代わりに、私たちが持っているハイレベルな目標をご紹介します：*

*- NNS側では、ガバナンスへの参加、新しいノードのオンボーディング、Internet Computer の成長に重点を置いています。*

*- SNSについては、SNSを立ち上げる際のプロジェクト体験の改善、SNSが立ち上げられた後のDAOメンバーの体験の改善、10倍のプロジェクトをサポートするためのフレームワークの拡張に重点を置いています。*

**NNSチームの最新情報を知りたい開発者は、どこを見ればいいのでしょうか？**

*NNS と SNScanisters の両方に提出された提案について、公式DFINITY フォーラムで定期的に最新情報を公開しています。このフォーラムでは、今後のAPIの変更や私たちが取り組んでいる新機能の最新情報を得ることができます。[以下は](https://forum.dfinity.org/t/nns-updates-june-12-2023/20670)フォーラムの投稿例です。*

*フォーラムでは、機能のアイデアやデザインについてコミュニティと議論しています。私たちのすべての主要な機能は、フォーラムの投稿とディスカッションから始まります。[以下は](https://forum.dfinity.org/t/enhancement-of-the-sns-launch-process-one-proposal/19548)、前述の1-プロポーザル機能のフォーラム投稿です。*

**NNS DAOまたはSNSイニシアチブの機能または側面で、チームが最も誇りに思っているものは何ですか？**

*NNSは現存するDAOの中で最も進化したものの1つです。他のDAOと比較した大きな利点は、プロポーザルの実行が完全にチェーン上で行われることと、プロポーザルがどのcanister 、どのメソッドでもトリガーできることです。これにより、コミュニティ全体で意思決定が行われ、完全に自動的に実行される可能性が無限に広がります！*

*NNS DAOは数百の提案で1年以上稼働しています！*

*私たちが構築を支援したSNSフレームワークによって、すでに2つのプロジェクトがSNS DAOを立ち上げることができました。これはとてもエキサイティングなことで、他のプロジェクトがこれに続くか楽しみです！*

**これは信じられないことで、間違いなく非常に誇らしいことです！スポットライト・インタビューの最後に、もう一つ質問があります。DFINITY の他の開発チームと比べて、NNSチームの特徴は何ですか？**

*私たちはかなり "新しい "チームです。多くのチームメンバーが昨年加入しました。私たちのチームのエンジニアは、アメリカとスイスにかなり均等に散らばっています。*

NNSチームの皆さん、私たちと一緒にNNSのあらゆることに飛び込んでいただき、本当にありがとうございました！私たちと同じように多くのことを学んでいただけたと思います。今月末には、また別のチーム・スポットライトのブログ記事を掲載しますので、ぜひご覧ください！

\-DFINITY

<!---


![NNS Team spotlight](../../static/img/blog/dev-update-blog-nns-1.jpeg)

Hello devs and welcome to a new blog post series: team spotlight! 

Every two weeks, we're going to be showcasing one of the DFINITY teams to learn what they're working on and dive deeper into how the team fits into the IC ecosystem. Through this new series, developers will get updates and insight into teams other than the SDK and Motoko teams, which are regularly covered on this blog through the 'Developer weekly update' series. 

For the first installment of this series, we sat down with the Network Nervous System team to learn more about the NNS, the SNS initiative, and the current goals the team is focused on.

**To get started, let's first dive into the focus and purpose of the NNS team. What would you say the mission of the NNS team is?**

*NNS stands for Network Nervous System. The NNS is the DAO (decentralized autonomous organization) that governs and coordinates the full Internet Computer Protocol. It coordinates, for example, how nodes are assigned to subnets and how the Internet Computer protocol is updated to new versions.*
*So, as the name suggests, the main mission of the NNS team is to contribute to the design, implementation, and maintenance of the NNS DAO. In addition to this, the NNS team is a key contributor to the design and implementation of the Service Nervous System (SNS), which is a DAO framework provided by the IC platform that allows individual applications to decentralize their governance.*

**The NNS team has quite the responsibility to manage and contribute to, what an exciting team to be a part of! What is the composition of the NNS team?**

*The team consists of 6 engineers, 1 engineering manager, and 2 researchers spread across Switzerland and USA.*

**What products or features on the IC are the primary focus of the NNS team?**

*There are two main products that the NNS team works on:*

*- The NNS DAO*

*- The SNS DAO framework, which consists of the SNS DAO and the SNS launchpad which is used to launch an SNS.*


**Since we've gotten an overview of the NNS DAO already, so let's talk a bit more about the SNS initiative. Could you give a brief overview of what the NNS team is responsible for in regards to the SNS initiative?**

*The NNS team is one of the main contributors of the SNS initiative. We helped build and contribute to maintaining the SNS Swap canister which is responsible for collecting initial funding and decentralizing the SNS during the launch. We are also the main responsible team for maintaining:*

*- The SNS Governance canister, responsible for making the decisions in the DAO.*

*- The SNS Root canister, which is responsible for upgrading other canisters.*

*- The NNS canister called “SNS Wasm modules canister” that is responsible for deploying and involved in upgrading the SNS canisters.*


**Does the NNS DAO have a role that it plays in the SNS initiative and the launching of new SNSs?**

*The NNS DAO approves and initiates the launch of new SNSs. This means that if someone wants to hand over a dapp’s control to a new SNS, then this process needs approval from the NNS community. You can read more details about this process [here](https://internetcomputer.org/docs/current/developer-docs/integrations/sns/launching/launch-summary).*

**Let's talk about the team's roadmap. What is the primary project that the NNS team is working on or focused on currently?**

*We usually work on different projects in parallel (combining NNS and SNS features). One project that we currently work on is making the SNS launch process more user friendly. At the moment, launching an SNS includes 2 NNS proposals and quite some manual steps from the developer team that is handing over their dapp to a new SNS.*

*As the name suggests, once the  “1-proposal” feature is implemented, this full process will be done in one single NNS proposal. This means that there is one NNS proposal where the IC community decides whether an SNS should be launched for a given dapp and if this proposal is adopted the full SNS launch is triggered and executed automatically.*

**That sounds like it will drastically improve the SNS launch process! Could you give some insight into what pieces or components are being developed to achieve this goal?**

*Changing this process requires changing multiple components, including the NNS governance to introduce the new proposal and the SNS Wasm modules canister which coordinates the deployment of the SNS canisters.*

**We briefly touched on the benefits of this project, but are there any additional benefits to introducing the "1-proposal" feature?**

*It will tremendously simplify the SNS launch flow for developers who want to hand over their dapp to an SNS. Moreover, it will simplify the process for the NNS community who now only have to understand and verify one NNS proposal to make a decision for whether an SNS should be launched.*

**Are there any other major roadmap items currently planned for the NNS team?** 

*It’s hard to talk about specific features as the environment we operate in is a bit dynamic. Instead, here are the high level goals we have:*

*- On the NNS side, our focus is on increasing governance participation, new node onboarding, and growth of the Internet Computer.*

*- For SNS, the focus is on improving the project experience when launching an SNS, improving the DAO member experience once an SNS is launched, and extending the framework to support 10x projects.*

**For developers who want to stay up to date with the latest NNS team updates, where should they be looking?** 

*We publish regular updates for proposals submitted for both the NNS and the SNS canisters on the official DFINITY forum. There users can stay up to date with upcoming API changes and new functionality that we are working on. [Here](https://forum.dfinity.org/t/nns-updates-june-12-2023/20670) is an example forum post.*

*We use the forum to discuss feature ideas and designs with the community. All our major features start with a forum post followed by a discussion. [Here](https://forum.dfinity.org/t/enhancement-of-the-sns-launch-process-one-proposal/19548) is the forum post for the 1-proposal feature mentioned above.*

**What are some features or aspects of the NNS DAO or the SNS initiative that the team is the most proud of?**

*The NNS is one of the most evolved DAOs that exists. A major advantage that it has compared to other DAOs is that the execution of proposals happens fully on chain and that a proposal can trigger any method on any canister - this allows for endless possibilities regarding which decisions can now be made by a whole community and executed fully automatically!*

*The NNS DAO has been running for more than a year with hundreds of proposals!*

*The SNS framework that we helped build has already allowed 2 projects to launch an SNS DAO. This is pretty exciting and we are looking forward to seeing what other projects will follow!*

**That's incredible, definitely something to be extremely proud of! To close out our spotlight interview, we've got one more question. What makes the NNS team unique compared to some of the other dev teams at DFINITY?**

*We are a pretty “new” team - many team members joined in the last year. The engineers in our team are spread pretty evenly between the US and CH.*

Thank you so much to the NNS team for sitting down with us and diving into all things NNS! We hope that you learned as much as we did. Be sure to check in later in the month for another team spotlight blog post! 

-DFINITY

-->
