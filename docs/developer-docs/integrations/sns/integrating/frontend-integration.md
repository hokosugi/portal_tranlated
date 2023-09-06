# SNSフロントエンドの統合

## 概要

例えば、dapp canister のフロントエンドに SNS DAO 機能の一部を統合して、dapp ユーザーが同じアプリケーションで直接関連する SNS プロポーザル（dapp を管理・変更するもの）に投票できるようにしたい場合があります。
しかし、ウォレットdapp または DEX を構築するプロジェクトに携わる場合、SNS スワップ、SNS ガバナンス、または SNS 台帳へのインターフェイスを提供するなど、SNS とのインターフェイスを検討することもできます。

## sns-jsライブラリ

SNSプロジェクトとのインターフェースには、[sns-jsライブラリを](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md)利用できます。

`sns-js`
最初のアプローチは探索的なもので、SNSプロジェクトのSNSルート IDを提供するだけで、適切な への呼び出しをルーティングするラッパーを初期化できます。これは、すべての関数に対して単一のエントリーポイントを持つことを意味します。 2つ目のアプローチは、より一般的なもので、必要な特定の をインスタンス化します。canister canister
 canisters 

統合方法については、以下のリンクを参照してください：

- [SNSガバナンス](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsgovernancecanister)
- [SNSルート](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsrootcanister)
- [SNSスワップ](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsswapcanister)
- [SNSラッパーは](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snswrapper)、すべてのSNSを把握しているため、異なるSNSのデータにアクセスするのに便利です。

<!---
# SNS frontend integration
## Overview
There are different reasons why you might want to interface with an SNS project.
For example, you might want to integrate some of the SNS DAO functionality in the frontend of your dapp canister such that the dapp users can also vote on relevant SNS proposals (that govern and change the dapp) directly in the same application.
However, you might also consider interfacing with SNSs if you work on a project that builds a wallet dapp or DEX, for example to provide an interface to the SNS swap, the SNS governance, or the SNS ledger. 

## sns-js library
For interfacing with an SNS project, you can use the [sns-js library](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md).

`sns-js` can be utilized with two distinct approaches. 
The first approach is explorative, where you only need to provide the SNS root canister ID of your SNS project to initialize a wrapper that handles routing the calls to the appropriate canister. This means having a single entry point for all functions.
The second approach, which is more common, involves instantiating the specific canisters you require.

Please refer to the following links to learn how to integrate with: 
* [SNS governance](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsgovernancecanister)
* [SNS root](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsrootcanister)
* [SNS swap](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snsswapcanister)
* [SNS wrapper](https://github.com/dfinity/ic-js/blob/main/packages/sns/README.md#factory-snswrapper) which knows all SNSs and can therefore serve as a nice way to access different SNSs' data.
-->
