---

title: "Announcing the Motoko Dev Server: live-reloading for Web3 dapps"
description: Introducing mo-dev, a flexible live-reload server for quickly building and testing Motoko services on the Internet Computer.
tags: [New features]
image: /img/blog/motoko-dev-server.jpg
---
# Motoko Dev Serverを発表：Web3のライブ・リローディングdapps

[![Motoko Dev Server](/img/blog/motoko-dev-server.jpg)](https://github.com/dfinity/motoko-dev-server)

- [ミディアムポスト](https://medium.com/dfinity/announcing-the-motoko-dev-server-live-reloading-for-web3-dapps-20363088afb4)
- [開発者フォーラムのトピック](https://forum.dfinity.org/t/announcing-mo-dev-live-reloading-for-motoko-dapps/21007)
- [GitHub リポジトリ](https://github.com/dfinity/motoko-dev-server)

Internet Computer でMotoko サービスを素早く構築しテストするための柔軟なライブ・リロード・サーバー、[mo-dev](https://github.com/dfinity/motoko-dev-server) を紹介できることを嬉しく思います。

## 背景

ライブリロード（より具体的には、[ホットモジュールリプレース](https://webpack.js.org/concepts/hot-module-replacement/)）は、ウェブ開発者の生産性を大幅に向上させる技術として知られています。その仕組みについては、[このブログ記事を](https://blog.logrocket.com/complete-guide-full-stack-live-reload/)チェックすることを強くお勧めします。

これは、従来のウェブ開発の世界では解決された問題です。[Vite](https://vitejs.dev/)、[Next.js](https://nextjs.org/docs/architecture/fast-refresh)、[Webpackといった](https://webpack.js.org/configuration/dev-server/)開発サーバーのような堅牢なソリューションでは、フロントエンドのコードを1行変更するだけで、通常はページを更新することなく、ブラウザですぐに結果を確認することができます。

しかし、ブロックチェーン上で動作する分散型アプリケーション（または "dapp" ）を開発する場合、この機能はほとんど存在しません。Solidityスマートコントラクト用の[ZepKitなど](https://blog.openzeppelin.com/solidity-hot-reloading-using-zepkit)、いくつかのオプションは存在しますが、Web3ライブリロードの現在のステートでは、Web2からの高い期待を考えると、多くのことが望まれています。

-----

そこで、[Motoko プログラミング言語の](https://internetcomputer.org/docs/current/motoko/main/motoko)出番です。素早いコンパイル時間、モジュールベースのインポート、[安定した変数](https://internetcomputer.org/docs/current/motoko/main/upgrades)セマンティクスを持つMotoko は、ゲームを変えるライブリロードワークフローの理想的な候補です。

過去6ヶ月間、DFINITY 社内のMotoko プロジェクトでフルスタックのライブリロードを採用し、膨大な開発時間を節約するとともに、Internet Computer dapps のエンドユーザーエクスペリエンスを向上させるためのさまざまなアイデアを迅速に試すことができました。

これは、Motoko dapps とスマートコントラクトのライブ・リロード・ワークフローを容易にするコマンドライン・ツールである[Motoko Dev Server](https://github.com/dfinity/motoko-dev-server)（略して`mo-dev` ）を使用して実現しました。

`mo-dev` は、[Motoko Playground](https://play.motoko.org/)、[Developer Experience Feedback Board](https://dx.internetcomputer.org/)、さらには言語独自の[基本ライブラリなど](https://github.com/dfinity/motoko-base)、幅広いプロジェクトですでに使用されています。

それぞれのユースケースで必要なライブ・ロード機能は異なるため、プロジェクトに応じて選択できるスイスアーミーナイフのよう[な](https://dfinity.org/foundation/)機能を作成することにしました：

- canisters をローカルのレプリカにデプロイします。
- 言語バインディングの生成
- ユニットテストの実行 (`.test.mo` で終わるファイル)
- コマンドの実行
- 上記の任意の組み合わせ

機能の完全なリストについては、プロジェクトの GitHub リポジトリ[github.com/dfinity](https://github.com/dfinity/motoko-dev-server)/[motoko-dev-server をチェックしてください。](https://github.com/dfinity/motoko-dev-server)

`mo-dev` を使いたい理由はお分かりいただけたと思います：

## オンラインデモ

何もダウンロードせずに`mo-dev` を試してみたい方は、[ブラウザだけで実行](https://gitpod.io/#https://github.com/rvanasa/vite-react-motoko)できるオンライン・サンプル・プロジェクトをご覧ください[（ソースコード](https://github.com/rvanasa/vite-react-motoko#readme)）。

## インストール

ターミナルで以下のコマンドを実行してください（Node.js ≥ 16が必要です）：

``` sh
npm install -g mo-dev
```

または、プロジェクトの[GitHubリリースから](https://github.com/dfinity/motoko-dev-server/releases)スタンドアロンのバイナリをダウンロードすることもできます。

ツールをインストールしたら、`mo-dev --help` を実行して、使用例と各利用可能な機能の説明を表示します。

## 高潔なUI

[Candid UI](https://internetcomputer.org/docs/current/developer-docs/backend/motoko/candid-ui)を使ってMotoko スマートコントラクトを開発しているとしましょう。ファイル変更時にcanister を再デプロイするコマンドです：

``` sh
mo-dev --deploy -y
```

`-y` フラグは、canister インターフェイスのアップグレードに関する[dfx](https://internetcomputer.org/docs/current/references/cli-reference/dfx-deploy)からのプロンプトに自動的に「yes」と応答します（canister データをクリアする可能性があります）。使用するケースに応じて、このフラグを付けるか付けないかは自由です。

## フルスタックDapp

`mo-dev` は、[Viteや](https://vitejs.dev/) [Create React Appの](https://create-react-app.dev/)ような一般的なフロントエンドビルドツールとうまく動作するように特別に設計されています。

新しいプロジェクトを始めたい場合は、[Vite + React +Motoko](https://github.com/rvanasa/vite-react-motoko#readme)テンプレート（またはさらにシンプルな[プレーン JavaScript バージョン](https://github.com/rvanasa/vite-react-motoko/tree/simplified-js#readme)）の使用を検討してください。

そうでなければ、これはバックエンドをライブリロードするための良い出発点ですMotoko canister ：

``` sh
mo-dev --generate --deploy -y
```

`--generate` フラグは、Motoko ソースコードに変更を加えるたびに、JavaScript 言語バインディングを自動的に作成して更新します。

この機能を単独で使用することも可能です (`mo-dev --generate`)。

## 高度な使用法

Motoko Dev Server を既存のウェブアプリにプラグインしたい人のために、DFINITY で採用したスケーラブルな Vite プロジェクト構成を紹介します：

- プロジェクトのルートディレクトリで`npm install -D npm-run-all mo-dev` を実行します。
- 実行するフロントエンドの npm スクリプトを追加します。`vite`
- 関連するフラグを付けて`mo-dev` を実行するバックエンド npm スクリプトを追加します。
- 開始 npm スクリプトを次のように変更します。`run-p frontend backend`

このプロジェクト設定で、`npm start` は Vite とMotoko の開発サーバーをシームレスに統合されたコンソール出力で実行します。`npm run frontend` と`npm run backend` を使って出力を別々のターミナルに分けることができます。

この設定のもう一つの利点は、`mo-dev` コマンドをグローバルにインストールしなくても、誰でも開発サーバーを実行できることです。これは、オープンソースの貢献を奨励したり、チームの一員として作業したりするのに適しています。

このプロジェクト設定は、今後予定されている[dfx new](https://internetcomputer.org/docs/current/references/cli-reference/dfx-new)コマンドの変更にデフォルトで含まれます。

`mo-dev` motoko-matchers のようなサードパーティライブラリと互換性のあるテストランナーが同梱されています。

ユニットテストを登録するには、プロジェクト内の任意の場所に`*.test.mo` という拡張子のファイルを作成します。たとえば、`2vxsx-fae` が匿名[プリンシパル](https://medium.com/dfinity/internet-computer-basics-part-1-principals-and-identities-215e8f239da4)であることを保証する基本的なユニットテストです：

``` motoko
import { print } "mo:base/Debug";
import Principal "mo:base/Principal";

let myPrincipal = Principal.fromText("2vxsx-fae");

print("Anonymous?");
assert Principal.isAnonymous(myPrincipal);

print("Yep!");
```

`*.test.mo` ファイルを作成したら、次のコマンドを実行します：

``` sh
mo-dev --test
```

Motoko ファイルを作成したら、次のコマンドを実行してください。これにより、 ソースファイルを変更するたびに、すべてのユニットテストが実行されます。

`--deploy` で`--test` を使っている場合、開発サーバーはcanister を再デプロイする前に、関連するテストがすべて成功するまで待ちます。

ライブリロード機能を使わずにテストを実行したい場合は、`mo-dev` と一緒に自動的にインストールされる`mo-test` コマンドを使用します：

``` sh
mo-test --help
```

特定のテストファイルをフィルタリングするために`-f` フラグを含めることもできます：

``` sh
mo-test -f Foo # Run test files starting with "Foo"
mo-test -f Foo.test.mo # Only run test files named "Foo.test.mo"
mo-test -f Foo -f Bar # Run test files starting with "Foo" or "Bar"
```

デフォルトでは、テストランナーはMotoko インタプリタを使用します。パフォーマンスが重要な場合は、[Wasmtime を](https://wasmtime.dev/)インストールし、test コマンドに`--testmode wasi` を追加することで、コンパイル済みの[WASI](https://wasi.dev/)ランタイムに切り替えることができます。

[Motoko VS Code 拡張機能には](https://github.com/dfinity/vscode-motoko#readme)、テストランナー用の UI も含まれています：

![VS Code unit testing integration](https://user-images.githubusercontent.com/522097/219227189-71bb8d54-1904-49f2-8fe8-253b5a709e3f.png)

## 継続的インテグレーション

Motoko Dev Server は、[GitHub Actions](https://github.com/features/actions)、[Travis](https://www.travis-ci.com/)、[CircleCI](https://circleci.com/) などの[CI](https://semaphoreci.com/continuous-integration)環境で使用できます。これらの環境では、ファイルの変更を待つ代わりに`mo-dev` が自動的に終了します。

CI 環境変数を`true` または`1` に設定することで、ローカルシステムでも同じ効果を得ることができます：

``` sh
CI=true mo-dev --generate --deploy
```

## 最後に

`mo-dev` を使って作業する際に便利な雑学をいくつか紹介します：

- ライブリロードは Git のブランチを切り替えたときにも機能します。コードレビューやペアプログラミングをするときに覚えておくと便利です。
- [dfx generate](https://internetcomputer.org/docs/current/references/cli-reference/dfx-generate) を使ったことがある人なら、`dfx generate` と`dfx deploy` の両方を先に実行する必要があるという、よくあるキャッチ22に遭遇したことがあるかもしれません。`mo-dev --generate --deploy` は、この状況を自動的に処理してくれます。
- `mo-dev --exec <command>` を使って、カスタムライブリロードロジックを定義することができます。

-----

フィードバックを歓迎します！バグを見つけたり、新しい機能が欲しい場合は、[GitHub の](https://github.com/dfinity/motoko-dev-server/issues) [github.com/dfinity](https://github.com/dfinity/motoko-dev-server)/[motoko-dev-server までお気軽にご連絡ください。](https://github.com/dfinity/motoko-dev-server)

そうでなければ、私たちの[開発者体験フィードバック掲示板に](https://dx.internetcomputer.org/)リクエストを投稿することを検討してください。この掲示板は`mo-dev` で構築されています！ソースコードは[github.com/dfinity](https://github.com/dfinity/feedback)/feedback にあります。

お読みいただきありがとうございました。ありがとうございました！

<!---


# Announcing the Motoko Dev Server: live-reloading for Web3 dapps

[![Motoko Dev Server](/img/blog/motoko-dev-server.jpg)](https://github.com/dfinity/motoko-dev-server)

* [Medium post](https://medium.com/dfinity/announcing-the-motoko-dev-server-live-reloading-for-web3-dapps-20363088afb4)
* [Developer forum topic](https://forum.dfinity.org/t/announcing-mo-dev-live-reloading-for-motoko-dapps/21007)
* [GitHub repository](https://github.com/dfinity/motoko-dev-server)

We are excited to introduce [mo-dev](https://github.com/dfinity/motoko-dev-server), a flexible live-reload server for quickly building and testing Motoko services on the Internet Computer.

## Background

Live reloading (or more specifically, [hot module replacement](https://webpack.js.org/concepts/hot-module-replacement/)) is a well-established technology known to massively improve the productivity of web developers. I highly recommend checking out [this blog post](https://blog.logrocket.com/complete-guide-full-stack-live-reload/) for a great explanation of how it works.

This is a solved problem in the world of conventional web development. Robust solutions such as the [Vite](https://vitejs.dev/), [Next.js](https://nextjs.org/docs/architecture/fast-refresh), and [Webpack](https://webpack.js.org/configuration/dev-server/) dev servers make it possible to change a line of front-end code and immediately see the result in your browser, usually without even refreshing the page.

However, this feature is almost nonexistent when developing a decentralized application (or “dapp”) running on a blockchain. Several options exist — such as [ZepKit](https://blog.openzeppelin.com/solidity-hot-reloading-using-zepkit) for Solidity smart contracts — but the current state of Web3 live reloading leaves much to be desired given the sky-high expectations from Web2.

---

This is where the [Motoko programming language](https://internetcomputer.org/docs/current/motoko/main/motoko) comes in. With quick compilation times, module-based imports, and [stable variable](https://internetcomputer.org/docs/current/motoko/main/upgrades) semantics, Motoko is the ideal candidate for a game-changing live reload workflow.

Over the past six months, DFINITY has adopted full-stack live reloading in our internal Motoko projects, saving a huge amount of development time and allowing us to quickly try lots of different ideas to improve the end-user experience of our Internet Computer dapps.

This was made possible using the [Motoko Dev Server](https://github.com/dfinity/motoko-dev-server) (or `mo-dev` for short), a command-line tool which facilitates a live-reload workflow for Motoko dapps and smart contracts.

`mo-dev` is already used in a wide range of projects such as the [Motoko Playground](https://play.motoko.org/), [Developer Experience Feedback Board](https://dx.internetcomputer.org/), and even the language’s own [base library](https://github.com/dfinity/motoko-base).

Each use case requires different live-reload capabilities, so we decided to create a Swiss Army knife ([if you will](https://dfinity.org/foundation/)) of features which you can select depending on your project:

* Deploy canisters to the local replica
* Generate language bindings
* Run unit tests (files ending with `.test.mo`)
* Execute commands
* Any combination of the above

For a complete list of features, check out the project’s GitHub repository at [github.com/dfinity/motoko-dev-server](https://github.com/dfinity/motoko-dev-server).

Now that you know why you’d want to use `mo-dev`, here are a few ways to get started:

## Online Demo

If you’re curious to try `mo-dev` without downloading anything, here’s an online example project which you can [run entirely in your browser](https://gitpod.io/#https://github.com/rvanasa/vite-react-motoko) ([source code](https://github.com/rvanasa/vite-react-motoko#readme)).

## Installation

Run the following command in your terminal (requires Node.js ≥ 16):

```sh
npm install -g mo-dev
```

Alternatively, you can download a standalone binary from the project’s [GitHub releases](https://github.com/dfinity/motoko-dev-server/releases).

Once you’ve installed the tool, run `mo-dev --help` to view usage examples and descriptions for each available feature.

## Candid UI

Let’s say you’re developing a Motoko smart contract using the [Candid UI](https://internetcomputer.org/docs/current/developer-docs/backend/motoko/candid-ui). Here’s a command which will redeploy the canister on file change:

```sh
mo-dev --deploy -y
```

The `-y` flag automatically responds “yes” to prompts from [dfx](https://internetcomputer.org/docs/current/references/cli-reference/dfx-deploy) about upgrading the canister interface (potentially clearing canister data). Feel free to include or omit this depending on your use case.

## Full-Stack Dapp

`mo-dev` is specifically designed to play well with popular front-end build tools such as [Vite](https://vitejs.dev/) and [Create React App](https://create-react-app.dev/).

If you want to start a new project, consider using the [Vite + React + Motoko](https://github.com/rvanasa/vite-react-motoko#readme) template (or the even simpler [plain JavaScript version](https://github.com/rvanasa/vite-react-motoko/tree/simplified-js#readme)).

Otherwise, this is a good starting point for live reloading a back-end Motoko canister:

```sh
mo-dev --generate --deploy -y
```

The `--generate` flag automatically creates and updates JavaScript language bindings whenever you make changes to your Motoko source code.

It’s also possible to use this feature by itself (`mo-dev --generate`).

## Advanced Usage

For those wanting to plug the Motoko Dev Server into an existing webapp, here is a scalable Vite project configuration we’ve adopted at DFINITY:

* Run `npm install -D npm-run-all mo-dev` in your project’s root directory
* Add a frontend npm script which runs `vite`
* Add a backend npm script which runs `mo-dev` with relevant flags
* Change the start npm script to `run-p frontend backend`

With this project configuration, `npm start` will run the Vite and Motoko dev servers with a seamlessly integrated console output. You can split the outputs via `npm run frontend` and `npm run backend` in separate terminals.

Another benefit of this configuration is that anyone can run the dev server without needing to globally install the `mo-dev` command, which is great for encouraging open-source contributions or working as part of a team.

This project configuration will be included by default in the upcoming changes to the [dfx new](https://internetcomputer.org/docs/current/references/cli-reference/dfx-new) command.

`mo-dev` ships with a test runner compatible with third-party libraries such as motoko-matchers.

To register a unit test, create a file with the extension `*.test.mo` anywhere in your project. For example, here is a basic unit test which asserts that `2vxsx-fae` is the anonymous [principal](https://medium.com/dfinity/internet-computer-basics-part-1-principals-and-identities-215e8f239da4):

```motoko
import { print } "mo:base/Debug";
import Principal "mo:base/Principal";

let myPrincipal = Principal.fromText("2vxsx-fae");

print("Anonymous?");
assert Principal.isAnonymous(myPrincipal);

print("Yep!");
```

Once you’ve created a `*.test.mo` file, run the following command:

```sh
mo-dev --test
```

This will run all unit tests whenever you change a Motoko source file.

Note that when using `--test` with `--deploy`, the dev server will wait until all relevant tests succeed before redeploying your canister.

If you want to run tests without the live-reloading functionality, you can use the `mo-test` command which is automatically installed alongside `mo-dev`:

```sh
mo-test --help
```

You can also include the `-f` flag to filter specific test files:

```sh
mo-test -f Foo # Run test files starting with "Foo"
mo-test -f Foo.test.mo # Only run test files named "Foo.test.mo"
mo-test -f Foo -f Bar # Run test files starting with "Foo" or "Bar"
```

By default, the test runner uses the Motoko interpreter. If performance is critical, you can switch to a compiled [WASI](https://wasi.dev/) runtime by installing [Wasmtime](https://wasmtime.dev/) and adding `--testmode wasi` to the test command.

The [Motoko VS Code extension](https://github.com/dfinity/vscode-motoko#readme) also includes a UI for the test runner:

![VS Code unit testing integration](https://user-images.githubusercontent.com/522097/219227189-71bb8d54-1904-49f2-8fe8-253b5a709e3f.png)

## Continuous Integration

The Motoko Dev Server can be used in [CI](https://semaphoreci.com/continuous-integration) environments such as [GitHub Actions](https://github.com/features/actions), [Travis](https://www.travis-ci.com/), and [CircleCI](https://circleci.com/). In these environments, `mo-dev` will automatically terminate instead of waiting for file changes.

You can achieve the same effect on your local system by setting the CI environment variable to `true` or `1`:

```sh
CI=true mo-dev --generate --deploy
```

## Final Thoughts

Here are a few miscellaneous tips which might come in handy while working with `mo-dev`:

* Live reloading works when switching Git branches, which is useful to keep in mind for code reviews and pair programming.
* If you’ve used [dfx generate](https://internetcomputer.org/docs/current/references/cli-reference/dfx-generate), you might have encountered a common catch-22 where both `dfx generate` and `dfx deploy` require running each other first. `mo-dev --generate --deploy` automatically handles this situation for you.
* You can define custom live-reload logic using `mo-dev --exec <command>`.

---

Feedback is welcome! If you find a bug or want to see a new feature, please feel free to [reach out on GitHub](https://github.com/dfinity/motoko-dev-server/issues) (or give us a ⭐ to support the project) at [github.com/dfinity/motoko-dev-server](https://github.com/dfinity/motoko-dev-server).

Otherwise, consider submitting a request on our [Developer Experience Feedback Board](https://dx.internetcomputer.org/), which itself was built with `mo-dev`! If you're curious, you can find the source code at [github.com/dfinity/feedback](https://github.com/dfinity/feedback).

Thanks for reading. Cheers!

-->
