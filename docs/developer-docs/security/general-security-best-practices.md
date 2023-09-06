---

sidebar_position: 1
sidebar_label: General
---
# 一般的なセキュリティのベストプラクティス

## 概要

この文書には、一般的なセキュリティのベストプラクティスに関する情報が含まれています。

## クエリレスポンスがセキュリティに関連する場合、そのクエリレスポンスを認証します。

### セキュリティ上の問題

(アップデートコールとは対照的に)[クエリーコールに対する](/references/ic-interface-spec.md#https-interface)レスポンスは、canister/subnetによって閾値署名されません。そのため、悪意のあるレプリカやバウンダリノードがデータを変更し、その真正性を侵害する可能性があります。アップデートコールがクエリーコールへのレスポンスに依存している場合、これは特に危険です。

### 推奨事項

- 真正性保証が必要なすべてのセキュリティ関連のクエリ応答データ（これはdApp ごとに評価する必要があります）は、認証変数を使用して IC によって認証されるべきです。[certified-mapの](https://github.com/dfinity/cdk-rs/tree/main/library/ic-certified-map)ような既存のデータ構造の使用を検討。データ認証はフロントエンドで検証されなければなりません。

- あるいは、これらの呼び出しは、呼び出し元によってアップデートコールとして発行されなければなりません（例えば、agent-jsにおいて）。すべての問い合わせは、呼び出し元によって更新として発行することもできることに注意してください。

- 例として、[Internet Identityの](https://github.com/dfinity/internet-identity/blob/b29a6f68bbe5a49d048e12bc7a3263a9f43d080b/src/internet_identity/src/main.rs#L775-L808)資産認証、[NNSdapp](https://github.com/dfinity/nns-dapp/blob/372c3562127d70c2fde059bc9c268e8ae858583e/rs/src/assets.rs#L121-L145) 、または[Internet Identityのcanister 署名実装が](https://github.com/dfinity/internet-identity/blob/main/src/internet_identity/src/signature_map.rs)あります。

### のデータ機密性Internet Computer

#### セキュリティ上の懸念

Internet Computer にデータを格納する場合、データアクセスには2つのレベルがある。

1.  ノードは、サブネットに保存されているすべてのデータを読むことができます。これには、canister に保存されているすべてのデータとともに、canister に送受信されるすべてのメッセージが含まれます。これは、ノードが、canister で利用可能なすべてのデータを抽出できることを意味します。これは、ノードに TEE ベースのセキュリティを実装することで変化します。

2.  エンドユーザクライアントは、ノードとcanisters が利用できるようにしたデータにのみアクセスできます。サブネットのノードが悪さをしてデータを漏らさなければ、クライアントが読むことができるのは、イングレスメッセージに対するレスポンスと、ノードが送信したクエリだけです。どのデータをクライアントに公開するかは、canister 。

サブネットのステートツリーに格納されているデータの部分的な情報は常に漏れます。したがって、「True」か「False」のどちらかしかないブール値のように、エントロピー値が低いデータは完全に漏れて、完全に公開される可能性があります。エントロピーの高いデータのリークはごくわずかです。

サブネットのステート・ツリーに格納される可能性のあるユーザー関連データには2種類あります。1つ目は、ユーザがcanister にイングレス・メッセージを送信する場合です。メッセージ・ハッシュとレスポンスは両方ともサブネットのステート・ツリーに格納され、クライアントが安全に取得できるようになります。イングレス・メッセージは、エージェントによって実装され、通常はユーザーに公開されない高エントロピーのnonceを含む必要があります。メッセージ応答はcanister によって決定され、高エントロピー値を含まないかもしれません。もしcanister レスポンスが低エントロピーの値で構成されている場合、そのデータはイングレスメッセージ送信者以外のユーザに漏洩する可能性があります。

ユーザー関連データの2つ目のタイプは、canister によって保持される認証変数で、サブネットのステートツリーを通じて公開されます。canister が低エントロピーのデータをステートツリーに配置した場合、そのデータは、そのデータ部分にアクセスすべきではないユーザーに漏れる可能性があります。

#### 推奨

外部ユーザーからデータの機密性を保護する必要がある開発者は、サブネットのステートツリー内のデータが十分なレベルのエントロピーを持つようにする必要があります。128ビットを推奨します。データ自体に十分なエントロピーがない場合は、ランダム性を利用した人工的なデータを追加することをお勧めします。

特に、canister 、イングレス・メッセージに対するレスポンスに高エントロピーのデータを含めることで、送信者以外の外部ユーザーにデータが漏れないようにすることができます。あるいは、canister 、秘密にしておくべき変数に高エントロピーのデータを追加することで、認証された変数のデータが漏れないようにすることができます。

さらに、イングレス・メッセージ・レスポンスと同様に、低エントロピー・データを含むcanister のプライベート・カスタム・セクションは、権限のないユーザーに漏れる可能性があります。したがって、canister のプライベートカスタムセクションには、十分なレベルのエントロピーを使用する必要があります。128ビットを推奨します。データ自体に十分なエントロピーがない場合は、ランダム性を利用した人工的なデータを追加することをお勧めします。

## に固有ではありません。Internet Computer

このセクションのベストプラクティスは非常に一般的なものであり、Internet Computer に特化したものではありません。このリストは決して完全なものではなく、過去に問題になった非常に具体的な懸念事項をいくつか挙げているだけです。

### 既知の脆弱性を持つサードパーティ製コンポーネントを使わないでください。

#### セキュリティ上の懸念

脆弱で古いコンポーネントを使うことは、[大きなセキュリティリスク](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/)です。

#### 推奨事項

- サードパーティコンポーネントを、既知の脆弱性のデータベースと定期的に照合してください：
  
  - Rust:[cargo audit](https://crates.io/crates/cargo-audit) を使ってください。
  
  - JavaScript / NPM:[npm audit](https://docs.npmjs.com/cli/v8/commands/npm-audit) を使ってください。

- 既知の脆弱性があれば、ビルドは失敗するはずです。

- メンテナンスされておらず、信頼できない可能性のあるリポジトリのフォーク版を使用しないでください。

- 広く使われておらず、十分な（理想的にはサードパーティによる）レビューを受けていない可能性のあるサードパーティ製コンポーネントの使用は避けましょう。

- 使用しているコンポーネントのバージョンを固定し、自動的に破損したアップデートに切り替わらないようにしてください。

### 暗号を自分で実装しないでください。

#### セキュリティ上の懸念

暗号アルゴリズムを実装するときに間違いを犯しやすく、セキュリティバグにつながります。

#### 推奨

- オープンソースで、多くの人のレビューを受けているような、よく知られたライブラリを使用してください。例えば、JavaScript の[Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API)を使ったり、Rust の[sha256](https://crates.io/crates/sha256)のようなクレートを使ってください。

### 安全な暗号方式を使いましょう

#### セキュリティ上の懸念

いくつかの暗号方式は壊れています（古いTLSのバージョン、MD5、SHA1、DESなど）。これらを使うことはセキュリティ上の問題を引き起こします。

#### 推奨

暗号を使う必要がある場合は、壊れておらず、既知の問題がない暗号方式だけを使いましょう。NISTやIETFなどで規格化されたアルゴリズムを使うのが理想的です。

参考文献

- [OWASP 暗号の失敗例](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)

### コードのテスト

#### セキュリティ上の懸念

テストカバレッジが小さいと、コードの変更が難しくなり、正しさやセキュリティの特性に違反し、バグにつながる可能性があるためです。対応するテストがない場合、レビュー（および、セキュリティレビュー）において、正しさとセキュリティ特性を検証することは困難です。

#### 推奨

canister の実装とフロントエンドのコードについて、特に、セキュリティに関連する特性や不変量について、テストを書いてください。

- [効果的なRustのcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) ：
  
  - [アップグレードのテスト](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades)。
  - [コードをターゲットに依存しない](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent)ようにします。

- [システム API 呼び出しがある場合でも、canister のコードをテストして](rust-canister-development-security-best-practices#test-your-canister-code)ください。

- [テストについては、DFINITY Rust ガイドラインを](https://docs.dfinity.systems/dfinity/spec/meta/rust.html#_tests)参照してください。

- wasmレベルの単体テストには、[Motoko Matchersの](https://github.com/kritzcreek/motoko-matchers)使用を検討してください。

- Motoko-level のユニットテストについては、[ canister モジュールを](https://kritzcreek.github.io/motoko-matchers/Canister.html)検討してください。[ここと](https://github.com/dfinity/motoko-base/blob/master/test/resultTest.mo) [ここにも](https://github.com/dfinity/motoko-base/blob/master/test/textTest.mo)テスト例があります。例として、[インボイスの](https://github.com/dfinity/invoice-canister)エンドツーエンドテストとユニットテスト[ canister も参照してください。](https://github.com/dfinity/invoice-canister)

- 長期にわたるテストシナリオについては、[Motoko BigTest](https://github.com/matthewhammer/motoko-bigtest) を検討してください。

### 本番環境でのテストコードと開発コードは避けてください

#### セキュリティ上の懸念

開発またはテストセットアップにのみ使用されるコードパスをプロダクションコードに含めるのは危険です。何か問題が発生した場合（そして、時には発生します！）、本番環境にセキュリティバグをもたらすかもしれません。

例えば、認証を検証するための公開鍵が、信頼できないソースから取得された問題を見たことがあります。

#### 推奨

可能な限り、本番環境でのテストコードや開発コードを避けてください。

<!---

# General security best practices

## Overview

This document contains information regarding general security best practices.

## Certify query responses if they are relevant for security

### Security concern

The responses to [query calls](/references/ic-interface-spec.md#https-interface) (as opposed to update calls) are not threshold-signed by the canister/subnet. Thus, a single malicious replica or boundary node may change the data, violating its authenticity. This is especially risky if update calls depend on the response to query calls.

### Recommendation

-   All security-relevant query response data that needs authenticity guarantees (this needs to be assessed for each dApp) should be certified by the IC using certified variables. Consider using existing data structures such as [certified-map](https://github.com/dfinity/cdk-rs/tree/main/library/ic-certified-map). The data certification must be validated in the frontend.

-   Alternatively, these calls have to be issued as update calls by the caller (e.g. in agent-js), but that impacts performance: it takes a few seconds. Note that every query can also be issued as an update by the caller.

-   Examples are asset certification in [Internet Identity](https://github.com/dfinity/internet-identity/blob/b29a6f68bbe5a49d048e12bc7a3263a9f43d080b/src/internet_identity/src/main.rs#L775-L808), [NNS dapp](https://github.com/dfinity/nns-dapp/blob/372c3562127d70c2fde059bc9c268e8ae858583e/rs/src/assets.rs#L121-L145), or the [canister signature implementation in Internet Identity](https://github.com/dfinity/internet-identity/blob/main/src/internet_identity/src/signature_map.rs).

### Data confidentiality on the Internet Computer

#### Security concern

When storing data on the Internet Computer, there are two levels of data access.

1. Nodes are able to read all data that is stored on a subnet. This includes all messages sent to or from a canister, along with all data stored in a canister. This means a node could extract all data available to a canister. This will change with the implementation of TEE-based security for nodes.

2. End user clients can only access whatever data that nodes and canisters have made available to them. If the subnet's nodes do not misbehave and leak data, clients can only read the responses to ingress messages and queries that they have sent. The canister decides what data is exposed to the client. 

Partial information on data that is stored in the subnet state tree will always leak. Therefore, data with a low-entropy value may entirely leak and be fully exposed, such as a Boolean value that can only be either "True" or "False". Leakage on data with a high-entropy is negligible. 

There are two types of user-related data that may be stored in the subnet state tree. The first is when a user sends an ingress message to a canister, the message hash and the response are both stored in the subnet state tree to be retrieved securely by the client. The ingress message should contain a high-entropy nonce that is implemented by the agent and typically not exposed to the user. The message response is determined by the canister and may not contain a high-entropy value. If the canister response consists of a low-entropy value, then the data may be leaked to users other than the ingress message sender. 

The second type of user-related data is certified variables maintained by a canister that are also exposed through the subnet state tree. If a canister places low-entropy data into the state tree, then the data may leak to users who should not have access to that piece of data. 

#### Recommendation 

For developers that need to protect the confidentiality of their data against external users, they should ensure that data in the subnet state tree has a sufficient level of entropy. 128 bits is recommended. If the data does not have enough entropy itself, then adding some artificial data using randomness would be recommended. 

In particular, a canister can ensure that responses to ingress messages do not leak data to external users, other than the sender, by including high-entropy data in the response. Or, a canister can ensure that data in certified variables is not leaked by adding high-entropy data to the variables that should be kept confidential. 

Additionally, similarly to ingress message responses, a canister's private custom sections that contain low-entropy data could leak to unauthorized users. Therefore, a sufficent level of entropy for canister private custom sections should be used. 128 bits is recommended. If the data does not have enough entropy itself, then adding some artificial data using randomness would be recommended. 

## Nonspecific to the Internet Computer

The best practices in this section are very general and not specific to the Internet Computer. This list is by no means complete and only lists a few very specific concerns that have led to issues in the past.

### Don’t use third-party components with known vulnerabilities

#### Security concern

Using vulnerable and outdated components is a [big security risk](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/).

#### Recommendation

-   Regularly check your third party components against databases of known vulnerabilities:

    -   Rust: use [cargo audit](https://crates.io/crates/cargo-audit).

    -   JavaScript / NPM: use [npm audit](https://docs.npmjs.com/cli/v8/commands/npm-audit).

-   This should be integrated into the build process, the build should fail if there are known vulnerabilities.

-   Don’t use forked versions of repositories that are not maintained and may not be trustworthy.

-   Avoid using third party components that are not widely used and may not have had sufficient (ideally third party) review.

-   Pin the versions of the components you are using to avoid switching to corrupt updates automatically.

### Don’t implement crypto yourself

#### Security concern

It is easy to make mistakes when implementing cryptographic algorithms, leading to security bugs.

#### Recommendation

-   Use well known libraries that may be open source and have been reviewed by many people. For example, use the [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) in JavaScript or use crates such as [sha256](https://crates.io/crates/sha256) in Rust.

### Use secure cryptographic schemes

#### Security concern

Some cryptographic schemes have been broken (old TLS versions, MD5, SHA1, DES, …​), or they could be so new that they have not yet been appropriately researched. Using these introduces security issues.

#### Recommendation

If you need to use crypto, only use cryptographic schemes that have not been broken and do not have known issues. Ideally use algorithms that have been standardized by e.g. NIST or IETF.

References:

-   [OWASP Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)

### Test your code

#### Security concern

Having small test coverage is risky, as code changes become difficult and may violate correctness and security properties, leading to bugs. It is hard to verify correctness and security properties in reviews (and security reviews) if there are no corresponding tests.

#### Recommendation

Write tests for canister implementations and frontend code, especially for security relevant properties and invariants.

-   In [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html): 
    - [Test upgrades](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades).
    - [Make code target-independent](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent).

-   See also [test your canister code even in presence of System API calls](rust-canister-development-security-best-practices#test-your-canister-code).

-   Consider the [DFINITY Rust guidelines on testing](https://docs.dfinity.systems/dfinity/spec/meta/rust.html#_tests).

-   For wasm-level unit testing, consider using [Motoko Matchers](https://github.com/kritzcreek/motoko-matchers).

-   For Motoko-level unit testing, consider [the canister module](https://kritzcreek.github.io/motoko-matchers/Canister.html). There are also some example tests [here](https://github.com/dfinity/motoko-base/blob/master/test/resultTest.mo) and [here](https://github.com/dfinity/motoko-base/blob/master/test/textTest.mo). As an example see also the end-to-end tests and unit tests for the [invoice canister](https://github.com/dfinity/invoice-canister).

-   For long-running test scenarios, consider [Motoko BigTest](https://github.com/matthewhammer/motoko-bigtest).

### Avoid test and dev code in production

#### Security concern

It is risky to include code paths in production code that are only used for development or testing setups. If something goes wrong (and it sometimes does!), this may introduce security bugs in production.

For example, we have seen issues where the public key to verify certification was fetched from an untrusted source, since this is what is done on test networks.

#### Recommendation

Avoid test and dev code in production code whenever possible.

-->
