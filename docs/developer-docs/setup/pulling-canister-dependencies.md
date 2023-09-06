# canister 依存関係のプル

## 概要

IC上のcanisters の相互運用性は、多くの開発者とそのワークフローにとって不可欠な機能です。`dfx` は、サードパーティcanisters をローカルの開発者環境で作成、統合、テストするための一貫した開発者ワークフローを提供します。

サードパーティcanisters には、NNS、ledger、IIなどのDFINITY によって作成されたcanisters が含まれますが、ICコミュニティの開発者によって作成されたcanisters も含まれます。開発者は、統合するためにサードパーティcanisters に依存し、通常、以下のようなことのためにローカルで統合を開発しテストする方法を必要とします：

- 統合と他のcanister コードの正確さのテスト。
- cycles 。
- 本番環境以外のデータや環境を使用したテストの実行。
- ローカルで実行すると、完了までの時間が短縮されます。

これらのcanisters を IC メインネットから引き出し、ローカルのレプリカを使用して開発・テストするには [`dfx deps`](/docs/references/cli-reference/dfx-deps.md)コマンドとワークフローを使用できます。

このワークフローでは、**サービスプロバイダが** canister を`pullable` に設定し、canister を IC メインネットにデプロイします。サービス・プロバイダーは、DFINITY 、または公開されたサードパーティのcanister を作成するコミュニティ開発者の場合もあります。

そして、**サービス・コンシューマは** canister を依存関係としてメインネットから直接プルし、その依存関係をローカル・レプリカにデプロイすることができます。

このガイドでは、このワークフローとその使用方法について説明します。

## canister が必要かどうかの判断`pullable`

`pullable` として設定されているcanister をプルするには、サービスコンシューマはcanister ID だけが必要です。

canister を引き出す前に、まずcanister を`pullable` に設定する必要があります。さらに、canister は`pullable` である*べきかという*質問を提案しなければなりません。

#### `Pullable` の例を示します：

例：canister が静的なcanister IDで公共サービスを提供している場合、canister が`pullable` であることは理にかなっています。

あるサービスcanister が他のcanisters にも依存している場合、それらの依存関係も`pullable` であるべきです。

#### `pullable` 以外の例：

canister が個人的な使用を目的としており、他の人が使用することを意図していない場合、canister が`pullable` であることは意味がありません。

他の開発者が自分のインスタンスをデプロイするためにcanister が公開されている場合、インスタンスのcanister ID は固定ではないので、canister が`pullable` であることは意味がありません。このcanister タイプの例は、dfx が生成するアセットcanister です。

## `Pullable` dfx.jsonの例

canister を`pullable` にするには、`dfx.json` ファイルに`pullable` 定義を含める必要があります。以下はその例です：

``` json
{
  "canisters": {
    "service": {
      "type": "motoko",
      "main": "src/pullable/main.mo",
      "pullable": {
        "dependencies": [],
        "wasm_url": "https://github.com/lwshang/pullable/releases/latest/download/service.wasm",
        "init_guide": "A natural number, e.g. 1"
      }
    }
  }
}
```

:::caution
 `pullable` canister の wasm モジュールは、サービス利用者が依存関係を引き出すときにダウンロードできるように、URL を介してホストされている必要があります。

プロジェクトがGitHub上でオープンソースである場合、GitHubリリースは無料で良い選択肢です。GitHub URLスキーマは次のとおりです：

`https://github.com/<USERNAME>/<REPONAME>/releases/latest/download/<FILENAME>`

この機能の将来のバージョンでは、レプリカからの直接wasmダウンロードがサポートされるでしょう。
::：

## サービスプロバイダのワークフロー概要

まず、サービスプロバイダは`dfx.json` ファイルでcanister を`pullable` に設定する必要があります。

`pullable` "service"canister を持つプロバイダ`dfx.json` の例を以下に示します：

``` json
{
    "canisters": {
        "service": {
            "type": "motoko",
            "main": "src/main.mo",
            "pullable": {
                "wasm_url": "http://example.com/a.wasm",
                "wasm_hash": "d180f1e232bafcee7d4879d8a2260ee7bcf9a20c241468d0e9cf4aa15ef8f312",
                "dependencies": [
                    "yofga-2qaaa-aaaaa-aabsq-cai"
                ],
                "init_guide": "A natural number, e.g. 10."
            }
        }
    }
}
```

`pullable` オブジェクトは`dfx` メタデータの一部としてシリアライズされ、wasm に添付されます。

`pullable` オブジェクトをよりよく理解するために、各プロパティを詳しく見てみましょう。

- `wasm_url`:ローカルにデプロイされるcanister wasm モジュールをダウンロードするための URL。
- `wasm_hash`:`wasm_url` にある wasm モジュールの SHA256 ハッシュ。このフィールドはオプションです。ほとんどの場合、`wasm_url` にある wasm モジュールはオンチェーンの wasm モジュールと同じです。つまり、dfx はモジュールハッシュを取得・検証するためにステートツリーを読むことができます。場合によっては、`wasm_url` のwasmモジュールはオンチェインのwasmモジュールと同じではありません。例えば、Internet Identitycanister はローカルに統合される`development` バリアントを提供します。このような場合、`wasm_hash` は期待されるハッシュを提供し、`dfx` はダウンロードされた wasm をこれと照合して検証します。

:::caution
 `wasm_url` にある wasm モジュールの`wasm_hash` が一致しない場合、dfx はハッシュの不一致を示すエラーメッセージとともに中断します。この場合、サービス利用者はサービス提供者に連絡してください。
正しいwasmモジュールを`wasm_url` からダウンロードできるようにするのはサービスプロバイダの責任です：

- `dependencies`:直接依存するCanister ID (`Principal`) の配列。
- `init_guide`:コンシューマがcanister を初期化する方法を示すメッセージ。

### Canister メタデータ要件

本番環境またはメインネット上で動作する本番環境で使用されるサービスプロバイダcanister は、公開された`dfx` メタデータを持つ必要があります。

`wasm_url` からダウンロードしたcanister ワームは、以下のメタデータ（パブリックまたはプライベート）を持つ必要があります：

- `candid:service`
- `candid:args`
- `dfx`

すべてのメタデータ・セクションは、canister がビルドされるときに`dfx` によって処理されます。

### デプロイプロセス

サービスプロバイダは、`pullable` canister をデプロイするために、以下のデプロイプロセスを使用します。

- #### ステップ 1: コマンドを使用してcanister をメインネットにデプロイします：

<!-- end list -->

    dfx deploy --network ic

- #### ステップ 2: GitHub を使用している場合は、`git tag` と`GitHub release` をコマンドでデプロイします：

<!-- end list -->

    git tag 0.1.0
    git push --tags

[このガイドに従って](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release)リリースを作成できます。

- #### ステップ 3: リリースアセットに wasm をアタッチします。

リリースを編集し、デプロイされた wasm をリリースアセットとしてアタッチします。

デプロイされた wasm ファイルは以下の場所にあります：

    .dfx/ic/canisters/<CANISTER_NAME>/<CANISTER_NAME>.wasm

### CI でのサービスプロバイダプロセスの自動化

[CI の設定例では](https://github.com/lwshang/pullable/blob/main/.github/workflows/release.yml)、GitHub Action を使って上記のデプロイルーチンを自動化する方法を示します。

CI によるワークフローは次のようになります：

1.  git タグをプッシュし、GitHub リリースが完了するのを待ちます。
2.  リリースアセット (`wget https://github.com/lwshang/pullable/releases/latest/download/service.wasm`) からcanister wasm をダウンロードします。
3.  ダウンロードした wasm (`dfx canister --network ic install service --wasm service.wasm --argument '(1 : nat)' --mode upgrade`) を使ってcanister をインストール（アップグレード）します。

## サービス・コンシューマー・ワークフローの概要

以下のワークフローは、サービスコンシューマが`pullable` canister を依存関係としてプルするために使用できます。

### ステップ 1:`dfx.json` で依存関係を "pull" 宣言します。

まず、メインネットから依存関係をプルするには、`dfx.json` ファイルに、canister 用の`dependencies` 構成を含める必要があります。

サービスコンシューマが "dapp" という名前のcanister を開発しており、2 つのプル依存関係を持つ`dfx.json` の例を以下に示します：

- 「dep\_b」は、メインネット上のcanister ID が`yhgn4-myaaa-aaaaa-aabta-cai` です。
- canister dep\_b "のメインネット上のIDは`yahli-baaaa-aaaaa-aabtq-cai` です。"dep\_c "のメインネット上のIDは です。

<!-- end list -->

``` json
{
    "canisters": {
        "dapp": {
            "type": "motoko",
            "main": "src/main.mo",
            "dependencies": [
                "dep_b", "dep_c"
            ]
        },
        "dep_b": {
            "type": "pull",
            "id": "yhgn4-myaaa-aaaaa-aabta-cai"
        },
        "dep_c": {
            "type": "pull",
            "id": "yahli-baaaa-aaaaa-aabtq-cai"
        }
    }
}
```

### ステップ 2:`dfx deps pull` コマンドを使用して依存関係をプルします。

`dfx deps pull` コマンドを実行すると、以下のことが行われます：

1.  まず、`dfx` メタデータの`dependencies` フィールドを再帰的にフェッチすることで、依存関係グラフを解決します。
2.  次に、`wasm_url` からすべての直接および間接依存関係の wasm を共有キャッシュにダウンロードします。
3.  次に、ダウンロードされたwasmのハッシュが、`wasm_hash` メタデータまたはメインネットにデプロイされたcanister のハッシュと照合されます。
4.  次に、ダウンロードされた wasm から`candid:args`,`candid:service`,`dfx` メタデータが抽出されます。
5.  `deps/` フォルダがプロジェクトルートに作成されます。
6.  直接依存する`candid:service` は`deps/candid/<CANISTER_ID>.did` として保存されます。
7.  すべての直接・間接依存関係の主要な情報を含む`deps/pulled.json` が保存されます。

サンプルプロジェクトでは、`deps/` ：

- `yhgn4-myaaa-aaaaa-aabta-cai.did` と : " " でインポートできる候補ファイルがあります。`yahli-baaaa-aaaaa-aabtq-cai.did`dapp
- `pulled.json`:以下の内容のjsonファイル：

<!-- end list -->

``` json
{
  "canisters": {
    "yofga-2qaaa-aaaaa-aabsq-cai": {
      "dependencies": [],
      "wasm_hash": "e9b8ba2ad28fa1403cf6e776db531cdd6009a8e5cac2b1097d09bfc65163d56f",
      "init_guide": "A natural number, e.g. 10.",
      "candid_args": "(nat)"
    },
    "yhgn4-myaaa-aaaaa-aabta-cai": {
      "name": "dep_b",
      "dependencies": [
        "yofga-2qaaa-aaaaa-aabsq-cai"
      ],
      "wasm_hash": "f607c30727b0ee81317fc4547a8da3cda9bb9621f5d0740806ef973af5b479a2",
      "init_guide": "No init arguments required",
      "candid_args": "()"
    },
    "yahli-baaaa-aaaaa-aabtq-cai": {
      "name": "dep_c",
      "dependencies": [
        "yofga-2qaaa-aaaaa-aabsq-cai"
      ],
      "wasm_hash": "016df9800dc5760785646373bcb6e6bb530fc17f844600991a098ef4d486cf0b",
      "init_guide": "A natural number, e.g. 20.",
      "candid_args": "(nat)"
    }
  }
}
```

このファイルには3つの依存関係があります：

- `yhgn4-myaaa-aaaaa-aabta-cai`:`dfx.json` の "dep\_b" 。
- `yahli-baaaa-aaaaa-aabtq-cai`:`dfx.json` の "dep\_c" .
- `yofga-2qaaa-aaaaa-aabsq-cai`: "dep\_b "と "dep\_c "の両方が依存する間接的な依存関係。

:::caution
`dfx deps pull` はデフォルトでICメインネット(`--network ic`)に接続します。`--network local`.
:: のように、他のネットワークを選択することもできます：

### ステップ3: init引数の設定`dfx deps init`

コマンド`dfx deps init` を実行すると、`pulled.json` ファイル内のすべての依存関係を繰り返し、`init` 引数を必要としないものには空の引数を設定します。そして、`init` 引数を必要とする依存関係のリストを表示します。

コマンド`dfx deps init <CANISTER> --argument <ARGUMENT>` を実行すると、個々の依存関係に対して`init` 引数が設定されます。init 引数は`deps/init.json` に記録されます。

上記の例を使用して、以下のコマンドを実行できます：

- init 引数を設定するには：

<!-- end list -->

    dfx deps init
    WARN: The following canister(s) require an init argument. Please run `dfx deps init <NAME/PRINCIPAL>` to set them individually:
    yofga-2qaaa-aaaaa-aabsq-cai
    yahli-baaaa-aaaaa-aabtq-cai (dep_c)

- 引数なしで個々の依存関係の`init` 引数を設定しようとすると、以下のエラーが発生します：

<!-- end list -->

    dfx deps init yofga-2qaaa-aaaaa-aabsq-cai
    Error: Canister yofga-2qaaa-aaaaa-aabsq-cai requires an init argument. The following info might be helpful:
    init_guide => A natural number, e.g. 10.
    candid:args => (nat)

    dfx deps init deps_c
    Error: Canister yahli-baaaa-aaaaa-aabtq-cai (dep_c) requires an init argument. The following info might be helpful:
    init_guide => A natural number, e.g. 20.
    candid:args => (nat)

- init引数に`--argument` フラグを使用した引数を設定するには、以下のコマンドを使用できます：

<!-- end list -->

    dfx deps init yofga-2qaaa-aaaaa-aabsq-cai --argument 10
    dfx deps init deps_c --argument 20

結果として生成されるファイル`init.json` は以下の内容になります：

``` json
{
  "canisters": {
    "yofga-2qaaa-aaaaa-aabsq-cai": {
      "arg_str": "10",
      "arg_raw": "4449444c00017d0a"
    },
    "yhgn4-myaaa-aaaaa-aabta-cai": {
      "arg_str": null,
      "arg_raw": null
    },
    "yahli-baaaa-aaaaa-aabtq-cai": {
      "arg_str": "20",
      "arg_raw": "4449444c00017d14"
    }
  }
}
```

### ステップ4:`dfx deps deploy` コマンドを使用して、プルされた依存関係をローカルレプリカにデプロイします。

`dfx deps deploy` コマンドを実行します：

1.  まず、同じメインネットcanister ID を持つローカルレプリカ上に依存関係を作成します。
2.  次に、`init.json` ファイルの init 引数を使ってダウンロードした wasm をインストールします。

特定の依存関係をデプロイするために名前やプリンシパルを指定することもできます。

上記の例を使うと、以下のコマンドを実行して全ての依存関係をデプロイすることができます：

    dfx deps deploy
    Creating canister: yofga-2qaaa-aaaaa-aabsq-cai
    Installing canister: yofga-2qaaa-aaaaa-aabsq-cai
    Creating canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)
    Installing canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)
    Creating canister: yahli-baaaa-aaaaa-aabtq-cai (dep_c)
    Installing canister: yahli-baaaa-aaaaa-aabtq-cai (dep_c)

特定の 1 つの依存関係をデプロイするには、以下のコマンドを使用できます：

    dfx deps deploy yofga-2qaaa-aaaaa-aabsq-cai
    Installing canister: yofga-2qaaa-aaaaa-aabsq-cai

    dfx deps deploy dep_b
    Installing canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)

:::info
`dfx deps deploy` は、常に匿名 ID でcanister を作成し、依存関係とアプリケーションcanisters が異なるコントローラになるようにします。また、canister の状態が破棄されるように、canister を常に「再インストール」モードでインストールします。
::：

## 対話的な例

`dfx deps` ワークフローを使用するための概念と概要について説明しました。

このサンプル・プロジェクトでは、アプリケーションcanister が IC メインネットから依存関係を取得し、ローカルに統合する様子を示します。

この例では、`app` canister が`double_service` というメソッドを定義し、`service` canister にcanister を呼び出します。

- #### ステップ1: まず、[IC SDKが](https://github.com/dfinity/sdk)インストールされていることを確認します。

- #### ステップ2：ターミナルウィンドウを開き、コマンドで新しいdfxプロジェクトを作成します：

<!-- end list -->

    dfx new pull_deps_example

- #### ステップ 3: 次に、テキストエディタまたはコードエディタでプロジェクトの`dfx.json` ファイルを開きます。以下の設定でプル依存関係を宣言します：

<!-- end list -->

    {
      "canisters": {
        "service": {
          "type": "pull",
          "id": "ig5e5-aqaaa-aaaan-qdxya-cai"
        }
      },
    }

ファイルを保存します。

- #### ステップ 4:`dfx deps pull` を使用してメインネットからプルします：

<!-- end list -->

    dfx deps pull
    Fetching dependencies of canister ig5e5-aqaaa-aaaan-qdxya-cai...
    Found 1 dependencies:
    ig5e5-aqaaa-aaaan-qdxya-cai
    Pulling canister ig5e5-aqaaa-aaaan-qdxya-cai...

- #### ステップ 5:`init` 引数を設定します：

<!-- end list -->

    dfx deps init service --argument 1

- #### ステップ6: コマンドを使ってローカルレプリカにデプロイします：

<!-- end list -->

    dfx start --clean --background
    dfx deps deploy
    dfx deploy app

- #### ステップ7：以下の呼び出しを行ってアプリをテストします：

<!-- end list -->

    dfx canister call app double_service

このステップでは、`app` canister 'の`double_service` メソッドが呼び出され、`service` canister にインターcanister コールを送信します。両方のcanisters がローカルレプリカにデプロイされたので、呼び出しは成功します。

出力は以下のようになります：

    (2 : nat)

## よくある質問

- #### なぜプロジェクトのサブフォルダではなく、共有キャッシュにwasmをダウンロードするのですか？

バージョン管理にバイナリファイルを含めることを推奨しているわけではありません。Internet Computer では、どのcanister もメインネット上で動いている最新バージョンは1つだけです。サービスコンシューマはその最新バージョンに統合する必要があります。

`dfx deps pull` 特定の実行をロックする代わりに、常に最新の依存関係を取得します。プルされた はすべて共有キャッシュに最新バージョンがあり、異なるプロジェクトで再利用できます。canister 

- #### バージョン管理に`deps/` フォルダーを含めるべきですか？

はい。`deps/` ファイルは、依存するcanister のビルドと IDE サポートを可能にします。必要な wasm ファイルも共有キャッシュにあれば、すべてのアプリケーションと依存関係を統合してデプロイし、テストすることができます。

canister 開発者チームを考えてみましょう：

1.  開発者 1 は[サービス・コンシューマのワークフローに従い](#service-consumer-workflow)、生成されたすべての`deps/` ファイルをソース・コントロールに含めます。
2.  開発者 2 は開発者 1 が作成したブランチをプルし、`dfx deps pull` コマンドを再度実行します。
    - `pulled.json` に変更がなければ、すべての依存関係は最新です。開発者 2 は init 引数を設定せずに`dfx deps deploy` を再度実行できます。
    - `pulled.json` に変更がある場合、開発者 2 は`dfx deps deploy` を実行してみて、すべての init 引数がまだ有効かどうかを確認できます。その後、開発者 2 は必要に応じて`dfx deps init` を実行し、ソース管理を更新することができます。

これらのファイルは、CIが古い依存関係を検出するのにも役立ちます。

## リソース

- [`dfx deps` CLIリファレンス](https://github.com/dfinity/sdk/blob/master/docs/cli-reference/dfx-deps.md)。
- [GitHub サンプルプロジェクト](https://github.com/lwshang/pullable.git)
- [依存関係をプルするコンセプトのドキュメント](https://github.com/dfinity/sdk/blob/master/docs/concepts/pull-dependencies.md)

<!---
# Pulling canister dependencies

## Overview

The interoperability of canisters on the IC is a vital feature for many developers and their workflows. `dfx` provides a consistent developer workflow for creating, integrating and testing third-party canisters with local developer environments. 

Third-party canisters include canisters created by DFINITY, such as NNS, ledger, II, and others, but also include canisters that have been created by developers in the IC community. Developers depend on third-party canisters to integrate with and typically need a way to develop and test the integrations locally for things such as:
- Testing the accuracy of the integration and other canister code.
- Performing tests without paying cycles.
- Performing tests using non-production data and environments.
- Performing tests with faster completion time when run locally. 

To pull these canisters from the IC mainnet to be developed and tested using a local replica, the [`dfx deps`](/docs/references/cli-reference/dfx-deps.md) command and workflow can be used. 

In this workflow, a **service provider** configures a canister to be `pullable`, then deploys the canister to the IC mainnet. A service provider can be DFINITY or it can be a community developer creating a public, third-party canister. 

Then, a **service consumer** can pull the canister as a dependency directly from the mainnet and then deploy the dependency on a local replica. 

In this guide, we'll further describe this workflow and how to use it.

## Determining if a canister should be `pullable`

To pull a canister that's been configured as `pullable`, the service consumer only requires the canister ID. 

Before pulling a canister, first the canister must be configured to be `pullable`. Additionally, we must propose the question of *should* the canister be `pullable`?

#### `Pullable` examples:

If a canister is providing a public service at a static canister ID, then it makes sense for the canister to be `pullable`.

If a service canister also depends on other canisters, those dependencies should also be `pullable`. 

#### Non-`pullable` examples:

If the canister is meant for personal use and not intended for others to use, it does not make sense for the canister to be `pullable`. 

If a canister wasm is published for other developers to use for deploying their own instance, it does not make sense for the canister to be `pullable` since the canister ID of the instance is not static; users can test integrations locally and deploy them directly. An example of this canister type is the asset canister generated by dfx. 

## `Pullable` dfx.json example

For a canister to be `pullable`, the `dfx.json` file must include a `pullable` definition. Below is an example:

```json
{
  "canisters": {
    "service": {
      "type": "motoko",
      "main": "src/pullable/main.mo",
      "pullable": {
        "dependencies": [],
        "wasm_url": "https://github.com/lwshang/pullable/releases/latest/download/service.wasm",
        "init_guide": "A natural number, e.g. 1"
      }
    }
  }
}
```

:::caution
The wasm module of a `pullable` canister must be hosted via a URL so that service consumers can download it when pulling the dependency.

GitHub Releases are a good, free option if the project is open source on GitHub. The GitHub URL schema is:

`https://github.com/<USERNAME>/<REPONAME>/releases/latest/download/<FILENAME>`

In a future version of this feature, direct wasm downloads from the replica will likely be supported.
:::

## Service provider workflow overview
First, a service provider must configure a canister to be `pullable` by setting it as such in the `dfx.json` file. 

An example of a provider `dfx.json` which has a `pullable` "service" canister can be found below:

```json
{
    "canisters": {
        "service": {
            "type": "motoko",
            "main": "src/main.mo",
            "pullable": {
                "wasm_url": "http://example.com/a.wasm",
                "wasm_hash": "d180f1e232bafcee7d4879d8a2260ee7bcf9a20c241468d0e9cf4aa15ef8f312",
                "dependencies": [
                    "yofga-2qaaa-aaaaa-aabsq-cai"
                ],
                "init_guide": "A natural number, e.g. 10."
            }
        }
    }
}
```

The `pullable` object will be serialized as a part of the `dfx` metadata and attached to the wasm.

To better understand the `pullable` object, let's look at each property in depth. 

- `wasm_url`: A URL used to download the canister wasm module which will be deployed locally.
- `wasm_hash`: A SHA256 hash of the wasm module located at `wasm_url`. This field is optional. In most cases, the wasm module at `wasm_url` will be the same as the on-chain wasm module. This means that dfx can read the state tree to obtain and verify the module hash. In some cases, the wasm module at `wasm_url` is not the same as the on-chain wasm module. For example, the Internet Identity canister provides a `development` variant to be integrated locally. In these cases, `wasm_hash` provides the expected hash, and `dfx` verifies the downloaded wasm against this.

:::caution
If the `wasm_hash` of the wasm module at `wasm_url` does not match, dfx will abort with an error message indicating there is a hash mismatch. In this scenario, the service consumer should contact the service provider. It is the responsibility of the service provider to assure that the correct wasm module can be downloaded from the `wasm_url`. 
:::

- `dependencies`: An array of Canister IDs (`Principal`) of direct dependencies.
- `init_guide`: A message to guide consumers how to initialize the canister.

### Canister metadata requirements

A service provider canister used in production or in a production environment running on the mainnet should have public `dfx` metadata.

The canister wasm downloaded from `wasm_url` should have the following metadata (public or private):

- `candid:service`
- `candid:args`
- `dfx`

All metadata sections are handled by `dfx` when the canister is built.

### Deployment process
Service providers will use the following deployment process to deploy their `pullable` canister.

- #### Step 1: Deploy the canister to the mainnet with the command:

```
dfx deploy --network ic
```

- #### Step 2: If you're using GitHub, `git tag` and `GitHub release` with the commands:

```
git tag 0.1.0
git push --tags
```

You can follow [this guide](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository#creating-a-release) to create a release.

- #### Step 3: Attach the wasm to the release assets. 
Edit the release and attach the deployed wasm as a release asset.

The deployed wasm file will be located at:

```
.dfx/ic/canisters/<CANISTER_NAME>/<CANISTER_NAME>.wasm
```

### Automating the service provider process in CI

An [example CI configuration](https://github.com/lwshang/pullable/blob/main/.github/workflows/release.yml) demonstrates how to use a GitHub Action to automate the deploy routine described above.

The workflow with CI will follow these steps:

1. Push a git tag and wait for the GitHub release to complete.
2. Download the canister wasm from the release assets (`wget https://github.com/lwshang/pullable/releases/latest/download/service.wasm`).
3. Install (upgrade) the canister using the downloaded wasm (`dfx canister --network ic install service --wasm service.wasm --argument '(1 : nat)' --mode upgrade`).

## Service consumer workflow overview

The following workflow can be used for service consumers to pull a `pullable` canister as a dependency. 

### Step 1: Declare "pull" dependencies in `dfx.json`.

First, to pull the dependencies from the mainnet, the `dfx.json` file must include the `dependencies` configuration for the canister. 

An example `dfx.json` in which the service consumer is developing a canister named "dapp", which has two pull dependencies, can be found below:

- "dep_b" has canister ID of `yhgn4-myaaa-aaaaa-aabta-cai` on the mainnet.
- "dep_c" has canister ID of `yahli-baaaa-aaaaa-aabtq-cai` on the mainnet.

```json
{
    "canisters": {
        "dapp": {
            "type": "motoko",
            "main": "src/main.mo",
            "dependencies": [
                "dep_b", "dep_c"
            ]
        },
        "dep_b": {
            "type": "pull",
            "id": "yhgn4-myaaa-aaaaa-aabta-cai"
        },
        "dep_c": {
            "type": "pull",
            "id": "yahli-baaaa-aaaaa-aabtq-cai"
        }
    }
}
```

### Step 2: Pull the dependencies using the `dfx deps pull` command.

Running the command `dfx deps pull` will do the following:

1. First, it will resolve the dependency graph by fetching the `dependencies` field in the `dfx` metadata recursively.
2. Then, it will download the wasm of all direct and indirect dependencies from `wasm_url` into the shared cache.
3. Next, the hash of the downloaded wasm will be verified against `wasm_hash` metadata or the hash of the canister deployed on mainnet. 
4. Then, `candid:args`, `candid:service`, `dfx` metadata will be extracted from the downloaded wasm.
5. The `deps/` folder is created in the project root.
6. The `candid:service` of direct dependencies is saved as `deps/candid/<CANISTER_ID>.did`.
7. The `deps/pulled.json` which contains major info of all direct and indirect dependencies is saved.

For the example project, you will find following files in `deps/`:

- `yhgn4-myaaa-aaaaa-aabta-cai.did` and `yahli-baaaa-aaaaa-aabtq-cai.did`: Candid files that can be imported by "dapp".
- `pulled.json`: A json file with the following content:

```json
{
  "canisters": {
    "yofga-2qaaa-aaaaa-aabsq-cai": {
      "dependencies": [],
      "wasm_hash": "e9b8ba2ad28fa1403cf6e776db531cdd6009a8e5cac2b1097d09bfc65163d56f",
      "init_guide": "A natural number, e.g. 10.",
      "candid_args": "(nat)"
    },
    "yhgn4-myaaa-aaaaa-aabta-cai": {
      "name": "dep_b",
      "dependencies": [
        "yofga-2qaaa-aaaaa-aabsq-cai"
      ],
      "wasm_hash": "f607c30727b0ee81317fc4547a8da3cda9bb9621f5d0740806ef973af5b479a2",
      "init_guide": "No init arguments required",
      "candid_args": "()"
    },
    "yahli-baaaa-aaaaa-aabtq-cai": {
      "name": "dep_c",
      "dependencies": [
        "yofga-2qaaa-aaaaa-aabsq-cai"
      ],
      "wasm_hash": "016df9800dc5760785646373bcb6e6bb530fc17f844600991a098ef4d486cf0b",
      "init_guide": "A natural number, e.g. 20.",
      "candid_args": "(nat)"
    }
  }
}
```

In this file, you can see there are three dependencies:

- `yhgn4-myaaa-aaaaa-aabta-cai`: "dep_b" in `dfx.json`.
- `yahli-baaaa-aaaaa-aabtq-cai`: "dep_c" in `dfx.json`.
- `yofga-2qaaa-aaaaa-aabsq-cai`: an indirect dependency that both "dep_b" and "dep_c" depend on.

:::caution
`dfx deps pull` connects to the IC mainnet by default (`--network ic`). You can choose other network as usual, e.g. `--network local`.
:::

### Step 3: Set init arguments using `dfx deps init`

Running the command `dfx deps init` will iterate over all dependencies in the `pulled.json` file and set an empty argument for any that do not need an `init` argument. Then, it will print the list of dependencies that do require an `init` argument.

Running the command `dfx deps init <CANISTER> --argument <ARGUMENT>` will set the `init` argument for an individual dependency. The init arguments will be recorded in `deps/init.json`.

Using the example above, we can run the following commands:

- To set the init arguments:

```
dfx deps init
WARN: The following canister(s) require an init argument. Please run `dfx deps init <NAME/PRINCIPAL>` to set them individually:
yofga-2qaaa-aaaaa-aabsq-cai
yahli-baaaa-aaaaa-aabtq-cai (dep_c)
```

- If you try to set an `init` argument for an individual dependency without an argument, it will result in the following error:

```
dfx deps init yofga-2qaaa-aaaaa-aabsq-cai
Error: Canister yofga-2qaaa-aaaaa-aabsq-cai requires an init argument. The following info might be helpful:
init_guide => A natural number, e.g. 10.
candid:args => (nat)
```

```
dfx deps init deps_c
Error: Canister yahli-baaaa-aaaaa-aabtq-cai (dep_c) requires an init argument. The following info might be helpful:
init_guide => A natural number, e.g. 20.
candid:args => (nat)
```

- To set an init argument with an argument using the `--argument` flag, the following commands can be used:

```
dfx deps init yofga-2qaaa-aaaaa-aabsq-cai --argument 10
dfx deps init deps_c --argument 20
```

The resulting generated file `init.json` will have the following content:

```json
{
  "canisters": {
    "yofga-2qaaa-aaaaa-aabsq-cai": {
      "arg_str": "10",
      "arg_raw": "4449444c00017d0a"
    },
    "yhgn4-myaaa-aaaaa-aabta-cai": {
      "arg_str": null,
      "arg_raw": null
    },
    "yahli-baaaa-aaaaa-aabtq-cai": {
      "arg_str": "20",
      "arg_raw": "4449444c00017d14"
    }
  }
}
```

### Step 4: Deploy the pulled dependencies on a local replica using the `dfx deps deploy` command.

Running the `dfx deps deploy` command will:

1. First, create the dependencies on the local replica with the same mainnet canister ID.
2. Then, it will install the downloaded wasm with the init arguments in the `init.json` file.

You can also specify the name or principal to deploy one particular dependency.

Using the example above, we can run the following command to deploy all dependencies:

```
dfx deps deploy
Creating canister: yofga-2qaaa-aaaaa-aabsq-cai
Installing canister: yofga-2qaaa-aaaaa-aabsq-cai
Creating canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)
Installing canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)
Creating canister: yahli-baaaa-aaaaa-aabtq-cai (dep_c)
Installing canister: yahli-baaaa-aaaaa-aabtq-cai (dep_c)
```

To deploy one particular dependency, the following command can be used: 

```
dfx deps deploy yofga-2qaaa-aaaaa-aabsq-cai
Installing canister: yofga-2qaaa-aaaaa-aabsq-cai
```

```
dfx deps deploy dep_b
Installing canister: yhgn4-myaaa-aaaaa-aabta-cai (dep_b)
```

:::info
`dfx deps deploy` always creates the canister with the anonymous identity so that dependencies and application canisters will have different controllers. It also will always install the canister in "reinstall" mode so that the canister status will be discarded.
:::

## Interactive example

Now that we've explored the concepts and overview of using the `dfx deps` workflow, let's take a look at using an interactive example to demonstrate the functionality. 

This example project will demonstrate an application canister pulling its dependency from the IC mainnet and integrating with it locally. 

In this example, the `app` canister defines a method called `double_service` which makes an inter-canister call to the `service` canister.

- #### Step 1: First, assure that you have installed the [IC SDK](https://github.com/dfinity/sdk).

- #### Step 2: Then, open a terminal window and create a new dfx project with the command:

```
dfx new pull_deps_example
```

- #### Step 3: Then, open the project's `dfx.json` file in a text or code editor. Declare the pull dependency with the following configuration:

```
{
  "canisters": {
    "service": {
      "type": "pull",
      "id": "ig5e5-aqaaa-aaaan-qdxya-cai"
    }
  },
}
```

Save the file.

- #### Step 4: Use `dfx deps pull` to pull from the mainnet:

```
dfx deps pull
Fetching dependencies of canister ig5e5-aqaaa-aaaan-qdxya-cai...
Found 1 dependencies:
ig5e5-aqaaa-aaaan-qdxya-cai
Pulling canister ig5e5-aqaaa-aaaan-qdxya-cai...
```

- #### Step 5: Configure the `init` argument:

```
dfx deps init service --argument 1
```

- #### Step 6: Deploy on a local replica with the commands:

```
dfx start --clean --background
dfx deps deploy
dfx deploy app
```

- #### Step 7: Test the app by making the following call:
  
```
dfx canister call app double_service
```

In this step, the `app` canister's `double_service` method is being called, which sends an inter-canister call to the `service` canister. The call will succeed since both canisters are now deployed on the local replica.

The output will resemble the following:

```
(2 : nat)
```

## Frequently asked questions

- #### Why download wasm into shared cache instead of a project subfolder?

We don't want to encourage including binary files in version control. On the Internet Computer, every canister only has one latest version running on mainnet. Service consumers should integrate with that latest version. 

`dfx deps pull` always gets the latest dependencies instead of locking on a particular run. Every pulled canister has the latest version in the shared cache and can be reused by different projects.

- #### Should I include `deps/` folder in version control?

Yes. `deps/` files enable the dependent canister to build and get IDE support. If the required wasm files are also available in the shared cache, all application and dependencies can be deployed and tested integrally.

Considering a canister developer team:

1.  Developer 1 follows the [service consumer workflow](#service-consumer-workflow) and includes all generated `deps/` files in source control.
2.  Developer 2 pulls the branch by Developer 1 and runs the `dfx deps pull` command again.
    - If the `pulled.json` has no change, then all dependencies are still up to date. Developer 2 can run `dfx deps deploy` without setting init arguments again.
    - If there are changes in `pulled.json`, Developer 2 can try to run `dfx deps deploy` to see if all init arguments are still valid. Then Developer 2 can run `dfx deps init` if necessary and update source control.

These files also help CI to detect outdated dependencies.

## Resources

- [`dfx deps` CLI reference](https://github.com/dfinity/sdk/blob/master/docs/cli-reference/dfx-deps.md).
- [GitHub example project](https://github.com/lwshang/pullable.git).
- [Pull dependencies concept documentation](https://github.com/dfinity/sdk/blob/master/docs/concepts/pull-dependencies.md).

-->
