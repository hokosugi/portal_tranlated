# 永続ストレージ

## 概要

dapp の例では、Motoko にシンプルなdapp を構築する方法を示しています。dapp は単純なカウンタで、カウンタをインクリメントし、カウンタ値を取得し、バックエンド関数を呼び出してカウンタ値をリセットします。関数はCandidインターフェースを通して公開されます。

この例dapp の目的は、dapp が変更され、再デプロイされた後でもカウンタ値が持続する、シンプルなカウンタdapp を構築することです。

この例では次のことを説明します：

- Motoko を使用して新しいcanister スマートコントラクトを作成します。
- カウンターのバックエンド関数を追加します（インクリメント、カウントの取得、カウントのリセット）。
- canister スマートコントラクトをローカルにデプロイします。
- `dfx` を使って Candid UI とコマンドラインでバックエンドをテストします。

## インストール

このサンプルプロジェクトは、学習とテストの目的で、クローン、インストール、ローカルへのデプロイが可能です。手順は、macOSまたはLinuxのいずれかでサンプルを実行することに基づいていますが、Windows上でWSL2を使用する場合、手順は同じになります。

### 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールします。
- \[[git を](https://git-scm.com/downloads)ダウンロードし、インストールしてください。
- \[https://github.com/dfinity/examples/ 以下のプロジェクトファイルをGitHubからダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ1：プロジェクトのファイルがあるフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/persistent-storage
    dfx start --background

### ステップ 2:canister をビルドしてデプロイします：

    dfx deploy

### ステップ 3: コマンドの使用法：`dfx canister call <project>  <function>`

    dfx canister call persistent_storage increment

出力：

    (1 : Nat)

    dfx canister call persistent_storage get

出力：

    (1 : Nat)

    dfx canister call persistent_storage reset

出力: 出力: 出力: 出力: 出力: 出力: 出力: 出力

    (0 : Nat)

保存された値の永続性は、`increment` 関数を再度呼び出し、値がゼロより大きいことを確認することでテストできます。次に、Motoko コードに変更を加えます。関数名`get` を`getCount` に変更するなど、どんな些細な変更でもかまいません。その後、プロジェクトを再デプロイし、`getCount` 関数を呼び出して、カウンタ値が初期値（0）に戻らなかったことを確認します。

``` bash
$ dfx canister call persistent_storage increment
(1 : Nat)
// Make code change
$ dfx deploy
$ dfx canister call persistent_storage getCount
(1 : Nat)
```

## アーキテクチャ

dapp の例の2つの主要な部分は、バックエンドとキャンディド・インターフェイスです。このサンプルプロジェクトにはフロントエンドはありません。

### Motoko バックエンド

バックエンド関数は`src/persistent_storage/main.mo` Motoko ファイルにあります。バックエンドはカウンタ値を保存し、カウンタ値を取得、インクリメント、リセットする関数を持っています。さらに、バックエンドはカウンタ値がdapp のアップグレード後も持続することを保証します。

#### カウンタ変数

現在のカウンタ値は、actor に数値として格納されます。

``` javascript
actor {
    stable var counter : Nat = 0;
}
```

#### インクリメント()

`increment()` 関数はカウンタ変数をインクリメントします。

``` javascript
public func increment() : async Nat {
    counter += 1;
    return counter;
};
```

この関数は、インクリメントされたカウンタ変数を返します。

#### get()

`get()` 関数は、現在のカウンタ値を返します。

``` javascript
public query func get() : async Nat {
    return counter;
};
```

#### reset()

`reset()` 関数は、カウンタ値を 0 にリセットし、その値を返します。

``` javascript
public func reset() : async Nat {
    counter := 0;
    return counter;
};
```

### Candid インターフェース

Candid UIは、バックエンドをテストするための簡単で使いやすいインターフェースを提供します。UI は自動的に生成され、canister ID は`dfx canister id <canister_name>` コマンドを使用して見つけることができます：

``` bash
$ dfx canister id __Candid_UI
r7inp-6aaaa-aaaaa-aaabq-cai
$ dfx canister id persistent_storage
rrkah-fqaaa-aaaaa-aaaaq-cai
```

**http://\<candid\_canister\_id\>.localhost:4943/?id=\<backend\_canister\_id\> を使用することで見つけることができます。**

<!---
# Persistent storage

## Overview
The example dapp shows how to build a simple dapp in Motoko, which will have persistent storage. The dapp is a simple counter, which will increment a counter, retrieve the counter value and reset the counter value by calling backend functions. The functions are exposed through a Candid interface.

The purpose of this example dapp is to build a simple counter dapp, where the counter value will persist even after the dapp has changed and been re-deployed.

This example covers:

- Create new canister smart contract using Motoko.
- Add backend functions for a counter (increment, get count and reset count).
- Deploy the canister smart contract locally.
- Test backend with Candid UI and command line using `dfx` .


## Installation
This example project can be cloned, installed and deployed locally, for learning and testing purposes. The instructions are based on running the example on either macOS or Linux, but when using WSL2 on Windows, the instructions will be the same.

### Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download and install [git.](https://git-scm.com/downloads)
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/persistent-storage
dfx start --background
```

### Step 2: Build and deploy the canister:

```
dfx deploy
```

### Step 3: Command usage: `dfx canister call <project>  <function>`

```
dfx canister call persistent_storage increment
```

Output:

```
(1 : Nat)
```

```
dfx canister call persistent_storage get
```

Output:

```
(1 : Nat)
```

```
dfx canister call persistent_storage reset
```

Output:

```
(0 : Nat)
```

The persistence of the stored value can be tested by calling the `increment` function again and making sure the value is larger than zero. Then make a change in the Motoko code, any minor change like changing the function name `get` to `getCount`, and then re-deploy the project and call the `getCount` function to verify that the counter value did not change back to the initial value (0).

```bash
$ dfx canister call persistent_storage increment
(1 : Nat)
// Make code change
$ dfx deploy
$ dfx canister call persistent_storage getCount
(1 : Nat)
```

## Architecture
The two main parts of the example dapp are the backend and the Candid interface. This example project does not have a frontend.

### Motoko backend
The backend functions are located in the `src/persistent_storage/main.mo` Motoko file. The backend stores the counter value, and has functions to get, increment and reset the counter value. Furthermore the backend insures the counter value persists upgrades of the dapp.

#### Counter variable
The current counter value is stored as a number in the actor.

```javascript
actor {
    stable var counter : Nat = 0;
}
```

#### increment()
The `increment()` function increments the counter variable.

```javascript
public func increment() : async Nat {
    counter += 1;
    return counter;
};
```

The function is returning the incremented counter variable.

#### get()
The `get()` function returns the current counter value.

```javascript
public query func get() : async Nat {
    return counter;
};
```

#### reset()
The `reset()` function resets the counter value to 0 and returns the value.

```javascript
public func reset() : async Nat {
    counter := 0;
    return counter;
};
```

### Candid interface
The Candid UI provides an easy, user friendly interface for testing the backend. The UI is automatically generated, and the canister ID can be found by using the `dfx canister id <canister_name>` command:

```bash
$ dfx canister id __Candid_UI
r7inp-6aaaa-aaaaa-aaabq-cai
$ dfx canister id persistent_storage
rrkah-fqaaa-aaaaa-aaaaq-cai
```

**http://<candid_canister_id>.localhost:4943/?id=<backend_canister_id>**


-->
