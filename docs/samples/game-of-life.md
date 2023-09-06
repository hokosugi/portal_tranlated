# 人生ゲーム

## 概要

この例題には、Conway の Game of Life の一連の実装が含まれています。

その主な目的は、Motoko の安定変数を使ったステート保存アップグレードを実証することです。この実装は参考のためのものであり、効率やネットワーク遅延の隠蔽のために最適化されたものではありません。

`src` ディレクトリには、バージョン v0 の初期実装が含まれていますが、その内容は、後に`versions/v1` と`versions/v2` のディレクトリの内容に置き換わります。実際のプロジェクトでは、適切なソース管理が行われていれば、src ディレクトリが 1 つ存在し、異なるバージョンのコードがリポジトリの異なるブランチに存在することがあります。

ディレクトリ src (バージョン v0) には、非常にシンプルな React UI のアプリケーションがあります。

ディレクトリ`versions/v1` と`versions/v2` には、デプロイされたcanister の再デプロイメントによるライブアップグレードを説明するために使用される、src への逐次アップグレードが含まれています。

アップグレードをわかりやすくするために、各バージョンではライブセルの表示に異なる桁を使用しています (0,1,2)。

これはMotoko の例で、現在 Rust のバリエーションはありません。

## 前提条件

この例には以下のインストールが必要です：

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\][Node.jsを](https://nodejs.org/en/download/)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードします: https://github.com/dfinity/examples/

ターミナル・ウィンドウを開きます。

### ステップ1：プロジェクトのファイルがあるフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

    cd examples/motoko/life
    dfx start --background

### ステップ 2:canister をデプロイします：

    dfx deploy

デプロイメントステップでは、life\_assetscanister のcanister id が報告されるはずです。

コマンドを使ってlife\_assetsにアクセスできるURLを控えておいてください：

    echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id life_assets)"

### ステップ3: 先ほどのコマンドの出力で返されたリンクをクリックして、ブラウザでフロントエンドを開きます。

このようなフロントエンドが表示されるはずです：

![Game of Life](./_attachments/game-of-life.png)

**Step**ボタンをクリックしてください。グリッドはGame of Lifeの次の世代に進みます。

**Run**ボタンをクリックしてください。グリッドが（ゆっくりと）連続した世代をアニメーションします。

**Pause**ボタンをクリックしてください。アニメーションが一時停止します。

## 他のバージョンへのアップグレード

v0 の実装にはアップグレードの規定がないため、v0 の実装を再デプロイするたびに、グリッドは同じ（擬似ランダムな）初期状態に再初期化されます。

グリッドのステートは、`src/State.mo` ファイルで説明されているように、ネストされたミュータブルなブール値の配列として素朴に表現されます：

    module {
    
      public type Cell = Bool;
      public type State = [[var Cell]];
      ...
    }

`src/life/grid` は、ステートから構築され、ステートを維持する単純なクラスとして表現されます。ここでは詳細を省略します。

`src/life/main` のメインactor はランダムなステートを作成し、2つのグリッドオブジェクト、現在のグリッドと次のグリッド (`cur` と`nxt`) を保持します。Life の`next()` メソッドは、Grid メソッドコール`cur.next(nxt)` を使用して、`cur` から`nxt` をアップデートすることで、Game of Life を次の世代に進めます。その後、`cur` と`nxt` の役割が入れ替わり、`cur` のスペースを次の世代で再利用します（ダブルバッファリングの単純な応用）。このロジックは`src/life/main.mo` ファイルに記述されています：

    import Random = "Random";
    import State = "State";
    import Grid = "Grid";
    
    actor Life {
    
      let state = do {
        let rand = Random.new();
        State.new(64, func (i, j) { rand.next() % 2 == 1 });
      };
    
      var cur = Grid.Grid(state);
    
      var nxt = Grid.Grid(State.new(cur.size(), func (i, j) { false; }));
    
      public func next() : async Text {
        cur.next(nxt);
        let temp = cur;
        cur := nxt;
        nxt := temp;
        cur.toText();
      };
    
      public query func current() : async Text {
        cur.toText()
      };
    
    };

このactor の変数はどれも安定変数として宣言されていませんので、アップグレードしてもその値は保持されず、新規インストール時と同じように再初期化されることに注意してください。

### v1 へのアップグレード

v1の実装にアップグレードするには、以下のコマンドを実行してください：

    mv src versions/v0
    mv versions/v1 src
    dfx deploy

その後、同じブラウザのタブに戻り、更新します (またはリンクを再読み込みします)。
「**実行」**ボタンをクリックし、飽きたら「**一時停止」**ボタンをクリックします。**Details\]**を開き、\[**View State**\]をクリックします。表示された\#v1のステートを見てください。

![Game of life v1](./_attachments/game-of-life2.png)

v0からアップグレードすると、v0をデプロイしたときと同じように、ステートがランダムになります。これは、v0のコードがステート変数の安定を宣言していなかったためで、アップグレードされたactor 、リタイアしたactor に以前のステートの値がないため、ステートを再初期化せざるを得なくなります。

しかし、v1プロジェクトを2回目に再デプロイした場合、おそらくちょっとした編集を行った後であれば、デプロイ前のグリッドの最後の状態が、デプロイをまたいで保存され、ステート保存アップグレードが行われます。ステートのランダムイニシャライザーはスキップされ、ステートにはアップグレード前の値が設定されます。

### v2 へのアップグレード

v2 実装にアップグレードするには、以下のコマンドを実行します：

    mv src versions/v1
    mv versions/v2 src
    dfx deploy

同じブラウザのタブに戻ります。タブを更新します。グリッドのテキスト表示の表示文字を変更する以外は、現在のグリッドのステートは変更されない（つまり保持される）ことに注意してください。

**Run** ボタンをクリックし、飽きたら**Pause**ボタンをクリックします。**Details\]**を開き、\[**View State**\]をクリックします。表示された\#v2のステートを見てください。

![Game of life v2](./_attachments/game-of-life3.png)

<!---
# Game of Life

## Overview

This example contains a series of implementations of Conway's Game of Life.

Its main purpose is to demonstrate state-preserving upgrades using Motoko's stable variables. The implementations are meant to be instructive and are not optimized for efficiency or to hide network latency, which a production implementation would need to consider.

Our `src` directory contains the initial, version v0, implementation but its contents will later be replaced with contents from directories `versions/v1` and `versions/v2`. In a real project, with proper source control, there might be a single src directory, with different versions of code residing in different branches of the repository.

Directory src (version v0) contains an application with a very simple React UI.

Directories `versions/v1` and `versions/v2` contain sequential upgrades to src used to illustrate live upgrade by re-deployment of a deployed canister.

To make upgrades apparent, each version uses a different digit to display live cells (0,1,2).

This is a Motoko example that does not currently have a Rust variant. 


## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Install [Node.js](https://nodejs.org/en/download/).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd examples/motoko/life
dfx start --background
```

### Step 2: Deploy the canister:

```
dfx deploy
```

The deployment step should report a canister id for the life_assets canister.

Take note of the URL at which the life_assets is accessible using the command:

```
echo "http://127.0.0.1:4943/?canisterId=$(dfx canister id life_assets)"
```

### Step 3: Open the frontend in your browser by clicking on the link returned in the output of the previous command.

You should see a frontend like this: 

![Game of Life](./_attachments/game-of-life.png)

Click the button **Step**. The grid will advance to the next generation of the Game of Life.

Click the button **Run**. The grid will (slowly) animate sequential generations.

Click the button **Pause**. The animation will pause.

## Upgrading to other versions

Because the v0 implementation makes no provision for upgrades, every time you re-deploy the v0 implementation, the grid will be re-initialized to the same(pseudo-random) initial state.

The state of the grid is represented naively as a nested, mutable array of Boolean values, as described in the `src/State.mo` file:

```
module {

  public type Cell = Bool;
  public type State = [[var Cell]];
  ...
}
```

A `src/life/grid` is represented as a simple class constructed from, and maintaining, state. We omit the details here.

The main actor in `src/life/main` creates a random state and maintains two grid objects, the current and next grid (`cur` and `nxt`). Life's `next()` method advances the Game of Life to the next generation by updating `nxt` from `cur`, using Grid method call `cur.next(nxt)`. The roles of `cur` and `nxt` are then swapped to re-use `cur`'s space for the next generation (a simple application of double-buffering). This logic is described in the `src/life/main.mo` file:

```
import Random = "Random";
import State = "State";
import Grid = "Grid";

actor Life {

  let state = do {
    let rand = Random.new();
    State.new(64, func (i, j) { rand.next() % 2 == 1 });
  };

  var cur = Grid.Grid(state);

  var nxt = Grid.Grid(State.new(cur.size(), func (i, j) { false; }));

  public func next() : async Text {
    cur.next(nxt);
    let temp = cur;
    cur := nxt;
    nxt := temp;
    cur.toText();
  };

  public query func current() : async Text {
    cur.toText()
  };

};
```

Note that none of the variables in this actor are declared stable so their values will not be preserved across upgrade, but re-initialized as on a fresh installation.


### Upgrading to v1
To upgrade to the v1 implementation, issue these commands:

```
mv src versions/v0
mv versions/v1 src
dfx deploy
```

Then, return to the same browser tab and refresh (or re-load the link). Note the current grid state is unchanged (thus preserved), apart from changing display character in grid.
Click button **Run**, then click button **Pause** when bored. Open **Details** and click **View State**. Admire the #v1 state on display.

![Game of life v1](./_attachments/game-of-life2.png)

After first upgrading from v0 the state will be random, as on deploying v0. This is because the v0 code did not declare its state variable stable, forcing the upgraded actor to re-initialize state as no previous value for state is available in the retired actor.

However, if you re-deploy the v1 project a second time, perhaps after making a minor edit, you'll see the last state of the grid, before deployment, preserved across the deployment, in a state-preserving upgrade. The random initializer for state is skipped and state just assumes the value it had before the upgrade.


### Upgrading to v2:
To upgrade to the v2 implementation, issue these commands:

```
mv src versions/v1
mv versions/v2 src
dfx deploy
```

Return to the same browser tab. Refresh the tab. Note the current grid state is unchanged (thus preserved), apart from changing the display character in the textual display of the grid.

Click button **Run**, then click button **Pause** when bored. Open **Details** and click **View State**. Admire the #v2 state on display.

![Game of life v2](./_attachments/game-of-life3.png)
-->
