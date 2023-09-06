# 7:canister 間通話の発信

## 概要

開発者にとってInternet Computer ブロックチェーンの最も重要な機能の一つは、あるcanister 内の関数を別のcanister から呼び出す機能です。canisters間でコールを行うこの機能は、**canister 間コールとも呼ばれ、**複数のdapps で機能を再利用したり共有したりすることができます。

たとえば、職業上のネットワーキング、コミュニティ・イベントの開催、募金活動の主催のために、dapp を作成したいと思うかもしれません。これらのdapps にはそれぞれ、ソーシャル・コンポーネントがあるかもしれません。ソーシャル・コンポーネントは、友人や家族、現在の同僚や以前の同僚など、何らかの基準や共通の関心に基づいて、ユーザーがソーシャルな関係を識別できるようにするものです。

このソーシャル・コンポーネントに対処するために、ユーザー関係を保存するための単一のcanister を作成し、プロフェッショナル・ネットワーキング、コミュニティ・オーガナイジング、または資金調達のアプリケーションを書いて、ソーシャル・コネクションのためにcanister で定義されている関数をインポートして呼び出すとよいでしょう。その後、ソーシャルコネクションcanister を使用する追加アプリケーションを構築したり、ソーシャルコネクションcanister が提供する機能を拡張して、さらに幅広い他の開発者のコミュニティで使用できるようにすることができます。

この例では、上記のような、より手の込んだプロジェクトやユースケースの基礎として使用できる、canister 呼び出しを設定する簡単な方法を紹介します。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしてください。

## 新しいdfxプロジェクトの作成

まだ開いていなければ、ローカルコンピュータでターミナルウィンドウを開いてください。

まず、コマンドで新しい dfx プロジェクトを作成します：

    dfx new intercanister

次に、プロジェクトのディレクトリに移動します：

    cd intercanister

`src` ディレクトリの下に、新しいcanisters ディレクトリを2つ作成します：

    mkdir src/canister1
    mkdir src/canister2

新しいファイル`src/canister1/main.mo` を作成します。

このファイルに、次のコードを挿入します：

    import Canister2 "canister:canister2";
    
    actor {
        public func main() : async Nat {
            return await Canister2.getValue();
        };
    };

次に、もう1つの新しいファイル`src/canister2/main.mo` を作成します。

このファイルに、次のコードを挿入します：

    import Prim "mo:prim";
    
    actor {
        public func getValue() : async Nat {
            Prim.debugPrint("Hello from canister 2!");
            return 10;
        };
    };

`dfx.json` ファイルを開き、既存のコンテンツを削除します。次に、次のコードを挿入します：

    {
     "canisters": {
      "canister1": {
        "main": "src/canister1/main.mo",
        "type": "motoko"
      },
      "canister2": {
        "main": "src/canister2/main.mo",
        "type": "motoko"
      }
    },
      "defaults": {
        "build": {
          "args": "",
          "packtool": ""
        }
      },
      "output_env_file": ".env",
      "version": 1
    }

## ローカル実行環境canister の起動

ローカル・コンピュータ上でローカル実行環境を起動するには、次のコマンドを実行します：

    dfx start --clean

:::info
このガイドでは、`--clean` オプションを使用して、ローカルのcanister 実行環境をクリーンなステートで起動します。

このオプションは、通常のオペレーションを中断させる可能性のあるオーファンのバックグラウンド・プロセスやcanister 識別子を削除します。例えば、プロジェクト間を移動する際に`dfx stop` を発行し忘れた場合、バックグラウンドや別のターミナルでプロセスが実行されている可能性があります。`--clean` オプションを使用すると、実行中のプロセスを手動で見つけて終了させることなく、ローカルのcanister 実行環境を起動して次のステップに進むことができます。
::：

## プロジェクトのデプロイ

プロジェクトのディレクトリにあるコマンドを使用して、新しいcanisters をデプロイします：

    dfx deploy

## コマンドと対話します。canisters

次のコマンドを使用して、`canister1` から`canister2` に電話をかけます：

```
dfx canister call canister1 main 
```

出力は以下のようになるはずです：

    2023-06-15 15:53:39.567801 UTC: [Canister ajuq4-ruaaa-aaaaa-qaaga-cai] Hello from canister 2!
    (10 : nat)

また、`src/canister1/main.mo` ファイルで次のコードを使用することで、canister id を使用して、以前にデプロイされたcanister にアクセスすることもできます：

    actor {
        public func main(canisterId: Text) : async Nat {
            let canister2 = actor(canisterId): actor { getValue: () -> async Nat };
            return await canister2.getValue();
        };
    };

次に、`canisterID` を以前にデプロイされたcanister のプリンシパル ID に置き換えて、次の呼び出しを使用します：

    dfx canister call canister1 main "canisterID"

たとえば、`ajuq4-ruaaa-aaaaa-qaaga-cai` というプリンシパル ID を持つcanister の場合、これは先ほど使用した`canister2` のプリンシパル ID です：

    dfx canister call canister1 main "ajuq4-ruaaa-aaaaa-qaaga-cai"

## 次のステップ

次に、次のステップの[最適化を見て](./optimizing.md)みましょう。[ canisters](./optimizing.md)

<!---
# 7: Making inter-canister calls

## Overview
One of the most important features of the Internet Computer blockchain for developers is the ability to call functions in one canister from another canister. This capability to make calls between canisters—also sometimes referred to as **inter-canister calls**—enables you to reuse and share functionality in multiple dapps.

For example, you might want to create a dapp for professional networking, organizing community events, or hosting fundraising activities. Each of these dapps might have a social component that enables users to identify social relationships based on some criteria or shared interest, such as friends and family or current and former colleagues.

To address this social component, you might want to create a single canister for storing user relationships then write your professional networking, community organizing, or fundraising application to import and call functions that are defined in the canister for social connections. You could then build additional applications to use the social connections canister or extend the features provided by the social connections canister to make it useful to an even broader community of other developers.

This example will showcase a simple way to configure inter-canister calls that can be used as the foundation for more elaborate projects and use-cases such as those mentioned above. 

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new dfx project 

Open a terminal window on your local computer, if you don’t already have one open.

First, create a new dfx project with the command:

```
dfx new intercanister
```

Then, navigate into the project's directory:

```
cd intercanister
```

Create two new directories under the `src` directory for your new canisters:

```
mkdir src/canister1
mkdir src/canister2
```

Create a new file, `src/canister1/main.mo`.

In this file, insert the following code:

```
import Canister2 "canister:canister2";

actor {
    public func main() : async Nat {
        return await Canister2.getValue();
    };
};
```

Then, create another new file, `src/canister2/main.mo`.

In this file, insert the following code:

```
import Prim "mo:prim";

actor {
    public func getValue() : async Nat {
        Prim.debugPrint("Hello from canister 2!");
        return 10;
    };
};
```

Open the `dfx.json` file and delete the existing content. Then, insert the following code:

```
{
 "canisters": {
  "canister1": {
    "main": "src/canister1/main.mo",
    "type": "motoko"
  },
  "canister2": {
    "main": "src/canister2/main.mo",
    "type": "motoko"
  }
},
  "defaults": {
    "build": {
      "args": "",
      "packtool": ""
    }
  },
  "output_env_file": ".env",
  "version": 1
}
```

## Starting the local canister execution environment 

To start the local execution environment on your local computer, run the following command:

```
dfx start --clean
```

:::info
For this guide, we’re using the `--clean` option to start the local canister execution environment in a clean state.

This option removes any orphan background processes or canister identifiers that might disrupt normal operations. For example, if you forgot to issue a `dfx stop` when moving between projects, you might have a process running in the background or in another terminal. The `--clean` option ensures that you can start the local canister execution environment and continue to the next step without manually finding and terminating any running processes.
:::

## Deploy your project 

Deploy your new canisters with the command in your project's directory:

```
dfx deploy
```

## Interacting with the canisters

Use the following command to make a call from `canister1` to `canister2`:

```
dfx canister call canister1 main 
```

The output should resemble the following:

```
2023-06-15 15:53:39.567801 UTC: [Canister ajuq4-ruaaa-aaaaa-qaaga-cai] Hello from canister 2!
(10 : nat)
```

Alternatively, you can also use a canister id to access a previously deployed canister by using the following piece of code in the `src/canister1/main.mo` file:

```
actor {
    public func main(canisterId: Text) : async Nat {
        let canister2 = actor(canisterId): actor { getValue: () -> async Nat };
        return await canister2.getValue();
    };
};
```

Then, use the following call, replacing `canisterID` with the principal ID of a previously deployed canister:

```
dfx canister call canister1 main "canisterID"
```

For example, for a canister with the principal ID of `ajuq4-ruaaa-aaaaa-qaaga-cai`, which is the principal ID of `canister2` that we used earlier:

```
dfx canister call canister1 main "ajuq4-ruaaa-aaaaa-qaaga-cai"
```

## Next steps

Next, let's take a look at [optimizing canisters](./optimizing.md)
-->
