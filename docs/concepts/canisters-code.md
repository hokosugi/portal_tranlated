# Canisters およびコード

## 概要

心に留めておくべき最も重要な原則の1つは、Internet Computer 、分散された複製された方法でソフトウェアを実行できるブロックチェーンであるということです。

Internet Computer 上で実行されるdapp のソースコードを書くときは、そのソースコードを**WebAssembly モジュールに**コンパイルします。あなたのプログラムを含むWebAssembly モジュールをInternet Computer ブロックチェーン上にデプロイすると、そのプログラムは、 と呼ばれる概念的な計算ユニットの内部で実行されます。 **canister**Canisters はさまざまなプログラミング言語で開発できます。Internet Computer のために特別に設計されたプログラミング言語であるMotoko の他に、C、Rust、JavaScript/TypeScript、AssemblyScript、Python などの既存のプログラミング言語を使用することもできます。

デプロイされると、ブラウザのようなフロントエンドクライアントを通して、canister のために定義したエントリーポイント関数にアクセスすることで、エンドユーザーはcanister と対話することができます。

## アーキテクチャ

### Canisters プログラムとステートの両方を含む

canister は、アプリケーションまたはサービスのコンパイル済みコードと依存関係を含むソフトウェアユニットとしてデプロイされるという点で、コンテナと似ています。

コンテナ化によって、アプリケーションを環境から切り離すことができ、簡単で信頼性の高いデプロイが可能になります。しかし、canister は、現在のソフトウェアの**ステートに関する**情報も格納するという点で、コンテナとは異なります。

コンテナ化されたアプリケーションには、アプリケーションを実行する環境のステートに関する情報が含まれるかもしれませんが、canister は、その関数が呼び出された結果生じたステート変更の記録を永続化することができます。

### クエリと更新メソッド

プログラムとステートの両方からなるcanister という概念は重要です。特に、canister のエンドポイントを呼び出すときに期待される動作に関係します。 呼び出しには2つのタイプしかありません：

- コミットしない**クエリーコール**（ステートの変更は破棄される）。
- コミット**更新コール**（ステート変更は永続化されます）。

| タイプ | 覚えておくべきポイント |
| --- | --- |
| **クエリーコール** | **アップデートコール** |
| ユーザーがcanister の現在のステートをクエリーコールしたり、canisterのステートを**変更せずに**操作する関数をコールできるようにします。 | ユーザーがcanister のステートを変更し、**変更が永続化さ**れるようにします。 |
| 同期で、即座に応答します。 | 非同期に応答。 |
| canister を保持するどのノードに対しても行うことができます。つまり、セキュリティーとパフォーマンスの間には本質的なトレードオフがあります。 | 結果を返すにはコンセンサスを通過する必要があります。コンセンサスが必要なため、canister のステートを変更して結果を返すのに時間がかかります。サブネット内のレプリカの3分の2が結果に合意する必要があるため、結果は信頼できますが、呼び出しに時間がかかります。 |
| canister のステートへの変更を永続化することを許可しないため、基本的にクエリーコールは読み取り専用の操作となります。 | 呼び出されたcanister は、他の が公開している関数を呼び出すことができます。canisters |
| 呼び出されたcanister が、他のcanisters によって公開された関数を、canister 間呼び出しとして呼び出すことを許可しないでください。(この制限は一時的なものであり、canisters は将来、クエリーコールを処理するときに、他のcanisters によって公開された関数を呼び出すことができるようになることに注意)。 |  |

:::tip
開発者として、canister クエリーコールとcanister ステートを変更するコールの間のこの関係を認識することは重要です。特に、セキュリティとパフォーマンスの間の本質的なトレードオフに留意してください。
::：

## 認証データ

クエリーコールはコンセンサスを経由しないので、データの更新ではなく読み取りだけが必要な場合は、可能な限り[認証応答を](https://internetcomputer.org/docs/current/developer-docs/security/general-security-best-practices)使用する必要があります。

認証はcanister レベル、つまりサブネット単位で行われます。認証されたレスポンスは、アップデートコール中にあらかじめ認証されている必要があります。

## のdapps 。Internet Computer

プログラマーやソフトウェア開発者にとって、Internet Computer ブロックチェーンは、dapps の設計、構築、デプロイ方法を簡素化するフレームワークの中で、独自の機能と機会を提供します。このフレームワークの重要な部分は、新しい汎用プログラミング言語、Motoko です。Motoko は、Internet Computer ブロックチェーンが提供する以下のような独自の機能を最大限に活用するために特別に設計されたプログラミング言語です：

- `actor` オブジェクトとクラスを使用してプログラムを直接定義できること。

- `async` 、`await` 構文の使用により、非同期メッセージングを同期処理のようにプログラミング可能。

- メッセージのシリアライズとデシリアライズの自動サポート。

- 外部データベースやストレージボリュームを使用せずに、データ構造を使用して直交永続化を活用する機能。

最新の高水準プログラミング言語として、Motoko は以下のような独自の主要機能を提供します：

- 大きな整数演算とオーバーフロー保護のサポート。

- 各プログラムを静的にチェックし、起こりうるすべての入力に対して型エラーなしに実行できることを保証する健全な型システム。

- 関数の抽象化、ユーザー定義型定義、ユーザー定義actorのサポート。

構文規則やサポートされている機能など、Motoko プログラミング言語自体の詳細情報については、[**Motoko プログラミング言語ガイドを**](/motoko/main/about-this-guide.md)参照してください。

他のプログラミング言語をサポートするCanister 開発キット (CDK) については、この[**概要を**](../developer-docs/backend/choosing-language.md)参照してください。

次の図は、Internet Computer エコシステムの一部としての開発環境を簡略化してドリルダウンしたものです。

![Your development environment as part of the Internet Computer ecosystem](_attachments/SDK-protocol-network.svg)

## Canisters actor、そしてあなたが作成するコード

Motoko プログラミング言語を使用してプログラムを書く準備をする際に、最も重要な原則の1つは、Motoko が*actor ベースのプログラミング・モデルを使用していることです。*

オブジェクト **actor**は分離されたステートでメッセージを処理する特殊なオブジェクトで、メッセージをリモートで非同期に処理することができます。

一般的に、各canister には、1つのactor オブジェクトのコンパイルされたコードが含まれます。各canister には、インターフェースの説明やフロントエンドのアセットなどの追加情報が含まれることもあります。複数のcanisters を含むプロジェクトを作成することはできますが、各canister に含めることができるのは、1 つのactor だけです。

## コードがコンパイルされる理由WebAssembly

Motoko コードをコンパイルすると、結果はWebAssembly モジュールになります。WebAssembly は低レベルのコンピュータ命令フォーマットで、移植性が高く、ほとんどの最新のコンピュータハードウェア上でプログラムの実行をきれいに抽象化します。インターネット上で実行されるプログラムに広くサポートされており、Internet Computer 上で実行されることを意図したdapps のデプロイに自然に適合します。

Motoko を使えば、開発者はポータブルなWebAssembly にコンパイルしながら、シンプルで高水準の言語を使って安全なdapps を提供することができます。

Motoko 言語は、型安全性やパターンマッチングのような、他の高水準モダン言語に共通する機能の多くを提供します。さらに、Motoko は、Internet Computer に特に適した方法で、actorを使用してメッセージング・サービスを定義するためのビルトイン・サポートを提供します。

このガイドでは、SDK を使用したプログラムの記述に関連して、Motoko プログラミング言語の基本的な機能を紹介します。Motoko プログラミング言語自体の詳細については、[**Motoko プログラミング言語ガイドを**](/motoko/main/about-this-guide.md)参照してください。

## ID と認証

ユーザー主導のcanister 操作とcanister-tocanister 操作の主な違いの 1 つは、canisters がInternet Computer に明示的に登録された ID を持っていることです。

ユーザー・プリンシパルの中央レジストリはないが、ユーザーは1つ（または複数）のデジタル署名鍵を使用して自分自身を識別することを選択できます。ユーザーの秘密鍵はメッセージに署名するために使用され、公開鍵とともにInternet Computer に送信される。Internet Computer はユーザーを認証し、プリンシパルをcanisterに渡す。canister は、プリンシパルに基づいて望む認証ポリシーを実装することを選択できる。

高度なレベルでは、初回ユーザーはInternet Computer との最初のやりとりの間に、署名されていない鍵ペアを生成し、公開鍵からプリンシパル識別子を導出する。再訪ユーザーは、ユーザーエージェントが安全に保存した秘密鍵(または複数の鍵)を使用して認証される。複数のcanisters にアクセスできるユーザは、各canister に関連する認証に使用される鍵およびデバイスを管理できる。

1人のユーザーが、異なるデバイス（異なるコンピュータ、携帯電話、タブレットで実行されるブラウザなど）からcanisters にアクセスするために、複数の公開鍵と秘密鍵のペアを持つことができますが、これらの派生鍵はすべて一次識別子にマッピングされます。

## リソースの消費とcycles

すべてのcanisters は、実行のためのCPUcycles 、メッセージのルーティングのための帯域幅、永続化されたデータのためのストレージといったリソースを消費します。これらのリソースは、 と呼ばれるコスト単位を使用して支払われます。 **cycles**Cycles は ICP トークンを変換することで入手でき、各canister によってローカル残高に保存されます。

- Canisters は完全な実行（all or nothing）に対して支払うことができなければなりませんが、 の単位に関連するコストは、効率的なプログラムの費用対効果を高めます。cycles 

- canister が消費できるcycles の数に制限を設けることで、プラットフォームは悪意のあるコードがリソースを完全に乗っ取るのを防ぐことができます。

- Cycles は、安定的またはデフレ的な方法で運用の実質コストを反映することを意図しており、プログラムの実行コストは変わらないか、運用効率に応じて減少します。そのため、ICPから への変換レートは、現在のICP市場価値に基づいて適宜調整されます。運用コストが比較的安定しているため、例えば100万件のメッセージを処理するために必要な を予測することが容易になります。cycles cycles 

## リソース

canisters に関する詳細情報をお探しの場合は、以下の関連リソースをご覧ください：

- [Introducingcanisters - an evolution of smart contracts (video)](https://www.youtube.com/watch?v=LKpGuBOXxtQ).

- [IC SDKとは何ですか？(ビデオ）](https://www.youtube.com/watch?v=60uHQfoA8Dk)。

- [最初のアプリケーションのデプロイ（ビデオ）](https://www.youtube.com/watch?v=yqIoiyuGYNA)。

<!---
# Canisters and code

## Overview

One of the most important principles to keep in mind is that the Internet Computer is a blockchain that allows running software in a distributed, replicated way.

When you write source code for a dapp that runs on the Internet Computer, you compile the source code into a **WebAssembly module**. When you deploy the WebAssembly module that contains your program on the Internet Computer blockchain, the program is executed inside a conceptual computational unit called a **canister**. Canisters can be developed in various programming languages. Besides Motoko, a programming language purposefully designed for the Internet Computer, you can also use existing programming languages like C, Rust, JavaScript/TypeScript, AssemblyScript, and Python. 

Once deployed, end-users can interact with the canister by accessing the entry point functions you have defined for that canister through a frontend client such as a browser.

## Architecture
### Canisters include both program and state

A canister is similar to a container in that both are deployed as a software unit that contains compiled code and dependencies for an application or service.

Containerization allows for applications to be decoupled from the environment, allowing for easy and reliable deployment. The canister differs from a container, however, in that it also stores information about the current software **state**.

While a containerized application might include information about the state of the environment in which the application runs, a canister is able to persist a record of state changes that resulted from its functions being called.

### Query and update methods

This concept of a canister consisting of both program and state is an important one. In particular, it relates to the behavior one should expect when calling an end-point of the canister. There are only two types of calls: 
- Non-committing **query calls** (any state change is discarded).
- Committing **update calls** (state changes are persisted).


| Type      | Key points to remember|
|-----------|-----------------------|
|**Query calls**|**Update calls**   |
|Allow the user to query the current state of a canister or call a function that operates on the canister’s state **without changing it**.|Allow the user to change the state of the canister and have **changes persisted**.|
|Are synchronous and answered immediately.|Are answered asynchronously.|
|Can be made to any node that holds the canister; the result does not go through consensus. That is, there is an inherent tradeoff between security and performance: the reply from a single node is fast, but might be untrustworthy or inaccurate.|Must pass through consensus to return the result. Because consensus is required, changing the state of a canister, and returning the result can take time. There is an inherent tradeoff between security and performance: the result is trustworthy because two-thirds of the replicas in a subnet must agree on the result, but the call is slow.|
|Do not allow changes to the state of the canister to be persisted, so essentially query calls are read-only operations.|The called canister can invoke functions exposed by other canisters|
|Do not allow the called canister to invoke functions exposed by other canisters as inter-canister calls. (Note that this restriction is temporary and that canisters will be able to invoke functions exposed by other canisters when processing query calls in the future.)| |

:::tip
As a developer, it is important to recognize this relationship between the calls that query the canister and the calls that change the canister state. In particular, you should keep in mind the inherent tradeoff between security and performance.
:::

## Certified data

Since query calls do not go through consensus, [certified responses](https://internetcomputer.org/docs/current/developer-docs/security/general-security-best-practices) should be used wherever possible when data only needs to be read, not updated. 

Certification happens on the canister level, i.e. is done by a subnet. Any certified response has to be pre-certified during an update call, so that the certificate can simply be fetched in a query call at a later point in time.

## How to develop dapps for the Internet Computer

For programmers and software developers, the Internet Computer blockchain provides unique capabilities and opportunities within a framework that simplifies how you can design, build, and deploy dapps. A key part of this framework is a new, general purpose programming language, Motoko. Motoko is a programming language that has been specifically designed to take full advantage of the unique features that the Internet Computer blockchain provides, including:

-   The ability to define programs directly using `actor` objects and classes.

-   The use of `async` and `await` syntax to enable programming asynchronous messaging as if it was synchronous processing.

-   Automatic support for message serialization and deserialization.

-   The ability to leverage orthogonal persistence using data structures without external databases or storage volumes.

As a modern, high-level programming language, Motoko provides some key features of its own, including:

-   Support for big integer operations and overflow protection.

-   A sound type system that statically checks each program to ensure it can execute without type errors on all possible inputs.

-   Support for function abstractions, user-defined type definitions, and user-defined actors.

For more detailed information about the Motoko programming language itself, including syntactical conventions and supported features, see the [**Motoko programming language guide**](/motoko/main/about-this-guide.md).

For information about Canister Development Kits (CDKs) supporting other programming languages, see this [**overview**](../developer-docs/backend/choosing-language.md).

The following diagram provides a simplified drill-down view of the development environment as part of the Internet Computer ecosystem.

![Your development environment as part of the Internet Computer ecosystem](_attachments/SDK-protocol-network.svg)

## Canisters, actors, and the code you produce

One of the most important principles to keep in mind when preparing to write programs using the Motoko programming language is that Motoko uses an *actor-based* programming model.

An **actor** is a special kind of object that processes messages in an isolated state, enabling messages to be handled remotely and asynchronously.

In general, each canister includes the compiled code for one actor object. Each canister may also include some additional information such as interface descriptions or frontend assets. You can create projects that include multiple canisters, but each canister can only include one actor.

## Why your code is compiled into WebAssembly

When you compile Motoko code, the result is a WebAssembly module. WebAssembly is a low-level computer instruction format that is portable and abstracts program execution cleanly over most modern computer hardware. It is broadly supported for programs that run on the internet and a natural fit for deploying dapps that are intended to run on the Internet Computer.

With Motoko, developers can compile to portable WebAssembly while still delivering secure dapps using a simple and high-level language.

The Motoko language offers many of the features that are common to other higher-level modern languages—like type safety and pattern-matching. In addition, Motoko provides built-in support for defining messaging services using actors in a way that is especially well-suited to the Internet Computer and is easy to learn whether you are a new or experienced programmer.

This guide provides an introduction to the basic features of the Motoko programming language in the context of writing programs using the SDK. For more detailed information about the Motoko programming language itself, see the [**Motoko programming language guide**](/motoko/main/about-this-guide.md).

## Identities and authentication

One of the main differences between a user-initiated canister operation and a canister-to-canister operation is that canisters have an explicitly registered identity on the Internet Computer.

There is no central registry for user principals, but users may choose to identify themselves using one (or more) digital signing keys. The user’s private key is used to sign messages, which are sent along with their public key to the Internet Computer. The Internet Computer authenticates the user and passes the principal to the canister — the canister may choose to implement whatever authorization policies it wants based on principals.

At a high level, first-time users generate an unsigned key pair and derive their principal identifier from the public key during their first interaction with the Internet Computer. Returning users are authenticated using the private key (or keys) that have been stored securely by the user agent. Users with access to multiple canisters can manage the keys and devices used for authentication associated with each canister.

A single user can have multiple public-private key pairs for accessing canisters from different devices—such as browsers running on different computers, mobile phones, or tablets—but these derived keys all map to a primary identifier.

## Resource consumption and cycles

All canisters consume resources, CPU cycles for execution, bandwidth for routing messages, and storage for persisted data. These resources are paid for using a unit of cost called **cycles**. Cycles can be obtained by converting ICP tokens and are stored by each canister in a local balance.

-   Canisters must be able to pay for complete execution (all or nothing), but the cost associated with a unit of cycles will make efficient programs cost-effective.

-   By setting limits on how many cycles a canister can consume, the platform can prevent malicious code from completely taking over resources.

-   Cycles are intended to reflect the real cost of operations in a stable or deflationary way so that the cost of program execution remains the same or decreases with operational efficiency. As such, the conversion rate of ICP to cycles is adjusted accordingly, based on the current ICP market value. The relative stability of operational costs makes it easier to predict the cycles required to process, for example, a million messages.

## Resources

If you are looking for more information about canisters, check out the following related resources:

-   [Introducing canisters — an evolution of smart contracts (video)](https://www.youtube.com/watch?v=LKpGuBOXxtQ).

-   [What is the IC SDK? (video)](https://www.youtube.com/watch?v=60uHQfoA8Dk).

-   [Deploying your first application (video)](https://www.youtube.com/watch?v=yqIoiyuGYNA).

-->
