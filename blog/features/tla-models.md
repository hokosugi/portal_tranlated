---

title: The Internet Computer - Weed Out the Bugs with TLA+ Models
description: Today, DFINITY is open-sourcing all of its TLA+ models. To top it off, the Foundation is also publishing a comprehensive technical tutorial to help devs apply TLA+ to canister smart contracts.
tags: [Technology]
author: Ognjen Maric
image: /img/blog/tla-models.webp
---
今年初め、私たちはInternet Computer 上のcanister スマートコントラクトに TLA+ を適用することの利点を強調しました。 TLDR: (1) 即時の金銭的損失の可能性があるため、スマートコントラクトのセキュリティと正しさが重要であること、(2) TLA+ のような形式的手法は一般的にシステムの正しさとセキュリティを向上させること、(3) TLA+ はcanisters からリエントランシーバグと呼ばれる非常に頻繁に発生するバグを取り除くのに特に優れていること。

いくつかのクリーンアップ作業の後、私たちはTLA+モデルのすべてをオープンソース化したことを発表できることを嬉しく思います。以下のcanisters のモデルが GitHub にあります：

- NNS および SNS ガバナンス（台帳との相互作用に注目canister ）。

- ICP台帳（ブロックアーカイブに注目）

- ckBTCマイナー

- SNSスワップcanister

私たちは、これらのモデルが実例となり、あなた自身のcanister モデルを作成するのに役立つことを願っています。さらに、このプロセスをガイドするための詳細な技術チュートリアルも共有しています。このチュートリアルでは、canisters の特殊性を TLA+ でどのようにモデル化するかという一般的な戦略を提供しています。

TLA+は一般的なモデリング言語であり、canisters に特化したものではないため、私たちはInternet Computer スタックの基礎となる部分にも適用しました。たとえば、TLA+は、Internet Computer が Internet Identitycanister をスムーズに移行できるように支援しました。TLA+は、Internet Identityクライアントcanisters がきれいにアップグレードできない可能性がある、移行設計の初期バージョンのコーナーケースを最初に検出し、その後修正の検証を支援しました。リポジトリには以下のモデルも含まれています：

- 関係者用の接続確立サブプロトコルdapp 。

- Internet Computer コンセンサスアルゴリズムの安全特性。

- インターネットアイデンティティ移行手順

- サブネット分割手順。

以上です！これらのリソースがお役に立てば幸いです。コミュニティが作成したcanisters やdapps の新しい TLA+ モデルを見るのを楽しみにしています。もし作成した場合は、開発者フォーラムで広く ICP コミュニティに知らせるようにしてください。次は、TLA+モデルを実際のRustコードにリンクするためのツールの開発にまもなく着手します。もちろん、これらのツールも生産準備が整い次第オープンソース化しますので、ご期待ください！

TLA+モデルのオープンソースコードは[こちら](https://github.com/dfinity/tla-models)。

技術チュートリアルは[こちら](https://mynosefroze.com/blog/2023-08-09-tla_for_canisters)。

Internet Computer についてもっと知る : internetcomputer.org
ツイッターで技術者をフォローしてください：@DFINITYDev

Internet Computer でビルドを開始 :[Developer Docs](https://internetcomputer.org/docs/current/home).

<!---



Earlier this year, we highlighted the benefits of applying TLA+ to canister smart contracts on the Internet Computer. The TLDR: (1) the potential for immediate monetary loss makes security and correctness critical for smart contracts, (2) formal methods such as TLA+ increase systems’ correctness and security in general, and (3) TLA+ is in particular good at weeding out a very frequent class of bugs, called reentrancy bugs, from canisters.

After some cleanup work, we’re excited to announce that we’ve just open sourced all of our TLA+ models. You’ll find models of the following canisters on GitHub:

- NNS and SNS governance (focusing on interactions with the ledger canister)

- ICP ledger (focusing on block archival)

- ckBTC minter

- SNS swap canister


We hope that these models provide you with real-life examples to draw from and help you create your own canister models. Additionally we’re sharing a companion in-depth technical tutorial to guide you through this process. The tutorial provides a general strategy of how to model the idiosyncrasies of canisters in TLA+.

Since TLA+ is a general modeling language and isn’t specific to canisters, we have also applied it to parts of the underlying Internet Computer stack. For example, TLA+ helped ensure that the Internet Computer could smoothly migrate the Internet Identity canister. TLA+ first detected corner cases in an early version of the migration design, which could have rendered Internet Identity client canisters unable to cleanly upgrade, and later helped verify the fixes. The repository also includes models of:

- Connection establishment subprotocol for the people parties dapp.

- The Internet Computer consensus algorithm’s safety properties.

- The Internet Identity migration procedure.

- The subnet splitting procedure.


That’s it! We hope you find these resources useful. We look forward to seeing new TLA+ models for community-created canisters and dapps. If you create some, be sure to let the wider ICP community know in the developer forum. Next up, we will soon start working on some tooling to link the TLA+ models to actual Rust code, to address the problem of models and the code diverging as code is modified over time. Of course, these tools will also be open sourced as soon as they are production-ready, so stay tuned!

Get the open-source code for TLA+ models [here](https://github.com/dfinity/tla-models).

Check out the technical tutorial [here](https://mynosefroze.com/blog/2023-08-09-tla_for_canisters).

Learn more about the Internet Computer: internetcomputer.org
Follow the tech on Twitter: @DFINITYDev

Start building on the Internet Computer: [Developer Docs](https://internetcomputer.org/docs/current/home).

-->
