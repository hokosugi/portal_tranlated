---

title: Local deployment
---
# 現地展開

## 概要

この**クイック・スタート**・シナリオでは、IC SDK を初めてインストールし、canister をInternet Computer ブロックチェーンにデプロイするのではなく、**ローカルcanister 実行**環境で実行することを想定しています。

手始めに、`greet` と呼ばれる関数を1つだけ持つ単純な 'Hello, world\!'canister をビルドしてデプロイしてみましょう。`greet` 関数は1つのテキスト引数を受け取り、コマンドラインを使ってcanister を実行した場合はターミナルに、ブラウザでcanister にアクセスした場合は HTML ページに、「**Hello, everyone\!**

## 前提条件

このリリースのIC SDKをダウンロードしてインストールする前に、以下を確認してください：

- \[x\] インターネットに接続し、ローカルコンピュータのシェル端末にアクセスできること。

- \[x\] フロントエンド開発用のデフォルトのテンプレートファイルをプロジェクトに含めたい場合は、`node.js` がインストールされていること。

## ダウンロードとインストール

IC SDKの最新バージョンは、ローカルコンピュータのターミナルシェルから直接ダウンロードできます。

:::caution
この手順は**macOS**または**Linux**マシン用です。Windowsの手順については、[こちらを](/docs/developer-docs/setup/install/index.mdx)ご覧ください。
::：

ダウンロードとインストール

- #### ステップ1：ローカルコンピュータでターミナルシェルを開きます。
  
  例えば、アプリケーション \> ユーティリティを開き、**ターミナルを**ダブルクリックするか、<span class="keycombo">⌘+スペースバー</span> を押して検索を開き、`terminal` と入力します。

- #### ステップ2：以下のコマンドを実行してIC SDKパッケージをダウンロードし、インストールします：
  
  ```
    sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
  ```
  
  このコマンドは、DFINITY 実行コマンドラインインターフェイス（CLI）とその依存関係をローカルコンピュータにインストールする前に、ライセンス契約を読み、同意するよう促します。

- #### ステップ 3:`y` と入力し、Return を押してインストールを続行します。
  
  このコマンドは、ローカルコンピュータにインストールされているコンポーネントに関する情報を表示します。

## IC SDKが使用可能であることを確認します。

インストール・スクリプトがエラーなしで実行された場合、IC上で動作するプログラムの開発を開始するために必要なすべてのものがローカル・コンピュータで利用可能になります。

IC SDKが使用可能であることを確認するには、以下の手順に従います：

- #### ステップ1：まだ開いていない場合は、ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ2：次のコマンドを実行して、DFINITY 実行コマンドラインインターフェイス（CLI）がインストールされており、`dfx` 実行ファイルが PATH にあることを確認します：
  
  ```
    dfx --version
  ```
  
  このコマンドは、`dfx` コマンドライン実行ファイルのバージョン情報を表示します。最新バージョンは[リリースノートで](https://github.com/dfinity/sdk/releases)確認できます。

- #### ステップ3: 次のコマンドを実行して、他の`dfx` コマンドラインサブコマンドの使用情報をプレビューします：
  
  ```
    dfx --help
  ```
  
  このコマンドは、`dfx` 親コマンドとそのサブコマンドの使用情報を表示します。

## 新しいプロジェクトを作成します。

Dapps Internet Computer 新しいプロジェクトを作成します。 親コマンドとそのサブコマンドを使用してプロジェクトを作成します。`dfx` 

このガイドでは、デフォルト・サンプルのdapp から開始し、プロジェクト内のスターター・ファイルを使用してdapp を作成する方法を説明します。新しいプロジェクトを作成すると、`dfx` コマンドラインインターフェイスはデフォルトのプロジェクトディレクトリ構造をワークスペースに追加します。プロジェクト・ディレクトリを構成するテンプレート・ファイルについては、「[デフォルト・プロジェクトの探索](/developer-docs/backend/motoko/explore-templates.md)」で説明します。

最初のアプリケーション用に新しいプロジェクトを作成するには、以下の手順に従います：

- #### ステップ 1: まだ開いていなければ、ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ 2: 以下のコマンドを実行して、`hello` という名前の新しいプロジェクトを作成します：
  
  ```
    dfx new hello
  ```
  
  `dfx new hello` コマンドを実行すると、`hello` プロジェクトディレクトリ、テンプレートファイル、`hello` Git リポジトリが作成されます。
  
  `hello` の代わりに別のプロジェクト名を使う場合は、その名前をメモしておいてください。この手順では、`hello` の代わりにそのプロジェクト名を使う必要があります。

- #### ステップ 3: 次のコマンドを実行して、プロジェクト ディレクトリに変更します：
  
  ```
    cd hello
  ```

## ローカル デプロイの開始

最初のプロジェクトをビルドする前に、ローカルのcanister 実行環境に接続する必要があります。ベストプラクティスとして、このステップでは**ターミナルシェルを 2 つ開いて**おく必要があります。1 つのターミナルでcanister の実行操作を開始して確認し、もう 1 つのターミナルでプロジェクトを管理できます。

ローカルのcanister 実行環境を準備するには

- #### ステップ 1: ローカルコンピューターで、新しい 2 番目のターミナルウィンドウまたはタブを開きます。

- #### ステップ 2: 必要であれば、プロジェクトのルート・ディレクトリに移動します。
  
  これで、**2つのターミナルが**開き、両方のターミナルで**プロジェクト・ディレクトリーが** **カレント作業ディレクトリーになって**いるはずです。

- #### ステップ3: 2つ目のターミナルで以下のコマンドを実行して、コンピュータのローカルcanister 実行環境を起動します：
  
  ```
    dfx start
  ```
  
  プラットフォームとローカルのセキュリティ設定によっては、警告が表示されるかもしれません。受信ネットワーク接続の許可または拒否を求めるプロンプトが表示されたら、「**許可**」をクリックします。

- #### ステップ 4:canister の実行操作を表示するターミナルウィンドウを開いたままにして、フォーカスを新しいプロ ジェクトを作成した最初のターミナルウィンドウに切り替えます。
  
  残りの手順は、canister 実行操作が表示されていないターミナルで実行してください。

## アプリケーションの登録、ビルド、デプロイ

ローカルのcanister 実行環境に接続したら、dapp をローカルに登録、ビルド、デプロイできます。

最初のdapp をローカルにデプロイするには：

- #### ステップ 1: 必要であれば、プロジェクトのルート・ディレクトリにいることを確認します。

- #### ステップ 2: 必要であれば、以下のコマンドを実行して、`node` モジュールがプロジェクト・ディレクトリで利用可能であることを確認します：
  
  ```
    npm install
  ```
  
  このステップの詳細については、[プロジェクトでノードが利用可能であることを確認するを](/developer-docs/frontend/index.md#troubleshoot-node)参照してください。

- #### ステップ 3: 以下のコマンドを実行して、最初のdapp を登録、ビルド、デプロイします：
  
  ```
    dfx deploy
  ```
  
  `dfx deploy` コマンドの出力には、実行した操作に関する情報が表示されます。例えば、このステップでは2つの識別子を登録します。1つは`hello_backend` メイン・プログラム用で、もう1つは`hello_frontend` フロントエンド・ユーザ・インターフェイス用です：
  
  ```
    Creating a wallet canister on the local network.
    The wallet canister on the "local" network for user "mainnet" is "rwlgt-iiaaa-aaaaa-aaaaa-cai"
    Deploying all canisters.
    Creating canisters...
    Creating canister hello_backend...
    hello_backend canister created with canister id: rrkah-fqaaa-aaaaa-aaaaq-cai
    Creating canister hello_frontend...
    hello_frontend canister created with canister id: ryjl3-tyaaa-aaaaa-aaaba-cai
    Building canisters...
    Building frontend...
    Installing canisters...
    Creating UI canister on the local network.
    The UI canister on the "local" network is "r7inp-6aaaa-aaaaa-aaabq-cai"
    Installing code for canister hello_backend, with canister ID rrkah-fqaaa-aaaaa-aaaaq-cai
    Installing code for canister hello_frontend, with canister ID ryjl3-tyaaa-aaaaa-aaaba-cai
    Uploading assets to asset canister...
    Starting batch.
    Staging contents of new and changed assets:
      /sample-asset.txt 1/1 (24 bytes)
      /logo2.svg 1/1 (15139 bytes)
      /index.js.map 1/1 (681670 bytes)
      /index.js.map (gzip) 1/1 (156940 bytes)
      /favicon.ico 1/1 (15406 bytes)
      /main.css 1/1 (537 bytes)
      /main.css (gzip) 1/1 (297 bytes)
      /index.html 1/1 (690 bytes)
      /index.html (gzip) 1/1 (386 bytes)
      /index.js (gzip) 1/1 (152884 bytes)
      /index.js 1/1 (637230 bytes)
    Committing batch.
    Deployed canisters.
    URLs:
      Frontend canister via browser
        hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
      Backend canister via Candid interface:
        hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai
  ```
  
  ただし、別の名前でプロジェクトを作成した場合は、`hello_backend` や`hello_frontend` ではなく、canister の名前がプロジェクト名と一致します。
  
  また、**最初にデプロイ**するとき、`dfx` は`default` ID と`default` ID が管理するローカルのcycle ウォレットを作成します。cycles ウォレットはcanister の特別なタイプで、他の に転送することができます。 [cycles](/concepts/tokens-cycles.md)を他のcanisters に転送できます。
  
  **このサンプルdapp をローカルに展開**するには、デフォルトの開発者 ID やcycles ウォレットの使用、cycles の管理について何も知る必要はありません。これらのトピックについては後で説明します。今は、これらは自動的に作成されることだけ覚えておいてください。

- #### ステップ 4: 以下のコマンドを実行して、`hello_backend` canister と定義済みの`greet` 関数を呼び出します：
  
  ```
    dfx canister call hello_backend greet everyone
  ```
  
  このコマンド例を詳しく見てみましょう：
  
  - `dfx canister call` コマンドでは、canister の名前と呼び出すメソッド（関数）を指定する必要があります。
  
  - `hello_backend` の名前を指定します。 **canister**には呼び出したいメソッドの名前を指定します。
  
  - `greet` は、  で呼び出したい`hello` canister**関数の**名前を指定します。
  
  - `everyone` は、 関数に渡すテキスト・データ型の引数です。`greet` 
  
  ただし、別の名前でプロジェクトを作成した場合、canister の名前はプロジェクト名と同じになるので、`hello_backend` の代わりに使用した名前に合わせてコマンドラインを修正する必要があることを忘れないでください。プロジェクト名に`test` を選択した場合、バックエンドcanister は`test_backend` と呼ばれ、フロントエンドcanister `test_frontend` と呼ばれます。

- #### ステップ 5: コマンドが`greet` 関数の戻り値を表示することを確認します。
  
  例えば
  
  ```
    ("Hello, everyone!")
  ```

## dapp フロントエンドのテスト

dapp がデプロイされたことを確認し、コマンドラインを使ってその動作をテストしたので、ウェブブラウザを使ってフロントエンドにアクセスできることを確認してみましょう。

- #### ステップ1: ブラウザを開きます。

- #### ステップ 2: 前のステップで`dfx deploy` コマンドが出力した URL に移動します。

`Frontend canister via browser` の後の URL を使ってください（この例では`http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai` です）。

このURLに移動すると、基本的なHTMLページが表示され、サンプル画像ファイル、入力フィールド、ボタンが表示されます。例えば

![Sample HTML page](_attachments/frontend-prompt.png)

- #### ステップ 3: 挨拶を入力し、**\[Click Me\]**ボタンをクリックして挨拶を返します。
  
  例
  
  ![Hello](_attachments/frontend-result.png)

## ローカルのcanister 実行環境を停止します。

ブラウザでアプリケーションをテストした後、ローカルのcanister 実行環境を停止して、バックグラウンドで実行し続けないようにすることができます。

ローカルの配置を停止するには

- #### ステップ 1: 開発サーバーが表示されているターミナルで Control-C キーを押して、 開発サーバーのプロセスを中断します。

- #### ステップ 2:canister 実行操作が表示されているターミナルで、Control-C を押してローカルネットワークプロセスを中断します。

- #### ステップ 3: 次のコマンドを実行して、ローカルコンピューター上で実行されているローカルのcanister 実行環境を停止します：
  
  ```
    dfx stop
  ```

## まとめ

この**クイック・スタートでは**、dapps を独自に開発するための基本的なワークフローを紹介するために、いくつかの重要なステップにだけ触れました。Motoko の使用方法や、Internet Computer ブロックチェーン上で実行するためのdapps の開発方法について学ぶのに役立つ、より詳細な例やガイドは、ドキュメント全体を通して見つけることができます。

## リソース

次に行くべき場所をいくつか紹介します：

- サンプルdapps を探索するための[ICの構築](../../samples/overview.md)。

- さまざまなICのコンセプトとサービスについて学ぶには、[コンセプト](../../concepts/index.md)。

- dapp をInternet Computer ブロックチェーンにデプロイするためにcycles に変換したい ICP トークンをお持ちの場合は、[ICP トークンをcycles に変換して](./deploy-mainnet.md#convert-icp)ください。

- [IC](../../references/glossary.md)内で使用されるさまざまな用語の定義を学ぶための[IC用語集](../../references/glossary.md)。

- cycles をお持ちで、Internet Computer ブロックチェーンメインネットにアプリケーションをデプロイする準備ができている場合は、[オンチェーンデプロイメントを](./deploy-mainnet.md)ご利用ください。

- [Candid](/docs/developer-docs/backend/candid/candid-concepts.md)インターフェース記述言語 Candid がどのようにサービスの相互運用性とコンポーザビリティを実現するかを学ぶには、[Candid とは何ですか](/docs/developer-docs/backend/candid/candid-concepts.md)？

<!---


# Local deployment

## Overview

This **quick start** scenario assumes that you are installing the IC SDK for the first time and want to run a canister in a **local canister execution environment** instead of deploying it to the Internet Computer blockchain.

To get started, let’s build and deploy a simple 'Hello, world!' canister that has just one function—called `greet`. The `greet` function accepts one text argument and returns the result with a greeting similar to **Hello, everyone!** in a terminal if you run the canister using the command-line or in an HTML page if you access the canister in a browser.

## Prerequisites

Before you download and install this release of the IC SDK, verify the following:

-   [x] You have an internet connection and access to a shell terminal on your local computer.

-   [x] You have `node.js` installed if you want to include the default template files for frontend development in your project.

## Download and install

You can download the latest version of the IC SDK directly from within a terminal shell on your local computer.

:::caution
These instructions are for **macOS** or **Linux** machines. For Windows instructions, please see [here](/docs/developer-docs/setup/install/index.mdx).
:::

To download and install:

- #### Step 1:  Open a terminal shell on your local computer.

    For example, open Applications > Utilities, then double-click **Terminal** or press <span class="keycombo">⌘+spacebar</span> to open Search, then type `terminal`.

- #### Step 2:  Download and install the IC SDK package by running the following command:

        sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"

    This command prompts you to read and accept the license agreement before installing the DFINITY execution command-line interface (CLI) and its dependencies on your local computer.

- #### Step 3:  Type `y` and press Return to continue with the installation.

    This command displays information about the components being installed on the local computer.

## Verify the IC SDK is ready to use

If the installation script runs without any errors, everything you need to start developing programs that run on the IC will be available on your local computer.

To verify the IC SDK is ready to use:

- #### Step 1:  Open a terminal shell on your local computer, if you don’t already have one open.

- #### Step 2:  Check that you have the DFINITY execution command-line interface (CLI) installed and the `dfx` executable is available in your PATH by running the following command:

        dfx --version

    The command displays version information for the `dfx` command-line executable. You can see the latest version in the [release notes](https://github.com/dfinity/sdk/releases).
    
- #### Step 3:  Preview usage information for the other `dfx` command-line sub-commands by running the following command:

        dfx --help

    The command displays usage information for the `dfx` parent command and its subcommands.

## Create a new project

Dapps on the Internet Computer start as **projects**. You create projects using the `dfx` parent command and its subcommands.

For this guide, we’ll start with the default sample dapp to illustrate creating dapp using the starter files in a project. When you create a new project, the `dfx` command-line interface adds a default project directory structure to your workspace. We cover the template files that make up a project directory in the [explore the default project](/developer-docs/backend/motoko/explore-templates.md) guide.

To create a new project for your first application:

- #### Step 1:  Open a terminal shell on your local computer, if you don’t already have one open.

- #### Step 2:  Create a new project named `hello` by running the following command:

        dfx new hello

    The `dfx new hello` command creates a new `hello` project directory, template files, and a new `hello` Git repository for your project.

    If you use a different project name instead of `hello`, make note of the name you used. You’ll need to use that project name in place of the `hello` project name throughout these instructions.

- #### Step 3:  Change to your project directory by running the following command:

        cd hello

## Start the local deployment

Before you can build your first project, you need to connect to the local canister execution environment. As a best practice, this step requires you to have **two terminal shells** open, so that you can start and see canister execution operations in one terminal and manage your project in another.

To prepare the local canister execution environment:

- #### Step 1:  Open a new second terminal window or tab on your local computer.

- #### Step 2:  Navigate to the root directory for your project, if necessary.

    You should now have **two terminals** open with your **project directory** as your **current working directory** in both terminals.

- #### Step 3:  Start the local canister execution environment on your computer in your second terminal by running the following command:

        dfx start

    Depending on your platform and local security settings, you might see a warning displayed. If you are prompted to allow or deny incoming network connections, click **Allow**.

- #### Step 4:  Leave the terminal window that displays canister execution operations open and switch your focus to the first terminal window where you created your new project.

    You should perform the remaining steps in the terminal that doesn’t display canister execution operations.

## Register, build, and deploy the application

After you connect to the local canister execution environment you can register, build, and deploy your dapp locally.

To deploy your first dapp locally:

- #### Step 1:  Check that you are still in the root directory for your project, if needed.

- #### Step 2:  Ensure that `node` modules are available in your project directory, if needed, by running the following command:

        npm install

    For more information about this step, see [ensuring node is available in a project](/developer-docs/frontend/index.md#troubleshoot-node).

- #### Step 3:  Register, build, and deploy your first dapp by running the following command:

        dfx deploy

    The `dfx deploy` command output displays information about the operations it performs. For example, this step registers two identifiers. One is for the `hello_backend` main program and the other one for the `hello_frontend` frontend user interface and installation information similar to the following:

        Creating a wallet canister on the local network.
        The wallet canister on the "local" network for user "mainnet" is "rwlgt-iiaaa-aaaaa-aaaaa-cai"
        Deploying all canisters.
        Creating canisters...
        Creating canister hello_backend...
        hello_backend canister created with canister id: rrkah-fqaaa-aaaaa-aaaaq-cai
        Creating canister hello_frontend...
        hello_frontend canister created with canister id: ryjl3-tyaaa-aaaaa-aaaba-cai
        Building canisters...
        Building frontend...
        Installing canisters...
        Creating UI canister on the local network.
        The UI canister on the "local" network is "r7inp-6aaaa-aaaaa-aaabq-cai"
        Installing code for canister hello_backend, with canister ID rrkah-fqaaa-aaaaa-aaaaq-cai
        Installing code for canister hello_frontend, with canister ID ryjl3-tyaaa-aaaaa-aaaba-cai
        Uploading assets to asset canister...
        Starting batch.
        Staging contents of new and changed assets:
          /sample-asset.txt 1/1 (24 bytes)
          /logo2.svg 1/1 (15139 bytes)
          /index.js.map 1/1 (681670 bytes)
          /index.js.map (gzip) 1/1 (156940 bytes)
          /favicon.ico 1/1 (15406 bytes)
          /main.css 1/1 (537 bytes)
          /main.css (gzip) 1/1 (297 bytes)
          /index.html 1/1 (690 bytes)
          /index.html (gzip) 1/1 (386 bytes)
          /index.js (gzip) 1/1 (152884 bytes)
          /index.js 1/1 (637230 bytes)
        Committing batch.
        Deployed canisters.
        URLs:
          Frontend canister via browser
            hello_frontend: http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai
          Backend canister via Candid interface:
            hello_backend: http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai&id=rrkah-fqaaa-aaaaa-aaaaq-cai

    If you created a project with a different name, however, your canister names will match your project name instead of `hello_backend` and `hello_frontend`.

    You should also note that the **first time you deploy**, `dfx` creates a `default` identity and a local cycle wallet controlled by your `default` identity. A cycles wallet is a special type of canister that enables you to transfer [cycles](/concepts/tokens-cycles.md) to other canisters.

    **To deploy this sample dapp locally**, you don’t need to know anything about your default developer identity, using a cycles wallet, or managing cycles. We’ll cover these topics later; for now, just note that these are created for you automatically.

- #### Step 4:  Call the `hello_backend` canister and the predefined `greet` function by running the following command:

        dfx canister call hello_backend greet everyone

    Let’s take a closer look at this example command:

    -   The `dfx canister call` command requires you to specify a canister name and a method—or function—to call.

    -   `hello_backend` specifies the name of the **canister** you want to call.

    -   `greet` specifies the name of the **function** you want to call in the `hello` canister.

    -   `everyone` is the text data type argument that you want to pass to the `greet` function.

    Remember, however, that if you created a project with a different name, the canister name will match your project name and you’ll need to modify the command line to match the name you used instead of `hello_backend`. If you were to choose `test` as the projects name, your backend canister would be called `test_backend` and the frontend canister `test_frontend`.

- #### Step 5:  Verify the command displays the return value of the `greet` function.

    For example:

        ("Hello, everyone!")

## Test the dapp frontend

Now that you have verified that your dapp has been deployed and tested its operation using the command line, let’s verify that you can access the frontend using your web browser.

- #### Step 1:  Open a browser.

- #### Step 2:  Navigate to the URL output by the `dfx deploy` command in the previous step. 

Use the URL after `Frontend canister via browser`; in our case that's `http://127.0.0.1:4943/?canisterId=ryjl3-tyaaa-aaaaa-aaaba-cai`.

Navigating to this URL displays a basic HTML page with a sample asset image file, an input field, and a button. For example:

![Sample HTML page](_attachments/frontend-prompt.png)

- #### Step 3:  Type a greeting, then click **Click Me** button to return the greeting.

    For example:

    ![Hello](_attachments/frontend-result.png)

## Stop the local canister execution environment

After testing the application in the browser, you can stop the local canister execution environment so that it doesn’t continue running in the background.

To stop the local deployment:

- #### Step 1:  In the terminal that displays the development server, press Control-C to interrupt the development server process.

- #### Step 2:  In the terminal that displays canister execution operations, press Control-C to interrupt the local network process.

- #### Step 3:  Stop the local canister execution environment running on your local computer by running the following command:

        dfx stop

## Conclusion

This **quick start** touched on only a few key steps to introduce the basic workflow for developing dapps of your own. You can find more detailed examples and guides to help you learn about how to use Motoko and how to develop dapps to run on the Internet Computer blockchain throughout the documentation.

## Resources
Here are some suggestions for where to go next:

-   [Building on the IC](../../samples/overview.md) to explore sample dapps.

-   [Concepts](../../concepts/index.md) to learn about different IC concepts and services. 

-   [Convert ICP tokens to cycles](./deploy-mainnet.md#convert-icp) if you have ICP tokens that you want to convert to cycles to enable you to deploy dapp to the Internet Computer blockchain.

-   [IC glossary](../../references/glossary.md) to learn the definitions of various terms used within the IC. 

-   [On-chain deployment](./deploy-mainnet.md) if you have cycles and are ready to deploy an application to the Internet Computer blockchain mainnet.

-   [What is Candid?](/docs/developer-docs/backend/candid/candid-concepts.md) to learn how the Candid interface description language enables service interoperability and composability.


-->
