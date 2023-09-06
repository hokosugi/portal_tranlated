# ウェブゲームのホストInternet Computer

## 概要

このサンプルプロジェクトでは、Unity と Godot ゲームエンジンを使って [Internet Computer](https://internetcomputer.org/)にデプロイする方法を示します。

UnityやGodotのゲームエンジンを使ってWebゲームを作ってみましょう。

## IC GameKitを使ったWebゲームの自動作成

## 前提条件

- \[x\][IC SDKが](/developer-docs/setup/install/index.mdx)インストールされていることを確認してください。
- \[[git を](https://git-scm.com/downloads)ダウンロードしてインストールしてください。

### オプション1：Unity

### 前提条件

- \[x\][Unityが](https://unity.com/download)インストールされていることを確認してください。

- #### ステップ1：**Universal Render Pipeline**テンプレートを選択して新しいUnityプロジェクトを作成します。

- #### ステップ2：**IC GameKit**パッケージをインストールします：
  
  - 以下のURLを入力し、Unity Package Managerの**Add package from git URL**...メニューからパッケージをインストールできます：
    `https://github.com/dfinity/ic-gamekit.git?path=/unity/com.ic.gamekit`

- #### ステップ 3:`IC Connector` を有効にします：
  
  - IC Gamekitパッケージのインストールが完了したら、`Project Settings -> IC Settings` でInternet Computer の設定を行います。今のところ、2つの基本的なオプションしかサポートしていません：
    - Canister name: 生成された IC プロジェクトの ファイルにあるアセット名 を設定します。`dfx.json` canister 
    - ICコネクタを有効にする：ICコネクタのオン/オフを設定できます。これをチェックすると、Godot HTML5 エクスポートパスの下に`ic-project` というフォルダが生成されます。現在は、プロジェクトのエクスポートのみがサポートされています。

- #### ステップ 4:**ビルド設定**ウィンドウで**WebGL**ビルドターゲットに切り替えます。

- #### ステップ**5：Player Settings**ウィンドウの**WebGL**タブで**Settings を**選択します。
  
  - バージョン0.12.0より前のdfx SDKを使用している場合は、**圧縮形式を** **無効に**設定します。\
    詳細については、[こちらのドキュメントを](https://github.com/dfinity/ic-gamekit/tree/main)参照してください。
  - 生成されるファイル数を減らすために、**Minimal**WebGLテンプレートを選択します。

- #### ステップ6：**Build Settings**ウィンドウでWebGLにビルドします。
  
  前のステップで**IC Connector**を有効にすると、WebGL ビルド出力フォルダの下に`ic-project` というフォルダが生成されます。

### オプション2：Godot

### 前提条件

- \[[Godot](https://godotengine.org/download)がインストールされていることを確認してください。

- #### ステップ 1: 新しい Godot プロジェクトを作成します。

- #### ステップ 2: IC GameKit プラグインをインストールします：
  
  - Unity Package Manager の**Add package from git URL**... メニューから以下の URL を入力し、パッケージをインストールします：
    `https://github.com/dfinity/ic-gamekit.git?path=/godot/com.ic.gamekit`

- #### ステップ 3:`IC Connector` を有効にします：
  
  - IC Gamekitパッケージのインストールが完了したら、`Project Settings -> IC Settings` でInternet Computer の設定を行います。今のところ、2つの基本的なオプションしかサポートしていません：
    - Canister name: 生成された IC プロジェクトの ファイルにあるアセット名 を設定します。`dfx.json` canister 
    - ICコネクタを有効にする：ICコネクタのオン/オフを設定できます。これをチェックすると、Godot HTML5 エクスポートパスの下に`ic-project` というフォルダが生成されます。現在はプロジェクトのエクスポートのみがサポートされています。

- #### ステップ 4:`Export` ウィンドウで`HTML5` プリセットに切り替えます。

- #### ステップ 5：`Export Project` ボタンでプロジェクトをエクスポートします。
  
  前のステップで`IC Connector` を有効にすると、HTML5 エクスポートフォルダの下に`ic-project` というフォルダが生成されます。

## ウェブゲームを手動で作成

:::info
手動でウェブゲームプロジェクトを生成するのは面倒でエラーが発生しやすいと思われるかもしれませんが、[IC GameKitを使って](https://github.com/dfinity/ic-gamekit)自動で行うことを強くお勧めします。このツールはまだ開発中です。フィードバックがあればお知らせください。
::：

このサンプルにはバックエンドがないので、`dfx new project_name` コマンドを使ってテンプレートを設定するメリットはありません。必要なのは`dfx.json` ファイルだけです。

### オプション 1: Unity

- #### ステップ 1:`unity-webgl-sample` という名前のフォルダを作成します。
- #### ステップ 2:`unity-webgl-sample` フォルダーの下に`dfx.json` という名前のファイルを作成します。
- #### ステップ 3:dapp プロジェクトのサンプルをインストールします：

<!-- end list -->

    git clone https://github.com/dfinity/examples
    cd examples/hosting/unity-webgl-template

- #### ステップ 4: このコマンドを実行してローカルネットワークを開始します：

<!-- end list -->

    dfx start --background

- #### ステップ 5: ローカルネットワークが起動したら、このコマンドを実行してcanisters をデプロイします：

<!-- end list -->

    dfx deploy

:::caution
ICメインネットにデプロイした後にエラーコード500が表示された場合は、次のようにURLにrawキーワードを使用してみてください: https://\<canister-id\>.raw.ic0.app.
::：

- #### ステップ 6: デプロイされると、ターミナルから次のような応答が出力されます：

<!-- end list -->

    Committing batch.
    Deployed canisters.
    URLs:
      Frontend canister via browser
        unity_webgl_template_assets: http://127.0.0.1:4943/?canisterId=c5kvi-uuaaa-aaaaa-qaaia-cai

ウェブブラウザでフロントエンドcanister URLに移動します。デフォルトのゲームはこのようになります：

![Unity Game Example](./_attachments/unity-game-example.png)

### オプション 2: Godot

- #### ステップ 1:dapp プロジェクトのサンプルをダウンロードします：

<!-- end list -->

    git clone https://github.com/dfinity/examples
    cd examples/hosting/godot-html5-template

- #### ステップ 2: このコマンドを実行してローカルネットワークを開始します：

<!-- end list -->

    dfx start --background

- #### ステップ 3: ローカルネットワークが起動したら、このコマンドを実行してcanisters をデプロイします：

<!-- end list -->

    dfx deploy

:::caution
ICメインネットにデプロイした後にエラーコード500が表示された場合は、次のようにURLにrawキーワードを使用してみてください: https://\<canister-id\>.raw.ic0.app.
::：

- #### ステップ 4: デプロイされると、ターミナルから次のような応答が出力されます：

<!-- end list -->

    Committing batch.
    Deployed canisters.
    URLs:
      Frontend canister via browser
        godot_html5_assets: http://127.0.0.1:4943/?canisterId=ctiya-peaaa-aaaaa-qaaja-cai

ウェブブラウザでフロントエンドcanister URLに移動し、ゲームを表示します：

![Godot Game Example](./_attachments/godot-game-example.png)

<!---
# Hosting a web game on the Internet Computer

## Overview 
This sample project demonstrates how to deploy a web game on the [Internet Computer](https://internetcomputer.org/) through [Unity](https://unity.com/) and [Godot](https://godotengine.org/) game engines.

Let’s create a web game by using Unity or Godot game engine.

## Creating a web game automatically using IC GameKit

## Prerequisites
- [x] Before you begin, make sure you have the [IC SDK](/developer-docs/setup/install/index.mdx) installed.
- [x] Download and install [git.](https://git-scm.com/downloads)

### Option 1: Unity

### Prerequisites:
- [x] Make sure you have [Unity](https://unity.com/download) installed.

- #### Step 1: Create a new Unity Project with **Universal Render Pipeline** template selected, or you can use you own game as well.
- #### Step 2: Install the **IC GameKit** package:
  - You can install the package from Unity Package Manager via using the **Add package from git URL...** menu, by inputting the following URL:
    `https://github.com/dfinity/ic-gamekit.git?path=/unity/com.ic.gamekit`
- #### Step 3: To enable `IC Connector`:
    - After installing the IC Gamekit package successfully, you can go to `Project Settings -> IC Settings` to config Internet Computer settings. We only support two basic options for now:
        - Canister name: this configs the asset canister name in the `dfx.json` file for the generated IC project.
        - Enable IC connector: you can turn on/off the IC Connector. With this checked, a folder named `ic-project` will be generated under the Godot HTML5 export path. Now only Export Project is supported.
- #### Step 4: Switch to **WebGL** build target in the **Build Settings** window.
- #### Step 5: Under the **Settings for WebGL** tab in the **Player Settings** window.
  - Set **Compression Format** to **Disabled** if you are using dfx SDK with version before 0.12.0.  
    You can check [this document](https://github.com/dfinity/ic-gamekit/tree/main) for more information.
  - Choose the **Minimal** WebGL template to reduce the number of files generated.
- #### Step 6: Build to WebGL in the **Build Settings** window.  
  With **IC Connector** enabled in the previous step, a folder named `ic-project` will be generated under the WebGL build output folder.

### Option 2: Godot

### Prerequisites:
- [x] Make sure you have [Godot](https://godotengine.org/download) installed.

- #### Step 1: Create a new Godot project.
- #### Step 2: Install the IC GameKit plugin:
  - You can install the package from Unity Package Manager via using the **Add package from git URL...** menu, by inputting the following URL:
    `https://github.com/dfinity/ic-gamekit.git?path=/godot/com.ic.gamekit`
- #### Step 3: To enable `IC Connector`:
    - After installing the IC Gamekit package successfully, you can go to `Project Settings -> IC Settings` to config Internet Computer settings. We only support two basic options for now:
        - Canister name: this configs the asset canister name in the `dfx.json` file for the generated IC project.
        - Enable IC connector: you can turn on/off the IC Connector. With this checked, a folder named `ic-project` will be generated under the Godot HTML5 export path. Now only Export Project is supported.
- #### Step 4: Switch to `HTML5` preset in the `Export` window
- #### Step 5: Export the project by `Export Project` button.  
  With `IC Connector` enabled in the previous step, a folder named `ic-project` will be generated under the HTML5 export folder.

## Creating a web game manually 

:::info
You may find generating the web game project manually is tedious and error-prone, we highly recommend you to use [IC GameKit](https://github.com/dfinity/ic-gamekit) to do this automatically. The tool is still in development, let us know if you have any feedback.
:::

Since there is no backend in this sample, there is not any benefit of using the `dfx new project_name` command to set up a template. The `dfx.json` file is all that is needed.

### Option 1: Unity
- #### Step 1: Create a folder named `unity-webgl-sample`.
- #### Step 2: Create a file named `dfx.json` under the `unity-webgl-sample` folder.
- #### Step 3: Install the example dapp project:

```
git clone https://github.com/dfinity/examples
cd examples/hosting/unity-webgl-template
```

- #### Step 4: Start the local network by running this command:

```
dfx start --background
```

- #### Step 5: When the local network is up and running, run this command to deploy the canisters:

```
dfx deploy
```

:::caution
If you get error code 500 after deploying to the IC mainnet, try to use raw keyword in the URL like this: https://\<canister-id\>.raw.ic0.app.
:::

- #### Step 6: Once deployed, the terminal will output a response similar to:

```
Committing batch.
Deployed canisters.
URLs:
  Frontend canister via browser
    unity_webgl_template_assets: http://127.0.0.1:4943/?canisterId=c5kvi-uuaaa-aaaaa-qaaia-cai
```

Navigate to the frontend canister URL in a web browser. The default game will look like this:

![Unity Game Example](./_attachments/unity-game-example.png)

### Option 2: Godot
- #### Step 1: Download the example dapp project:

```
git clone https://github.com/dfinity/examples
cd examples/hosting/godot-html5-template
```

- #### Step 2: Start the local network by running this command:

```
dfx start --background
```

- #### Step 3: When the local network is up and running, run this command to deploy the canisters:

```
dfx deploy
```

:::caution
If you get error code 500 after deploying to the IC mainnet, try to use raw keyword in the URL like this: https://\<canister-id\>.raw.ic0.app.
:::

- #### Step 4: Once deployed, the terminal will output a response similar to:

```
Committing batch.
Deployed canisters.
URLs:
  Frontend canister via browser
    godot_html5_assets: http://127.0.0.1:4943/?canisterId=ctiya-peaaa-aaaaa-qaaja-cai
```

Navigate to the frontend canister URL in a web browser to view your game:

![Godot Game Example](./_attachments/godot-game-example.png)

-->
