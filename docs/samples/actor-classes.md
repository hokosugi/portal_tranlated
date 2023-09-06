# Actor クラス

## 概要

この例では、actor クラスの簡単な使用法を示します。 クラスは、プログラムが新しいactorを動的にインストールすることを可能にします（つまり、canisters ）。また、複数のcanister プロジェクトと、共有関数を介したactor 間通信を使用するactorの例も示します。

この例では、Motoko actor の2つ、`Map` と`Test` を定義しています。

Map は、`Nat` を`Text` の値にマッピングする、シンプルな分散キーバリューストアで、エントリは、オンデマンドでインストールされる、少数の個別の Bucketactorに格納されます。

`Map.mo` Motoko クラス ) をライブラリ からインポートします。また、作成するバケット間で を共有するために、 ベースライブラリをインポートします。actor `Bucket(i, n` `Buckets.mo` `Cycles` cycles `ExperimentalCycles` 

Map 内で`Buckets.Bucket(n, i)` を呼び出すたびに、キーが i にハッシュされた（キーの余りを n で除算した） Map のエントリ専用の新しい Bucket インスタンス（n の i 番目）がインスタンス化されます。

Bucketactor クラスの各非同期インスタンス化は、新しい Bucketcanister の動的でプログラム的なインストールに対応します。

それぞれの新しい Bucket には、そのインストールと運用のコストを支払うのに十分なcycles が提供されなければなりません。Map は、`Bucket(n, i)` への各非同期呼び出しに、`Cycles.add(cycleShare` への呼び出しを使用して、Map の初期cycle 残高を等しく追加することで、これを達成します。）

`Test.mo` actor は（インストールされた）`Map` canister をインポートし、`Maps` Candid interface を使用してMotoko タイプを決定します。`Test`のrunメソッドは、単純に24の連続するエントリをMapに入れます。これらのエントリは、キーバリューストアを構成するバケットに均等に分散されます。最初のエントリをバケットに追加するのは、その後のエントリを追加するよりも時間がかかります。

これはMotoko の例で、現在のところ Rust バリアントはありません。

## 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/classes
    dfx start --background

### ステップ 2:canisters `Map` と`Test` をデプロイします：

    dfx deploy

### ステップ 3:canister Test の run メソッドを起動します：

    dfx canister call Test run '()'

出力は以下のようになるはずです：

    debug.print: putting: (0, "0")
    debug.print: putting: (1, "1")
    debug.print: putting: (2, "2")
    debug.print: putting: (3, "3")
    debug.print: putting: (4, "4")
    debug.print: putting: (5, "5")
    debug.print: putting: (6, "6")
    debug.print: putting: (7, "7")
    debug.print: putting: (8, "8")
    debug.print: putting: (9, "9")
    debug.print: putting: (10, "10")
    debug.print: putting: (11, "11")
    debug.print: putting: (12, "12")
    debug.print: putting: (13, "13")
    debug.print: putting: (14, "14")
    debug.print: putting: (15, "15")
    debug.print: putting: (16, "16")
    debug.print: putting: (17, "17")
    debug.print: putting: (18, "18")
    debug.print: putting: (19, "19")
    debug.print: putting: (20, "20")
    debug.print: putting: (21, "21")
    debug.print: putting: (22, "22")
    debug.print: putting: (23, "23")
    ()

<!---
# Actor classes

## Overview

This example demonstrates a simple use of actor classes, which allow a program to dynamically install new actors (that is, canisters). It also demonstrates a multi-canister project, and actors using inter-actor communication through shared functions.

The example defines two Motoko actors, `Map` and `Test`.

Map is a simple, distributed key-value store, mapping `Nat` to `Text` values, with entries stored in a small number of separate Bucket actors, installed on demand.

`Map.mo` imports a Motoko actor class `Bucket(i, n`) from library `Buckets.mo`. It also imports the `ExperimentalCycles` base library, naming it `Cycles` for short, in order to share its cycles amongst the bucket it creates.

Each call to `Buckets.Bucket(n, i)` within Map instantiates a new Bucket instance (the i-th of n) dedicated to those entries of the Map whose key hashes to i (by taking the remainder of the key modulo division by n).

Each asynchronous instantiation of the Bucket actor class corresponds to the dynamic, programmatic installation of a new Bucket canister.

Each new Bucket must be provisioned with enough cycles to pay for its installation and running costs. Map achieves this by adding an equal share of Map's initial cycle balance to each asynchronous call to `Bucket(n, i)`, using a call to `Cycles.add(cycleShare`).

The `Test.mo` actor imports the (installed) `Map` canister, using `Maps` Candid interface to determine its Motoko type. `Test`'s run method simply puts 24 consecutive entries into Map. These entries are distributed evenly amongst the buckets making up the key-value store. Adding the first entry to a bucket take longer than adding a subsequent one, since the bucket needs to be installed on first use.

This is a Motoko example that does not currently have a Rust variant. 


## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/classes
dfx start --background
```

### Step 2: Deploy the canisters `Map` and `Test`:

```
dfx deploy
```

### Step 3: Invoke the run method of canister Test:

```
dfx canister call Test run '()'
```

The output should resemble the following:

```
debug.print: putting: (0, "0")
debug.print: putting: (1, "1")
debug.print: putting: (2, "2")
debug.print: putting: (3, "3")
debug.print: putting: (4, "4")
debug.print: putting: (5, "5")
debug.print: putting: (6, "6")
debug.print: putting: (7, "7")
debug.print: putting: (8, "8")
debug.print: putting: (9, "9")
debug.print: putting: (10, "10")
debug.print: putting: (11, "11")
debug.print: putting: (12, "12")
debug.print: putting: (13, "13")
debug.print: putting: (14, "14")
debug.print: putting: (15, "15")
debug.print: putting: (16, "16")
debug.print: putting: (17, "17")
debug.print: putting: (18, "18")
debug.print: putting: (19, "19")
debug.print: putting: (20, "20")
debug.print: putting: (21, "21")
debug.print: putting: (22, "22")
debug.print: putting: (23, "23")
()
```
-->
