# レベル1：宇宙士官候補生

- [1.1:ライブデモの探索](1.1-live-demo.md)自分自身のdapps の開発を始める前に、`dfx deploy --playground` コマンドを通してMotoko プレイグラウンドを利用する、ライブでデプロイされたcanister を探索してみましょう。このモジュールでは、以下を取り上げます：
  
  - Motoko プレイグラウンドの概要。
  - `dfx deploy --playground` コマンドの概要。
  - `dfx` を使用したMotoko プレイグラウンドへのcanister のデプロイ。
  - CLI を介したcanister との対話。
  - Candid インターフェイスを介したcanister との対話。

- 1\.[2:Motoko レベル 1](1.2-motoko-lvl1.md):dapp を独自に開発するために、まずMotoko コードを書く基本をカバーする必要があります。このモジュールでは、以下を取り上げます：
  
  - 基本概念と用語
  - Motoko 構文
  - 基本ライブラリの使用。
  - 宣言と式。
  - actor の定義
  - 値と評価
    - プリミティブ値。
    - 非プリミティブ値
  - 値の印刷
  - テキスト引数の渡し方

- 1\.[3: 最初のdapp](1.3-first-dapp.md) を[開発](1.3-first-dapp.md)する ：
  
  - 新しいプロジェクトの作成
  - プロジェクトのファイル構造の確認。
  - バックエンドcanister コードの記述。
    - actor の作成。
    - `getQuestion` メソッドの定義。
    - クエリーコールとアップデートコールの比較。
    - データを格納するデータ構造の作成。
    - 追加の依存関係のインポート。
    - `votes` 変数の宣言。
    - `getVotes` メソッドの宣言。
    - `votes` メソッドの宣言
    - `resetVotes` メソッドの宣言。
    - 最終コード
  - dapp をローカルにデプロイします。
  - 開発済みのフロントエンドコードを追加します。
  - dapp の再デプロイ。

- [1.4:cycles](1.4-using-cycles.md) の[取得と使用](1.4-using-cycles.md) ：
  
  - cycles の概要。
  - 開発者 ID の作成。
  - cycles クーポンを使用したcycles の取得。
  - ICP トークンのcycles への変換。

- 1\.[5:canisters](1.5-deploying-canisters.md) の[デプロイ](1.5-deploying-canisters.md)：
  
  - メインネットへのデプロイ。

- 1.6:[ canisters の](1.6-managing-canisters.md)[管理](1.6-managing-canisters.md) ：
  
  - canister の ID の取得。
  - canister 情報の取得。
  - canister のコントローラとしての ID の追加 .
  - canister の実行ステートの管理。
  - canister のcycles 残高の確認 .
  - canister の補充。
  - canister からのcycles の取得 .
  - canister の凍結しきい値の設定。
  - canister の削除 .

<!---
# Level 1: Space cadet 

- [1.1: Exploring a live demo](1.1-live-demo.md): Before we begin developing our own dapps, let's explore a live, deployed canister that utilizes the Motoko playground through the `dfx deploy --playground` command. This module covers:
    - Overview of Motoko Playground.
    - An overview of the `dfx deploy --playground` command.
    - Deploying a canister to Motoko Playground using `dfx`.
    - Interacting with the canister via the CLI.
    - Interacting with the canister via the Candid interface.

- [1.2: Motoko level 1](1.2-motoko-lvl1.md): To develop our own dapp, we first need to cover the fundamentals of writing Motoko code. This module covers:
    - Basic concepts and terms.
    - Motoko syntax.
    - Using the base library.
    - Declarations and expressions.
    - Defining an actor.
    - Values and evaluation:
        - Primitive values.
        - Non-primitive values.
    - Printing values.
    - Passing text arguments.

- [1.3: Developing your first dapp](1.3-first-dapp.md): 
    - Creating a new project.
    - Reviewing the project's file structure.
    - Writing the backend canister code.
        - Creating an actor.
        - Defining the `getQuestion` method.
        - Query calls vs. update calls.
        - Creating a data structure to store the data.
        - Importing additional dependencies.
        - Declaring the `votes` variable.
        - Declaring the `getVotes` method.
        - Declaring the `votes` method.
        - Declaring the `resetVotes`  method.
        - Final code.
    - Deploying the dapp locally.
    - Adding pre-developed frontend code.
    - Re-deploying the dapp.

- [1.4: Acquiring and using cycles](1.4-using-cycles.md): 
    - Overview of cycles.
    - Creating a developer identity.
    - Acquiring cycles using a cycles coupon.
    - Converting ICP tokens to cycles.

- [1.5: Deploying canisters](1.5-deploying-canisters.md): 
    - Deploying to the mainnet.

- [1.6: Managing canisters](1.6-managing-canisters.md): 
    - Obtaining a canister's ID.
    - Obtaining canister information.
    - Adding an identity as a controller of a canister.
    - Managing the running state of a canister.
    - Checking the cycles balance of a canister.
    - Topping up a canister.
    - Getting cycles back from a canister.
    - Setting the canister's freezing threshold.
    - Deleting a canister.
-->
