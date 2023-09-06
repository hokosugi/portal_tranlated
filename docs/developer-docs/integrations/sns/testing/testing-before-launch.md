---

sidebar_position: 1
---
# ローンチ前のSNSテスト

SNSを立ち上げる前に、徹底的にテストすることをお勧めします。

## SNSの分散化をテストするには、主に以下の2つの方法があります。

### ローカルマシン

開発者は**[ローカル](./testing-locally.md)**マシンでテストできます。開発者を支援するために、DFINITY は`sns-testing` リポジトリを作成しました。このリポジトリには、開発者が SNS プロセスをテストするのに役立つスクリプトがあります。開発者はInternet Computer のローカルバージョンをローカルマシンで実行し、dapp をローカルにデプロイし、dapp を分散化する[段階を](../launching/launch-summary.md)実行することができます。

** `sns-testing` リポジトリの主な目的は、開発者が実際にdapp を分散化するプロセスをテストすることです。**

特に、開発者は`sns-testing` リポジトリを以下のことに利用できます：

- プロポーザルの開始
- プロポーザルの通過
- 分散スワップの開始
- DAO 投票によるdapp のアップグレード。

:::info
`sns-testing` は、ローカルで SNS プロセスをテストする一つの形態に過ぎません。開発者は自由に他のものを使ったり、`sns-testing` をフォーク/修正したり、独自のものを作ったりしてください。
::：

### メインネットで

開発者がSNSのプロセスをテストしたら、** [メイン](./testing-on-mainnet.md)**ネット**上で「SNSテストフライト**」を行うことを強くお勧めします。SNS testflightとは、開発者がdapp を（メインネットに）デプロイし、その制御を（メインネット上の）模擬SNSに渡すことです。

**SNS testflight を行う主な目的は、dapp が分散化された*後に*どのように動作するかを開発者が体験することであり、開発者はdapp が分散化に対応していることを確認することができます。分散化の実際のプロセスをテストするものではありません。**

:::info
テストフライトはレポやツールのセットではなく、*アクティビティ*（デプロイし、dapp 、そのコントロールを模擬SNSに渡す）です。そのため、[メインネット上でのテストの](./testing-on-mainnet.md)指示は様々なツールを利用していますが、開発者はもちろん好きなツールを使うことができます。
::：

とりわけ、SNSテストフライトを実行した後に開発者が発見した問題の例をいくつか紹介します：

- 開発者は、dapp を更新する提案を作成するための、よりよいパイプラインが必要であることに気づきます。
- 開発者は、分散化が時期尚早であることに気づき、いくつかの点を修正する必要があることに気づきます。
- 開発者は、分散化する前に、より良いモニタリングが必要であることに気づきます。

dappSNS**testflightで**使われるモックSNSは、dapp の分散化後のライフcycle がどのように見えるかを開発者に提供します。

<!---


# SNS testing before launch

Before launching an SNS it is advisable to thoroughly test it. 

## Two main ways for testing an SNS decentralization

### On a local machine

Developers can test **[on a local machine](./testing-locally.md)**. To help developers, DFINITY has created the `sns-testing` repo which has scripts that help developers test the SNS process. They can run a local version of the Internet Computer on their local machine, deploy their dapp locally and run through [the stages](../launching/launch-summary.md) of decentralizing their dapp. 

**The main intent of `sns-testing` repo is for a developer to test the actual process of decentralizing their dapp.**

Among other things, developers can use `sns-testing` repo to: 
* Initiate proposals.
* Pass proposals.
* Start decentralization swaps.
* Upgrade the dapp via DAO voting.

:::info
`sns-testing` is just one form of testing SNS process locally. Developers should feel free to use others, fork/modify `sns-testing` or create their own.
:::


### On the mainnet

Once a developer has tested the process of an SNS, it is highly recommended they do an **"SNS testflight" [on the mainnet](./testing-on-mainnet.md)**. An SNS testflight is when a developer deploys their dapp (to the mainnet) and hands control of it to a mock SNS (on the mainnet). 

**The main intent of performing an SNS testflight is for a developer to experience how a dapp works *after* it has been decentralized, so developer can make sure their dapp is ready for decentralization. It does not test the actual process of decentralizing it.**

:::info 
A testflight is not a repo or set of tools, but an *activity* (deploying and dapp and handing control of it to a mock SNS), so the instructions for [testing on the mainnet](./testing-on-mainnet.md) utilize various tools, but developers can of course use any tools they wish. 
:::

Among other things, here are some examples of issues developers find after running an SNS testflight: 
* Developers notice they need a better pipeline for creating proposals to update a dapp.
* Developers notice they may have decentralized prematurely and need to fix some things.
* Developers notice they may need better monitoring before decentralizing.

The mock SNS used in a SNS testflight gives developers the ability to see how post-decentralization lifecycle of a dapp looks like, but still provides a way for a developer to keep control of their dapp. **Therefore, developers are encouraged to run and perform an SNS testflight on the mainnet, potentially for multiple days or weeks, to ensure that all aspects have been covered.**

-->
