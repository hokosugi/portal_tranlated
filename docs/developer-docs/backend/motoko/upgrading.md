# 6: アップグレードcanisters

## 概要

canister をアップグレードすると、デプロイされたcanister の既存のステート を維持しながら、コードを変更することができます。この機能は、Internet Computer の主要機能の一部であり、canister スマートコントラクトのステートを、従来のデータベースの代わりにWasmメモリとグローバルを使用して永続化します。この保存のステートは**直交持続と**呼ばれます。

Motoko は、安定ストレージとして知られる機能を通じて、 の安定メモリを使用して のステートを保持するための高レベルサポートを提供します。この機能は、 コンパイラとアプリケーション・データの両方の変更に対応するように設計されています。Internet Computer canister Motoko 

次の例を考えてみましょう。dapp があり、職業プロフィールとソーシャルコネクションを管理しているとします。dapp に新機能を追加するためには、canister のコードを、以前に保存したデータを失うことなく更新できる必要があります。canister アップグレードを使用すると、プログラムの状態を失うことなく、プログラムの変更に伴って既存のcanister 識別子を更新することができます。

Motoko で記述されたcanister をアップグレードするときにステー トを保持するには、stable キーワードを使用して、保持したい変数を特定 してください。Motoko での変数状態の保持の詳細については、[安定変数とアップグレード](https://internetcomputer.org/docs/current/motoko/main/upgrades)方法を参照してください。

## 前提条件

作業を始める前に、[開発者環境ガイドの](./dev-env.md)説明に従って開発者環境をセットアップしていることを確 認してください。

## アップグレードの互換性の確認

アップグレードの前にcanister のアップグレード互換性を確認するには、[このドキュメントを](../../../motoko/main/compatibility)ご覧ください。

## アップグレードcanister

canister をアップグレードするには、まず、新しいターミナルを開き、プロジェ クト・ディレクトリに移動します。

次に、ローカルのcanister 実行環境をコマンドで起動します：

    dfx start --clean --background

:::info
 canisters を IC ブロックチェーンなどのリモート実行環境で実行するように登録する場合は、--network コマンドラインオプションを指定し、このパラメータで指定された環境でタスクを実行します。
::：

次に、アップグレードしたいcanister のcanister 識別子を取得します。そのためには、以下のコマンドを使用します：

    dfx canister id [canister-name]

次に、アップデートに含めたいcanister のコードに変更を加えます。

:::info
プログラムは、変数宣言で stable キーワードを使用して、ステー トを維持する変数を特定する必要があることに注意してください。

安定変数の宣言に関する詳細は、[安定変数の宣言のセクションを](https://internetcomputer.org/docs/current/motoko/main/upgrades#declaring-stable-variables)参照してください。
::：

すべてのcanisters をアップグレードするには、次のコマンドを使用します：

    dfx canister install --all --mode upgrade

単一のcanister をアップグレードするには、次のコマンドを使用します：

    dfx canister install [canister-id] --mode upgrade

## 次のステップ

次に、[canister 間の呼び出しを見て](intercanister-calls.md)みましょう。

<!---
# 6: Upgrading canisters

## Overview
Upgrading a canister allows you to preserve the existing state of a deployed canister while making changes to the code. This functionality is part of a key feature of the Internet Computer which allows canister smart contract states to persist using Wasm memory and globals instead of a traditional database. This state of preservation is referred to as **orthogonal persistence**.

Motoko provides high-level support for preserving a canister's state using the Internet Computer's stable memory through a feature known as stable storage. This feature is designed to accommodate changes to both the Motoko compiler and the application data. 

Consider the following example: you have a dapp that manages professional profiles and social connections. To add a new feature to the dapp, you need to be able to update the canister code without losing any of the previously stored data. A canister upgrade enables you to update existing canister identifiers with program changes without losing the program state.

To preserve state when you are upgrading a canister written in Motoko, be sure to use the stable keyword to identify the variables you want to preserve. For more information about preserving variable state in Motoko, see [stable variables and upgrade methods](https://internetcomputer.org/docs/current/motoko/main/upgrades). 

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Verify upgrade compatibility

To verify the upgrade compatibility of your canister prior to upgrading, please review [this documentation](../../../motoko/main/compatibility).

## Upgrading a canister

To upgrade a canister, first open a new terminal and navigate to your project directory.

Then, start the local canister execution environment with the command:

```
dfx start --clean --background
```

:::info
If you were registering canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the --network command-line option to perform tasks on the environment specified under this parameter.
:::


Next, obtain the canister identifier of the canister(s) you'd like to upgrade. To do so, the following command can be used:

```
dfx canister id [canister-name]
```

Then, make any changes to the canister's code that you'd like to be included in the update.

:::info
Note that your program must identify the variables for which to maintain state by using the stable keyword in the variable declaration.

For more information about declaring stable variables, see the [declaring stable variables section](https://internetcomputer.org/docs/current/motoko/main/upgrades#declaring-stable-variables).
:::


To upgrade all canisters, the following command can be used:

```
dfx canister install --all --mode upgrade
```

To upgrade a single canister, use the command:

```
dfx canister install [canister-id] --mode upgrade
```

## Next steps

Next, let's take a look at [inter-canister calls](intercanister-calls.md).

-->
