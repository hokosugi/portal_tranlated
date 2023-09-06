# トラブルシューティング

## 概要

このセクションでは、以下のタスクに関連する一般的な問題のトラブルシューティング、解決、または回避に役立つ情報を提供します：

- IC SDK のダウンロードとインストール

- canisters の作成、ビルド、またはデプロイ。

- [IC SDK](../setup/install/index.mdx) の使用。

- ローカルのcanister 実行環境の実行。

## 既存のプロジェクトの移行

現在のところ、旧バージョンの IC SDK を使用して作成したプロジェクトの自動移行や後方互換性はありません。最新バージョンにアップグレードした後、以前のバージョンのIC SDKで作成したプロジェクトをビルドまたはインストールしようとすると、エラーや失敗のメッセージが表示されることがあります。

しかし、多くの場合、`dfx.json` 設定ファイルの`dfx` 設定を手動で変更し、現在インストールされている IC SDK のバージョンと互換性があるようにプロジェクトを再構築することで、以前のリリースのプロジェクトを引き続き使用できます。

たとえば、IC SDK バージョン`0.8.0` で作成されたプロジェクトがある場合、`dfx.json` ファイルをテキストエディタで開き、`dfx` 設定を最新バージョンに変更するか、セクションを完全に削除します。

## ローカルcanister 実行環境の再起動

ローカルのcanister 実行環境を起動すると、ステート が古くなっているために失敗する場合があります。`dfx start` を実行してローカルのcanister 実行環境を起動する際に問題が発生した場合：

- #### ステップ1：実行環境が使用するICエミュレーションを表示するターミナルで、Control-Cを押してローカルcanister 実行環境のプロセスを中断します。

- #### ステップ2：以下のコマンドを実行して、ローカルcanister 実行環境を停止します：
  
  ```
    dfx stop
  ```

- #### ステップ 3: 次のコマンドを実行して、ローカルのcanister 実行環境をクリーンなステートで再起動します：
  
  ```
    dfx start --clean
  ```
  
  `--clean` オプションはチェックポイントと古いステート情報をプロジェクトのキャッシュから削除するので、ローカルのcanister 実行環境と Web サーバープロセスをクリーンなステートで再起動できます。

:::caution
ただし、`dfx start --clean` を実行してステート情報をリセットした場合、既存のcanisters も削除されることに注意してください。
::：

- #### ステップ4：`dfx start --clean` を実行した後、以下のコマンドを実行してcanisters を再作成します：
  
  dfxcanister create --all
  dfx build
  dfxcanister install --all

## canisters ディレクトリの削除

IC への接続に成功し、canister 識別子を登録した後、canisters のビルドまたはデプロイに問題が発生した場合は、canisters の再構築または再デプロイを試みる前に、`canisters` ディレクトリを削除する必要があります。

プロジェクトのルート・ディレクトリで次のコマンドを実行すると、プロジェクトの`canisters` ディレクトリを削除できます：

    rm -rf ./.dfx/* canisters/*

## dfx の再インストール

遭遇する可能性のあるバグの多くは、IC SDKをアンインストールして再インストールすることで対処できます。ここでは、IC SDKを再インストールする方法をいくつか紹介します。

開発環境にIC SDKが1つのバージョンしかインストールされていない場合は、通常、以下のコマンドを実行することで、最新バージョンのIC SDKをアンインストールして再インストールすることができます：

    ~/.cache/dfinity/uninstall.sh && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

IC SDK のバイナリの場所を変更した場合（バイナリのタイトルは`dfx` ）、次のコマンドを実行して、PATH にあるバージョンの IC SDK をアンインストールし、最新バージョンの IC SDK を再インストールします：

    rm -rf ~/.cache/dfinity && rm $(which dfx) && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

## Xcode の前提条件

IC SDKのいくつかのバージョンでは、macOSコンピュータで新しいプロジェクトを作成する際にXcodeをインストールするようプロンプトが表示されました。このプロンプトは削除され、`dfx new` コマンドは macOS 開発者ツールをインストールする必要はありません。ただし、プロジェクトの Git リポジトリを作成する場合は、**Developer Command Line Tools**をインストールする必要があります。

開発者ツールがインストールされているかどうかは、`xcode-select -p` を実行して確認できます。開発者ツールをインストールするには、`xcode-select --install` を実行します。

## Apple ARMシリコン

Apple シリコンを搭載した Mac を使用していて問題がある場合 (`bad CPU type in executable: dfx` など)、Rosetta をインストールする必要があるかもしれません。

``` shell
softwareupdate --install-rosetta 
```

## VM使用時のビルド失敗

UbuntuやCentOSの仮想マシンイメージを使用してIC SDKを実行している場合、`dfx build` コマンドを実行しようとすると、次のようなエラーメッセージが表示されることがあります：

    Building hello...
    An error occurred:
    Io(
        Os {
            code: 2,
            kind: NotFound,
            message: "No such file or directory",
        },
    )

## アドレス使用中エラーまたはオーファンプロセス

ローカルでプロジェクトを開発している場合、ローカルのcanister 実行環境を別のターミナルまたはバックグラウンドで実行していることがよくあります。ローカル環境のプロセスが適切に終了されない場合、アドレスがすでに使用中であることを示すオペレーティング・システム・エラーが表示されたり、`dfx stop` コマンドを使用してプロセスを正常に停止できないことがあります。

この問題に遭遇する可能性のあるシナリオはいくつかあります。たとえば、ローカルのプロジェクト・ディレクトリーで`dfx start` を実行した後、canister 実行環境のプロセスを停止せずに別のローカルのプロジェクト・ディレクトリーに変更すると、この問題が発生する可能性があります。

アドレスが既に使用されている、またはバックグラウンドでプロセスが既に実行されているというメッセージが表示されたり、そのアドレスが既に使用されていると疑われる問題が発生した場合は、以下の手順を実行してください：

- #### ステップ1：以下のコマンドを実行して、localhostへのデフォルトバインディングを使用している場合に4943ポートをリッスンしているプロセスを確認します：
  
  ```
    lsof -i tcp:4943
  ```

- #### ステップ2：以下のコマンドを実行して、バックグラウンドで実行されているプロセスを終了します：
  
  ```
    killall dfx replica
  ```

- #### ステップ3：現在のターミナルを閉じ、新しいターミナルウィンドウを開きます。

- #### ステップ4：新しいターミナルで以下のコマンドを実行して、ローカルのcanister 実行環境をクリーンなステートで実行します：
  
  ```
    dfx start --clean --background
  ```

## メモリーリーク

メモリ・リークの修正は継続的なプロセスです。メモリリークに関連するエラーメッセージが表示された場合は、以下の手順を実行してください：

- #### ステップ 1:`dfx stop` を実行して、現在実行中のプロセスを停止します。

- #### ステップ2：さらなる劣化を防ぐためにIC SDKをアンインストールします。

- #### ステップ3：IC SDKを再インストールします。

- #### ステップ4：`dfx start` を実行して、レプリカプロセスを再起動します。

または、`.cache/dfinity` ディレクトリを削除し、最新の IC SDK`dfx` バイナリを再インストールすることもできます。例えば

    rm -rf ~/.cache/dfinity && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

<!---
# Troubleshooting resources

## Overview

This section provides information to help you troubleshoot and resolve or work around common issues that are related to the following tasks:

-   Downloading and installing the IC SDK.

-   Creating, building, or deploying canisters.

-   Using the [IC SDK](../setup/install/index.mdx).

-   Running the local canister execution environment.

## Migrating an existing project

Currently, there is no automatic migration or backward compatibility for any projects that you might have created using previous versions of the IC SDK. After upgrading to the latest version, you might see error or failure messages if you attempt to build or install a project created with a previous version of the IC SDK.

In many cases, however, you can continue to work with projects from a previous release by manually changing the `dfx` setting in the `dfx.json` configuration file, then rebuilding the project to be compatible with the version of the IC SDK you have currently installed.

For example, if you have a project that was created with IC SDK version `0.8.0`, open the `dfx.json` file in a text editor and change the `dfx` setting to the latest version or remove the section entirely.

## Restarting the local canister execution environment

In some cases, starting the local canister execution environment fails due to stale state. If you encounter issues when running `dfx start` to start the local canister execution environment:

- #### Step 1:  In the terminal that displays the IC emulation the execution environment uses, press Control-C to interrupt the local canister execution environment process.

- #### Step 2:  Stop the local canister execution environment by running the following command:

        dfx stop

- #### Step 3:  Restart the local canister execution environment in a clean state by running the following command:

        dfx start --clean

    The `--clean` option removes checkpoints and stale state information from your project’s cache so that you can restart the local canister execution environment and web server processes in a clean state.

:::caution
Keep in mind, however, that if you reset the state information by running `dfx start --clean`, your existing canisters are also removed.
:::

- #### Step 4: After running `dfx start --clean`, recreate your canisters by running the following commands:

    dfx canister create --all
    dfx build
    dfx canister install --all

## Removing the canisters directory

If you run into problems building or deploying canisters after successfully connecting to the IC and registering canister identifiers, you should remove the `canisters` directory before attempting to rebuild or redeploy the canisters.

You can remove the `canisters` directory for a project by running the following command in the project’s root directory:

    rm -rf ./.dfx/* canisters/*

## Reinstalling dfx

Many of the bugs you might encounter can be addressed by uninstalling and reinstalling the IC SDK. Here are a few ways to reinstall the IC SDK.

If you only have one version of the IC SDK installed in your development environment, you can usually run the following command to uninstall and reinstall the latest version of IC SDK:

    ~/.cache/dfinity/uninstall.sh && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

If you have modified the location of the IC SDK binary (the binary is titled `dfx`), you might want run the following command to uninstall the version of the IC SDK that is in your PATH, then reinstall the latest version of the IC SDK:

    rm -rf ~/.cache/dfinity && rm $(which dfx) && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

## Xcode prerequisite

Some versions of the IC SDK prompted you to install Xcode when creating a new project on a macOS computer. The prompt has been removed and the `dfx new` command does not require you to install any macOS developer tools. However, you should have **Developer Command Line Tools** installed if you want to create a Git repository for your project.

You can check whether you have the developer tools installed by running `xcode-select -p`. You can install the developer tools by running `xcode-select --install`.

## Apple ARM silicon
If you are using a Mac with Apple silicon and are having issues (such as `bad CPU type in executable: dfx`), you may need to install Rosetta.

```shell
softwareupdate --install-rosetta 
```

## Failed build when using VMs

If you are running the IC SDK using a virtual machine image on Ubuntu or CentOS, you might see an error message that looks like this when you attempt to run the `dfx build` command:

    Building hello...
    An error occurred:
    Io(
        Os {
            code: 2,
            kind: NotFound,
            message: "No such file or directory",
        },
    )

## Address in use error or orphan processes

If you are developing projects locally, you often have the local canister execution environment running either in a separate terminal or in the background. If the local environment processes do not get properly terminated, you might see operating system errors indicating that an address is already in use or or be unable to stop processes normally using the `dfx stop` command.

There are several scenarios in which you might encounter this issue. For example, if you run `dfx start` in a local project directory then change to a different local project directory without first stopping the canister execution environment processes, you might see this issue.

If you encounter an issue where you suspect or you receive a message that an address is already in use or that a process is already running in the background, perform the following steps:

- #### Step 1:  Run the following command to see which process is listening to the 4943 port if you are using the default binding to localhost:

        lsof -i tcp:4943

- #### Step 2:  Run the following command to terminate any orphan processes:

        killall dfx replica

- #### Step 3:  Close the current terminal and open a new terminal window.

- #### Step 4:  In the new terminal, run the following command to run the local canister execution environment in a clean state:

        dfx start --clean --background

## Memory leak

Fixing memory leaks is an ongoing process. If you encounter any error messages related to memory leaks, you should do the following:

- #### Step 1:  Run `dfx stop` to stop currently running processes.

- #### Step 2:  Uninstall the IC SDK to prevent further degradation.

- #### Step 3:  Re-install the IC SDK

- #### Step 4:  Run `dfx start` to restart replica processes.

Alternatively, you can remove the `.cache/dfinity` directory and re-install the latest IC SDK `dfx` binary. For example:

    rm -rf ~/.cache/dfinity && sh -ci "$(curl -sSL https://internetcomputer.org/install.sh)"

-->
