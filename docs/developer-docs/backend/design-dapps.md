---

sidebar_position: 10
---
# Dapp デザイン

## 概要

dapps のアイデアを考え出すと、プロジェクトの構成や編成方法について多くの設計上の決定を行うことになります。Internet Computer では、アプリの実装を計画する際に特に注意すべき設計上の決定事項がいくつかあります。

## シングルまたはマルチcanister アーキテクチャ

dapp を設計するときに考慮したい最初の決定事項の1つは、単一のcanister でカプセル化するか、複数のcanisters で構成するかです。

たとえば、フロントエンドのないシンプルなサービスを書いている場合、プロジェクト管理とメンテナンスを簡素化し、機能の追加に集中するために、単一のcanister を使いたいと思うかもしれません。dapp にフロントエンドのアセットとバックエンドのビジネスロジックの両方がある場合、プロジェクトは少なくとも2つのcanisters で構成されるでしょう。1つはユーザーインターフェイスコンポーネントを管理するためのcanister で、もう1つはアプリケーションが提供するバックエンドサービスのためのcanister です。

また、より専門的なcanisters からインポートして呼び出したり、他の開発者が利用できるように、再利用可能な一般的なサービスを独自のcanister に配置することも検討できます。[LinkedUpの](https://github.com/dfinity/linkedup)サンプルdapp は、プロフェッショナルサービスdapp を2つのcanisters に分割することで、このアプローチを示しています。LinkedUpの例では、ソーシャルなつながりを確立する関数は`connectd` canister で定義され、`linkedup` canister で定義されるプロフェッショナルなプロフィールを設定するための関数とは別に定義されています。例えば、プロフィールの属性や共有されたコネクションに基づいてイベントをスケ ジュールするために、canister でdapp を拡張することは容易に想像できます。

## 型やユーティリティからactorを分離

プロジェクトのアーキテクチャを計画する際、一般的なプラクティスの 1 つは、メインのactor のコードを 1 つのファイルに配置し、プログラムで使用する型や、actor を必要としないユーティリティ関数を定義するための追加ファイルを別に配置することです。

例えば、dapp のバックエンドロジックを以下のファイルで構成するように設定します：

- `Main.mo` `main.rs` クエリーコールとアップデートコールを送信するために を必要とする関数。actor 

- `Util.mo` または、 が使用するためにインポートできるヘルパー関数を含む 。actor `util.rs` 

- `Types.mo` または  のすべてのデータ型定義。`types.rs` dapp

## クエリーコールの使用

[クエリおよびアップデート・メソッドで](/concepts/canisters-code.md#query-update)説明したように、クエリはアップデート・コールよりも高速に結果を返します。したがって、`query` として関数を明示的にマークすることは、アプリケーションのパフォーマンスを向上させる効果的な戦略です。計画と設計の段階では、クエリやアップデートを実行する関数ではなく、クエリーコールを使用する最善の方法を検討する必要があります。

dappsしかし、クエリがコンセンサスを経ず、ブロックチェーン上に表示されないというセキュリ ティとパフォーマンスのトレードオフも考慮する必要があります。dapps によっては、このトレードオフは適切かもしれません。例えば、ブログプラットフォームを開発している場合、タグに一致する記事を取得するクエリは、おそらく大多数のノードが結果に同意することを保証するためにコンセンサスを経る必要はありません。しかし、dapp 、財務データのような機密情報を取得する場合は、基本的なクエリが提供するよりも、結果についてより多くの保証が必要かもしれません。

基本クエリの代替として、Internet Computer は**認証クエリも**サポートしています。認証 ク エ リ を使用す る と 、 エ ン ド ユーザーが信頼で き る**認証 さ れた応答を**受信で き る よ う にな り ます。認証済みクエリの使用は高度なテクニックであるため、チュートリアルやその他の開発者向けのドキュメントでは扱っていませんが、認証の仕組みや、クエリに対する応答として認証済みデータを返すようにプログラムを構成するために必要なことについては、[ICインターフェース仕様で](/references/ic-interface-spec.md)学ぶことができます。

## データの保存と検索

Internet Computer を使用すると、**安定したメモリを**使用して長期的なデータ保存（しばしば直交永続性と呼ばれます）を処理し、**クエリーコールを**使用してデータを取得することができます。1つまたは複数のキーを使用して効率的にデータを取得するには、通常、ハッシュテーブルのようなデータ構造を使用します。より伝統的なデータベースをcanister の中に実装することも可能です。

<!---

# Dapp design considerations

## Overview

As you come up with ideas for dapps, you are going to make many design decisions about how to structure and organize your project. On the Internet Computer, there are a few design decisions that you should pay particular attention to as you plan the implementation for your app.

## Single or multiple canister architecture

One of the first decisions you might want to consider when designing your dapp is whether it should be encapsulated in a single canister or consist of multiple canisters.

For example, if you are writing a simple service with no frontend, you might want to use a single canister to simplify project management and maintenance and focus on adding features. If your dapp has both frontend assets and backend business logic, your project is likely to consist of at least two canisters, with one canister for managing user interface components and another canister for the backend services the application provides.

In planning, you might also consider placing some common reusable services in their own canister so that they can be imported and called from other more-specialized canisters or made available for other developers to use. The [LinkedUp](https://github.com/dfinity/linkedup) sample dapp illustrates this approach by splitting the professional service dapp into two canisters. In the LinkedUp example, the functions that establish social connections are defined in the `connectd` canister and separate from the functions used to set up professional profiles that are defined in the `linkedup` canister. It is easy to imagine extending the dapp with a third canister, for example to schedule events based on profile attributes or shared connections.

## Segregating actors from types and utilities

In planning the architecture for your project, one common practice is to place the code for the main actor in one file with separate additional files for defining the types you program uses and utility functions that don’t require an actor.

For example, you might set up the backend logic for your dapp to consist of the following files:

-   `Main.mo` or `main.rs` with the functions that require an actor to send query and update calls.

-   `Util.mo` or `util.rs` with helper functions that can be imported for the actor to use.

-   `Types.mo` or `types.rs` with all of the data type definitions for your dapp.

## Using query calls

As discussed in [query and update methods](/concepts/canisters-code.md#query-update), queries return results faster than update calls. Therefore, explicitly marking a function as a `query` is an effective strategy for improving application performance. In the planning and design phase, you should consider how best to use query calls instead of functions that can perform queries or updates.

That is a good general rule to follow and can be applied broadly to most categories of dapps. However, you should also consider the security and performance trade-off that queries don’t go through consensus and do not appear on the blockchain. For some dapps, that trade-off might be appropriate. For example, if you are developing a blogging platform, queries that retrieve articles matching a tag probably don’t warrant going through consensus to ensure that a majority of nodes agree on the results. However, if your dapp is retrieving sensitive information—like financial data—you might want more assurance about your results than a basic query provides.

As an alternative to basic queries, the Internet Computer also supports **certified queries**. Certified queries enable you to receive **authenticated responses** that end users can trust. Using certified queries is an advanced technique that is not covered in the tutorials or other developer-focused documentation, but you can learn about how the authentication works and what you need to do to configure your program to return certified data in response to queries in the [IC interface specification](/references/ic-interface-spec.md).

## Data storage and retrieval

The Internet Computer enables you to use **stable memory** to handle long-term data storage—often referred to as orthogonal persistence—and to use **query calls** to retrieve your data. Efficiently retrieving data using one or more keys can typically be achieved by using data structures like hash tables. It is also possible to implement a more traditional database inside a canister.

-->
