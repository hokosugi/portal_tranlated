# 15: 複数のactorの使用

## 概要

このガイドでは、複数のactorを持つプロジェクトを作成します。現在、1 つのMotoko ファイルで定義できるactor は 1 つだけで、1 つのactor は常に 1 つのcanister にコンパイルされます。しかし、複数のactorを持つ**プロジェクトを**作成し、同じ`dfx.json` 設定ファイルから複数のcanisters をビルドすることができます。

このガイドでは、同じプロジェクト内に3つのactorの別々のプログラム・ファイルを作成します。このプロジェクトでは、関連性のない以下のactorを定義します：

- `assistant` actor は、ToDoリストにタスクを追加したり表示したりする関数を提供します。
  
  簡単のため、このガイドのコード・サンプルには、ToDo 項目を追加する関数と、追加された ToDo 項目の現在のリストを表示する関数のみが含まれています。このcanisterより完全なバージョンは、アイテムを完了としてマークしたり、リストからアイテムを削除したりする関数が追加されており、[simple to-do checklist](https://github.com/dfinity/examples/tree/master/motoko/simple-to-do) として[examples](https://github.com/dfinity/examples/)リポジトリにあります。

- `rock_paper_scissors` actor は、ハードコードされたジャンケン大会の勝者を決定する関数を提供します。
  
  このコードサンプルでは、Motoko プログラムで、`switch` と`case` の基本的な使い方を、ハードコードされたプレイヤーと選択肢で説明しています。

- `daemon` actor は、デーモンの起動と停止のためのモック関数を提供します。
  
  このコード・サンプルでは、単に変数を代入し、デモ用にメッセージを表示します。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていなければ、ローカル・コンピュータでターミナル・シェルを開いてください。

以下のコマンドを実行して、新しいプロジェクトを作成します：

    dfx new multiple_actors

以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd multiple_actors

## デフォルト設定の変更

新しいプロジェクトを作成すると、デフォルトの`dfx.json` 設定ファイルがプロジェクト・ディレクトリに追加されることはすでにおわかりでしょう。このガイドでは、ビルドしたいactor を定義する各canister の場所を指定するために、このファイルにセクションを追加する必要があります。

テキストエディタで`dfx.json` 設定ファイルを開き、デフォルトの`multiple_actors_backend` canister 名とソース・ディレクトリを`assistant` に変更します。

例えば、`canisters` キーの下に：

    "assistant": {
      "main": "src/assistant/main.mo",
      "type": "motoko"
    },

設定ファイルのこの`canisters` セクションに設定を追加するため、`assistant` メイン・ソース・コード・ファイルの場所とcanister タイプを囲む中括弧の後にも**カンマを**追加する必要があります。

ファイルから`multiple_actors_frontend` セクションを削除します。

`rock_paper_scissors` canister の新しいcanister 名、ソース・コードの場所、canister タイプを追加し、`daemon` プログラム・ファイルの新しいcanister 名、ソース・コードの場所、canister タイプを`assistant` canister 定義の下に追加します。

変更後、`dfx.json` ファイルの`canisters` セクションは以下のようになります：

    {
      "canisters": {
        "assistant": {
          "main": "src/assistant/main.mo",
          "type": "motoko"
        },
        "rock_paper_scissors": {
          "main": "src/rock_paper_scissors/main.mo",
          "type": "motoko"
        },
        "daemon": {
          "main": "src/daemon/main.mo",
          "type": "motoko"
        }
      },
      "defaults": {
        "build": {
          "packtool": ""
        }
      },
      "version": 1
    }

変更を保存し、`dfx.json` ファイルを閉じて続行します。

以下のコマンドを実行して、デフォルトのソース・ファイル・ディレクトリの名前を、`dfx.json` 構成ファイルで指定された名前と一致するように変更します：

    cp -r src/multiple_actors_backend/ src/assistant/

次のコマンドを実行して、`assistant` ソース・ファイル・ディレクトリをコピーし、`rock_paper_scissors` actor 用のメインcanister ファイルを作成します：

    cp -r src/assistant/ src/rock_paper_scissors/

次のコマンドを実行して、`assistant` ソース・ファイル・ディレクトリをコピーし、`daemon` actor のメインcanister ファイルを作成します：

    cp -r src/assistant/ src/daemon/

## デフォルトのcanisters

これで、`src` ディレクトリに 3 つの別々のディレクトリができ、それぞれにテンプレート`main.mo` ファイルができました。このガイドでは、各テンプレート`main.mo` ファイルの内容を異なるactor に置き換えます。

テキストエディタで`src/assistant/main.mo` ファイルを開き、既存のコンテンツを削除します。

このコードをコピーして、ファイルに貼り付けます：

    import Array "mo:base/Array";
    import Nat "mo:base/Nat";
    
    // Define the actor
    actor Assistant {
    
      stable var todos : [ToDo] = [];
      stable var nextId : Nat = 1;
    
      // Define to-do item properties
      type ToDo = {
        id : Nat;
        description : Text;
        completed : Bool;
      };
    
      // Add to-do item utility
      func add(todos : [ToDo], description : Text, id : Nat) : [ToDo] {
        let todo : ToDo = {
          id = id;
          description = description;
          completed = false;
        };
        Array.append(todos, [todo])
    };
    
      // Show to-do item utility
      func show(todos : [ToDo]) : Text {
        var output : Text = "\n___TO-DOs___";
        for (todo : ToDo in todos.vals()) {
          output #= "\n(" # Nat.toText(todo.id) # ") " # todo.description;
          if (todo.completed) { output #= " ✔"; };
        };
        output
      };
    
      public func addTodo (description : Text) : async () {
        todos := add(todos, description, nextId);
        nextId += 1;
      };
    
      public query func showTodos () : async Text {
        show(todos)
      };
    
    };

変更を保存し、`main.mo` ファイルを閉じて続行します。

テキストエディタで`src/rock_paper_scissors/main.mo` ファイルを開き、既存のコンテンツを削除します。

このコードをコピーしてファイルに貼り付けます：

    import I "mo:base/Iter"; 
    
    actor RockPaperScissors {
    
      stable var alice_score : Nat = 0;
      stable var bob_score : Nat = 0;
      stable var alice_last : Choice = #scissors;
      stable var bob_last : Choice = #rock;
    
      type Choice = {
        #rock;
        #paper;
        #scissors;
      };
    
      public func contest() : async Text {
        for (i in I.range(0, 99)) {
          battle_round();
        };
        var winner = "The contest was a draw";
        if (alice_score > bob_score) winner := "Alice won" 
        else if (alice_score < bob_score) winner := "Bob won";
        return (winner);
      };
    
      func battle_round() : () {
        let a = alice(bob_last);
        let b = bob(alice_last);
    
        switch (a, b) {
          case (#rock, #scissors) alice_score += 1;
          case (#rock, #paper) bob_score += 1;
          case (#paper, #scissors) alice_score += 1;
          case (#paper, #rock) bob_score += 1;
          case (#scissors, #paper) alice_score += 1;
          case (#scissors, #rock) bob_score += 1;
          case (#rock, #rock) alice_score += 0;
          case (#paper, #paper) bob_score += 0;
          case (#scissors, #scissors) alice_score += 0;
        };
    
        alice_last := a;
        bob_last := b;
    
        return ();
      };
      
      // Hard-coded players and choices
      func bob(last : Choice) : Choice {
        return #paper;
      };
    
      func alice(last : Choice) : Choice {
        return #rock;
      };
    };

変更を保存し、`main.mo` ファイルを閉じて続行します。

テキストエディタで`src/daemon/main.mo` ファイルを開き、既存のコンテンツを削除します。

このコードをコピーしてファイルに貼り付けます：

    actor Daemon {
      stable var running = false;
    
      public func launch() : async Text {
        running := true;
        debug_show "The daemon process is running";
      };
    
      public func stop(): async Text {
        running := false;
        debug_show "The daemon is stopped";
      };
    };

変更を保存し、`main.mo` ファイルを閉じて続行します。

## ローカルのcanister 実行環境を起動します。

`multiple_actors` プロジェクトをローカルにインストールする前に、ローカルのcanister 実行環境を起動する必要があります。Internet Computer ブロックチェーンメインネットにデプロイするつもりなら、このセクションは飛ばしてかまいません。

ローカル・コンピューターで新しいターミナル・ウィンドウまたはタブを開きます。

必要に応じて、プロジェクトのルート・ディレクトリに移動します。

以下のコマンドを実行して、ローカルのcanister 実行環境を起動します：

    dfx start --clean

このガイドでは、`--clean` オプションを使用して、ローカルのcanister 実行環境をクリーンなステートで起動します。

このオプションは、通常の運用を中断させる可能性のあるオーファンのバックグラウンド・プロセスやcanister 識別子を削除します。例えば、プロジェクト間を移動する際に`dfx stop` を発行し忘れた場合、バックグラウンドや別のターミナルでプロセスが実行されている可能性があります。`--clean` オプシ ョ ン を使用す る こ と で、 ローカルのcanister 実行環境を起動 し 、 実行中のプ ロ セ ス を手動で発見 し て終了 さ せ る こ と な く 、 次のス テ ッ プへ進む こ と がで き ます。

canister 実行操作を表示するターミナルは開いたままにしておき、新しいプロジェクトを作成した元のターミナルにフォーカスを切り替えます。

## マルチcanister をデプロイします。dapp

マルチcanister dapp をデプロイするには、canisters をローカルのcanister 実行環境またはInternet Computer ブロックチェーンメインネットに登録、ビルド、インストールする必要があります。

プロジェクトのディレクトリで以下のコマンドを実行して、アプリケーションを登録、ビルド、デプロイします：

    dfx deploy

Internet Computer ブロックチェーンメインネットにdapp をデプロイするには、`--network` オプションと`dfx.json` ファイルで設定したネットワークエイリアスを指定して`dfx deploy` コマンドを実行します。たとえば、ネットワークエイリアス`ic` で指定された URL を使用してInternet Computer メインネットに接続する場合は、次のようなコマンドを実行します：

    dfx deploy --network ic

`dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。たとえば、`dfx.json` 構成ファイルで定義された 3 つのcanisters のcanister 識別子が表示されます。

    Deployed canisters.
    URLs:
    Backend canister via Candid interface:
    assistant: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=ahw5u-keaaa-aaaaa-qaaha-cai
    daemon: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=aax3a-h4aaa-aaaaa-qaahq-cai
    rock_paper_scissors: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=c5kvi-uuaaa-aaaaa-qaaia-cai

## コマンドを呼び出して、デプロイメントを確認します。canisters

これで、3つの **canisters**これで、ローカルcanister 実行環境またはInternet Computer ブロックチェーンメインネット上に、3 つのスマートコントラクトがデプロイされ、`dfx canister call` コマンドを使用してそれらをテストできます。

`dfx canister call` コマンドを使用して、`addTodo` 関数を使用してcanister `assistant` を呼び出し、次のコマンドを実行して追加したいタスクを渡します：

    dfx canister call assistant addTodo '("Schedule monthly demos")'

次のコマンドを実行して、コマンドが`showTodos` 関数を使用して ToDo リスト項目を返すことを確認します：

    dfx canister call assistant showTodos

コマンドは以下のような出力を返します：

    ("
    ___TO-DOs___
    (1) Schedule monthly demos")

次のコマンドを実行して、`contest` 関数を使用してcanister `rock_paper_scissors` を呼び出すには、`dfx canister call` コマンドを使用します：

    dfx canister call rock_paper_scissors contest

コマンドは、以下のようなハードコードされたコンテストの結果を返します：

    ("Bob won")

`dfx canister call` コマンドを使用して、次のコマンドを実行して`launch` 関数を使用してcanister `daemon` を呼び出します：

    dfx canister call daemon launch

モック`launch` 関数が "The daemon process is running" メッセージを返すことを確認します：

    (""The daemon process is running"")

Internet Computer ブロックチェーンメインネット上で実行されるcanisters をテストするには、上記と同じコマンドを使用し、`--network` オプションと`dfx.json` ファイルで設定されたネットワークエイリアスを指定します。

## 次のステップ

次のガイドでは、[IDを使用したアクセス制御について](./access-control.md)説明します。

<!---
# 15: Using multiple actors

## Overview
In this guide, you are going to create a project with multiple actors. Currently, you can only define one actor in a Motoko file and a single actor is always compiled to a single canister. You can, however, create **projects** that have multiple actors and can build multiple canisters from the same `dfx.json` configuration file.

For this guide, you are going to create separate program files for three actors in the same project. This project defines the following unrelated actors:

-   The `assistant` actor provides functions to add and show tasks in a to-do list.

    For simplicity, the code sample for this guide only includes the functions to add to-do items and to show the current list of to-do items that have been added. A more complete version of this canister—with additional functions for marking items as complete and removing items from the list—is available in the [examples](https://github.com/dfinity/examples/) repository as [simple to-do checklist](https://github.com/dfinity/examples/tree/master/motoko/simple-to-do).

-   The `rock_paper_scissors` actor provides a function for determining a winner in a hard-coded rock-paper-scissors contest.

    This code sample illustrates the basic use of `switch` and `case` in a Motoko program with hard-coded players and choices.

-   The `daemon` actor provides mock functions for starting and stopping a daemon.

    This code sample simply assigns a variable and prints messages for demonstration purposes.

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal shell on your local computer, if you don’t already have one open.

Create a new project by running the following command:

```
dfx new multiple_actors
```

Change to your project directory by running the following command:

```
cd multiple_actors
```

## Modify the default configuration

You have already seen that creating a new project adds a default `dfx.json` configuration file to your project directory. For this guide, you need to add sections to this file to specify the location of each canister that defines an actor you want to build.

Open the `dfx.json` configuration file in a text editor, then change the default `multiple_actors_backend` canister name and source directory to `assistant`.

For example, under the `canisters` key:

```
"assistant": {
  "main": "src/assistant/main.mo",
  "type": "motoko"
},
```

Because you are going to add settings to this `canisters` section of the configuration file, you must also add a **comma** after the curly brace that encloses the location of the `assistant` main source code file and the canister type.

Remove the `multiple_actors_frontend` section from the file.

Add a new canister name, source code location, and canister type for the `rock_paper_scissors` canister and a new canister name, source code location, and canister type for the `daemon` program files below the `assistant` canister definition.

After making the changes, the `canisters` section of the `dfx.json` file should look similar to this:

```
{
  "canisters": {
    "assistant": {
      "main": "src/assistant/main.mo",
      "type": "motoko"
    },
    "rock_paper_scissors": {
      "main": "src/rock_paper_scissors/main.mo",
      "type": "motoko"
    },
    "daemon": {
      "main": "src/daemon/main.mo",
      "type": "motoko"
    }
  },
  "defaults": {
    "build": {
      "packtool": ""
    }
  },
  "version": 1
}
```

Save your changes and close the `dfx.json` file to continue.

Change the name of the default source file directory to match the name specified in the `dfx.json` configuration file by running the following command:

```
cp -r src/multiple_actors_backend/ src/assistant/
```

Copy the `assistant` source file directory to create the main canister file for the `rock_paper_scissors` actor by running the following command:

```
cp -r src/assistant/ src/rock_paper_scissors/
```

Copy the `assistant` source file directory to create the main canister file for the `daemon` actor by running the following command:

```
cp -r src/assistant/ src/daemon/
```

## Modify the default canisters

You now have three separate directories in the `src` directory, each with a template `main.mo` file. For this guide, you will replace the content in each template `main.mo` file with a different actor.

Open the `src/assistant/main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the file:

```
import Array "mo:base/Array";
import Nat "mo:base/Nat";

// Define the actor
actor Assistant {

  stable var todos : [ToDo] = [];
  stable var nextId : Nat = 1;

  // Define to-do item properties
  type ToDo = {
    id : Nat;
    description : Text;
    completed : Bool;
  };

  // Add to-do item utility
  func add(todos : [ToDo], description : Text, id : Nat) : [ToDo] {
    let todo : ToDo = {
      id = id;
      description = description;
      completed = false;
    };
    Array.append(todos, [todo])
};

  // Show to-do item utility
  func show(todos : [ToDo]) : Text {
    var output : Text = "\n___TO-DOs___";
    for (todo : ToDo in todos.vals()) {
      output #= "\n(" # Nat.toText(todo.id) # ") " # todo.description;
      if (todo.completed) { output #= " ✔"; };
    };
    output
  };

  public func addTodo (description : Text) : async () {
    todos := add(todos, description, nextId);
    nextId += 1;
  };

  public query func showTodos () : async Text {
    show(todos)
  };

};
```

Save your changes and close the `main.mo` file to continue.

Open the `src/rock_paper_scissors/main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the file:

```
import I "mo:base/Iter"; 

actor RockPaperScissors {

  stable var alice_score : Nat = 0;
  stable var bob_score : Nat = 0;
  stable var alice_last : Choice = #scissors;
  stable var bob_last : Choice = #rock;

  type Choice = {
    #rock;
    #paper;
    #scissors;
  };

  public func contest() : async Text {
    for (i in I.range(0, 99)) {
      battle_round();
    };
    var winner = "The contest was a draw";
    if (alice_score > bob_score) winner := "Alice won" 
    else if (alice_score < bob_score) winner := "Bob won";
    return (winner);
  };

  func battle_round() : () {
    let a = alice(bob_last);
    let b = bob(alice_last);

    switch (a, b) {
      case (#rock, #scissors) alice_score += 1;
      case (#rock, #paper) bob_score += 1;
      case (#paper, #scissors) alice_score += 1;
      case (#paper, #rock) bob_score += 1;
      case (#scissors, #paper) alice_score += 1;
      case (#scissors, #rock) bob_score += 1;
      case (#rock, #rock) alice_score += 0;
      case (#paper, #paper) bob_score += 0;
      case (#scissors, #scissors) alice_score += 0;
    };

    alice_last := a;
    bob_last := b;

    return ();
  };
  
  // Hard-coded players and choices
  func bob(last : Choice) : Choice {
    return #paper;
  };

  func alice(last : Choice) : Choice {
    return #rock;
  };
};
```

Save your changes and close the `main.mo` file to continue.

Open the `src/daemon/main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the file:

```
actor Daemon {
  stable var running = false;

  public func launch() : async Text {
    running := true;
    debug_show "The daemon process is running";
  };

  public func stop(): async Text {
    running := false;
    debug_show "The daemon is stopped";
  };
};
```

Save your changes and close the `main.mo` file to continue.

## Start the local canister execution environment

Before you can install the `multiple_actors` project locally, you need to start your local canister execution environment. If you intend to deploy it to the Internet Computer blockchain mainnet, then you can skip this section.

Open a new terminal window or tab on your local computer.

Navigate to the root directory for your project, if necessary.

Start the local canister execution environment by running the following command:

```
dfx start --clean
```

For this guide, we’re using the `--clean` option to start the local canister execution environment in a clean state.

This option removes any orphan background processes or canister identifiers that might disrupt normal operations. For example, if you forgot to issue a `dfx stop` when moving between projects, you might have a process running in the background or in another terminal. The `--clean` option ensures that you can start the local canister execution environment and continue to the next step without manually finding and terminating any running processes.

Leave the terminal that displays canister execution operations open and switch your focus to your original terminal where you created your new project.

## Deploy your multi-canister dapp

To deploy the multi-canister dapp you need to register, build, and install the canisters either to the local canister execution environment or to the Internet Computer blockchain mainnet.

Register, build, and deploy the application by running the following command in your project's directory:

```
dfx deploy
```

To deploy the dapp on the Internet Computer blockchain mainnet, run `dfx deploy` command specifying the `--network` option and the network alias configured in the `dfx.json` file. For example, if you are connecting to the Internet Computer mainnet using to the URL specified by the network alias `ic` you would run a command similar the following:

```
dfx deploy --network ic
```

The `dfx deploy` command output displays information about the operations it performs. For example, the command displays the specific canister identifiers for the three canisters defined in the `dfx.json` configuration file.

```
Deployed canisters.
URLs:
Backend canister via Candid interface:
assistant: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=ahw5u-keaaa-aaaaa-qaaha-cai
daemon: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=aax3a-h4aaa-aaaaa-qaahq-cai
rock_paper_scissors: http://127.0.0.1:8080/?canisterId=c2lt4-zmaaa-aaaaa-qaaiq-cai&id=c5kvi-uuaaa-aaaaa-qaaia-cai
```

## Verify deployment by calling the canisters

You now have three **canisters** smart contracts deployed, either on your local canister execution environment or on the Internet Computer blockchain mainnet, and can test them by using `dfx canister call` commands.

Use the `dfx canister call` command to call the canister `assistant` using the `addTodo` function and pass it the task you want to add by running the following command:

```
dfx canister call assistant addTodo '("Schedule monthly demos")'
```

Verify that the command returns the to-do list item using the `showTodos` function by running the following command:

```
dfx canister call assistant showTodos
```

The command returns output similar to the following:

```
("
___TO-DOs___
(1) Schedule monthly demos")
```

Use the `dfx canister call` command to call the canister `rock_paper_scissors` using the `contest` function by running the following command:

```
dfx canister call rock_paper_scissors contest
```

The command returns the result of the hard-coded contest similar to the following:

```
("Bob won")
```

Use the `dfx canister call` command to call the canister `daemon` using the `launch` function by running the following command:

```
dfx canister call daemon launch
```

Verify the mock `launch` function returns "The daemon process is running" message:

```
(""The daemon process is running"")
```

To test your canisters running on the Internet Computer blockchain mainnet use the same command as above, specifying the `--network` option and the network alias configured in the `dfx.json` file.

## Next steps

In the next guide, let's discuss [access control using identities](./access-control.md)
-->
