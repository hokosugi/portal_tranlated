# Actor リファレンス

## 概要

actorこの例では、あるプリンシパルのテキスト表現をactor 型の値に変換するためのactor 参照の簡単な使い方を示します。

Actor 参照は，生のプリンシパル識別子に型付けされたインタフェースを提供するために，必要なときだけ，控えめに使用されるべきです。 参照を使用することは、プログラマがコンパイラに対して、指定されたプリンシパルが指定されたインターフェイスに従うことを保証することになるので、事実上安全ではないとみなされます。与えられたプリンシパルが与えられたインターフェイスに従うことを保証するのは、コンパイラではなくプログラマの責任です。actor 

:::caution
正しくないインターフェイスを提供すると、その後のactor との通信がシリアライズエラー（メモリエラーではない）で失敗する可能性があります。
::：

この例では、1つのMotoko actor を定義しています:`main.mo` 名前ICを、actor 参照 "aaaaa-aa "に対するインタフェースをアサートして得られるactor にバインドします。これは、よく知られた(つまり、システム提供の)管理canister のテキスト形式のIDであり、通常、IC上のcanisters をインストール、トップアップ、およびその他の方法で管理するために使用されます。

管理canister の完全なインターフェースは、インターフェースコンピュータのインターフェース仕様で提供されています。この単純な例では、指定された操作のサブセットのみが必要であり、Candidサブタイピングにより、完全な仕様に記述されているよりも情報量の少ない型でインポートすることもできます。より多くの操作へのアクセスを提供するためには、元のCandidシグネチャの適切なMotoko 変換で、actor 型に追加するだけです。

私たちのactor は、actor のcycle 残高の半分をバーンするために、そのローカル ICactor 参照を使用して、一時的なcanister を提供、作成、照会、停止、および削除する単一のバーンメソッドを公開します。

このICの応用は説明のためのものであり、必ずしも有用ではありません。

これはMotoko の例であり、現在Rustのバリエーションはありません。

## 前提条件

この例には以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ 1: プロジェクト・ファイルのあるフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

    cd examples/motoko/actor-reference
    dfx start --background

### ステップ 2:canister をデプロイします：

    dfx deploy

### ステップ 3:canister `actor_reference` の`burn` メソッドを呼び出します：

    dfx canister call actor_reference burn '()'

出力は以下のようになります：

    [Canister by6od-j4aaa-aaaaa-qaadq-cai] balance before: 3091661916488
    [Canister by6od-j4aaa-aaaaa-qaadq-cai] cycles: 1538138650552
    [Canister by6od-j4aaa-aaaaa-qaadq-cai] balance after: 1545830657728

## リソース

- [Actorと同期データ](../motoko/main/actors-async.md)。
- [ Motoko 基本的な概念と用語](../motoko/main/basic-concepts.md)。

<!---
# Actor reference

## Overview

This example demonstrates a simple use of an actor reference to convert a textual representation of some principal to a value of an actor type, that, now typed as an actor, can be communicated with by calling the shared functions of its interface.

Actor references should be used sparingly, and only when necessary, to provide a typed interface to a raw principal identifier. Using an actor reference is regarded as unsafe since it is effectively an assurance, made by the programmer to the compiler, that the given principal obeys the given interface. It's the programmer's responsibility, as opposed to the compiler's, to ensure that the given principal obeys the given interface.

:::caution
Providing an incorrect interface may cause subsequent communication with the actor to fail with serialization (but not memory) errors.
:::

The example defines one Motoko actor: `main.mo` binds the name IC to the actor obtained by asserting an interface for the textual actor reference "aaaaa-aa". This is the identity, in textual format, of the well-known (that is, system provided) management canister which is typically used to install, top up, and otherwise manage canisters on the IC.

The full interface of the management canister is provided in the Interface Computer interface specification. For this simple example, we only need a subset of the specified operations, and, due to Candid sub-typing, can even import them at less informative types than described in the full specification. To provide access to more operations, one would simply add them to the actor type, at the appropriate Motoko translation of the original Candid signature.

Our actor exposes a single burn method that uses its local IC actor reference to provision, create, query, stop and delete a transient canister, in order to burn half of the actor's cycle balance.

This application of IC is meant to be illustrative, not necessarily useful.

This is a Motoko example that does not currently have a Rust variant. 


## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/actor-reference
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

### Step 3: Invoke the `burn` method of canister `actor_reference`:

```
dfx canister call actor_reference burn '()'
```

The output will resemble the following:

```
[Canister by6od-j4aaa-aaaaa-qaadq-cai] balance before: 3091661916488
[Canister by6od-j4aaa-aaaaa-qaadq-cai] cycles: 1538138650552
[Canister by6od-j4aaa-aaaaa-qaadq-cai] balance after: 1545830657728
```

## Resources

- [Actors and sync data](../motoko/main/actors-async.md).
- [Basic Motoko concepts and terms](../motoko/main/basic-concepts.md).
-->
