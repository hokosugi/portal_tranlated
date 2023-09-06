---

title: Introducing dfx deps!
description: Previously known as the `dfx pull` command, `dfx deps` is a new set of subcommands designed to provide a consistent developer workflow for integrating and testing third-party canisters within local developer environments.
tags: [New features]
image: /img/blog/infinity-symbol.png
---
# dfx depsのご紹介！

[dfx deps](/img/blog/infinity-symbol.png)

本日、dfxの新機能であるdfx depsを発表します！

## `dfx deps` とは？

`dfx deps` は、サードパーティの をローカル環境に統合し、テストするための一貫した開発者ワークフローを提供するために設計された、新しいサブコマンドのセットです。サードパーティ は、Internet IdentityやNNS などの が作成した 、またはcanisters canisters canisters DFINITY canisters *静的な IDでcanister *パブリックサービスを提供するICコミュニティのメンバが作成した があります。canisters 

サードパーティcanister の統合をローカルでテストすることは、サードパー ティcanister の統合機能を検証するために重要であり、cycles 支払いや本番環境を使用する必要はありません。

`dfx deps` コマンドには以下のサブコマンドがあります：

- `dfx deps pull`:メインネットから依存関係をプルし、`deps/pulled.json` を生成します。直接の依存関係の Candid ファイルも`deps/candid/` に置かれます。
- `dfx deps init`:引き出された依存関係の`init` 引数を設定し、データを`deps/init.json` に保存します。
- `dfx deps deploy`:`deps/init.json` に記録された`init` 引数で、プルされた依存関係をローカルレプリカにデプロイします。

## ワークフローの概要

`dfx deps` ワークフローがどのように動作するか見てみましょう。**サービスプロバイダと** **サービスコンシューマの**2つの役割があります。

### サービスプロバイダ

**サービス**提供者は、canister を`pullable` に設定する役割を担います。

canister が`pullable` になるためには、以下のことが必要です：

- canister は、静的なcanister ID でパブリックサービスを提供します。
- `pullable` canister の wasm モジュールは、サービス利用者が依存関係を引き出すときにダウンロードできるように、URL を介してホストされていなければなりません。

Canisters 静的な ID を使用しないもの、または他の開発者が利用できるパブリックサービスを提供しないものは、 になるように設定するべきではありません。canister `pullable`

canister を`pullable` に設定するには、`dfx.json` ファイルを編集して、次の情報を含めます：

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

- `wasm_url`:ローカルにデプロイされるcanister wasm モジュールをダウンロードするための URL。
- `wasm_hash`:`wasm_url` にある wasm モジュールの SHA256 ハッシュ。このフィールドはオプションです。ほとんどの場合、`wasm_url` にある wasm モジュールはオンチェーンの wasm モジュールと同じです。
- `dependencies`:直接依存するCanister ID (プリンシパル) の配列。
- `init_guide`:コンシューマがcanister を初期化するためのメッセージ。

### サービスコンシューマ

canister が`pullable` になると、**サービスコンシューマは**メインネットから直接依存関係としてcanister を引き出し、その依存関係をローカルレプリカにデプロイすることができます。

メインネットから依存関係をプルするには、`dfx.json` ファイルにcanister の依存関係設定を含める必要があります。

たとえば、次の`dfx.json` ファイルは、canister `dapp` の 2 つの依存関係を構成します。これらの依存関係はどちらも静的なcanister ID を持っています：

- 「dep\_b」のメインネット上のcanister ID は yhgn4-myaaa-aaaaabta-cai です。
- 「dep\_c "のcanister IDは、メインネット上でyahli-baaaa-aaaaa-aabtq-caiです。

<!-- end list -->

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

これらの依存関係は、`dfx deps pull` コマンドを使用してプルできます。デフォルトでは、`dfx deps pull` は IC メインネットに接続します。ローカルにデプロイするには、`--network local` フラグを使用します。

このコマンドが呼ばれると、バックグラウンドで以下のようなことが行われます：

- `dfx` メタデータの dependencies フィールドを再帰的にフェッチすることで、依存グラフが解決されます。
- すべての直接依存と間接依存の wasm が`wasm_url` から共有キャッシュにダウンロードされます。
- ダウンロードされたwasmのハッシュは、`wasm_hash` メタデータまたはメインネットにデプロイされたcanister のハッシュと照合されます。
- ダウンロードされた wasm から`candid:args` 、`candid:service` 、`dfx metadata` が抽出されます。
- `deps/` フォルダがプロジェクトルートに作成されます。
- 直接依存する`candid:service` は`deps/candid/<CANISTER_ID>.did` として保存されます。
- `deps/pulled.json` ファイルが保存され、すべての直接および間接依存関係の主要な情報が含まれます。

`dfx deps pull` コマンドが実行されると、`dfx deps init` コマンドで init 引数を設定できます。このコマンドは、`pulled.json` ファイル内のすべての依存関係を繰り返し、init 引数を必要としないものには空の引数を設定します。そして、init引数を必要とする依存関係のリストを表示します。

コマンド`dfx deps init <CANISTER> --argument <ARGUMENT>` を実行すると、個々の依存関係のinit引数が設定されます。init 引数は`deps/init.json` に記録されます。

以下は、init 引数を指定して`dfx deps init` コマンドを実行した例です：

    dfx deps init yofga-2qaaa-aaaaa-aabsq-cai --argument 10
    dfx deps init deps_c --argument 20

このステップの後、`dfx deps deploy` コマンドを使用して、プルされた依存関係をローカルレプリカにデプロイできます。このコマンドは、同じメインネットcanister IDを持つローカルレプリカ上に依存関係を作成します。そして、`init.json` ファイルに init 引数を指定してダウンロードした wasm をインストールします。

`dfx deps deploy` 依存関係とアプリケーション が異なるコントローラを持つように、常に匿名 ID で を作成します。また、 の状態が破棄されるように、常に「再インストール」モードで をインストールします。canisters canister canister canister 

## 結論

`dfx deps` の機能は、dfx のバージョン`0.14.1` 以降で利用可能です。詳しくは[開発者向けドキュメントを](https://internetcomputer.org/docs/current/developer-docs/setup/pulling-canister-dependencies)ご覧ください。このドキュメントには、この機能を自分でテストするためのインタラクティブなサンプルが含まれています。

フィードバックがありましたら、[フォーラム](https://forum.dfinity.org/)または[Discord サーバまで](https://discord.com/invite/5PJMmmETQB)お知らせください。

\-DFINITY

<!---


# Introducing dfx deps!

[!dfx deps](/img/blog/infinity-symbol.png)

Today we're excited to announce a new dfx feature: dfx deps! 

## What is `dfx deps`?

`dfx deps` is a new set of subcommands designed to provide a consistent developer workflow for integrating and testing third-party canisters within local environments. Third-party canisters can be canisters created by DFINITY, such as the Internet Identity or NNS canisters, or they can be canisters created by members of the IC community that provide a public service at a *static canister ID*. 

Testing third-party canister integrations locally is important to verify the third-party canister's integration functionality without paying cycles or using production environments. 

The `dfx deps` command includes the following subcommands: 

- `dfx deps pull`: Pulls the dependencies from the mainnet and generates `deps/pulled.json`. The Candid files of direct dependencies will also be put into `deps/candid/`.
- `dfx deps init`: Sets the `init` arguments for the pulled dependencies and saves the data in `deps/init.json`.
- `dfx deps deploy`: Deploys the pulled dependencies on the local replica with the `init` arguments recorded in `deps/init.json`.

## Workflow overview 

Let's take a look at how the `dfx deps` workflow operates. There are two roles: the **service provider** and the **service consumer**. 

### Service provider

The **service provider** is responsible for configuring a canister to be `pullable`.

For a canister to be `pullable`, the following should be true:
- The canister provides a public service at a static canister ID. 
- The wasm module of a `pullable` canister must be hosted via a URL so that service consumers can download it when pulling the dependency.

Canisters that do not use a static canister ID, or do not provide a public service that can be utilized by other developers, should not be configured to be `pullable`. 

A canister is configured to be `pullable` by editing the `dfx.json` file to include the following information:

```
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

- `wasm_url`: A URL used to download the canister wasm module which will be deployed locally.
- `wasm_hash`: A SHA256 hash of the wasm module located at `wasm_url`. This field is optional. In most cases, the wasm module at `wasm_url` will be the same as the on-chain wasm module.
- `dependencies`: An array of Canister IDs (Principal) of direct dependencies.
- `init_guide`: A message to guide consumers how to initialize the canister.

### Service consumer

Once a canister is `pullable`, a **service consumer** can pull the canister as a dependency directly from the mainnet and then deploy the dependency on a local replica. 

To pull the dependencies from the mainnet, the `dfx.json` file must include the dependencies configuration for the canister.

For example, the following `dfx.json` file configures two dependencies for the canister `dapp`. Both of these dependencies have static canister IDs of:

- "dep_b" has canister ID of yhgn4-myaaa-aaaaa-aabta-cai on the mainnet.
- "dep_c" has canister ID of yahli-baaaa-aaaaa-aabtq-cai on the mainnet.

```
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

Then, these dependencies can be pulled using the `dfx deps pull` command. By default, the `dfx deps pull` connects to the IC mainnet. To deploy locally, the `--network local` flag can be used. 

When this command is called, several things happen in the background, including:

- The dependency graph is resolved by fetching the dependencies field in the `dfx` metadata recursively.
- The wasm of all direct and indirect dependencies is downloaded from the `wasm_url` into the shared cache.
- The hash of the downloaded wasm is verified against `wasm_hash` metadata or the hash of the canister deployed on the mainnet.
- The `candid:args`, `candid:service`, and `dfx metadata` will be extracted from the downloaded wasm.
- The `deps/` folder is created in the project root.
- The `candid:service` of direct dependencies is saved as `deps/candid/<CANISTER_ID>.did`.
- The `deps/pulled.json` file is saved, which contains major info of all direct and indirect dependencies.

Once the `dfx deps pull` command has been run, the init arguments can be set with the `dfx deps init` command. This command will iterate over all dependencies in the `pulled.json` file and set an empty argument for any that do not need an init argument. Then, it will print the list of dependencies that do require an init argument.

Running the command `dfx deps init <CANISTER> --argument <ARGUMENT>` will set the init argument for an individual dependency. The init arguments will be recorded in `deps/init.json`.

Below are examples of running the `dfx deps init` command with init arguments:

```
dfx deps init yofga-2qaaa-aaaaa-aabsq-cai --argument 10
dfx deps init deps_c --argument 20
```

After this step, the pulled dependencies can be deployed on a local replica using the `dfx deps deploy` command. This command will create the dependencies on the local replica with the same mainnet canister ID. Then, it will install the downloaded wasm with the init arguments in the `init.json` file.

`dfx deps deploy` always creates the canister with the anonymous identity so that dependencies and application canisters will have different controllers. It also will always install the canister in "reinstall" mode so that the canister status will be discarded.

## Conclusion

The `dfx deps` feature is available in dfx versions `0.14.1` and newer. You can learn more from our [developer documentation](https://internetcomputer.org/docs/current/developer-docs/setup/pulling-canister-dependencies), which includes an interactive example you can use to test the feature for yourself. 

As always, please let us know if you have any feedback either through our [forum](https://forum.dfinity.org/) or [Discord server](https://discord.com/invite/5PJMmmETQB).

-DFINITY

-->
