# シンプルなToDo

## 概要

この例では、シンプルな ToDo チェックリスト・アプリケーションの作成方法を説明します。

このアプリケーションは、次のMotoko ソース・コード・ファイルからビルドされます：

- `Utils.mo`ToDoチェックリスト項目の追加、完了、削除のコア関数。
- `Types.mo`ToDoチェックリスト項目の型定義。
- `Main.mo` actor の定義と、この によって公開されるメソッドが含まれます。canister

これはMotoko の例で、現在 Rust バリアントはありません。

## 前提条件

この例には以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/simple-to-do
    dfx start --background

### ステップ2：canister をデプロイします：

    dfx deploy

### ステップ 3: addTodo メソッドを呼び出して、ToDo チェックリストを作成します：

    dfx canister call simple_to_do addTodo '("Create a project")'
    dfx canister call simple_to_do addTodo '("Build the project")'
    dfx canister call simple_to_do addTodo '("Deploy the project")'

### ステップ 4: showTodosメソッドを呼び出して、ToDoチェックリストを表示します：

    dfx canister call simple_to_do showTodos

### ステップ 5: 入力した値が出力されることを確認します：

    ("
    ___TO-DOs___
    (1) Create a project
    (2) Build the project
    (3) Deploy the project")

### ステップ6: completeTodoメソッドを呼び出して、ToDoチェックリスト項目を完成させます：

    dfx canister call simple_to_do completeTodo '(1)'

### ステップ7: showTodosメソッドを呼び出して、ToDoチェックリストを表示します。

    dfx canister call simple_to_do showTodos

### ステップ 8: 返り値が期待したものと一致することを確認します。

    ("
    ___TO-DOs___
    (1) Create a project ✔
    (2) Build the project
    (3) Deploy the project")

<!---
# Simple to-do

## Overview
This example illustrates how to create a simple to-do checklist application. 

The application is built from the following Motoko source code files:

- `Utils.mo`: contains the core functions for adding, completing, and removing to-do checklist items.
- `Types.mo`: contains the type definition of a to-do checklist item.
- `Main.mo`: contains the actor definition and methods exposed by this canister.

This is a Motoko example that does not currently have a Rust variant. 

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/simple-to-do
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

### Step 3: Create a to-do checklist by invoking the addTodo method:

```
dfx canister call simple_to_do addTodo '("Create a project")'
dfx canister call simple_to_do addTodo '("Build the project")'
dfx canister call simple_to_do addTodo '("Deploy the project")'
```

### Step 4: Display the to-do checklist by invoking the showTodos method:

```
dfx canister call simple_to_do showTodos
```

### Step 5: Verify the output returns the values you inputted:

```
("
___TO-DOs___
(1) Create a project
(2) Build the project
(3) Deploy the project")
```

### Step 6: Complete a to-do checklist item by invoking the completeTodo method:

```
dfx canister call simple_to_do completeTodo '(1)'
```

### Step 7: Display the to-do checklist by invoking the showTodos method.

```
dfx canister call simple_to_do showTodos
```

### Step 8: Verify the return value matches what you would expect.

```
("
___TO-DOs___
(1) Create a project ✔
(2) Build the project
(3) Deploy the project")
```
-->
