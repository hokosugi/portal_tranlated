# ランダム迷路

## 概要

この例題では、暗号的なランダム性を使用してランダムな迷路を生成します。

この例題では

- 暗号化ランダム性を使用するためのライブラリ Random のインポート。
- 共有関数`Random.blob()` を使用したエントロピーの非同期リクエスト。
- ヘルパークラス`Random.Finite(entropy: blob)` を使用した境界付き離散乱数の生成。このクラスの各インスタンス f は、さまざまな分布からサンプリングするために呼び出されると、最初に与えられたエントロピーを消費します。例えば`f.coin()` への呼び出しが失敗すると、`null` が返され、`f` を破棄して、`Random.blob()` （例えば`f := Finite(await Random.blob())` ）への新しい呼び出しから得られる新鮮なエントロピーの塊から構築された Finite クラスの新しいインスタンスを使用する必要があります。

アプリケーションは、以下のMotoko ソースコードファイルから構築されます：

- `main.mo` actor の定義と、この によって公開されるメソッドが含まれています。canister

このactor は、Motoko の Random ライブラリを使用して、ユーザが指定したサイズの暗号化されたランダムな迷路を生成します。

関数 generate は、ライブラリ関数`Random.blob()` を非同期に呼び出し、Internet Computer から 256 ビットの生のエントロピー (32 バイトとして 256 個のランダムビット) を取得します。これらのblobのビットは、ライブラリRandom.moの他のクラスや関数を使用して、さまざまな離散分布からサンプルを生成するために消費されます。

これはMotoko の例で、現在のところ Rust バリアントはありません。

## 前提条件

この例題には以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。

ターミナル・ウィンドウを開きます。

### ステップ1：プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/random_maze
    dfx start --background

### ステップ 2: フロントエンドの依存関係をインストールします：

    npm install

### ステップ 3:canister をデプロイします：

    dfx deploy

### ステップ 4: ユーザーインターフェイスにアクセスできる URL をメモします。

    echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id random_maze_assets)"

![Maze interface](./_attachments/maze1.png)

迷路のサイズを入力し、**生成！を**選択します。迷路は以下のように表示されます。

![Random maze](./_attachments/maze2.png)

<!---
# Random maze

## Overview

The example generates a random maze using cryptographic randomness.

It illustrates:

- Importing library Random to use cryptographic randomness.
- Make asynchronous requests for entropy using shared function `Random.blob()`.
- Generating bounded, discrete random numbers using helper class `Random.Finite(entropy: blob)`. Each instance, f, of this class consumes its initially supplied entropy as it is called to sample from various distributions. Calls to, for example `f.coin()` can fail by returning `null`, requiring `f` to be discarded in favour of a fresh instance of the Finite class, constructed from a fresh blob of entropy obtained from a new call to `Random.blob()` (for example `f := Finite(await Random.blob())`).

The application is built from the following Motoko source code file:

- `main.mo`: contains the actor definition and methods exposed by this canister.

This actor use Motoko's Random library to generate a cryptographically random maze of user-specified size.

Function generate, calls library function `Random.blob()` asynchronously to obtain 256-bits of raw entropy (256 random bits as 32 bytes) from the Internet Computer. It makes these calls on demand as it is constructing a maze. The bits of these blobs are consumed to generate samples from a variety of discrete distributions using some of the other classes and functions of library Random.mo.

This is a Motoko example that does not currently have a Rust variant. 

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/random_maze
dfx start --background
```

### Step 2: Install front-end dependencies:

```
npm install
```

### Step 3: Deploy the canister:

```
dfx deploy
```

### Step 4: Take note of the URL at which the user interface is accessible.

```
echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id random_maze_assets)"
```

![Maze interface](./_attachments/maze1.png)

Enter a size for the maze, then select **Generate!**. The maze will be displayed below.

![Random maze](./_attachments/maze2.png)

-->
