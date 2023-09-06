---

sidebar_position: 3
---
# メインネットでのテスト (SNS testflight)

## 概要

開発者がSNSのプロセスをテストしたら、メインネット** [上で](./testing-on-mainnet.md)「SNSテストフライト**」を行うことを強くお勧めします。SNSテストフライトとは、開発者が（メインネットに）dapp をデプロイし、（メインネット上の）模擬SNSにそのコントロールを渡すことです。

**SNS testflight を行う主な目的は、dapp が分散化された*後に*どのように動作するかを開発者が体験することであり、開発者はdapps が分散化に対応していることを確認することができます。分散化の実際のプロセスをテストするものではありません。**

:::info
テストフライトはレポやツールのセットではなく、*アクティビティ*（dapp をデプロイし、その制御を模擬 SNS に渡す）です。そのため、[メインネット上でのテストの](./testing-on-mainnet.md)指示はさまざまなツールを利用していますが、開発者はもちろん好きなツールを使うことができます。
::：

とりわけ、SNSテストフライトを実行した後に開発者が発見した問題の例をいくつか紹介します：

- 開発者は、dapp を更新するプロポーザルを作成するための、よりよいパイプラインが必要であることに気づきます。
- 開発者は、分散化が早すぎたかもしれないことに気づき、いくつかの点を修正する必要があることに気づきます。
- 開発者は、分散化する前に、より良いモニタリングが必要であることに気づきます。

SNS testflightで使用されるモックSNSは、開発者にdapp の分散化後のライフcycle がどのようなものかを見る能力を与えますが、それでも開発者が自分のdapp をコントロールし続ける方法を提供します。**したがって、開発者は、すべての側面がカバーされていることを確認するために、メインネット上でSNS testflightを実行することが推奨されます。**

## SNSテストフライトと本番SNSの比較

SNSの本番展開との主な違いをまとめます：

- テストフライトSNSはNNSの代わりに開発者によってデプロイされます。
- 特に、NNSプロポーザルは関与しません。実際には分散スワップは行われません。
- 開発者は、testflight SNS に登録されたdapp のcanisters を直接制御し続けることもできます。
- メインネットにデプロイされる場合、testflight SNSは、専用の*SNSサブネットではなく*、通常の*アプリケーションサブネットに*デプロイされます。

## 前提条件

以下の手順を使用して SNS テストフライトを実行するには、次のツールが必要です：

- \[x\][dfx](https://github.com/dfinity/sdk)
- \[x\][sns-cli](https://github.com/dfinity/ic)
- \[x\][quill](https://github.com/dfinity/quill)
- \[x\][didc](https://github.com/dfinity/candid)(高度なテスト用)

:::info
開発者は、テストフライトの目標を達成するために、任意のツールセットを使用できます。
::：

### インストール`sns-cli`

`sns-cli` をローカルでビルドする代わりに、[Linux](https://download.dfinity.systems/ic/82a53257ed63af4f602afdccddadc684df3d24de/openssl-static-binaries/x86_64-linux/sns.gz)および[macOS](https://download.dfinity.systems/ic/82a53257ed63af4f602afdccddadc684df3d24de/openssl-static-binaries/x86_64-darwin/sns.gz) 用のコンパイル済みバイナリをダウンロードすることができます。リンク先のコミットハッシュは、コマンドを実行して取得した最新のものに置き換えることができます：

    ./gitlab-ci/src/artifacts/newest_sha_with_disk_image.sh origin/master

[IC monorepoの](https://github.com/dfinity/ic)ルートディレクトリから、次のコマンドを実行して取得した最新のものに置き換えることができます。

quill用のコンパイル済みバイナリは[こちらから](https://github.com/dfinity/quill/releases)ダウンロードできます。

didc用のコンパイル済みバイナリは[こちらから](https://github.com/dfinity/candid/releases)ダウンロードできます。

### バージョン

このガイドは以下のバージョンのツールでテストされています：

- **dfx： 0.13.1**
- **sns-cli: 82a53257ed63af4f602afdccddadc684df3d24de**
- **quill: v0.4.0**
- **didc: 0.3.0 (2022-11-17)**

## ハイレベル

テストフライトは通常、以下のステップで構成されます：

1.  SNScanisters をインポートしてダウンロードします。
2.  testflight SNS（アプリケーション・サブネットへのモック SNScanisters ）をメインネットにデプロイし、開発者neuron ID を保存します。
3.  dapp をデプロイします。
4.  模擬 SNScanister にdapp を登録（制御を引き渡す）。
5.  SNS プロポーザルを使ってcanisters のアップグレードをテストします。

開発者がテストフライトを実行する際に必要となる作業

- プロポーザルのチェック。
- SNS プロポーザルを介して SNS が管理するcanisters でコードを実行するテスト。
- テストフライトを中止します。

## テストフライトの実行手順

### ステップ1.SNS のインポートとダウンロードcanisters

プロジェクトの`dfx.json` ファイルにある SNScanisters をインポートし、WASM バイナリをダウンロードするには、プロジェクトのルートディレクトリで以下を実行します。

``` bash
DFX_IC_COMMIT=82a53257ed63af4f602afdccddadc684df3d24de dfx sns import
DFX_IC_COMMIT=82a53257ed63af4f602afdccddadc684df3d24de dfx sns download
```

をプロジェクトのルートディレクトリで実行します。

### ステップ 2.テストフライトSNS（アプリケーションサブネットへのモックSNScanisters ）をメインネットにデプロイし、開発者neuron IDを保存します。

testflight SNS をデプロイするには、以下を実行します。

``` bash
sns-cli deploy-testflight
```

メインネットにデプロイするには、追加の引数として`--network ic` を渡します。

デプロイが完了したら、SNS プロポーザルの提出に使用する開発者neuron ID
を必ず保存してください。
testflight SNS パラメータは、この開発者neuron ID
が testflight SNS を完全に制御できるように設定されています。
開発者neuron ID は、testflight デプロイメント出力の最後の部分です：

```
Developer neuron IDs: 
```

### ステップ 3.すべての SNS 管理対象dapp の追加コントローラとして SNS ルートを追加します。canisters

SNS ルートcanister を、testflight SNS で管理するすべてのcanisters
 の**追加**コントローラとして追加します。
 `test` というcanister の場合は、次のようにします：

``` bash
dfx canister update-settings --add-controller $(dfx canister id sns_root) test
```

メインネットでテストフライトを実行する場合、`dfx canister` の**両方の**呼び出しに、追加の引数
として`--network ic` を渡します。

### ステップ 4.dapp canisters を SNS ルートに登録します。

`quill` 経由で SNS 提案を提出することにより、テストフライト SNS
で管理される予定のすべてのcanisters を登録します。

ローカルでテストフライトを実行する場合は、環境変数`IC_URL`
をエクスポートして、ローカルの IC インスタンスを指定します、

``` bash
export IC_URL="http://localhost:8080/"
```

通常、このファイルはホームディレクトリの
`.config/dfx/identity/$(dfx identity whoami)/identity.pem`
にあります。

最後に、SNSプロポーザルを準備し、`quill` ：

``` bash
export DEVELOPER_NEURON_ID="594fd5d8dce3e793c3e421e1b87d55247627f8a63473047671f7f5ccc48eda63"
export PEM_FILE="/home/martin/.config/dfx/identity/$(dfx identity whoami)/identity.pem"
export CID="$(dfx canister id test)"
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Register dapp's canisters with SNS.\"; url=\"https://example.com/\"; summary=\"This proposal registers dapp's canisters with SNS.\"; action=opt variant {RegisterDappCanisters = record {canister_ids=vec {principal\"$CID\"}}}})" $DEVELOPER_NEURON_ID > register.json
quill send register.json
```

メインネットでテストフライトを実行する場合は、dapp のcanister ID を取得する際に、`dfx canister`
の追加引数として`--network ic` を渡します。
その他の場合は、`quill send` の追加引数として`--insecure-local-dev-mode` を渡します。

また、上記の`--proposal` の引数`RegisterDappCanisters`
を例えば次のように調整することで、1つのSNSプロポーザルで複数のcanisters を登録することもできます、

    RegisterDappCanisters = record {canister_ids=vec {principal\"$CID1\"; principal\"$CID2\";}}

SNS プロポーザルが実行されると、登録されたすべてのdapp のcanisters がdapps のようにリストされます。

``` bash
dfx canister call sns_root list_sns_canisters '(record {})'
```

メインネットでテストフライトを実行する場合は、上記の`dfx canister` の追加引数として`--network ic` を渡します。

### ステップ 5.SNS プロポーザルを使ってcanisters をアップグレードするテスト

canister をアップグレードする新しい wasm バイナリのパスを決めます。
 `dfx` でビルドしたプロジェクトの場合、このバイナリは通常、プロジェクトのルートディレクトリ
`.dfx/<network>/canisters/<canister-name>/<canister-name>.wasm`
の
にあります。`<network>` はネットワーク (例:`local` や`ic`)、`<canister-name>` は`dfx.json` に従ったcanister の名前
です。

これで、SNSプロポーザルを準備し、`quill` ：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-upgrade-canister-proposal --summary "This proposal upgrades test canister." --title "Upgrade test canister." --url "https://example.com/" --target-canister-id $CID --wasm-path "./.dfx/local/canisters/test/test.wasm" $DEVELOPER_NEURON_ID > upgrade.json

quill send upgrade.json | grep -v "^ *new_canister_wasm"
```

メインネットに対してテストフライトを実行しない限り、`quill send` の追加引数として`--insecure-local-dev-mode` を渡します。

上記の`grep -v "^ *new_canister_wasm"` を省略すると、出力に新しいWASMバイナリが表示されます。この場合、出力にはWASMのバイナリ全体が含まれ、巨大になる可能性があることに注意してください！

## 開発者が行うべきこと

### SNS プロポーザルを介して SNS が管理するcanisters 上でコードを実行するテスト

SNS プロポーザルを介して SNS が管理するcanisters でコードを実行するには、[汎用プロポーザルを](../managing/making-proposals.md)使用します。このようなプロポーザルを使用するには、このようなプロポーザルの動作を定義するSNS管理型canisters 、2つのパブリック関数(以下では**ジェネリック**関数と呼びます)を公開する必要があります：

- プロポーザルのペイロードを検証し、レンダリングする検証関数；
- プロポーザルペイロードを検証しレンダリングする検証関数。

検証関数は Candid 型の値
`variant { Ok: text; Err: text; }` を返す必要があります。例えば、Rust では`Result<String, String>` です。
検証関数が`Ok(rendering)` を返した場合、
プロポーザルが提出され、`rendering` 文字列がプロポーザルに
含まれます。
そうでない場合、プロポーザルの提出は失敗します。

実行関数は、検証関数
に渡されたのと同じバイナリペイロードを取得し、プロポーザルが受理された場合にそのコードが実行されます。
この戻り値は無視されるため、値を返すべきではありません。

ジェネリック関数を使用するには、まず
SNSプロポーザルを提出し、これらの関数
をSNSガバナンスに登録する必要があります：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Register generic functions.\"; url=\"https://example.com/\"; summary=\"This proposals registers generic functions.\"; action=opt variant {AddGenericNervousSystemFunction = record {id=1000:nat64; name=\"MyGenericFunctions\"; description=null; function_type=opt variant {GenericNervousSystemFunction=record{validator_canister_id=opt principal\"$CID\"; target_canister_id=opt principal\"$CID\"; validator_method_name=opt\"validate\"; target_method_name=opt\"execute\"}}}}})" $DEVELOPER_NEURON_ID > register-generic-functions.json

quill send register-generic-functions.json
```

この識別子は少なくとも1000でなければならないことに注意してください。
また、提案書ではレンダリングされますが、canister コード内の関数名
とは直接関係しない、ジェネリック関数
の名前と任意の説明を提供する必要があります（上記のサンプルコードでは、名前は`MyGenericFunctions` であり、説明は省略されています）。

ジェネリック関数の登録提案が受理されると、指定されたバイナリペイロードでジェネリック関数を実行するプロポーザル
を提出することができます：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Execute generic functions.\"; url=\"https://example.com/\"; summary=\"This proposal executes generic functions.\"; action=opt variant {ExecuteGenericNervousSystemFunction = record {function_id=1000:nat64; payload=blob \"DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00\"}}})" $DEVELOPER_NEURON_ID > execute-generic-functions.json

quill send execute-generic-functions.json
```

実行される汎用関数は、登録提案で定義された数値識別子で指定されます。
ペイロードは、両方の汎用関数に渡されるblobです。
上記のサンプルペイロード`blob \"DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00\"` は、以下の方法で取得されました。

``` bash
didc encode '(record {major=2:nat32; minor=3:nat32;})' --format blob
blob "DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00"
```

で取得され、canister Rust コードで Candid ペイロード（2 つのフィールドを持つレコード）としてデコードできます：

``` rust
#[derive(CandidType, Debug, Deserialize)]
struct Version {
  major: u32,
  minor: u32,
}

#[ic_cdk::update]
fn validate(x: Version) -> Result<String, String> {
  // ...
}
```

### testflight SNSのプロポーザルの確認

以下のように、testflight SNS のすべてのプロポーザルをリストできます：

``` bash
dfx canister call sns_governance list_proposals '(record {include_reward_status = vec {}; limit = 0; exclude_type = vec {}; include_status = vec {};})'
```

メインネットでテストフライトを実行するとき、`dfx canister` に追加引数として`--network ic` を渡します。
制限を指定して、最後のいくつかのプロポーザルのみを取得することもできます。

### テストフライトの中止

テストフライト中は開発者が登録されたdapp のcanisters を直接管理するため、
必要であれば、テストフライト中にdapp のcanisters を直接管理することができます。
ただし、必要なすべての操作
を SNS プロポーザルのみで実行できるようにすることを強く推奨します (dapp をアップグレードした後でもかまいません)。

dapp
の操作に必要な各種 SNS プロポーザルのテストが完了したら、 テストフライトを終了できます。 テストフライト SNS に登録されているすべての のコントローラになっていることを確認してください。
 canisters 

``` bash
dfx canister status test
```

メインネットでテストフライトを実行する場合は、`dfx canister` に追加の引数として`--network ic` を渡します。

この場合、SNS testflightcanisters を安全に削除することができます。
そうでない場合は、以下のコードで
を SNS ルートcanister に再インストールすることで、dapp のcanisters を制御できるようになります（開発者プリンシパル ID
とdapp のcanister ID をコードに貼り付けてください）：

``` rust
use candid::{CandidType, Principal};

#[derive(Default, Clone, CandidType, Debug)]
pub struct CanisterSettingsArgs {
    pub controllers: Option<Vec<candid::Principal>>,
    pub compute_allocation: Option<candid::Nat>,
    pub memory_allocation: Option<candid::Nat>,
    pub freezing_threshold: Option<candid::Nat>,
}

#[derive(CandidType)]
pub struct UpdateSettingsArgs {
    pub canister_id: candid::Principal,
    pub settings: CanisterSettingsArgs,
    pub sender_canister_version: Option<u64>,
}

#[ic_cdk::update]
async fn recover() {
    // put your developer principal here:
    let developer_principal = Principal::from_text("").unwrap();
    let can_settings = CanisterSettingsArgs {
        controllers: Some(vec![developer_principal]),
        compute_allocation: None,
        memory_allocation: None,
        freezing_threshold: None,
    };
    // put the registered canister ID here:
    let canister_id = Principal::from_text("").unwrap();
    let settings = UpdateSettingsArgs {
        canister_id,
        settings: can_settings,
        sender_canister_version: None,
    };
    ic_cdk::api::call::call::<_, ()>(Principal::from_text("aaaaa-aa").unwrap(), "update_settings", (settings,)).await.unwrap();
}
```

を入力し、起動します：

``` bash
dfx canister call sns_root recover '()'
```

メインネットでテストフライトを実行する場合は、`dfx canister` に追加の引数として`--network ic` を渡します。

<!---

# Testing on mainnet (SNS testflight)

## Overview

Once a developer has tested the process of an SNS, it is highly recommended they do an **"SNS testflight" [on the mainnet](./testing-on-mainnet.md)**. An SNS testflight is when a developer deploys their dapp (to the mainnet) and hands control of it to a mock SNS (on the mainnet). 

**The main intent of performing an SNS testflight is for a developer to experience how a dapp works *after* it has been decentralized, so developer can make sure their dapps is ready for decentralization. It does not test the actual process of decentralizing it.**

:::info 
A testflight is not a repo or set of tools, but an *activity* (deploying a dapp and handing control of it to a mock SNS), so the instructions for [testing on the mainnet](./testing-on-mainnet.md) utilize various tools, but developers can of course use any tools they wish. 
:::

Among other things, here are some examples of issues developers find after running an SNS testflight: 
* Developers notice they need better pipeline for creating proposals to update a dapp.
* Developers notice they may have been decentralized prematurely and need to fix some things.
* Developers notice they may need better monitoring before decentralizing.

The mock SNS used in a SNS testflight gives developers the ability to see what the post-decentralization lifecycle of a dapp looks like, but still provides a way for a developer to keep control of their dapp. **Therefore, developers are encouraged to run perform an SNS testflight on the mainnet, potentially for multiple days or weeks, to ensure that all aspects have been covered.**

## SNS Testflight vs SNS production

The main differences to production SNS deployment are summarized here:

* Testflight SNS is deployed by the developer instead of NNS; in particular, no NNS proposals are involved.
* No decentralization swap is actually performed; in particular, the developer has full control over the SNS for the entire duration of the testflight.
* The developer can also keep direct control over the dapp's canisters registered with testflight SNS.
* When deployed on the mainnet, testflight SNS is deployed to a regular *application subnet* instead of a dedicated *SNS subnet*.

## Prerequisites

To perform SNS testflight using the instructions that follow, you will need the following tools:

- [x] [dfx](https://github.com/dfinity/sdk)
- [x] [sns-cli](https://github.com/dfinity/ic)
- [x] [quill](https://github.com/dfinity/quill)
- [x] [didc](https://github.com/dfinity/candid) (for advanced tests)

:::info 
Developers can use any set of tools that accomplish the goals of a testflight.
:::

### Installing `sns-cli`

Instead of building `sns-cli` locally, you can download pre-compiled binaries for [Linux](https://download.dfinity.systems/ic/82a53257ed63af4f602afdccddadc684df3d24de/openssl-static-binaries/x86_64-linux/sns.gz) and [macOS](https://download.dfinity.systems/ic/82a53257ed63af4f602afdccddadc684df3d24de/openssl-static-binaries/x86_64-darwin/sns.gz). You can replace the commit hash in the links by the most recent one obtained via running the command:
```
./gitlab-ci/src/artifacts/newest_sha_with_disk_image.sh origin/master
```
from the root directory of the [IC monorepo](https://github.com/dfinity/ic).

You can download pre-compiled binaries for quill [here](https://github.com/dfinity/quill/releases).

You can download pre-compiled binaries for didc [here](https://github.com/dfinity/candid/releases).

### Versions

This guide has been tested with the following version of the tools:
- **dfx: 0.13.1**
- **sns-cli: 82a53257ed63af4f602afdccddadc684df3d24de**
- **quill: v0.4.0**
- **didc: 0.3.0 (2022-11-17)**

## High-level 

A testflight typically consists of the following steps:

1. Import and download the SNS canisters.
2. Deploy testflight SNS (mock SNS canisters to an application subnet) on the mainnet and store the developer neuron ID.
4. Deploy a dapp.
5. Register a dapp (hand over control) to the mock SNS canister.
6. Test upgrading canisters via SNS proposals.

Activities developers may need to do in running a testflight:
* Check the proposals.
* Test executing code on SNS managed canisters via SNS proposals.
* Abort the testflight.

## Steps to run a testflight

### Step 1. Import and download SNS canisters

To import the SNS canisters in the `dfx.json` file of your project and download their WASM binaries, run

```bash
DFX_IC_COMMIT=82a53257ed63af4f602afdccddadc684df3d24de dfx sns import
DFX_IC_COMMIT=82a53257ed63af4f602afdccddadc684df3d24de dfx sns download
```
in the root directory of your project.

### Step 2. Deploy testflight SNS (mock SNS canisters to an application subnet) on mainnet and store the developer neuron ID

To deploy the testflight SNS, run

```bash
sns-cli deploy-testflight
```
To deploy on the mainnet, pass `--network ic` as an additional argument.

After the deployment finishes, make sure to store the developer neuron ID
which you will use to submit SNS proposals.
The testflight SNS parameters are configured so that this developer neuron ID
has full control over the testflight SNS.
The developer neuron ID is the last part of the testflight deployment output, e.g.:

```
Developer neuron IDs: 
```

### Step 3. Add SNS root as an additional controller of all SNS managed dapp canisters

Add the SNS root canister as an **additional** controller of all the canisters
that you want to manage by the testflight SNS.
For a canister called `test`, you can do so as follows:

```bash
dfx canister update-settings --add-controller $(dfx canister id sns_root) test
```

When running the testflight on the mainnet, pass `--network ic` as an additional argument
to **both** invocations of `dfx canister`.

### Step 4. Register dapp canisters with SNS root

Register all canisters that are supposed to be managed by the testflight SNS
by submitting an SNS proposal via `quill`.

When running the testflight locally, export the environment variable `IC_URL`
to point to your local IC instance, e.g.,

```bash
export IC_URL="http://localhost:8080/"
```

Determine the **absolute** path to the PEM file of your identity.
Typically, this file is located at
`.config/dfx/identity/$(dfx identity whoami)/identity.pem`
under your home directory.

Finally, prepare and send the SNS proposal via `quill`:

```bash
export DEVELOPER_NEURON_ID="594fd5d8dce3e793c3e421e1b87d55247627f8a63473047671f7f5ccc48eda63"
export PEM_FILE="/home/martin/.config/dfx/identity/$(dfx identity whoami)/identity.pem"
export CID="$(dfx canister id test)"
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Register dapp's canisters with SNS.\"; url=\"https://example.com/\"; summary=\"This proposal registers dapp's canisters with SNS.\"; action=opt variant {RegisterDappCanisters = record {canister_ids=vec {principal\"$CID\"}}}})" $DEVELOPER_NEURON_ID > register.json
quill send register.json
```

When running the testflight on the mainnet, pass `--network ic` as an additional argument to `dfx canister`
when obtaining the dapp's canister IDs.
Otherwise, pass `--insecure-local-dev-mode` as an additional argument to `quill send`.

You can also register multiple canisters via a single SNS proposals by adjusting `RegisterDappCanisters`
in the `--proposal` argument above to, e.g.,

```
RegisterDappCanisters = record {canister_ids=vec {principal\"$CID1\"; principal\"$CID2\";}}
```

Once the SNS proposal is executed, you should see all the registered dapp's canisters listed as dapps in

```bash
dfx canister call sns_root list_sns_canisters '(record {})'
```
When running the testflight on the mainnet, pass `--network ic` as an additional argument to `dfx canister` above.

### Step 5. Test upgrading canisters via SNS proposals

Determine the path to the new wasm binary that you want to upgrade the canister to.
For projects build with `dfx`, this binary is typically located at
`.dfx/<network>/canisters/<canister-name>/<canister-name>.wasm`
under the root directory of your project,
where `<network>` is the network (e.g., `local` or `ic`) and `<canister-name>` is the name
of the canister according to `dfx.json`.

Now you can prepare and send the SNS proposal via `quill`:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-upgrade-canister-proposal --summary "This proposal upgrades test canister." --title "Upgrade test canister." --url "https://example.com/" --target-canister-id $CID --wasm-path "./.dfx/local/canisters/test/test.wasm" $DEVELOPER_NEURON_ID > upgrade.json

quill send upgrade.json | grep -v "^ *new_canister_wasm"
```
Unless you run the testflight against the mainnet, pass `--insecure-local-dev-mode` as an additional argument to `quill send`.

You can omit `grep -v "^ *new_canister_wasm"` above to see the new WASM binary in the output. Note that the output then contains the entire WASM binary and can be huge!

## Activities developer may need to do

### Test executing code on SNS managed canisters via SNS proposals

To execute code on SNS managed canisters via SNS proposals, you can use [generic proposals](../managing/making-proposals.md). To use such proposals, the SNS managed canisters that define the behavior of such a proposal must expose a pair of public functions (referred to as **generic** functions in the following):
- a validation function to validate and render the proposal payload;
- an execution function to perform an action given the proposal payload.

The validation function must return a value of the Candid type
`variant { Ok: text; Err: text; }`, e.g., `Result<String, String>` in Rust.
If the validation function returns `Ok(rendering)`, then
the proposal is submitted and the `rendering` string is included
into the proposal.
Otherwise, the proposal submission fails.

The execution function gets the same binary payload as passed to the validation function
and its code gets executed if the proposal is accepted.
It should not return any value because this return value is ignored.

To use generic functions, you must first
submit an SNS proposal to register these functions
with SNS governance:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Register generic functions.\"; url=\"https://example.com/\"; summary=\"This proposals registers generic functions.\"; action=opt variant {AddGenericNervousSystemFunction = record {id=1000:nat64; name=\"MyGenericFunctions\"; description=null; function_type=opt variant {GenericNervousSystemFunction=record{validator_canister_id=opt principal\"$CID\"; target_canister_id=opt principal\"$CID\"; validator_method_name=opt\"validate\"; target_method_name=opt\"execute\"}}}}})" $DEVELOPER_NEURON_ID > register-generic-functions.json

quill send register-generic-functions.json
```
You have to assign a distinct numeric identifier to all generic functions registered with SNS governance.
Note that this identifier has to be at least 1000.
You also have to provide a name and an optional description of the generic functions
that are rendered in the proposal, but do not directly relate to the functions' names
in the canister code (the name is `MyGenericFunctions` and the description omitted in the sample code above).

Once the proposal to register generic functions is accepted, you can submit proposals to execute them
with a given binary payload:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal --proposal "(record { title=\"Execute generic functions.\"; url=\"https://example.com/\"; summary=\"This proposal executes generic functions.\"; action=opt variant {ExecuteGenericNervousSystemFunction = record {function_id=1000:nat64; payload=blob \"DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00\"}}})" $DEVELOPER_NEURON_ID > execute-generic-functions.json

quill send execute-generic-functions.json
```

The generic functions to be executed are specified by their numeric identifier defined in their registration proposal.
The payload is a blob that is passed to both generic functions.
The above sample payload `blob \"DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00\"` was obtained via

```bash
didc encode '(record {major=2:nat32; minor=3:nat32;})' --format blob
blob "DIDL\01l\02\b9\fa\ee\18y\b5\f6\a1Cy\01\00\02\00\00\00\03\00\00\00"
```
and can be decoded as Candid payload (a record with two fields) in the canister Rust code:

```rust
#[derive(CandidType, Debug, Deserialize)]
struct Version {
  major: u32,
  minor: u32,
}

#[ic_cdk::update]
fn validate(x: Version) -> Result<String, String> {
  // ...
}
```

### Check the proposals of the testflight SNS

You can list all proposals in the testflight SNS as follows:

```bash
dfx canister call sns_governance list_proposals '(record {include_reward_status = vec {}; limit = 0; exclude_type = vec {}; include_status = vec {};})'
```
When running the testflight on the mainnet, pass `--network ic` as an additional argument to `dfx canister`.
You can also provide a limit and thus only obtain the last few proposals.

### Aborting the testflight

As the developer keeps direct control over the registered dapp's canisters during the testflight,
you can directly manage your dapp's canisters during the testflight if needed.
However, we strongly encourage you to make sure you can also perform all required operations
only via SNS proposals (possibly after upgrading your dapp).

Once you are done with testing all kinds of SNS proposals needed to operate your dapp,
you can finish the testflight.
Make sure that you are a controller of all the canisters registered with the testflight SNS, e.g.,
by invoking

```bash
dfx canister status test
```

When running the testflight on the mainnet, pass `--network ic` as an additional argument to `dfx canister`.

If this is the case, you can safely delete the SNS testflight canisters.
Otherwise, you can restore control over your dapp's canisters by reinstalling
the SNS root canister with the following code (make sure to paste the developer principal ID
and your dapp's canister ID into the code):

```rust
use candid::{CandidType, Principal};

#[derive(Default, Clone, CandidType, Debug)]
pub struct CanisterSettingsArgs {
    pub controllers: Option<Vec<candid::Principal>>,
    pub compute_allocation: Option<candid::Nat>,
    pub memory_allocation: Option<candid::Nat>,
    pub freezing_threshold: Option<candid::Nat>,
}

#[derive(CandidType)]
pub struct UpdateSettingsArgs {
    pub canister_id: candid::Principal,
    pub settings: CanisterSettingsArgs,
    pub sender_canister_version: Option<u64>,
}

#[ic_cdk::update]
async fn recover() {
    // put your developer principal here:
    let developer_principal = Principal::from_text("").unwrap();
    let can_settings = CanisterSettingsArgs {
        controllers: Some(vec![developer_principal]),
        compute_allocation: None,
        memory_allocation: None,
        freezing_threshold: None,
    };
    // put the registered canister ID here:
    let canister_id = Principal::from_text("").unwrap();
    let settings = UpdateSettingsArgs {
        canister_id,
        settings: can_settings,
        sender_canister_version: None,
    };
    ic_cdk::api::call::call::<_, ()>(Principal::from_text("aaaaa-aa").unwrap(), "update_settings", (settings,)).await.unwrap();
}
```

and invoking:

```bash
dfx canister call sns_root recover '()'
```

When running the testflight on the mainnet, pass `--network ic` as an additional argument to `dfx canister`.
-->
