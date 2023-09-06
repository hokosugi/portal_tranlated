# 11: 自然数のインクリメント

## 概要

このガイドでは、actor を 1 つ作成し、カウンタをインクリメントして値の永続性を示すいくつかの基本関数を提供するプログラムを記述します。

この例では、actor に`Counter` という名前を付けます。このプログラムでは、`currentValue` 変数を使用して、カウンタの現在値を表す自然 数を格納します。このプログラムは、以下の関数呼び出しをサポートしています：

- `increment` 関数コールは、現在値を 1 ずつインクリメントしてアップデートします（戻り値はありません）。

- `get` 関数コールは、カウンタの現在値をクエリーして返します。

- `set` 関数コールは、現在値を引数として指定した任意の数値にアップデートします。

このガイドでは、配置されたcanister で関数を呼び出してカウンタをインクリメントする簡単な例を示します。カウンタの値をインクリメントしてクエリーコールする関数を複数回呼び出すことで、変数のステート、つまり呼び出しの間の変数の値が存在することを確認できます。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていなければ、ローカル・コンピューターでターミナル・シェルを開いてください。

以下のコマンドを実行して新しいプロジェクトを作成します：

    dfx new my_counter

このコマンドにより、プロジェクト用の新しい`my_counter` プロジェクトが作成されます。

次のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd my_counter

## デフォルト設定の変更

新しいプロジェクトを作成すると、デフォルトの`dfx.json` 設定ファイルがプロジェクトディレクトリに追加されることはすでにおわかりでしょう。このガイドでは、デフォルトの設定を変更して、プロジェクトのメイン・プログラムの名前を変更します。

`dfx.json` 設定ファイルを変更するには、テキストエディタで`dfx.json` 設定ファイルを開き、デフォルトの`main` 設定を`main.mo` から`increment_counter.mo` に変更します。

例えば

    "main": "src/my_counter_backend/increment_counter.mo",

このガイドでは、ソース・ファイルの名前を`main.mo` から`increment_counter.mo` に変更することで、`dfx.json` 構成ファイルの設定がコンパイルするソース・ファイルを決定する方法を簡単に説明します。

より複雑なdapp では、依存関係を持つ複数のソース・ファイルがあり、`dfx.json` 構成ファ イルの設定を使用して管理する必要があるかもしれません。このようなシナリオでは、canisters と`dfx.json` ファイルに定義されたプログラムが複数あるため、`main.mo` という名前のファイルが複数あると混乱する可能性があります。

残りのデフォルト設定はそのままにしておいてもかまいません。

変更を保存し、`dfx.json` ファイルを閉じて続行します。

以下のコマンドを実行して、ソース・コード・ディレクトリ`src` のメイン・プログラム・ファイルの名前を、`dfx.json` 構成ファイルで指定された名前と一致するように変更します：

    mv src/my_counter_backend/main.mo src/my_counter_backend/increment_counter.mo

## デフォルト・プログラムの変更

ここまでは、プロジェクトのメイン・プログラムの名前を変更しただけです。次のステップでは、`Counter` という名前のactor を定義し、`increment` 、`get` 、`set` 関数を実装するために、`src/my_counter_backend/increment_counter.mo` ファイル内のコードを変更します。

デフォルト・テンプレートのソース・コードを修正するには、`src/my_counter_backend/increment_counter.mo` ファイルをテキスト・エディターで開き、既存の内容を削除します。

このコードをコピーして、`increment_counter.mo` ファイルに貼り付けます：

    // Create a simple Counter actor.
    actor Counter {
    stable var currentValue : Nat = 0;
    
    // Increment the counter with the increment function.
    public func increment() : async () {
    currentValue += 1;
    };
    
    // Read the counter value with a get function.
    public query func get() : async Nat {
    currentValue
    };
    
    // Write an arbitrary value with a set function.
    public func set(n: Nat) : async () {
    currentValue := n;
    };
    }

このサンプル・プログラムを詳しく見てみましょう：

- この例の`currentValue` 変数宣言には、`stable` キーワードが含まれており、ステート（設定、インクリメント、取得が可能な値）が存在することを示しています。

このキーワードは、プログラムがアップグレードされても変数の値が変更されな いことを保証します。

- `currentValue` 変数の宣言では、その型が自然数 (`Nat`) であることも指定されています。

- このプログラムには、2 つのパブリック更新メソッド（`increment` 関数と`set` 関数）、1 つのクエリ・メソッド（`get` 関数）があります。

安定変数と柔軟変数の詳細については、[**Motoko プログラミング言語ガイドの**](/motoko/main/about-this-guide.md) [安定変数とアップグレードメソッドを](/motoko/main/upgrades.md)参照してください。

クエリーとアップデートの違いについては、[canisters ](/concepts/canisters-code.md#canister-state) の[クエリーおよびアップデート・メソッドに](/concepts/canisters-code.md#query-update) [プログラムとステートの両方が](/concepts/canisters-code.md#canister-state)含まれるを参照してください。

変更を保存し、ファイルを閉じて続行します。

## ローカルのcanister 実行環境の開始

`my_counter` プロジェクトをビルドする前に、Internet Computer ブロックチェーンをシミュレートするローカルcanister 実行環境か、Internet Computer ブロックチェーンメインネットに接続する必要があります。

ローカルのcanister 実行環境を起動するには`dfx.json` ファイルが必要なので、プロジェクトのルート・ディレクトリにいることを確認してください。このガイドでは、ターミナル・シェルを2つに分けて、1つのターミナルでネットワーク操作を開始して確認し、もう1つのターミナルでプロジェクトを管理できるようにします。

ローカルのcanister 実行環境を起動するには、ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

:::info

- これで**2つのターミナルが**開くはずです。
- **プロジェクト・ディレクトリが** **現在の作業ディレクトリになって**いるはずです。
  ::：

以下のコマンドを実行して、あなたのコンピューターでローカルcanister 実行環境を起動します：

    dfx start

ローカルのcanister 実行環境を起動すると、ターミナルにネットワーク操作に関するメッセー ジが表示されます。

ネットワーク操作を表示するターミナルは開いたままにしておき、新しいプロジェクトを作成した元のターミナルにフォーカスを切り替えます。

## を登録、ビルド、およびデプロイします。dapp

開発環境で実行されているローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

プロジェクトのディレクトリで次のコマンドを実行して、dapp を登録、ビルド、デプロイします：

    dfx deploy

`dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。

## デプロイされたメソッドの呼び出しcanister

canister のデプロイに成功したら、canister によって提供されるメソッドを呼び出すエンドユーザーをシミュレートできます。このガイドでは、カウンターの値をクエリーする`get` メソッド、呼び出されるたびにカウンターをインクリメントする`increment` メソッド、およびカウンターを指定した任意の値に更新する引数を渡す`set` メソッドを呼び出します。

次のコマンドを実行して`get` 関数を呼び出し、デプロイされたcanister 上の`currentValue` 変数の現在値を読み取ります：

    dfx canister call my_counter_backend get

このコマンドは、`currentValue` 変数の現在値をゼロとして返します：

    (0 : nat)

次のコマンドを実行して`increment` 関数を呼び出し、デプロイされたcanister 上の`currentValue` 変数の値を 1 つインクリメントします：

    dfx canister call my_counter_backend increment

このコマンドは変数の値をインクリメントしてステートを変更しますが、結果は返しません。

次のコマンドを再実行して、デプロイされたcanister 上の`currentValue` 変数の現在の値を取得します：

    dfx canister call my_counter_backend get

コマンドは更新された`currentValue` 変数の値を 1 として返します：

    (1 : nat)

他のコマンドを実行して、他のメソッドの呼び出しや異なる値の使用を試してください。たとえば、以下のようなコマンドを実行して、カウンターの値を設定したり返したりしてみてください：

    dfx canister call my_counter_backend set '(987)'
    dfx canister call my_counter_backend get

これにより、`currentValue` の更新値が 987 になります。追加コマンドを実行すると

    dfx canister call my_counter_backend increment
    dfx canister call my_counter_backend get

を実行すると、インクリメントされた`currentValue` の 988 が返されます。

## Candid UI を使用してコードをテストしてください。

コードをテストするには、[こちらの](candid-ui.md)指示に従ってください。

## 次のステップ

次のガイドでは、[ canister にテキスト引数を渡す](hello-location.md)方法について説明します。

<!---
# 11: Incrementing a natural number

## Overview

In this guide, you are going to write a program that creates a single actor and provides a few basic functions to increment a counter and illustrate persistence of a value.

For this example, the actor is named `Counter`. The program uses the `currentValue` variable to contain a natural number that represents the current value of the counter. This program supports the following function calls:

-   The `increment` function call updates the current value, incrementing it by 1 (no return value).

-   The `get` function call queries and returns the current value of the counter.

-   The `set` function call updates the current value to an arbitrary numeric value you specify as an argument.

This guide provides a simple example of how you can increment a counter by calling functions on a deployed canister. By calling the functions to increment and query the counter value multiple times, you can verify that the variable state; that is, the value of the variable between calls—persists.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal shell on your local computer, if you don’t already have one open.

Create a new project by running the following command:

```
dfx new my_counter
```

The command creates a new `my_counter` project for your project.

Then change into your project directory by running the following command:

```
cd my_counter
```

## Modify the default configuration

You have already seen that creating a new project adds a default `dfx.json` configuration file to your project directory. In this guide, you will modify the default settings to use a different name for the main program in your project.

To modify the `dfx.json` configuration file, open the `dfx.json` configuration file in a text editor and change the default `main` setting from `main.mo` to `increment_counter.mo`.

For example:

```
"main": "src/my_counter_backend/increment_counter.mo",
```

For this guide, changing the name of the source file from `main.mo` to `increment_counter.mo` simply illustrates how the setting in the `dfx.json` configuration file determines the source file to be compiled.

In a more complex dapp, you might have multiple source files with dependencies that you need to manage using settings in the `dfx.json` configuration file. In a scenario like that—with multiple canisters and programs defined in your `dfx.json` file—having multiple files all named `main.mo` might be confusing.

You can leave the rest of the default settings as they are.

Save your change and close the `dfx.json` file to continue.

Change the name of the main program file in the source code directory `src` to match the name specified in the `dfx.json` configuration file by running the following command:

```
mv src/my_counter_backend/main.mo src/my_counter_backend/increment_counter.mo
```

## Modify the default program

So far, you have only changed the name of the main program for your project. The next step is to modify the code in the `src/my_counter_backend/increment_counter.mo` file to define an actor named `Counter` and implement the `increment`, `get`, and `set` functions.

To modify the default template source code, open the `src/my_counter_backend/increment_counter.mo` file in a text editor and delete the existing content.

Copy and paste this code into the `increment_counter.mo` file:

```
// Create a simple Counter actor.
actor Counter {
stable var currentValue : Nat = 0;

// Increment the counter with the increment function.
public func increment() : async () {
currentValue += 1;
};

// Read the counter value with a get function.
public query func get() : async Nat {
currentValue
};

// Write an arbitrary value with a set function.
public func set(n: Nat) : async () {
currentValue := n;
};
}
```

Let's take a closer look at this sample program:

-   You can see that the `currentValue` variable declaration in this example includes the `stable` keyword to indicate the state—the value that can be set, incremented, and retrieved—persists.

This keyword ensures that the value for the variable is unchanged when the program is upgraded.

-   The declaration for the `currentValue` variable also specifies that its type is a natural number (`Nat`).

-   The program includes two public update methods—the `increment` and `set` functions—and one a query method-the `get` function.

For more information about stable and flexible variables, see [stable variables and upgrade methods](/motoko/main/upgrades.md) in the [**Motoko programming language guide**](/motoko/main/about-this-guide.md).

For more information about the differences between a query and an update, see [query and update methods](/concepts/canisters-code.md#query-update) in [canisters include both program and state](/concepts/canisters-code.md#canister-state).

Save your changes and close the file to continue.

## Start the local canister execution environment

Before you can build the `my_counter` project, you need to either connect to a local canister execution environment simulating the Internet Computer blockchain or to the Internet Computer blockchain mainnet.

Starting the local canister execution environment requires a `dfx.json` file, so you should be sure you are in your project’s root directory. For this guide, you should have two separate terminal shells, so that you can start and see network operations in one terminal and manage your project in another.

To start the local canister execution environment, open a new terminal window or tab on your local computer.

:::info
-   You should now have **two terminals** open.
-   You should have the **project directory** as your **current working directory**.
:::

Start the local canister execution environment on your computer by running the following command:

```
dfx start
```

After you start the local canister execution environment, the terminal displays messages about network operations.

Leave the terminal that displays network operations open and switch your focus to your original terminal where you created your new project.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment running in your development environment, you can register, build, and deploy your dapp locally.

Register, build, and deploy your dapp by running the following command in your project's directory:

```
dfx deploy
```

The `dfx deploy` command output displays information about the operations it performs.

## Invoke methods on the deployed canister

After successfully deploying the canister, you can simulate an end-user invoking the methods provided by the canister. For this guide, you invoke the `get` method to query the value of a counter, the `increment` method that increments the counter each time it is called, and the `set` method to pass an argument to update the counter to an arbitrary value you specify.

Run the following command to invoke the `get` function, which reads the current value of the `currentValue` variable on the deployed canister:

```
dfx canister call my_counter_backend get
```

The command returns the current value of the `currentValue` variable as zero:

```
(0 : nat)
```

Run the following command to invoke the `increment` function to increment the value of the `currentValue` variable on the deployed canister by one:

```
dfx canister call my_counter_backend increment
```

This command increments the value of the variable, changing its state, but does not return the result.

Rerun the following command to get the current value of the `currentValue` variable on the deployed canister:

```
dfx canister call my_counter_backend get
```

The command returns the updated value of the `currentValue` variable as one:

```
(1 : nat)
```

Run additional commands to experiment with invoking other methods and using different values. For example, try commands similar to the following to set and return the counter value:

```
dfx canister call my_counter_backend set '(987)'
dfx canister call my_counter_backend get
```

This returns the updated value of the `currentValue` to be 987. Running the additional commands

```
dfx canister call my_counter_backend increment
dfx canister call my_counter_backend get
```

returns the incremented `currentValue` of 988.

## Test your code using the Candid UI.

To test your code, follow the instructions [here](candid-ui.md).

## Next steps

In the next guide, we'll cover [passing text arguments to a canister](hello-location.md).
-->
