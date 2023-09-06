---

sidebar_position: 1
---
# プログラミング言語の選択

## 概要

[canister スマート・コントラクトを](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts)作成するには、SDKを使用するのが一般的です。[IC SDKは](../setup/install/index.mdx#sdk-vs-cdk-vs-dfx)一般的なエントリーポイントです。IC SDKはいくつかのプログラミング言語に対応しています。

Internet Computer ブロックチェーンは標準WebAssembly モジュールにコンパイルされたdapps をサポートしているため、さまざまなプログラミング言語を使用して ICcanister スマートコントラクトを作成できます。特定のプログラミング言語でcanister を構築するには、その言語用の[canister 開発キット（CDK](../setup/install/index.mdx#sdk-vs-cdk-vs-dfx)）が必要です。CDKはIC SDKが使用するアダプタで、canisters の作成と管理に必要な機能を備えたプログラミング言語を提供します。簡単に始められるように、IC SDKにはすでに複数の言語のCDKが付属しています。

理論的には、WebAssembly モジュールにコンパイルできる言語であれば、[ICP](../../references/ic-interface-spec.md)スマートコントラクトとしてデプロイ可能な[ICに合わせた](../../references/ic-interface-spec.md)モジュールを作成できます。

実際には、異なる言語に対するCDKとライブラリのサポートの量はICP開発者のエコシステムによって異なるため、この記事では開発者になるための一般的な経路を示します。

最も一般的に使用される言語は以下の通りです：

- **Motoko**
  - [Motoko](/motoko/main/motoko.md)は、Internet Computer のユニークな機能をサポートし、親しみやすく堅牢なプログラミング環境を提供するために、DFINITY によって[特別に設計さ](https://stackoverflow.blog/2020/08/24/motoko-the-language-that-turns-the-web-into-a-computer/)れました。
  - DFINITY による[IC SDK](https://github.com/dfinity/sdk)を介してMotoko を使用することができます。
  - [ Motoko のcanisters 開発入門を](./motoko/index.md)ご覧ください。
  - ウェブベースの[Motoko プレイグラウンドを](https://m7sm4-2iaaa-aaaab-qabra-cai.ic0.app)使えば、Motoko の感覚をつかむことができます。
- **Rust**
  - Rust は、[IC SDK](https://github.com/dfinity/sdk)(開発者向けの一般的なパス) または[Rust CDK](https://github.com/dfinity/cdk-rs)(DFINITY) を使って使用できます。SDKとCDKの違いについては、こちらをご覧ください：SDKとCDKの違いについては、[SDK vs CDKを](../setup/install/index.mdx##SDK-vs-CDK)参照してください。
  - [Rustでの開発入門canisters ](./rust/index.md) を参照してください。
- **Python**
  - Pythonは、Web開発、データ分析、AI向けの読みやすく汎用性の高い言語です。
  - Pythonは[Demergent Labsの](https://github.com/demergent-labs) [Kybra](https://demergent-labs.github.io/kybra)CDKから利用できます。
- **TypeScript**
  - TypeScriptは[Demergent](https://github.com/demergent-labs) Labsの[Azle](https://demergent-labs.github.io/azle)CDKから利用できます。
- **Solidity**
  - Solidityはスマートコントラクトを記述するためのオブジェクト指向プログラミング言語です。様々なブロックチェーンプラットフォーム、特にEthereum上でのスマートコントラクトの実装に使用されています。
  - Internet Computer 、[Bitfinity](https://docs.bitfinity.network/) [EVMチームによる](https://bitfinity.network) [Bitfinityドキュメントを通じて](https://docs.bitfinity.network/)Solidityのサポートが利用可能になり、EVMベースのスマートコントラクトを作成する機能が提供されます。
  - これは、EthereumとSolidityに精通している開発者がInternet Computer'の機能を活用するための新しい可能性を開きます。
- **C++**
  - icpp Worldによる[icpp-pro](https://docs.icpp.world/)CDKを介してC++を使用できます。
  - [C++ でのcanisters の開発入門を](https://docs.icpp.world/getting-started.html)参照してください。

複数の言語で作業を分割することも可能です。異なるcanister スマートコントラクトは、[Candid](./candid/index.md)言語（ICPスマートコントラクトで一般的に使用される[IDL](https://en.wikipedia.org/wiki/Interface_description_language)）を使用して互いに会話します。ただし、Candidインターフェースの背後でどの言語が動作するかは問題ではありません。

## Motoko と Rust の比較

Motoko に興味を持つ開発者のために、Motoko と Rust（Web3 で人気のある言語）の比較を示します。

Motoko と Rust のどちらを選ぶかを決める際の経験則として：

- Motoko Rust は、アプリケーション開発者にとって、習得しやすく、人間工学的に使いやすい言語です。アプリケーションレイヤー（JavaScript、Ruby、Python、Solidityなど）のバックグラウンドを持つ開発者にとって馴染みのある構文とルールを持っています。 、サンプル、デモ、または小規模な契約を迅速に出荷するのに適していますが、そのライブラリエコシステムはまだ初期のものであるため、大規模なプロジェクトには困難が伴う可能性があります。Motoko 

- Rustは、Rustをすでに知っている人、システムエンジニアリング（CやC++）のバックグラウンドを持っている人、大規模なプロジェクトやより複雑なプロジェクトを始める人など、ライブラリのエコシステムを構築することが役に立つ、または重要な場合に適しています。

より詳細な比較については、次をお読みください。

### Internet Computer をご覧ください：

|  | Motoko | Rust |
| --- | --- | --- |
| 候補サポート | 完全自動。サポートはコンパイラとランタイム・システムに組み込まれています。 | ライブラリサポート。定期的に手動による介入/変換が必要。 |
| 安定したメモリサポート | 自動。パフォーマンスはまだ理想的ではありません。言語をバイパスすることは可能ですが、エラーが発生しやすい。 | ライブラリサポート。簡単な場合は自動、そうでない場合は手動実装が必要。Motoko よりも予測可能。 |
| 非同期データと制御フローのサポート | ネイティブ | ネイティブ |
| Actor パラダイムサポート ( = )canister actor | ネイティブ | エラーが発生しやすく、根深い言語機能（例：ボローチェッカー）と競合。 |
| IC固有の静的解析 | 様々な安全性チェックを実施。 | 静的チェックなし。Canisters 制限違反時にトラップする可能性あり。 |

### WebAssembly を考慮：

|  | Motoko | Rust |
| --- | --- | --- |
| Wasmのバイナリサイズ | 非常に小さい。 | 非常に大きいため、コンパクト化ツールが必要。 |
| Wasmのパフォーマンス | ベンチマーク未定 | ベンチマーク未定 |

### その他の考慮事項

|  | Motoko | Rust |
| --- | --- | --- |
| 言語の成熟度 | まだ成熟していません。 | 十分に成熟。しっかりしたライブラリサポート。 |
| ビルド時間 | Rustより速い。 | Motoko より遅い。 |
| 学習の難易度 | あまり難しくないです。 | かなり複雑で、考慮すべき詳細がたくさんあります。 |
| メモリ管理 | 自動GC（ガベージコレクション）。 | コンパイラによる強力なサポート。 |
| 外部関数インターフェースのサポート | まだありません。 | 典型的なCのFFI互換性。 |

<!---

# Choosing a programming language

## Overview

To create [canister smart contracts](https://internetcomputer.org/how-it-works/architecture-of-the-internet-computer/#canister-smart-contracts) it is common practice to use an SDK. The [IC SDK](../setup/install/index.mdx#sdk-vs-cdk-vs-dfx) is a common entry point. The IC SDK supports a few programming languages out of the box.

Because the Internet Computer blockchain supports dapps compiled to standard WebAssembly modules, one can use many different programming languages to create IC canister smart contracts. To build a canister with a particular programming language, one needs a [canister development kit (CDK)](../setup/install/index.mdx#sdk-vs-cdk-vs-dfx) for their particular language. A CDK is an adapter used by the IC SDK that provides a programming language with the features necessary to create and manage canisters. To make starting easier, the IC SDK already comes with CDK for multiple languages.

In theory, any language that can be compiled into a WebAssembly module, can produce modules [tailored for the IC](../../references/ic-interface-spec.md) deployable as an ICP smart contract.

In practice, the amount of CDK and library support for different languages varies across the ICP developer ecosystem, so this article lays out common paths for entering developers. 

The most common languages to use are:

- **Motoko**
  - [Motoko](/motoko/main/motoko.md) was [specifically designed](https://stackoverflow.blog/2020/08/24/motoko-the-language-that-turns-the-web-into-a-computer/) by DFINITY to support the unique features of the Internet Computer and to provide a familiar yet robust programming environment.
  - One can use Motoko via the [IC SDK](https://github.com/dfinity/sdk) by DFINITY.
  - See [introduction to developing canisters in Motoko](./motoko/index.md).
  - You can get a sense of Motoko by using the web-based [Motoko playground](https://m7sm4-2iaaa-aaaab-qabra-cai.ic0.app).
- **Rust**
  - One can use Rust via the either the [IC SDK](https://github.com/dfinity/sdk) (typical path for developers) or use the [Rust CDK](https://github.com/dfinity/cdk-rs) by DFINITY. To see difference between SDK and CDK, see: [SDK vs CDK](../setup/install/index.mdx##SDK-vs-CDK).
  - See [introduction to developing canisters in Rust](./rust/index.md).
- **Python**
  - Python is a readable, versatile language for web development, data analysis, and AI.
  - You can use Python via the [Kybra](https://demergent-labs.github.io/kybra) CDK by [Demergent Labs](https://github.com/demergent-labs).
- **TypeScript**
   - You can use TypeScript via the [Azle](https://demergent-labs.github.io/azle) CDK by [Demergent Labs](https://github.com/demergent-labs).
- **Solidity**
  - Solidity is an object-oriented programming language for writing smart contracts. It is used for implementing smart contracts on various blockchain platforms, most notably Ethereum.
  - The Solidity support on Internet Computer is available via the [Bitfinity docs](https://docs.bitfinity.network/) by the [Bitfinity EVM team](https://bitfinity.network), providing the ability to create EVM-based smart contracts.
  - This opens up new possibilities for developers familiar with Ethereum and Solidity to leverage the Internet Computer’s capabilities.
- **C++**
  - You can use C++ via the [icpp-pro](https://docs.icpp.world/) CDK by icpp World.
  - See [Introduction to developing canisters in C++](https://docs.icpp.world/getting-started.html)

It is also possible to split your work between multiple languages. Different canister smart contracts talk to each other using the [Candid](./candid/index.md) language (an [IDL](https://en.wikipedia.org/wiki/Interface_description_language) used commonly in ICP smart contracts). What language works behind the Candid interface, however, does not matter.

## A comparison between Motoko and Rust

To help developers interested in Motoko, here is a comparison of Motoko and Rust (a popular language in Web3). 

As a rule of thumb in deciding between Motoko and Rust:

* Motoko is much easier to learn and ergonomic to use for application developers. It has syntax and rules familiar to developers with a background in the application layer (JavaScript, Ruby, Python, Solidity, etc...). Motoko is good for getting sample, demo, or smaller contracts shipped quickly, but its library ecosystem is still early so can prove challenging for larger projects.

* Rust is a good place for those who already know Rust, come from a systems engineering background (C, C++), or are starting larger or more complex projects where having a baked library ecosystem is helpful or important.

For a more in-depth comparison, read on.

### Internet Computer considerations:

|                   | Motoko          | Rust        |
|-------------------|-----------------|-------------|
| Candid support | Fully automatic. Support is built into the compiler and runtime system. | Library-supported. Regularly needs manual intervention/conversion. |
| Stable memory support | Automatic, supported by the language. Performance is not ideal yet. Bypassing the language is possible, but error-prone. | Library-supported. Automatic in simple cases, otherwise manual implementations are needed. More predictable than Motoko. |
| Asynchronous data and control flow support | Native | Native |
| Actor paradigm support (canister = actor) | Native | Error-prone, conflicts with deep-rooted language features (e.g. the borrow checker). |
| IC-specific static analysis | Enforces various safety checks. | No static checking. Canisters may trap when violating restrictions. |

### WebAssembly considerations:

|                   | Motoko          | Rust        |
|-------------------|-----------------|-------------|
| Wasm binary size | Very small. | Very large, requires compacting tools. |
| Wasm performance | Benchmarks TBD | Benchmarks TBD |

### Other considerations:

|                   | Motoko          | Rust        |
|-------------------|-----------------|-------------|
| Language maturity | Not mature yet, lots of work left to do. | Mature (enough). Solid library support. |
| Build time | Faster than Rust. | Slower than Motoko. |
| Difficulty of learning | Not very hard. | Quite complex, lots of details to consider. |
| Memory management | Automatic GC (garbage collection). | Application-specific, strong support by the compiler. |
| Foreign function interface support | None yet. | Typical C FFI compatibility. |

-->
