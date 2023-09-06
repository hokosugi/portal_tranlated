---
 
hide_table_of_contents: true
---
# 参考文献

## 概要

このカテゴリでは、Internet Computer の様々なコンポーネントに関するリファレンス・ドキュメントをご覧いただけます。特定のメソッドのシグネチャを知りたい場合や、SDK で提供されている関数を知りたい場合、また、Internet Computer を構成するコンポーネントの全体像を把握したい場合などにご利用ください。

## SDKリファレンス

DFINITY コマンドライン実行環境（dfx）は、Internet Computer プラットフォーム用のdapps を作成、デプロイ、管理するための主要ツールです。

- [dfx commands and envars](cli-reference/index.md)セクションで、IC SDKとそのサブコマンドを調べてください。
- [dfx.json](dfx-json-reference.md)スキーマを確認してください。

## 言語リファレンス

Motoko とRustは、IC上でビルドするためにサポートされている2つの優れた言語です。探索することで、すべての機能に素早くアクセスできます：

- [Motoko ](../motoko/main/base//index.md) リファレンス
- [Rust](https://docs.rs/ic-cdk/latest/ic_cdk/)リファレンス。
- IC間でのデータ交換を可能にする主要な要素は、Candidと呼ばれるcanister インターフェイスの言語に依存しない記述です。[Candid](candid-ref.md)リファレンスを検索することで、機能的な説明を見ることができます。

## 機能リファレンス

現在、より多くのdapps がIC上に構築されつつあり、多くの、あるいはほとんどのアプリケーションでいくつかの機能性が必要とされることが明らかになっています。そのため、いくつかの機能性が設計・開発されています。

- 財務統合の詳細については、[元帳canister](ledger.md) をご覧ください。
- 認証およびIDサービスについては、[Internet Identity仕様を](ii-spec.md)参照してください。

## Internet Computer 参考文献

Internet Computerの複雑な動作を詳しく理解したい方は、[Internet Computer インタフェース](ic-interface-spec.md)仕様を参照してください。

## セキュリティのベストプラクティス

ここには、[セキュリティのベストプラクティスに関する](../developer-docs/security/index.md)さまざまな提案もあります。ICを構築する際にセキュリティの考え方で開発するのに役立つ情報だけでなく、より一般的な情報もあります。

## リソース

他の参考文献については、自由にチェックしてください：

- [ Internet Computer for geeks](https://eprint.iacr.org/2022/087.pdf)システム全体の概要を説明した論文。
- 様々なトピックをより深く掘り下げた[How it works](https://internetcomputer.org/how-it-works/)ビデオ。
- [Romanのブログ](https://mmapped.blog/posts.html)
- [カイルのブログ](https://kyle-peacock.com/blog/)

<!---


# Reference documentation

## Overview

In this category you will find reference documentation for various components of the Internet Computer. This is the place to come if you wanted to know the signature for a particular method, or which functions are offered in an SDK, or, if you are so inclined, simply to browse and get a better picture of the components that make up the Internet Computer.

## SDK references
The DFINITY command-line execution environment (dfx) is the primary tool for creating, deploying, and managing the dapps for the Internet Computer platform.

- Explore the IC SDK and its subcommands in the [dfx commands and envars](cli-reference/index.md) section.
- Check the [dfx.json](dfx-json-reference.md) schema. 
  
## Language references
Motoko and Rust are two of the better supported languages for building on the IC. You can quickly access all their functionality by exploring:
- The [Motoko references](../motoko/main/base//index.md).
- The [Rust references](https://docs.rs/ic-cdk/latest/ic_cdk/). 
- A major ingredient in allowing data exchange across the IC is a language independent description of the canister interfaces called Candid. You can see the functional description by exploring the [Candid references](candid-ref.md).

## Functionality references
Now that more dapps are being built on the IC, it becomes apparent that several functionalities will be required by many or most apps. As such, several functionalities have been designed and developed. 
- To learn more about financial integration explore the [ledger canister](ledger.md). 
- To reference authentication and identity services see the [Internet Identity specification](ii-spec.md). 

## Internet Computer references
For those who want to understand the intricacies of the Internet Computer’s behavior in detail, we refer you to the [Internet Computer interface specification](ic-interface-spec.md).

## Security best practices
We also include here various suggestions for [security best practices](../developer-docs/security/index.md). There is information useful for developing with a security mindset when building on the IC, but also more generally.

## Resources
For other references, feel free to check out:
- [The Internet Computer for geeks](https://eprint.iacr.org/2022/087.pdf) paper which gives an overview of the entire system.
- The [how it works](https://internetcomputer.org/how-it-works/) videos to take a deeper dive into various topics.
- [Roman's blog](https://mmapped.blog/posts.html).
- [Kyle's blog](https://kyle-peacock.com/blog/). 

-->
