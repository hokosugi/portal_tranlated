# プロジェクトの管理

## 概要

各プロジェクトの`dfx.json` 設定ファイルを変更することで、個々のプロジェクトの主要な設定を変更できます。これらの設定をプログラムで変更するには、`dfx config` コマンドを使用するか、手動で`dfx.json` ファイルを直接編集します。

## ソース・ディレクトリを変更する方法

`dfx build` コマンドを使用してプロジェクトのソース・コードをコンパイルする前に、dapp のソース・コードを格納するデフォルトの場所を確認しておくとよいでしょう。デフォルトでは、新しいプロジェクトを作成するときに使用する名前は、1つのcanister (`canister_name`) と1つの資産canister (`canister_name_assets`) に使用される名前です。dapp のソースコードは、`src/canister_name` ディレクトリにあることが期待されます。同様に、フロントエンドのソースコードのデフォルトの場所は`src/canister_name_assets/src` ディレクトリにあり、フロントエンドの出力は`dist/canister_name_assets` ディレクトリにあります。

しかし、dapp’s の複雑さやアーキテクチャによっては、dapp のソースコード、フロントエンドのソースコード、フロントエンドの出力のデフォルトの場所を変更したくなるかもしれません。

例えば、単純なdapp の場合、ディレクトリ・レベルを1つ削除して、ソース・コードを`src` ディレクトリに配置することができます：

```
  "main": "src/main.mo",
```

より複雑なdapps の場合は、多階層のディレクトリ構造を使用します：

    "canisters": {
      "profiles": {
        "main": "src/profiles/utils/main.mo"
      },
      "events": {
        "main": "src/events/calendar/main.mo"
      },
      "media": {
        "main": "src/events/reports/main.mo"
      }
    }

ソース・コード・ディレクトリのデフォルト設定を変更する場合は、`dfx.json` 設定ファイルの設定がファイル・システム上のディレクトリの場所と一致していることを確認してください。

## メインのdapp ファイル名を変更する方法

`dfx build` コマンドを使用してプロジェクトのソース・コードをコンパイルする前に、dapp’s ソース・コードに使用されている場所とファイル名を確認する必要があります。

たとえば、`factorial` dapp のcanister を構築し、dapp のソース・コードが`src/math/factorial.mo` にある場合、設定ファイルの`canisters` セクションの`main` 設定に正しいパスが指定されていることを確認する必要があります。

例えば

    "main": "src/math/factorial.mo",

dapp ファイル名の構成設定を変更しても、`dfx build` コマンドがコンパイルするソース・コードを探す場所に影響するだけであることに注意してください。設定ファイルを変更しても、ファイル・システム上のファイルやディレクトリの名前は変更されません。メインのdapp ファイルへのパスやファイル名自体を変更する場合は、必ずプロジェクト・ディレクトリー内の名前と場所を変更してください。

## dapp フロントエンドを提供する場所を変更する方法

`dfx.json` 設定ファイルのローカルネットワーク設定を変更することで、dapp フロントエンドのデフォルトのホスト名とポート番号を変更することができます。

たとえば、`bind` の設定を変更することで、ローカルネットワークの IP アドレスを変更できます：

    "networks": {
      "local": {
        "bind": "192.168.47.1:8000",
        "type": "ephemeral"
      }
    }

## リソース

- サンプルのdapps 。

- さまざまな IC の概念とサービスについて学ぶには、「[Concepts（概念）](../../concepts/index.md)」を参照してください。

- IC 内で使用されるさまざまな用語の定義については、[IC 用語集を](../../references/glossary.md)参照してください。

<!---
# Managing projects

## Overview

You can modify some key settings for individual projects by modifying each project’s `dfx.json` configuration file. You can use the `dfx config` command to change these settings programmatically or manually edit the `dfx.json` file directly.

## How to change the source directory

Before you compile source code for your project using the `dfx build` command, you might want to check the default location for storing the source code for your dapp. By default, the name you use to create a new project is the name used for one canister (`canister_name`) and one assets canister (`canister_name_assets`), and dapp source code is expected to be in the `src/canister_name` directory. Similarly, the default location for frontend source code is in the `src/canister_name_assets/src` directory and frontend output is located in the `dist/canister_name_assets` directory.

Depending on your dapp’s complexity and architecture, however, you might want to modify the default location for the dapp source code, the frontend source code, or frontend output.

For example, for a simple dapp, you might want to eliminate one directory level and place the source code in the `src` directory:

      "main": "src/main.mo",

For more complex dapps, you might want to use a multi-tiered directory structure:

    "canisters": {
      "profiles": {
        "main": "src/profiles/utils/main.mo"
      },
      "events": {
        "main": "src/events/calendar/main.mo"
      },
      "media": {
        "main": "src/events/reports/main.mo"
      }
    }

If you modify the default settings for a source code directory, be sure that the settings in the `dfx.json` configuration file match the directory location on the file system.

## How to change the main dapp filename

Before you compile source code for your project using the `dfx build` command, you should verify the location and file name used for your dapp’s source code.

For example, if you want to build a canister for the `factorial` dapp and the source code for the dapp is located in `src/math/factorial.mo`, you should be sure that you have the correct path specified for the `main` setting in the `canisters` section of the configuration file.

For example:

    "main": "src/math/factorial.mo",

Keep in mind that changing the configuration setting for the dapp file name only affects where the `dfx build` command looks for the source code to compile. Making changes in the configuration file does not rename any files or directories on the file system. If you change the path to the main dapp file or the name of the file itself, be sure to change the name and location within your project directory.

## How to change the location for serving the dapp frontend

You can change the default host name and port number for serving the dapp frontend by modifying the local network settings in the `dfx.json` configuration file.

For example, you can change the IP address for the local network by modifying the `bind` setting:

    "networks": {
      "local": {
        "bind": "192.168.47.1:8000",
        "type": "ephemeral"
      }
    }

## Resources

-   [Building on the IC](../../samples/overview.md) to explore sample dapps.

-   [Concepts](../../concepts/index.md) to learn about different IC concepts and services.  

-   [IC glossary](../../references/glossary.md) to learn the definitions of various terms used within the IC. 
-->
