# 12: テキスト引数の受け渡し

## 概要

このガイドでは、1 つのテキスト引数を 1 つのactor に渡し、コードをコンパイルしてcanister を作成し、引数を取得する、デフォルト・プログラムの簡単なバリエーションを提供します。このドキュメントでは、canister とcanister という用語は同義語とみなします。

このガイドでは、Candid インターフェース記述言語（IDL）を使用してターミナルのコマンドラインで引数を渡す方法と、テキスト引数に複数の値を指定できるようにプログラムを変更する方法を説明します。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新しいプロジェクトの作成

このガイドの新しいプロジェクトを作成するには、ローカル・コンピューターでターミナル・ウィンドウを開いてください。

次のコマンドを実行して新しいプロジェクトを作成します：

    dfx new location_hello

次のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd location_hello

## デフォルト設定の変更

デフォルトの[プロジェクト](explore-templates)ガイドの中で、新しいプロジェクトを作成するとデフォルトの`dfx.json` 設定ファイルがプロジェクトディレクトリに追加されることを説明しました。このファイルのデフォルト設定を常に確認し、使用したいプロジェクトの設定が正確に反映されていることを確認する必要があります。このガイドでは、デフォルトの設定を変更して、使用しない設定を削除します。

`dfx.json` 設定ファイルの設定を変更するには、`dfx.json` 設定ファイルをテキストエディタで開きます。

`location_hello` プロジェクトのデフォルト設定を確認し、不要な構成設定をすべて削除します。

:::info
このガイドではフロントエンドのアセットを作成しないので、`location_hello_frontend` のコンフィギュレーション設定をファイルからすべて削除できます。
::：

変更を保存し、ファイルを閉じて続行します。

## デフォルトプログラムの修正

[デフォルトプロジェクトの探索](explore-templates)ガイドでは、新しいプロジェクトを作成すると、デフォルトの`src` ディレクトリとテンプレート`main.mo` ファイルが作成されることを説明しました。

デフォルトのテンプレート・ソース・コードを修正するには、`src/location_hello_backend/main.mo` ソース・コード・ファイルをテキストエディタで開きます。

デフォルトのソース・コードを変更して、`greet` 関数を`location` 関数に、`name` 引数を`city` 引数に置き換えます。

例えば、ファイルの既存のコードを以下のように置き換えることができます：

    actor {
    public func location(city : Text) : async Text {
        return "Hello, " # city # "!";
    };
    };

変更を保存し、ファイルを閉じて続行します。

## ローカルの実行環境canister を起動します。

プロジェクトをビルドする前に、ローカルのcanister 実行環境またはInternet Computer ブロックチェーンメインネットに接続する必要があります。

ローカルでcanister 実行環境を起動するには`dfx.json` ファイルが必要なので、プロジェクトのルートディレクトリにいることを確認してください。このガイドでは、ターミナル・シェルを2つに分けて、1つのターミナルでネットワーク操作を開始して確認し、もう1つのターミナルでプロジェクトを管理できるようにします。

ローカルのcanister 実行環境を起動するには、ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

:::info

- これで**2つのターミナルが**開くはずです。
- **プロジェクト・ディレクトリが** **現在の作業ディレクトリになって**いるはずです。
  ::：

次のコマンドを実行して、ローカルコンピューターでcanister 実行環境を起動します：

    dfx start

受信ネットワーク接続の許可または拒否を求めるプロンプトが表示されたら、「**許可**」をクリックします。

ネットワーク操作を表示するターミナルを開いたままにして、フォーカスをプロジェクトを作成した元のターミナルに切り替えます。

## を登録、ビルド、およびデプロイします。dapp

ローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

以下のコマンドを実行して、アプリケーションを登録、ビルド、デプロイします：

    dfx deploy

`dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。

## テキスト引数を渡す

としてデプロイされました。 **canister**canister としてデプロイされ、`dfx canister call` コマンドを使用してプログラムをテストできます。

プログラムの`location` メソッドを呼び出し、次のコマンドを実行して`text` 型の`city` 引数を渡します：

    dfx canister call location_hello_backend location "San Francisco"

この場合の引数には`San` と`Francisco` の間にスペースが含まれているため、引数を引用符で囲む必要があります。コマンドは以下のような出力を表示します：

    ("Hello, San Francisco!")

引数にテキストを引用符で囲む必要があるスペースが含まれていない場合は、Candid インタフェース記述言語がデータ型を次のように推測します：

    dfx canister call location_hello_backend location Paris

Candid はデータ型を`Text` と推論し、プログラムからの出力を以下のようなテキストとして返します：

    ("Hello, Paris!")

プログラムで`location` メソッドを呼び出し、テキスト引数用の Candid インターフェース記述言語構文を使用して`city` 引数を明示的に渡します：

    dfx canister call location_hello_backend location '("San Francisco and Paris")'

コマンドは以下のような出力を表示します：

    ("Hello, San Francisco and Paris!")

プログラムは 1 つのテキスト引数しか受け付けないため、複数の文字列を指定すると最初の引数のみが返されます。例えば、このコマンドを試すと

    dfx canister call location_hello_backend location '("San Francisco","Paris","Rome")'

最初の引数`("Hello, San Francisco!")`のみが返されます。

## プログラムのソース・コードを修正

このガイドで学んだことをさらに発展させるために、ソース・コードを変更して異なる結果を返すようにすることもできます。たとえば、`location` 関数を変更して、複数の都市名を返すようにすることもできます。

テキストエディタで`dfx.json` 設定ファイルを開き、デフォルトの`location_hello` 設定を`favorite_cities` に変更します。

このステップでは、canister の名前と、canister のメイン・プログラムへのパスの両方を変更して、`favorite_cities` を使用するようにします。`dfx.json` ファイルは以下のようになります：

    {
    "canisters": {
        "favorite_cities": {
        "main": "src/favorite_cities/main.mo",
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

変更を保存し、`dfx.json` ファイルを閉じて続行します。

次のコマンドを実行して、`dfx.json` 構成ファイルで指定された名前に一致するように、`location_hello` ソース・ファイル・ディレクトリをコピーします：

    cp -r src/location_hello_backend src/favorite_cities

`src/favorite_cities/main.mo` ファイルをテキストエディタで開きます。以下のコード・サンプルをコピー・アンド・ペーストして、`location` 関数を 2 つの新しい関数に置き換えます：

```
actor {

public func location(cities : [Text]) : async Text {
    return "Hello, from " # (debug_show cities) # "!";
};

public func location_pretty(cities : [Text]) : async Text {
    var str = "Hello from ";
    for (city in cities.vals()) {
    str := str # city #", ";
    };
    return str # "bon voyage!";
}
};

```

このコード例の`Text` は角括弧 (`[ ]`) で囲まれていることにお気づきでしょう。このコード例の`Text` は角括弧 ( ) で囲まれています。角括弧で囲まれた型は、その型の**配列**であることを示します。し たがっ て こ の コ ン テ キ ス ト では、`[Text]` は UTF-8 キ ャ ラ ク タ の集合の配列を表 し 、 プ ロ グ ラ ムが複数のテ キ ス ト 文字列を受け入れ て返す こ と を可能に し ます。

また、このコードサンプルでは、`apply` という抽象化された配列の基本的な操作形式を使用しています：

    public func apply<A, B>(fs : [A -> B], xs : [A]) : [B] {
        var ys : [B] = [];
        for (f in fs.vals()) {
            ys := append<B>(ys, map<A, B>(f, xs));
        };
        ys;
    };

配列に対する操作を実行する関数については、Motoko 基本ライブラリの Array モジュールの説明、または**Motoko プログラミング言語リファレンスを**参照してください。配列の使用に焦点を当てた別の例については、[examples](https://github.com/dfinity/examples/)リポジトリの[quick sort](https://github.com/dfinity/examples/tree/master/motoko/quicksort)プロジェクトを参照してください。

プロジェクトのディレクトリで次のコマンドを実行して、dapp を登録、ビルド、デプロイします：

    dfx deploy

プログラムで`location` メソッドを呼び出し、以下のコマンドを実行して Candid インターフェース記述構文を使用して`city` 引数を渡します：

    dfx canister call favorite_cities location '(vec {"San Francisco";"Paris";"Rome"})'

このコマンドは、Candid インターフェース記述構文`(vec { val1; val2; val3; })` を使用して値のベクトルを返します。Candid インターフェイス記述言語の詳細については、[Candid](../candid/index.md)言語ガイドを参照してください。

このコマンドは、以下のような出力を表示します：

    ("Hello, from ["San Francisco", "Paris", "Rome"]!")

プログラムで`location_pretty` メソッドを呼び出し、以下のコマンドを実行してインターフェース記述構文を使用して`city` 引数を渡します：

    dfx canister call favorite_cities location_pretty '(vec {"San Francisco";"Paris";"Rome"})'

このコマンドは以下のような出力を表示します：

    ("Hello from San Francisco, Paris, Rome, bon voyage!")

Candid UI を使用してコードをテストします。

この例では、各関数はテキスト文字列の配列を受け入れます。したがって、最初に配列の長さを選択し、\[**呼び出し**\] をクリックする前に各項目の値を設定します。

![Specifying an array](_attachments/candid-favorite-cities-result.png)

## 次のステップ

次のガイドでは、[ウォレットからcycles を受け取る](simple-cycles.md)方法について説明します。

<!---
# 12: Passing text arguments

## Overview
This guide provides a simple variation on the default program that lets you pass a single text argument to a single actor, compile the code to create a canister, then retrieve the argument. Throughout this document, the terms canister and canister are considered synonymous.

This guide illustrates how to pass arguments on the command-line in a terminal using the Candid interface description language (IDL) and how to modify the program to allow it to accept more than one value for the text argument.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

To create a new project for this guide, open a terminal window on your local computer, if you don’t already have one open.

Then create a new project by running the following command:

```
dfx new location_hello
```

Then navigate into your project directory by running the following command:

```
cd location_hello
```

## Modify the default configuration

In the [explore the default project](explore-templates) guide, you saw that creating a new project adds a default `dfx.json` configuration file to your project directory. You should always review the default settings in the file to verify the information accurately reflects the project settings you want to use. For this guide, you’ll modify the default configuration to remove settings that aren’t used.

To modify settings in the `dfx.json` configuration file, open the `dfx.json` configuration file in a text editor.

Check the default settings for the `location_hello` project, then remove all of the unnecessary configuration settings.

:::info
Because this guide does not involve creating any frontend assets, you can remove all of the `location_hello_frontend` configuration settings from the file.
:::

Save your changes and close the file to continue.

## Modify the default program

In the [explore the default project](explore-templates) guide, you saw that creating a new project creates a default `src` directory with a template `main.mo` file.

To modify the default template source code, open the `src/location_hello_backend/main.mo` source code file in a text editor.

Modify the default source code to replace the `greet` function with a `location` function and the `name` argument with a `city` argument.

For example, you can replace the file's existing code with the following:

```
actor {
public func location(city : Text) : async Text {
    return "Hello, " # city # "!";
};
};
```

Save your changes and close the file to continue.

## Start the local canister execution environment

Before you can build your project, you need to connect to a local canister execution environment or the Internet Computer blockchain mainnet.

Starting a canister execution environment locally requires a `dfx.json` file, so you should be sure you are in your project’s root directory. For this guide, you should have two separate terminal shells, so that you can start and see network operations in one terminal and manage your project in another.

To start the local canister execution environment, open a new terminal window or tab on your local computer.

:::info
-   You should now have **two terminals** open.
-   You should have the **project directory** as your **current working directory**.
:::

Start the canister execution environment on your local computer by running the following command:

```
dfx start
```

If you are prompted to allow or deny incoming network connections, click **Allow**.

Leave the terminal that displays network operations open and switch your focus to your original terminal where you created your project.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment, you can register, build, and deploy your dapp locally.

Register, build, and deploy your application by running the following command:

```
dfx deploy
```

The `dfx deploy` command output displays information about the operations it performs.

## Pass a text argument

You now have a program deployed as a **canister** in your local canister execution environment and can test your program by using `dfx canister call` commands.

Call the `location` method in the program and pass your `city` argument of type `text` by running the following command:

```
dfx canister call location_hello_backend location "San Francisco"
```

Because the argument in this case includes a space between `San` and `Francisco`, you need to enclose the argument in quotes. The command displays output similar to the following:

```
("Hello, San Francisco!")
```

If the argument did not contain a space that required enclosing the text inside of quotation marks, you could allow the Candid interface description language to infer the data type like this:

```
dfx canister call location_hello_backend location Paris
```

Candid infers the data type as `Text` and returns the output from your program as text like this:

```
("Hello, Paris!")
```

Call the `location` method in the program and pass your `city` argument explicitly using the Candid interface description language syntax for Text arguments:

```
dfx canister call location_hello_backend location '("San Francisco and Paris")'
```

The command displays output similar to the following:

```
("Hello, San Francisco and Paris!")
```

Because your program only accepts a single text argument, specifying multiple strings returns only the first argument. For example, if you try this command:

```
dfx canister call location_hello_backend location '("San Francisco","Paris","Rome")'
```

Only the first argument—`("Hello, San Francisco!")`—is returned.

## Revise the source code in your program

To extend what you have learned in this guide, you might want to try modifying the source code to return different results. For example, you might want to modify the `location` function to return multiple city names.

Open the `dfx.json` configuration file in a text editor and change the default `location_hello` settings to `favorite_cities`.

For this step, you should modify both the canister name and the path to the main program for the canister to use `favorite_cities`. Your `dfx.json` file should look like this:

```
{
"canisters": {
    "favorite_cities": {
    "main": "src/favorite_cities/main.mo",
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

Save your changes and close the `dfx.json` file to continue.

Copy the `location_hello` source file directory to match the name specified in the `dfx.json` configuration file by running the following command:

```
cp -r src/location_hello_backend src/favorite_cities
```

Open the `src/favorite_cities/main.mo` file in a text editor. Copy and paste the following code sample to replace the `location` function with two new functions:

```
actor {

public func location(cities : [Text]) : async Text {
    return "Hello, from " # (debug_show cities) # "!";
};

public func location_pretty(cities : [Text]) : async Text {
    var str = "Hello from ";
    for (city in cities.vals()) {
    str := str # city #", ";
    };
    return str # "bon voyage!";
}
};

```

You might notice that `Text` in this code example is enclosed by square (`[ ]`) brackets. By itself, `Text` represents a collection of UTF-8 characters. The square brackets around a type indicate that it is an **array** of that type. In this context, therefore, `[Text]` indicates an array of a collection of UTF-8 characters, enabling the program to accept and return multiple text strings.

The code sample also uses the basic format of an `apply` operation for the array, which can be abstracted as:

```
public func apply<A, B>(fs : [A -> B], xs : [A]) : [B] {
    var ys : [B] = [];
    for (f in fs.vals()) {
        ys := append<B>(ys, map<A, B>(f, xs));
    };
    ys;
};
```

For information about the functions that perform operations on arrays, see the description of the Array module in the Motoko base library or the **Motoko programming language reference**. For another example focused on the use of arrays, see the [quick sort](https://github.com/dfinity/examples/tree/master/motoko/quicksort) project in the [examples](https://github.com/dfinity/examples/) repository.

Register, build, and deploy the dapp by running the following command in your project's directory:

```
dfx deploy
```

Call the `location` method in the program and pass your `city` argument using the Candid interface description syntax by running the following command:

```
dfx canister call favorite_cities location '(vec {"San Francisco";"Paris";"Rome"})'
```

The command uses the Candid interface description syntax `(vec { val1; val2; val3; })` to return a vector of values. For more information about the Candid interface description language, see the [Candid](../candid/index.md) language guide.

This command displays output similar to the following:

```
("Hello, from ["San Francisco", "Paris", "Rome"]!")
```

Call the `location_pretty` method in the program and pass your `city` argument using the interface description syntax by running the following command:

```
dfx canister call favorite_cities location_pretty '(vec {"San Francisco";"Paris";"Rome"})'
```

The command displays output similar to the following:

```
("Hello from San Francisco, Paris, Rome, bon voyage!")
```

Test your code using the Candid UI.

To test your code, follow the instructions [here](candid-ui.md).
In this example, each function accepts an array of text strings. Therefore, you first select the length of the array, then set values for each item before clicking **Call**.

![Specifying an array](_attachments/candid-favorite-cities-result.png)

## Next steps

In the next guide, we'll cover [accepting cycles from a wallet.](simple-cycles.md)
-->
