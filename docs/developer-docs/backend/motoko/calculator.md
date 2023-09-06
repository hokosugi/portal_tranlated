# 10: 電卓関数での整数の使用

## 概要

このガイドでは、基本的な算術演算を実行するためのいくつかのパブリックエントリポイント関数を持つ単一のactor を作成する簡単な電卓プログラムを書きます。

このガイドでは、actor を`Calc` と名付けます。このプログラムは`cell` 変数を使用して、電卓操作の現在の結果を表す整数値を格納します。

このプログラムは以下の関数コールをサポートしています：

- `add` 関数呼び出しは入力を受け付け、加算を実行します。

- `sub` 関数呼び出しは入力を受け付け、減算を実行します。

- `mul` 関数呼び出しは入力を受け付け、乗算を実行します。

- `div` 関数呼び出しは入力を受け付け、除算を実行します。

- `clearall` 関数は、以前の演算結果として格納された`cell` の値をクリアし、`cell` の値をゼロにリセットします。

`div` 関数には、プログラムがゼロで除算しようとするのを防ぐコードも含まれています。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていなければ、ローカル・コンピュータでターミナル・シェルを開いてください。

新しいプロジェクトを作成するには、以下のコマンドを実行します：

    dfx new calc

以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd calc

## デフォルト設定の変更

このガイドでは、デフォルトの`dfx.json` 設定ファイルを変更して、メイン・プログラムにもっと具体的な名前を使うようにします。

`dfx.json` 設定ファイルをテキストエディタで開きます。次に、`main` キー設定をデフォルトの`main.mo` プログラム名から`calc_main.mo` に変更します。

例えば

    "main": "src/calc_backend/calc_main.mo",

このガイドでは、ソース・ファイルの名前を`main.mo` から`calc_main.mo` に変更することで、`dfx.json` 構成ファイルの設定がコンパイルされるソース・ファイルを決定する方法を簡単に説明します。

より複雑なdapp では、単一の`main` プログラム・ファイルではなく、複数のソース・ファイルがあるかもしれません。より複雑なアプリケーションでは、`dfx.json` 設定ファイルの設定を使用して管理する必要がある、複数のソース・ファイル間の特定の依存関係もあるかもしれません。

このようなシナリオでは、`dfx.json` ファイルに複数のcanisters とプログラムが定義されていますが、`main.mo` という名前のファイルが複数あると、ワークスペースのナビゲーションが難しくなります。各プログラムに選択する名前は重要ではありませんが、`dfx.json` ファイルに設定した名前が、ファイルシステム内のプログラム名と一致していることが重要です。

変更を保存し、ファイルを閉じて続行します。

## デフォルト・プログラムの変更

このガイドでは、デフォルト・プログラムを基本的な算術演算を行うプログラムに置き換える必要があります。

デフォルト・プログラムを置き換えるには、以下のコマンドを実行して、テンプレート`main.mo` ファイルをコピーし、`calc_main.mo` という名前の新しいファイルを作成します：

    cp src/calc_backend/main.mo src/calc_backend/calc_main.mo

次に、`src/calc_backend/calc_main.mo` ファイルをテキストエディタで開き、既存の内容を削除します。

このコードをコピーして、`calc_main.mo` ファイルに貼り付けます：

    // This single-cell calculator defines one calculator instruction per
    // public entry point (add, sub, mul, div).
    
    // Create a simple Calc actor.
    actor Calc {
      var cell : Int = 0;
    
      // Define functions to add, subtract, multiply, and divide
      public func add(n:Int) : async Int { cell += n; cell };
      public func sub(n:Int) : async Int { cell -= n; cell };
      public func mul(n:Int) : async Int { cell *= n; cell };
      public func div(n:Int) : async ?Int {
        if ( n == 0 ) {
          return null // null indicates div-by-zero error
        } else {
          cell /= n; ?cell
        }
      };
    
      // Clear the calculator and reset to zero
      public func clearall() : async Int {
        if (cell : Int != 0)
          cell -= cell;
        return cell
      };
     };

このサンプル・コードでは整数型（`Int` ）を使用しているため、正負の数値を使用できることにお気づきでしょう。この電卓コードの関数を制限して正の数だけを使用したい場合は、データ型を変更して自然数 (`Nat`) データだけを使用できるようにします。

変更を保存し、ファイルを閉じて続行します。

## ローカルのcanister 実行環境の開始

`calc` プロジェクトをビルドする前に、開発環境のローカルで動作しているcanister 実行環境に接続するか、アクセス可能なサブネットに接続する必要があります。

ローカルでネットワークを起動するには`dfx.json` ファイルが必要なので、プロジェクトのルート・ディレクトリーにいることを確認してください。このガイドでは、ターミナル・シェルを 2 つに分け、1 つのターミナルでネッ トワークを起動して確認し、もう 1 つのターミナルでプロジェクトを管理できるようにします。

ローカルのcanister 実行環境を起動します：

ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

:::info

- **2つのターミナルが開いて**いるはずです。
- **プロジェクト・ディレクトリーを** **現在の作業ディレクトリーとして**ください。
  ::：

次に、以下のコマンドを実行して、あなたのマシンのローカルcanister 実行環境を起動します：

    dfx start

ローカル・ネットワークを起動すると、ターミナルにネットワーク操作に関するメッセー ジが表示されます。ローカル・ネットワークの開始後、ターミナルにはネットワーク操作に関するメッセー ジが表示されます。ネットワーク操作を表示するターミナルは開いたままにしておき、フォーカ スを新しいプロジェクトを作成した元のターミナルに切り替えてください。

## を登録、ビルド、およびデプロイします。dapp

ローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

プロジェクトのディレクトリで次のコマンドを実行して、dapp を登録、ビルド、デプロイします：

    dfx deploy

`dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。

## canister の機能のテスト

これで、ローカルの  実行環境に **canister**canister としてデプロイされました。`dfx canister call` コマンドを使用して、プログラムをテストできます。

`dfx canister call` コマンドを使用して`calc_backend` canister `add` 関数を呼び出し、次のコマンドを実行して入力引数`10` を渡します：

    dfx canister call calc_backend add '(10)'

シングルクォーテーションと括弧で囲まれた引数を渡すと、インターフェイス記述言語（IDL） は引数の型を解析するので、引数の型を手動で指定する必要はありません。

コマンドが`add` 関数に期待される値を返すことを確認します。例えば、プログラムには以下のような出力が表示されます：

    (10 : int)

次のコマンドを実行して、`mul` 関数を呼び出し、入力引数`3` を渡します：

    dfx canister call calc_backend mul '(3)'

コマンドが`mul` 関数に期待される値を返すことを確認します。例えば、プログラムは以下のような出力を表示します：

    (30 : int)

次のコマンドを実行して、`sub` 関数を呼び出し、`number` 型の入力引数`5` を渡します：

    dfx canister call calc_backend sub '(5)'

コマンドが`sub` 関数に期待される値を返すことを確認してください。例えば、プログラムは以下のような出力を表示します：

    (25 : int)

次のコマンドを実行して、`div` 関数を呼び出し、入力引数`5` を渡します：

    dfx canister call calc_backend div '(5)'

コマンドが`div` 関数に期待される値を返すことを確認します。例えば、プログラムは以下のような出力を表示します：

    (opt (5 : int))

`div` 関数がオプションの結果を返すことに気づくかもしれません。このプログラムでは、`div` 関数がゼロ除算エラーの場合に`null` を返すようにするために、結果をオプションにしています。

このプログラムのセル変数は整数なので、関数を呼び出して負の入力値を指定することもできます。例えば、次のようなコマンドを実行します：

    dfx canister call calc_backend mul '(-4)'

これは

(-20 : int)

`clearall` 関数を呼び出し、`cell` の値をゼロにリセットすることを確認します：

    dfx canister call calc_backend clearall

例えば、プログラムは以下のような出力を表示します：

    (0 : int)

## 次のステップ

次のガイドでは、[自然数のインクリメントを見て](counter-tutorial.md)みましょう。

<!---
# 10: Using integers in calculator functions

## Overview 
In this guide, you are going to write a simple calculator program that creates a single actor with several public entry-point functions to perform basic arithmetic operations.

For this guide, the actor is named `Calc`. The program uses the `cell` variable to contain an integer number that represents the current result of a calculator operation.

This program supports the following function calls:

-   The `add` function call accepts input and performs addition.

-   The `sub` function call accepts input and performs subtraction.

-   The `mul` function call accepts input and performs multiplication.

-   The `div` function call accepts input and performs division.

-   The `clearall` function clears the `cell` value stored as the result of previous operations, resetting the `cell` value to zero.

The `div` function also includes code to prevent the program from attempting to divide by zero.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal shell on your local computer, if you don’t already have one open.

To create a new project, run the following command:

```
dfx new calc
```

Change into your project directory by running the following command:

```
cd calc
```

## Modify the default configuration

For this guide, let’s modify the default `dfx.json` configuration file to use a more specific name for its main program.

Open the `dfx.json` configuration file in a text editor. Then, change the `main` key setting from the default `main.mo` program name to `calc_main.mo`.

For example:

```
"main": "src/calc_backend/calc_main.mo",
```

For this guide, changing the name of the source file from `main.mo` to `calc_main.mo` simply illustrates how the setting in the `dfx.json` configuration file determines the source file to be compiled.

In a more complex dapp, you might have multiple source files instead of a single `main` program file. More complex applications might also have specific dependencies between multiple source files that you need to manage using settings in the `dfx.json` configuration file. 

In a scenario like that, with multiple canisters and programs defined in your `dfx.json` file, having multiple files all named `main.mo` might make navigating your workspace more difficult. The name you choose for each program isn’t significant, but it is important that the name you set in the `dfx.json` file matches the name of your program in the file system.

Save your changes and close the file to continue.

## Modify the default program

For this guide, you need to replace the default program with a program that performs basic arithmetic operations.

To replace the default program, copy the template `main.mo` file to create a new file named `calc_main.mo` by running the following command:

```
cp src/calc_backend/main.mo src/calc_backend/calc_main.mo
```

Then, open the `src/calc_backend/calc_main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the `calc_main.mo` file:

```
// This single-cell calculator defines one calculator instruction per
// public entry point (add, sub, mul, div).

// Create a simple Calc actor.
actor Calc {
  var cell : Int = 0;

  // Define functions to add, subtract, multiply, and divide
  public func add(n:Int) : async Int { cell += n; cell };
  public func sub(n:Int) : async Int { cell -= n; cell };
  public func mul(n:Int) : async Int { cell *= n; cell };
  public func div(n:Int) : async ?Int {
    if ( n == 0 ) {
      return null // null indicates div-by-zero error
    } else {
      cell /= n; ?cell
    }
  };

  // Clear the calculator and reset to zero
  public func clearall() : async Int {
    if (cell : Int != 0)
      cell -= cell;
    return cell
  };
 };
```

You might notice that this sample code uses integer (`Int`) data types, enabling you to use positive or negative numbers. If you wanted to restrict the functions in this calculator code to only use positive numbers, you could change the data type to only allow natural (`Nat`) data.

Save your changes and close the file to continue.

## Start the local canister execution environment

Before you can build the `calc` project, you need to connect to the canister execution environment running locally in your development environment, or you need to connect to a subnet that you can access.

Starting the network locally requires a `dfx.json` file, so you should be sure you are in your project’s root directory. For this guide, you should have two separate terminal shells, so that you can start and see network operations in one terminal and manage your project in another.

To start the local canister execution environment:

Open a new terminal window or tab on your local computer.

:::info
-   You should now have **two terminals** open.
-   You should have the **project directory** as your **current working directory**.
:::

Then, start the local canister execution environment on your machine by running the following command:

```
dfx start
```

After you start the local network, the terminal displays messages about network operations. Leave the terminal that displays network operations open and switch your focus to your original terminal where you created your new project.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment, you can register, build, and deploy your dapp locally.

Register, build, and deploy your dapp by running the following command in your project's directory:

```
dfx deploy
```

The `dfx deploy` command output displays information about the operations it performs.

## Testing canister functionality

You now have a program deployed as a **canister** on your local canister execution environment. You can test the program by using `dfx canister call` commands.

Use the `dfx canister call` command to call the `calc_backend` canister `add` function and pass it the input argument `10` by running the following command:

```
dfx canister call calc_backend add '(10)'
```

When you pass an argument enclosed by the single quotation marks and parentheses,the interface description language (IDL) parses the argument type, so you don’t need to specify the argument type manually.

Verify that the command returns the value expected for the `add` function. For example, the program displays output similar to the following:

```
(10 : int)
```

Call the `mul` function and pass it the input argument `3` by running the following command:

```
dfx canister call calc_backend mul '(3)'
```

Verify that the command returns the value expected for the `mul` function. For example, the program displays output similar to the following:

```
(30 : int)
```

Call the `sub` function and pass it the input argument `5` of type `number` by running the following command:

```
dfx canister call calc_backend sub '(5)'
```

Verify that the command returns the value expected for the `sub` function. For example, the program displays output similar to the following:

```
(25 : int)
```

Call the `div` function and pass it the input argument `5` by running the following command:

```
dfx canister call calc_backend div '(5)'
```

Verify that the command returns the value expected for the `div` function. For example, the program displays output similar to the following:

```
(opt (5 : int))
```

You might notice that the `div` function returns an optional result. The program makes the result optional to enable the `div` function to return `null` in the case of a division-by-zero error.

Because the cell variable in this program is an integer, you can also call its functions and specify negative input values. For example, you might run the following command:

```
dfx canister call calc_backend mul '(-4)'
```

which returns:

(-20 : int)

Call the `clearall` function and verify it resets the `cell` value to zero:

```
dfx canister call calc_backend clearall
```

For example, the program displays output similar to the following:

```
(0 : int)
```

## Next steps

In the next guide, let's look at [incrementing natural numbers](counter-tutorial.md)
-->
