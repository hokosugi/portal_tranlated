# 管理canisters

## 概要

このセクションのチュートリアルに従ったり、[examples](https://github.com/dfinity/examples)リポジトリからexamplesをクローンしたりして、IC SDKの使い方を試したことがある方は、すでにプログラムをビルドして **canisters**.このセクションでは、canister のライフcycle に関する追加情報と、canisters の管理方法について説明します。

## canister 識別子の取得

ご希望の開発ワークフローに応じて、canister の一意な識別子を、プログラムをコンパイルする前または後に取得することができます。たとえば、コードを記述する前にサブネット上のcanister に一意の識別子を確保したい場合は、`dfx canister create` コマンドを実行します。このコマンドは、基本的に空のcanister プレースホルダーを作成し、そこに後でコードをインストールすることができます。作成されたcanister には一意な識別子が付けられます。

canister の一意な識別子を取得するには：

- #### ステップ 1: コンピュータで新しいターミナル・ウィンドウまたはタブを開きます。

- #### ステップ2：次のようなコマンドを実行して、作成する予定のcanister の新しいプロジェクトを作成します：
  
  ```
    dfx new YOUR-PROJECT-NAME
  ```
  
  プロジェクトに使用する名前は、デフォルトでcanister の名前としても使用されることに注意してください。

- #### ステップ3：新しいプロジェクト・ディレクトリに移動します。

- #### ステップ 4:`dfx.json` 設定ファイルを開き、使用するcanister 実行環境（IC ブロックチェーンなど）のホストとポートを設定します。
  
  ローカル・デプロイメントを使用している場合は、このステップを省略できます。
  
  また、コードをコンパイルする前に必要と思われる追加のcanisters の識別子を作成したい場合は、オプションでcanisters の名前を変更したり、canister の設定を設定ファイルに追加したりすることもできます。

- #### ステップ5： 必要であれば、以下のコマンドを実行して、ローカルのcanister 実行環境を起動します：
  
  ```
    dfx start --background
  ```
  
  ほとんどの場合、このステップはcanisters をローカルで実行する場合にのみ必要です。
  
  ICブロックチェーンなどのリモート実行環境で実行するためにcanisters を登録する場合は、このパラメータで指定された環境でタスクを実行するために、`--network` コマンドラインオプションを含めることになります。

- #### ステップ6：以下のコマンドを実行して、`dfx.json` で定義されたcanisters に一意な識別子を登録します：
  
  ```
    dfx canister create --all
  ```
  
  このコマンドにより、`.dfx/local` ディレクトリが作成され、プロジェクト用の`canister_ids.json` ファイルがそのディレクトリに追加されます。

## ローカル識別子付きcanister のビルド

プロジェクトのソースコードを書いたら、canister としてデプロイする前に、WebAssembly モジュールにコンパイルする必要があります。

ローカルでデバッグするためだけにプロジェクトをコンパイルするのであれば、ローカルで定義された識別子を生成することができます。

ローカルで定義された識別子を生成するには

- #### ステップ 1: 必要に応じた構成設定とプログラム・ロジックでプロジェクトを作成します。

- #### ステップ 2: 必要に応じて、ローカルのcanister 実行環境を起動します。
  
  ICブロックチェーンなどのリモート実行環境で実行するためにcanisters をコンパイルする場合は、このパラメータで指定された環境でタスクを実行するために、`--network` コマンドラインオプションを含めます。

- #### ステップ3：以下のコマンドを実行して、`dfx.json` で定義されたcanisters に対してハードコードされたローカル識別子を生成します：
  
  ```
    dfx build --check
  ```
  
  ICブロックチェーンにプロジェクトをデプロイする前に、ローカルで定義した識別子を置き換える一意のcanister 識別子を登録する必要があることに注意してください。

## デプロイcanisters

プログラムをコンパイルした後、コンパイルしたコードをローカルcanister 実行環境または IC ブロックチェーン上で実行するcanister にインストールできます。

事前またはビルドプロセス中に作成されたcanister 識別子が、デプロイ時にコードがインストールされる場所を決定します。

コードを初めてデプロイするには

- #### ステップ 1: 新しいターミナルを開き、プロジェクト・ディレクトリに移動します。

- #### ステップ 2: 必要であれば、ローカルのcanister 実行環境を起動します。
  
  ほとんどの場合、このステップはcanisters をローカルで実行する場合にのみ必要です。
  
  ICブロックチェーンなどのリモート実行環境で実行するためにcanisters を登録する場合は、このパラメータで指定した環境でタスクを実行するために、`--network` コマンドラインオプションを指定します。

- #### ステップ 3: デプロイしたいすべてのcanisters のcanister 識別子があることを確認します。

- #### ステップ4：以下のコマンドを実行して、canisters をすべてデプロイします：
  
  ```
    dfx canister install --all
  ```

## canister ID を検索します。

すべてのcanisters には一意の識別子があります。canister例えば、dapp のフロントエンドcanister にアクセスしたり、Candid Web インターフェースを使用してサービスと対話したりする場合は、適切なcanister 識別子を指定する必要があります。

識別子はcanisters がデプロイされる環境に固有であるため、情報を格納するために使用されるファイルは異なるディレクトリにあります。例えば、ローカルに配置されたcanister の識別子は、プロジェクトの`.dfx/local/canister_ids.json` ファイルにあります。

`dfx canister id` コマンドを実行すると、特定のcanister のcanister 識別子を調べることができます。たとえば、ローカルのcanister 実行環境にデプロイされた`lookup` canister のcanister 識別子を調べるには、次のコマンドを実行します：

    dfx canister id lookup

`ic` エイリアスで指定された環境にデプロイされた同じcanister のcanister 識別子を調べるには、次のコマンドを実行します：

    dfx canister --network=ic id lookup

## 既存のウォレットの追加canisters

ローカルで開発を行っている場合、新しいプロジェクトを作成すると、そのプロジェクトのcanisters で使用するデフォルトのウォレットが自動的に作成されます。以前作成したcanisters を使っているプロジェクトにウォレットを追加したい場合は、`dfx` にウォレットを生成させることができます。

既存のcanister で使用するウォレットを追加するには：

- #### ステップ 1: ターミナルを開き、プロジェクトディレクトリに移動します。

- #### ステップ 2: 必要であれば、以下のコマンドを実行してローカルのcanister 実行環境を停止します：
  
  ```
    dfx stop
  ```

- #### ステップ 3:`.dfx` ディレクトリを削除します。

- #### ステップ 4: 次のコマンドを実行して、ローカルのcanister 実行環境ネットワークを起動します：
  
  ```
    dfx start --clean
  ```

## を再インストールします。canister

開発中にcycle をインストールし、デバッグや改良を行いながらプログラムを更新したい場 合があります。

このような場合、登録したcanister 識別子を保持し、canister のコードやステー トは保持しないでください。たとえば、canister にはテスト・データしかなく、それを保持したくない場合や、プログラムを完全に変更することにした場合などです。このような場合、以前のプログラムのインストールに使用したcanister 識別子を使用して再インストールすることができます。

canister を再インストールするには：

- #### ステップ 1: 新しいターミナルを開き、プロジェクト・ディレクトリに移動します。

- #### ステップ 2: 必要であれば、ローカルのcanister 実行環境を起動します。
  
  ほとんどの場合、このステップはcanisters をローカルで実行する場合にのみ必要です。
  
  ICブロックチェーンなどのリモート実行環境で実行するためにcanisters を登録する場合は、このパラメータで指定した環境でタスクを実行するために、`--network` コマンドラインオプションを指定します。

- #### ステップ 3: 再デプロイするすべてのcanisters のcanister 識別子があることを確認します。

- #### ステップ 4：canister ごとに以下のコマンドを実行して、canisters をすべて再デプロイします：
  
  ```
    dfx canister install <canister id or name> --mode reinstall
  ```

canister にコードまたはステートに関連付けられているかどうかに関係なく、canister を置き換えるために`reinstall` モードを使用できることに注意してください。

## を所有する ID を設定します。canister

ほとんどの場合、`dfx canister create` コマンドを最初に実行すると、`default` ユーザ ID が自動的に作成されます。このデフォルトのIDは、ローカルのユーザーアカウント用に生成された公開鍵と秘密鍵のペアで構成されています。通常、この`default` IDは、作成するすべてのプロジェクトとデプロイするすべてのcanisters のデフォルトの所有者でもあります。ただし、`default` ユーザー ID が使用されないようにするために、選択した ID を積極的に作成して使用することができます。

例として、`registered_owner` ID を作成し、`pubs` プロジェクトの登録、ビルド、デプロイ、呼び出しに使用するシナリオを示します。

プロジェクトの ID を設定するには

- #### ステップ 1: 以下のコマンドを実行して、新しいプロジェクトを作成します：
  
  ```
    dfx new project
  ```

- #### ステップ 2: 以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：
  
  ```
    cd project
  ```

- #### ステップ 3: 以下のコマンドを実行して、バックグラウンドでローカルのcanister 実行環境を起動します：
  
  ```
    dfx start --background
  ```

- #### ステップ4：次のコマンドを実行して、新しい`registered_owner` IDを作成します：
  
  ```
    dfx identity new registered_owner
  ```

- #### ステップ 5: 次のコマンドを実行して、`registered_owner` ID を使用するようにアクティブ・ユーザー・コンテキストを設定します：
  
  ```
    dfx identity use registered_owner
  ```

- #### ステップ 6: 以下のコマンドを実行して、プロジェクト用にcanisters を登録、ビルド、デプロイします：
  
  ```
    dfx canister create --all
    dfx build --all
    dfx canister install --all
  ```
  
  これらのコマンドは、`registered_owner` IDを使用して実行され、そのユーザがデプロイされたcanisters の所有者になります。

- #### ステップ 7: 以下のコマンドを実行して、プロジェクトの \*`_backend` canister から`greet` 関数を呼び出し、デプロイが成功したことを確認します：
  
  ```
    dfx canister call project_backend greet '("Sam")'
  ```

## の実行ステートを管理します。canister

canister をデプロイすると、ユーザーや他のcanisters からのリクエストの受信と処理を開始できます。 リクエストの送信と返信の受信が可能なCanisters は、**実行**中ステートと見なされます。

通常、canisters はデフォルトで Running 状態に置かれますが、canister を一時的または恒久的に停止したい場合もあります。例えば、canister をアップグレードする前に停止したい場合などです。canister を停止すると、進行中で完了まで実行するかロールバックする必要があるメッセー ジを適切に処理することができます。また、canister を削除する前提条件として、canister を停止してメッセージキューをきれいに消去しておきたい場合もあるでしょう。

`dfx canister status` コマンドを実行すると、すべてのcanisters または指定したcanister の現在のステータスを確認できます。例えば、ローカルのcanister 実行環境で実行されているすべてのcanisters のステータスを確認するには、以下のコマンドを実行します：

    dfx canister status --all

このコマンドは、canisters が現在実行中の場合、以下のような出力を返します：

    Canister status_check's status is Running.
    Canister status_check_assets's status is Running.

現在実行中のcanisters を停止するには、`dfx canister stop` コマンドを実行します。

    dfx canister stop --all

このコマンドは、次のような出力を表示します：

    Stopping code for canister status_check, with canister_id 75hes-oqbaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q
    Stopping code for canister status_check_assets, with canister_id cxeji-wacaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q

`dfx canister status` コマンドを再実行すると、処理が必要な保留中のメッセージがないことを示すステー タス`Stopped` が表示されたり、処理が必要な飛行中のメッセージがあることを示すステー タス`Stopping` が表示されたりします。

canister を再起動するには、例えばcanister のアップグレードが成功した後、`dfx canister start` コマンドを実行します。例えば、canisters をすべて再起動するには、以下のコマンドを実行します：

    dfx canister start --all

このコマンドは、次のような出力を表示します：

    Starting code for canister status_check, with canister_id 75hes-oqbaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q
    Starting code for canister status_check_assets, with canister_id cxeji-wacaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q

## アップグレードcanister

canister 識別子は保持されますがステート は保持されないcanister 再インストールとは異なり、canister アップグレードでは、デプロイされたcanister の状態を保持し、コードを変更することができます。

たとえば、プロフェッショナルなプロフィールとソーシャルなつながりを管理するdapp があるとします。dapp に新機能を追加したい場合、canister のコードを、以前に保存されたデータを失うことなく更新できる必要があります。canister アップグレードでは、プログラムのステートを失うことなく、プログラムの変更に合わせて既存のcanister 識別子を更新することができます。

Motoko で記述されたcanister をアップグレードするときにステート を保持するには、必ず`stable` キーワードを使用して、保持したい変数を特定し ます。Motoko での変数のステート保持の詳細については、[安定変数とアップグレード](/motoko/main/upgrades.md)方法を参照してください。Rust で書かれたcanister をアップグレードする場合は、canister のアップグレード後にデータが適切に保存されるように、[Rust CDK の資産保存の](https://github.com/dfinity/cdk-rs/blob/master/examples/asset_storage/src/asset_storage_rs/lib.rs)例にあるように`pre_upgrade` と`post_upgrade` の関数を使用する必要があります。

canister をアップグレードするには ：

- #### ステップ 1: 新しいターミナルを開き、プロジェクトディレクトリに移動します。

- #### ステップ 2: 必要であれば、ローカルのcanister 実行環境を起動します。
  
  ほとんどの場合、このステップはcanisters をローカルで実行している場合にのみ必要です。
  
  ICブロックチェーンなどのリモート実行環境で実行するためにcanisters を登録する場合は、このパラメータで指定した環境でタスクを実行するために、`--network` コマンドラインオプションを指定します。

- #### ステップ 3: アップグレードしたいすべてのcanisters のcanister 識別子があることを確認します。
  
  プログラムでは、変数宣言で`stable` キーワードを使用して、ステー トを維持する変数を特定する必要があることに注意してください。
  
  安定変数の宣言の詳細については、[**Motoko のマニュアルを**](../../motoko/main/stablememory.md)参照してください。

- #### ステップ 4: 以下のコマンドを実行して、canisters をすべてアップグレードします：
  
  ```
    dfx canister install --all --mode upgrade
  ```

## を削除します。canister

特定のデプロイメント(ローカルまたはリモートのいずれか)上の特定のプ ロジェクトの特定のcanister またはすべてのcanisters を完全に削除する場 合は、コマンドを実行します：

    dfx canister delete <canister-name>

canister を削除すると、canister 識別子、コード、ステート が削除されます。ただし、canister を削除する前に、canister を停止して、保留中のメッセージ要求または応答を消去する必要があります。

## すべての削除canisters

プロジェクトのすべてのcanisters を削除するには、以下の手順に従います：

- #### ステップ 1: 新しいターミナルを開き、プロジェクト・ディレクトリに移動します。

- #### ステップ 2: 必要に応じて、ローカルのcanister 実行環境を起動します。
  
  ほとんどの場合、このステップはcanisters をローカルで実行している場合にのみ必要です。
  
  IC ブロックチェーンなどのリモート実行環境で実行するためにcanisters を削除する場合は、このパラメータで指定した環境でタスクを実行するために`--network` コマンドラインオプションを指定します。

- #### ステップ 3: 以下のコマンドを実行して、ローカルの実行環境canister で実行されているプロジェクトcanisters のステータスを確認します：
  
  ```
    dfx canister status --all
  ```

- #### ステップ 4: 以下のコマンドを実行して、プロジェクトcanisters をすべて停止します：
  
  ```
    dfx canister stop --all
  ```

- #### Step 5: 以下のコマンドを実行し、プロジェクトcanisters を全て削除します：
  
  ```
    dfx canister delete --all
  ```

## リソース

- [Building on the ICで](../../samples/overview.md)サンプルdapps 。

- さまざまなICの概念とサービスについて学ぶための[概念](../../concepts/index.md)。

- ICで使用される様々な用語の定義については、[IC用語集を](../../references/glossary.md)参照してください。

<!---
# Managing canisters

## Overview

If you have experimented with using the IC SDK by following the tutorials in this section or by cloning examples from the [examples](https://github.com/dfinity/examples) repository, you are already familiar with how to build and deploy programs as **canisters**. This section provides additional information about the canister lifecycle and how to manage canisters.

## Obtaining a canister identifier

Depending on your preferred development workflow, you can obtain a unique identifier for your canister, before or after you have a program ready to compile. For example, if you want to reserve a unique identifier for your canister on a subnet before you have written any code, you can do so by running the `dfx canister create` command. This command essentially creates an empty canister placeholder into which you can later install your code. The resulting canister will obtain a unique identifier.

To obtain a unique identifier for a canister:

- #### Step 1:  Open a new terminal window or tab on your computer.

- #### Step 2:  Create a new project for the canister you plan to create by running a command similar to the following:

        dfx new YOUR-PROJECT-NAME

    Note that the name you use for the project is also used as the canister name by default.

- #### Step 3:  Change to your new project directory.

- #### Step 4:  Open the `dfx.json` configuration file and set the host and port for the canister execution environment you want to use (e.g. the IC blockchain).

    If you are using a local deployment, you can skip this step.

    You can also optionally change the names of your canisters or add canister settings to the configuration file if you want to create identifiers for any additional canisters you think you will need before compiling code.

- #### Step 5:  Start the local canister execution environment, if necessary, by running the following command:

        dfx start --background

    In most cases, this step is only necessary if you are running the canisters locally.

    If you were registering canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 6:  Register unique identifiers for the canisters defined in the `dfx.json` by running the following command:

        dfx canister create --all

    The command creates the `.dfx/local` directory and adds the `canister_ids.json` file to that directory for the project.

## Build a canister with a local identifier

After you have written source code for your project, you need to compile it into a WebAssembly module before deploying it as a canister.

If you are only compiling your project for local debugging, you can generate a locally-defined identifier for your project.

To generate a locally-defined identifier:

- #### Step 1:  Create a project with the configuration settings and program logic to suit your needs.

- #### Step 2:  Start the local canister execution environment, if necessary.

    If you were compiling canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 3:  Generate hard-coded local identifiers for the canisters defined in the `dfx.json` by running the following command:

        dfx build --check

    Note that you must register unique canister identifiers to replace your locally-defined identifier before you can deploy the project on the IC blockchain.

## Deploy canisters

After you have compiled a program, you can install the compiled code in a canister running either on a local canister execution environment or on the IC blockchain.

The canister identifier that was created in advance or during the build process determines where your code is installed during deployment.

To deploy the code for the first time:

- #### Step 1:  Open a new terminal and navigate to your project directory.

- #### Step 2:  Start the local canister execution environment, if necessary.

    In most cases, this step is only necessary if you are running the canisters locally.

    If you were registering canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 3:  Verify you have canister identifiers for all of the canisters you want to deploy.

- #### Step 4:  Deploy all of the canisters by running the following command:

        dfx canister install --all

## Look up a canister ID

All canisters have unique identifiers. You often need to use these identifiers to interact with the canister. For example, if you want to access the frontend canister for a dapp or interact with a service using the Candid web interface, you must specify the appropriate canister identifier.

Because the identifiers are specific to the environment where the canisters are deployed, the files used to store the information are in different directories. For example, identifiers for a canister deployed locally are located in the project’s `.dfx/local/canister_ids.json` file.

You can look up the canister identifier for any specific canister by running the `dfx canister id` command. For example, to look up the the canister identifier for the `lookup` canister deployed on the local canister execution environment, you could run the following command:

    dfx canister id lookup

To look up the canister identifier for the same canister deployed on the environment specified by the `ic` alias, you would run the following command:

    dfx canister --network=ic id lookup

## Add a wallet for existing canisters

When you are doing local development, creating a new project automatically creates a default wallet for you to use with the canisters in that project. If you want to add a wallet for projects with canisters that you have previously created, you can force `dfx` to generate one by taking a couple of manual steps.

To add a wallet for use with an existing canister:

- #### Step 1:  Open a terminal and navigate to your project directory.

- #### Step 2:  Stop the local canister execution environment, if necessary, by running the following command:

        dfx stop

- #### Step 3:  Delete the `.dfx` directory.

- #### Step 4:  Start the local canister execution environment network by running the following command:

        dfx start --clean

## Reinstall a canister

During the development cycle, you might want to install, then update your program as you debug and improve it.

In this scenario, you might want to keep the canister identifier you have registered but without preserving any of the canister code or state. For example, your canister might only have test data that you don’t want to keep or you might have decided to change the program altogether. In these scenarios, you may want to reinstall under a canister identifier you used to install a previous program.

To reinstall a canister:

- #### Step 1:  Open a new terminal and navigate to your project directory.

- #### Step 2:  Start the local canister execution environment, if necessary.

    In most cases, this step is only necessary if you are running the canisters locally.

    If you were registering canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 3:  Verify you have canister identifiers for all of the canisters you want to re-deploy.

- #### Step 4:  Re-deploy all of the canisters by running the following command for every canister:

        dfx canister install <canister id or name> --mode reinstall

Note that you can use the `reinstall` mode to replace any canister, regardless of whether the canister has code or state associated with it.

## Set an identity to own a canister

In most cases, a `default` user identity is created for you automatically the first time you run the `dfx canister create` command. This default identity consists of the public and private key pair generated for your local user account. Typically, this `default` identity is also the default owner of all of the projects you create and all of the canisters you deploy. You can, however, proactively create and use identities of your choice to circumvent the `default` user identity from being used.

As an example, the following scenario illustrates creating a `registered_owner` identity that is then used to register, build, deploy, and call the `pubs` project.

To set an identity for a project:

- #### Step 1:  Create a new project by running the following command:

        dfx new project

- #### Step 2:  Change to the project directory by running the following command:

        cd project

- #### Step 3:  Start the local canister execution environment in the background by running the following command:

        dfx start --background

- #### Step 4:  Create a new `registered_owner` identity by running the following command:

        dfx identity new registered_owner

- #### Step 5:  Set the active user context to use the `registered_owner` identity by running the following command:

        dfx identity use registered_owner

- #### Step 6:  Register, build, and deploy canisters for the project by running the following commands:

        dfx canister create --all
        dfx build --all
        dfx canister install --all

    These commands run using the `registered_owner` identity, making that user the owner of the canisters deployed.

- #### Step 7:  Call the `greet` function from your project's *`_backend` canister to verify a successful deployment by running the following command:

        dfx canister call project_backend greet '("Sam")'

## Managing the running state of a canister

After you deploy a canister, it can begin receiving and processing requests from users and from other canisters. Canisters that are available to send requests and receive replies are considered in be in a **Running** state.

Although canisters are normally placed in the Running state by default, there are cases where you might want to temporarily or permanently stop a canister. For example, you might want to stop a canister before upgrading it. Stopping a canister helps to ensure proper handling of any messages that are in progress and need to either run to completion or be rolled back. You might also want to stop a canister to clear its message queue cleanly as a prerequisite to deleting the canister.

You can check the current status of all canisters or a specified canister by running the `dfx canister status` command. For example, to see the status for all canisters running on the local canister execution environment, you would run the following command:

    dfx canister status --all

This command returns output similar to the following if canisters are currently running:

    Canister status_check's status is Running.
    Canister status_check_assets's status is Running.

You can stop canisters that are currently running by running the `dfx canister stop` command.

    dfx canister stop --all

This command displays output similar to the following:

    Stopping code for canister status_check, with canister_id 75hes-oqbaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q
    Stopping code for canister status_check_assets, with canister_id cxeji-wacaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q

If you were to rerun the `dfx canister status` command, you might see a status of `Stopped` indicating that there were no pending messages that needed to processed or a status of `Stopping` indicating that there were messages in-flight that needed to be addressed.

To restart a canister-for example, after a successful canister upgrade—you can run the `dfx canister start` command. For example, to restart all of the canisters, you would run the following command:

    dfx canister start --all

This command displays output similar to the following:

    Starting code for canister status_check, with canister_id 75hes-oqbaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q
    Starting code for canister status_check_assets, with canister_id cxeji-wacaa-aaaaa-aaaaa-aaaaa-aaaaa-aaaaa-q

## Upgrade a canister

Unlike a canister reinstall that preserves the canister identifier but no state, a canister upgrade enables you to preserve the state of a deployed canister, and change the code.

For example, assume you have a dapp that manages professional profiles and social connections. If you want to add a new feature to the dapp, you need to be able to update the canister code without losing any of the previously-stored data. A canister upgrade enables you to update existing canister identifiers with program changes without losing the program state.

To preserve state when you are upgrading a canister written in Motoko, be sure to use the `stable` keyword to identify the variables you want to preserve. For more information about preserving variable state in Motoko, see [stable variables and upgrade methods](/motoko/main/upgrades.md). If you are upgrading a canister written in Rust, you should use `pre_upgrade` and `post_upgrade` functions as illustrated in the [Rust CDK asset storage](https://github.com/dfinity/cdk-rs/blob/master/examples/asset_storage/src/asset_storage_rs/lib.rs) example to ensure data is properly preserved after a canister upgrade.

To upgrade a canister:

- #### Step 1:  Open a new terminal and navigate to your project directory.

- #### Step 2:  Start the local canister execution environment, if necessary.

    In most cases, this step is only necessary if you are running the canisters locally.

    If you were registering canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 3:  Verify you have canister identifiers for all of the canisters you want to upgrade.

    Note that your program must identify the variables for which to maintain state by using the `stable` keyword in the variable declaration.

    For more information about declaring stable variables, see the [**Motoko documentation**](../../motoko/main/stablememory.md).

- #### Step 4:  Upgrade all of the canisters by running the following command:

        dfx canister install --all --mode upgrade

## Delete a canister

If you want to permanently delete a specific canister or all canisters for a specific project on a given deployment (either local, or remote), you can do so by running the command:
 
```
dfx canister delete <canister-name>
```

Deleting a canister removes the canister identifier, code, and state. Before you can delete a canister, however, you must first stop the canister to clear any pending message requests or replies.

## Delete all canisters

To delete all canisters for a project:

- #### Step 1:  Open a new terminal and navigate to your project directory.

- #### Step 2:  Start the local canister execution environment, if necessary.

    In most cases, this step is only necessary if you are running the canisters locally.

    If you were deleting canisters to run on a remote execution environment, e.g. the IC blockchain, you would include the `--network` command-line option to perform tasks on the environment specified under this parameter.

- #### Step 3:  Check the status of the project canisters running on the local canister execution environment by running the following command:

        dfx canister status --all

- #### Step 4:  Stop all of the project canisters by running the following command:

        dfx canister stop --all

- #### Step 5:  Delete all of the project canisters by running the following command:

        dfx canister delete --all

## Resources

-   [Building on the IC](../../samples/overview.md) to explore sample dapps.

-   [Concepts](../../concepts/index.md) to learn about different IC concepts and services.  

-   [IC glossary](../../references/glossary.md) to learn the definitions of various terms used within the IC. 

-->
