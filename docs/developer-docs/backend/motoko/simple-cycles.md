# 13: ウォレットからcycles を受け取る

## 概要

ローカルで開発をしているとき、プロジェクトのデフォルトのウォレットを使ってcycles を送ったりcycle の残高をチェックしたりできます。しかし、cycles を受け取って使用する必要があるcanisters についてはどうでしょうか。

このガイドでは、デフォルトのテンプレート・プログラムに、cycles を受信し、cycle の残高をチェックする機能を追加する方法を、簡単な例で説明します。

この例は以下のように構成されています：

- `wallet_balance` 関数を使用すると、canister の現在のcycle 残高を確認できます。

- `wallet_receive` 関数では、canister に送信されたcycles を受け取ることができます。

- `greet` 関数はテキスト引数を受け取り、端末に挨拶を表示します。

- `owner` 関数は、メッセージ呼び出し元が使用するプリンシパルを返します。

## 前提条件

開始する前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認し てください。

## 新しいプロジェクトの作成

アクセス制御とユーザ ID の切り替えをテストするための新しいプロジェクト・ディレクトリを作成する には、ローカル・コンピュータでターミナル・シェルを開いてください。

以下のコマンドを実行して、新しいプロジェクトを作成します：

    dfx new cycles_hello

以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd cycles_hello

## デフォルト・プログラムの変更

このガイドでは、cycles を受け取り、cycle の残高をチェックするための新しい関数を含めるために、テンプレートのソースコードを変更します。

テキストエディタで`src/cycles_hello_backend/main.mo` ファイルを開き、既存の内容を削除します。

このコードをコピーしてファイルに貼り付けます：

    import Nat64 "mo:base/Nat64";
    import Cycles "mo:base/ExperimentalCycles";
    
    shared(msg) actor class HelloCycles (
       capacity: Nat
      ) {
    
      var balance = 0;
    
      // Return the current cycle balance
      public shared(msg) func wallet_balance() : async Nat {
        return balance;
      };
    
      // Return the cycles received up to the capacity allowed
      public func wallet_receive() : async { accepted: Nat64 } {
        let amount = Cycles.available();
        let limit : Nat = capacity - balance;
        let accepted =
          if (amount <= limit) amount
          else limit;
        let deposit = Cycles.accept(accepted);
        assert (deposit == accepted);
        balance += accepted;
        { accepted = Nat64.fromNat(accepted) };
      };
    
      // Return the greeting
      public func greet(name : Text) : async Text {
        return "Hello, " # name # "!";
      };
    
      // Return the principal of the caller/user identity
      public shared(msg) func owner() : async Principal {
        let currentOwner = msg.caller;
        return currentOwner;
      };
    
    };

このプログラムのいくつかの重要な要素を見てみましょう：

- cyclesこのプログラムは、Motoko の基本ライブラリ`ExperimentalCycles`をインポートします。

- このプログラムでは、actor の代わりに`actor class` を使用します。これにより、複数のactor インスタンスを持つことができ、すべてのインスタンスに対して`capacity` までの異なるcycle 量を受け入れることができます。

- `capacity` はactor クラスの引数として渡されます。

- `msg.caller` は、呼び出しに関連付けられたプリンシパルを識別します。

変更を保存し、`main.mo` ファイルを閉じて続行します。

## ローカルcanister 実行環境の開始

`cycles_hello` プロジェクトをビルドする前に、開発環境でローカルに実行されているcanister 実行環境に接続するか、アクセス可能なサブネットに接続する必要があります。

ローカルのcanister 実行環境を起動するには、ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

次のコマンドを実行して、お使いのマシンでローカルのcanister 実行環境を起動します：

    dfx start --clean --background

このガイドでは、`--clean` オプションを使用して、ローカルのcanister 実行環境をクリーンなステートで起動します。

このオプションは、通常の運用を中断させる可能性のあるバックグラウンド・プロセスやcanister 識別子を削除します。例えば、プロジェクト間を移動する際に`dfx stop` を発行し忘れた場合、バックグラウンドや別のターミナルでプロセスが実行されている可能性があります。`--clean` オプシ ョ ン を指定す る こ と で、 ロ ーカルcanister 実行環境を起動 し 、 実行中のプ ロ セ ス を手動で発見 し て終了 さ せ る こ と な く 、 次のス テ ッ プへ進む こ と がで き ます。

ローカルのcanister 実行環境の起動操作が完了したら、次の手順に進むことができます。

## を登録、ビルド、およびデプロイします。dapp

ローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

プロジェクトのディレクトリで次のコマンドを実行して、dapp を登録、ビルド、デプロイします：

    dfx deploy --argument '(360000000000)' cycles_hello_backend

この例では、canister の`capacity` を 360,000,000,000cycles に設定しています。`dfx deploy` コマンドの出力には、このローカルプロジェクト用に作成されたウォレットcanister に関連する ID やウォレットcanister の識別子など、実行する操作に関する情報が表示されます。

例えば

    Deploying: cycles_hello_backend
    Creating a wallet canister on the local network.
    The wallet canister on the "local" network for user "ic_admin" is "bnz7o-iuaaa-aaaaa-qaaaa-cai"
    Creating canisters...
    Creating canister cycles_hello_backend...
    cycles_hello_backend canister created with canister id: bkyz2-fmaaa-aaaaa-qaaaq-cai
    Building canisters...
    Installing canisters...
    Creating UI canister on the local network.
    The UI canister on the "local" network is "bd3sg-teaaa-aaaaa-qaaba-cai"
    Installing code for canister cycles_Hello_backend, with canister ID bkyz2-fmaaa-aaaaa-qaaaq-cai
    Deployed canisters.
    URLs:
    Backend canister via Candid interface:
    cycles_hello_backend: http://127.0.0.1:8080/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai

## をテストします。dapp

ローカルのcanister 実行環境にdapp をデプロイした後、`dfx canister call` コマンドを使用してウォレット機能を試したり、プログラムをテストしたりできます。

次のコマンドを実行して、`default` ユーザー ID のプリンシパルをチェックします：

    dfx canister call cycles_hello_backend owner

このコマンドは、現在の ID に対して以下のような出力を表示します：

    (principal "g3jww-sbmtm-gxsag-4mecu-72yc4-kef5v-euixq-og2kd-sav2v-p2sb3-pae")

`dfx deploy` コマンドの実行に使用した ID に変更を加えていない場合は、`dfx identity get-principal` コマンドを実行しても同じプリンシパルが得られるはずです。cycles を送信したり、他の**カストディアン**ID にcycles を送信する許可を与えるなど、特定のタスクを実行するにはウォレットcanister の所有者である必要があるため、これは重要です。

以下のコマンドを実行して初期ウォレットcycle の残高を確認します：

    dfx canister call cycles_hello_backend wallet_balance

あなたはcanister にcycles を送信していないので、コマンドは以下の残高を表示します：

    (0 : nat)

以下のようなコマンドを実行して、canister プリンシパルを使用して、デフォルトのウォレットcanister から`cycles_hello_backend` canister にcycles を送ります：

    dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_send '(record { canister = principal "rrkah-fqaaa-aaaaa-aaaaq-cai"; amount = (256000000000:nat64); } )'

`wallet_balance` 関数を呼び出して、`cycles_hello_backend` canister に転送したcycles の数（許可された容量を下回る量を指定した場合）、または`dfx deploy` コマンドを実行したときに指定した`capacity` があることを確認します。

    dfx canister call cycles_hello_backend wallet_balance

コマンドは以下のような出力を表示します：

    (256_000_000_000)

`wallet_balance` 関数を呼び出して、以下のようなコマンドを実行してデフォルトのウォレットのcycles の数を確認します：

    dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_balance

コマンドは、レコードとして指定したウォレットcanister 識別子の残高を Candid 形式で返します。例えば、このコマンドは`amount` フィールド（ハッシュ`3_573_748_184` で表される）と残高 97,738,624,621,042cycles を持つレコードをこのように表示するかもしれません：

    (record { 3_573_748_184 = 97_738_624_621_042 })

この簡単なガイドでは、cycles はデフォルトのウォレットcanister の残高からのみ消費され、`cycles_hello` canister からは消費されません。

以下のようなコマンドを実行して`greet` 関数を呼び出します：

    dfx canister call cycles_hello_backend greet '("from DFINITY")'

`wallet_balance` 関数の呼び出しを再実行して、デフォルトのウォレットから差し引かれたcycles の数を確認してください：

    dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_balance

例えば、次のような結果が得られます：

    (record { 3_573_748_184 = 97_638_622_179_500 })

## リソース

cycles での作業に関する詳細情報をお探しの場合は、以下の関連リソースをご覧ください：

- [dfx identity（コマンドリファレンス）](/references/cli-reference/dfx-identity.md)。
- [ExperimentalCycles (基本モジュール)](/motoko/main/base/ExperimentalCycles.md)。
- [ cycles の管理 (Motoko 言語リファレンス)](/motoko/main/cycles.md)。
- [トークンとcycles (概要)](/concepts/tokens-cycles.md)。

## 次のステップ

次のステップでは、[ actor を](define-an-actor.md) [使ったクエリについて](define-an-actor.md)説明します。

<!---
# 13: Accepting cycles from a wallet

## Overview

When you are doing local development, you can use the default wallet in your project to send cycles and check your cycle balance. But what about the canisters that need to receive and use those cycles, e.g. to execute their functions and provide services to users?

This guide provides a simple example to illustrate how you might add the functions to receive cycles and check your cycle balance to the default template program.

This example consists of the following:

-   The `wallet_balance` function enables you to check the current cycle balance for the canister.

-   The `wallet_receive` function enables the program to accept cycles that are sent to the canister.

-   The `greet` function accepts a text argument and displays a greeting in a terminal.

-   The `owner` function returns the principal used by the message caller.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

To create a new project directory for testing access control and switching user identities, open a terminal shell on your local computer, if you don’t already have one open.

Create a new project by running the following command:

```
dfx new cycles_hello
```

Navigate into your project directory by running the following command:

```
cd cycles_hello
```

## Modify the default program

For this guide, you are going to modify the template source code to include new functions for accepting cycles and checking the cycle balance.

Open the `src/cycles_hello_backend/main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the file:

```
import Nat64 "mo:base/Nat64";
import Cycles "mo:base/ExperimentalCycles";

shared(msg) actor class HelloCycles (
   capacity: Nat
  ) {

  var balance = 0;

  // Return the current cycle balance
  public shared(msg) func wallet_balance() : async Nat {
    return balance;
  };

  // Return the cycles received up to the capacity allowed
  public func wallet_receive() : async { accepted: Nat64 } {
    let amount = Cycles.available();
    let limit : Nat = capacity - balance;
    let accepted =
      if (amount <= limit) amount
      else limit;
    let deposit = Cycles.accept(accepted);
    assert (deposit == accepted);
    balance += accepted;
    { accepted = Nat64.fromNat(accepted) };
  };

  // Return the greeting
  public func greet(name : Text) : async Text {
    return "Hello, " # name # "!";
  };

  // Return the principal of the caller/user identity
  public shared(msg) func owner() : async Principal {
    let currentOwner = msg.caller;
    return currentOwner;
  };

};
```

Let’s take a look at a few key elements of this program:

-   The program imports a Motoko base library—`ExperimentalCycles`—that provides basic functions for working with cycles.

-   The program uses an `actor class` instead of a single actor so that it can have multiple actor instances to accept different cycle amounts up to a `capacity` for all instances.

-   The `capacity` is passed as an argument to the actor class.

-   The `msg.caller` identifies the principal associated with the call.

Save your changes and close the `main.mo` file to continue.

## Start the local canister execution environment

Before you can build the `cycles_hello` project, you need to connect to the canister execution environment running locally in your development environment, or you need to connect to a subnet that you can access.

To start the local canister execution environment, open a new terminal window or tab on your local computer.

Start the local canister execution environment on your machine by running the following command:

```
dfx start --clean --background
```

For this guide, we’re using the `--clean` option to start the local canister execution environment in a clean state.

This option removes any orphan background processes or canister identifiers that might disrupt normal operations. For example, if you forgot to issue a `dfx stop` when moving between projects, you might have a process running in the background or in another terminal. The `--clean` option ensures that you can start the local canister execution environment and continue to the next step without manually finding and terminating any running processes.

After the local canister execution environment completes its startup operations, you can continue to the next step.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment, you can register, build, and deploy your dapp locally.

Register, build, and deploy your dapp by running the following command in your project's directory:

```
dfx deploy --argument '(360000000000)' cycles_hello_backend
```

This example sets the `capacity` for the canister to 360,000,000,000 cycles. The `dfx deploy` command output then displays information about the operations it performs, including the identity associated with the wallet canister created for this local project and the wallet canister identifier.

For example:

```
Deploying: cycles_hello_backend
Creating a wallet canister on the local network.
The wallet canister on the "local" network for user "ic_admin" is "bnz7o-iuaaa-aaaaa-qaaaa-cai"
Creating canisters...
Creating canister cycles_hello_backend...
cycles_hello_backend canister created with canister id: bkyz2-fmaaa-aaaaa-qaaaq-cai
Building canisters...
Installing canisters...
Creating UI canister on the local network.
The UI canister on the "local" network is "bd3sg-teaaa-aaaaa-qaaba-cai"
Installing code for canister cycles_Hello_backend, with canister ID bkyz2-fmaaa-aaaaa-qaaaq-cai
Deployed canisters.
URLs:
Backend canister via Candid interface:
cycles_hello_backend: http://127.0.0.1:8080/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
```

## Test the dapp

After you have deployed the dapp on your local canister execution environment, you can experiment with the wallet functions and test your program by using `dfx canister call` commands.

Check the principal for the `default` user identity by running the following command:

```
dfx canister call cycles_hello_backend owner
```

The command displays output similar to the following for the current identity:

```
(principal "g3jww-sbmtm-gxsag-4mecu-72yc4-kef5v-euixq-og2kd-sav2v-p2sb3-pae")
```

If you haven’t made changes to the identity you were using to run the `dfx deploy` command, you should get the same principal by running the `dfx identity get-principal` command. This is important because you must be the owner of the wallet canister to perform certain tasks such as sending cycles or granting other **custodian** identities permission to send cycles.

Check the initial wallet cycle balance by running the following command:

```
dfx canister call cycles_hello_backend wallet_balance
```

You haven’t sent any cycles to the canister, so the command displays the following balance:

```
(0 : nat)
```

Send some cycles from your default wallet canister to the `cycles_hello_backend` canister using the canister principal by running a command similar to the following:

```
dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_send '(record { canister = principal "rrkah-fqaaa-aaaaa-aaaaq-cai"; amount = (256000000000:nat64); } )'
```

Call the `wallet_balance` function to see that the `cycles_hello_backend` canister has the number of cycles you transferred, if you specified an amount under the allowed capacity, or the `capacity` you specified when you ran the `dfx deploy` command.

```
dfx canister call cycles_hello_backend wallet_balance
```

The command displays output similar to the following:

```
(256_000_000_000)
```

Call the `wallet_balance` function to see the number of cycles in your default wallet by running a command similar to the following:

```
dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_balance
```

The command returns the balance for the wallet canister identifier you specified as a record using Candid format. For example, the command might display a record with an `amount` field (represented by the hash `3_573_748_184`) and a balance of 97,738,624,621,042 cycles like this:

```
(record { 3_573_748_184 = 97_738_624_621_042 })
```

For this simple guide, cycles are only consumed from the balance in the default wallet canister, not from the `cycles_hello` canister.

Call the `greet` function by running a command similar to the following:

```
dfx canister call cycles_hello_backend greet '("from DFINITY")'
```

Rerun the call to the `wallet_balance` function to see the number of cycles deducted from your default wallet:

```
dfx canister call rwlgt-iiaaa-aaaaa-aaaaa-cai wallet_balance
```

For example, you might a result similar to this:

```
(record { 3_573_748_184 = 97_638_622_179_500 })
```

## Resources

If you are looking for more information about working with cycles, check out the following related resources:

-   [dfx identity (command reference)](/references/cli-reference/dfx-identity.md).
-   [ExperimentalCycles (base module)](/motoko/main/base/ExperimentalCycles.md).
-   [Managing cycles (Motoko language reference)](/motoko/main/cycles.md).
-   [Tokens and cycles (overview)](/concepts/tokens-cycles.md).

## Next steps

In the next step, we'll cover [querying using an actor](define-an-actor.md).
-->
