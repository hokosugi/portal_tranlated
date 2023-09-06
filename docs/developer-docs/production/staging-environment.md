# ステージング環境

多くのプロジェクトでは、通常のローカル環境とライブ環境以外にステージング環境を用意しておくと便利です。このページではそのようなステージング環境をセットアップする方法を説明します。

## ステージング環境の利点

"なぜ？"から始めるのは理にかなっています。なぜプロジェクトはステージング環境を必要とするのでしょうか？最大の理由の1つはテストです。
本番環境にデプロイされる前に、機能をエンドツーエンドでテストできる別のデプロイ環境を利用できることは非常に便利です。
開発者（と、おそらく一部のベータユーザー）が実際の環境で自分自身で試すことができます。
ローカルのデプロイは本番のInternet Computer を可能な限り忠実に反映しますが、それでも同じではありません。
たとえば、ローカルインスタンスは単一のサブネットとしてのみ実行されます。2つの異なるサブネットが互いに接続するcanisters をテストしたい場合、ローカルでこれを行うことはできません。

ステージング環境を用意する理由は他にもあります：

- 他のサービスとの統合のテスト。
- デプロイメントワークフローのテスト。
- すべてのユーザーに対して機能を本稼働させる前のコストの見積もり。
- エンドツーエンドのテスト

## ステージング環境の設定

このセクションでは、ステージング環境を設定する方法を示します。動作するステージング環境があれば、`--network ic` で実行する`dfx` コマンドを、`--network myStaging` で実行することができます。
もちろん、`myStaging` という名前は他の名前に置き換えることができます（2つの予約名を除く）：`ic` Internet Computer `local` `dfx start` で実行される暗黙のデフォルトネットワーク）。

ネットワーク（この文脈では'環境'とも呼ばれます）は、想定されたものと明示的に設定されたものの2つの方法で定義されます。`ic`
 `--network ic` を（ほとんどの）コマンドに追加すると、そのコマンドはライブのInternet Computer 環境のコンテキストで実行されます。
他のすべてのネットワークは`dfx.json` で明示的に設定されます。`dfx.json` `dfx new` `"networks"` セクションには、少なくとも ネットワークが含まれているはずです。  ネットワークは、 で他のネットワークが指定されていない場合、デフォルトで選択されるネットワークです。`local`
 `local` `--network`

### ネットワークの定義

`dfx.json` に`myStaging` という名前のステージング・ネットワークを追加するには、`dfx.json` の`"networks"` の下にこれを追加します：

``` json
"myStaging": {
    "providers": [
        "https://icp0.io"
    ],
    "type": "persistent"
}
```

`"providers"` のこの値は、`dfx` にネットワークへの接続先を指定します。これはハードコードされた`ic` ネットワークのものと同じです。
 `persistent` というタイプは、このネットワーク上のcanisters がそこに留まることを`dfx` に伝えます。そのため、canister 識別子は`canister_ids.json` ファイルに保存されます。

### ウォレットの設定

デフォルトでどのcycles ウォレットを使うかは、ネットワークごとに別々に保存されます。このため、新しく作成された`myStaging` ネットワークにはまだウォレットが設定されていません。
ほとんどの人はメインの`ic` ネットワークと同じcycles ウォレットを使いたいだけでしょう。そのためには、正しいIDが設定されていることを確認します(`dfx identity use <identity name>`)。
次に、`ic` ネットワークの現在設定されているウォレットを`dfx identity get-wallet --network ic` を使って読み取ります。
最後に、`dfx identity set-wallet <wallet id> --network myStaging` を使って新しく定義したネットワークのウォレットを設定します。
あるいは、この2つを組み合わせてワンライナーにします：

``` bash
dfx identity set-wallet "$(dfx identity get-wallet --network ic)" --network myStaging
```

ステージング環境用に別のcycles ウォレットを使いたい場合は、[ネットワークのクイックスタートに](/developer-docs/setup/deploy-mainnet.md)ある「Cycles ウォレットの作成」の手順に従ってください。

<!---
# Staging Environment

Many projects can benefit from having a staging environment available besides the usual local and live deployments. This page explains how to set up such a staging environment.

## Benefits of a staging environment

It makes sense to start with the "why?" Why would a project want a staging environment? One of the biggest reasons is testing.
Having a separate deployment environment available where features can be end-to-end tested before they get deployed to the production environment is very helpful.
It allows developers (and maybe some beta users) to try things for themselves in a real environment.
While local deployments mirror the live Internet Computer as closely as possible, it is still not the same.
For example, the local instance only runs as a single subnet. If you want to test canisters of two different subnets connecting to each other, you cannot do this locally.

Some more reasons for having a staging environment are:
- Testing integration with other services.
- Testing deployment workflows.
- Estimating costs before setting a feature live for all users.
- End-to-end testing.

## Setting up a staging environment

This section shows how to configure a staging environment. With a working staging environment it is possible to run any `dfx` command that would otherwise take `--network ic` with `--network myStaging` instead.
Of course, the name `myStaging` can be replaced with any other name (except the two reserved ones: `ic`, the built in live Internet Computer and `local`, the implicit default network that runs with `dfx start`).

Networks (or also 'environments' in this context) are defined in two ways: assumed and explicitly configured. Dfx only contains one network as an assumed network: the `ic` network.
If you add `--network ic` to (almost) any command, it will run in the context of the live Internet Computer environment.
All other networks are explicitly configured in `dfx.json`. Looking at any random `dfx.json` (e.g. a fresh one generated with `dfx new`), the `"networks"` section should contain at least the `local` network.
The `local` network is the network that gets chosen by default if no other network is specified with `--network`.

### Network definition

To add a staging network named `myStaging` to `dfx.json`, add this under `"networks"` in your `dfx.json`:

``` json
"myStaging": {
    "providers": [
        "https://icp0.io"
    ],
    "type": "persistent"
}
```

This value for `"providers"` tells `dfx` where to connect to the network. It is identical to the one in the hard-coded `ic` network.
The type `persistent` tells `dfx` that the canisters on this network will stay there. Because of that, the canister identifiers will be saved in the `canister_ids.json` file.

### Configuring a wallet

Which cycles wallet to use by default is stored separately for every network. Because of this, the newly created `myStaging` network has no wallet configured yet.
Most people will just want to use the same cycles wallet as on the main `ic` network. To do so, make sure the correct identity is set (`dfx identity use <identity name>`).
Then, read the `ic` network's currently configured wallet using `dfx identity get-wallet --network ic`.
Finally, set the wallet for the newly defined network with `dfx identity set-wallet <wallet id> --network myStaging`.
Or, combining the two into a one-liner:

``` bash
dfx identity set-wallet "$(dfx identity get-wallet --network ic)" --network myStaging
```

If you prefer to use a separate cycles wallet for the staging environment, follow the instructions in the step 'Creating a Cycles Wallet' in the [network quick start](/developer-docs/setup/deploy-mainnet.md).

-->
