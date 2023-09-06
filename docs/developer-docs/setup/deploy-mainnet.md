---

title: Mainnet deployment
---
# Internet Computer メインネットへのデプロイ

## 概要

このシナリオでは、IC SDKを初めてインストールし、デフォルトのプロジェクトをInternet Computer ブロックチェーンメインネットにデプロイすることを想定しています。

ローカル開発環境でのみプロジェクトをデプロイする場合は、[ローカル開発](./deploy-locally.md)シナリオを参照してください。

はじめに、`greet` という関数を1つだけ持つシンプルな 'Hello, world\!'dapp をビルドしてデプロイしてみましょう。`greet` 関数は1つのテキスト引数を受け取り、コマンドラインを使用してdapp を実行した場合はターミナルに、ブラウザでdapp にアクセスした場合は HTML ページに、「**Hello, everyone\!**

## 前提条件

このリリースのIC SDKをダウンロードしてインストールする前に、以下を確認してください：

- \[x\] インターネットに接続し、ローカルコンピュータのシェル端末にアクセスできること。

- \[x\] デフォルトプロジェクトのデフォルトフロントエンドにアクセスしたい場合は、`node.js` がインストールされていること。

- \[x\] ICPトークンかcycles 。

:::info
このガイドを完成させるには **cycles**が必要です。cycles を取得するには、ICP トークンをcycles に変換するか、他のソース、例えば他の開発者が管理するcanister やサードパーティのcycles プロバイダからcycles を提供されなければなりません。このガイドでは、ICP トークンが利用可能なアカウントを持っていることを前提に、ICP トークンをcycles に変換し、そのcycles を自分が管理する**cycles ウォレットに**転送する方法を説明します。

ICP トークンの入手方法については、[ICP トークンの入手](../setup/cycles/converting_icp_tokens_into_cycles.md)方法を参照してください。

ICP トークンを管理するためのNetwork Nervous System アプリケーションの使用方法については、[Network Nervous System dapp クイック](../../tokenomics/token-holders/nns-app-quickstart)スタートを参照してください。

作成後のデフォルトのcycles ウォレットの使用については、[デフォルトのcycles ウォレットを使用](/developer-docs/setup/cycles/cycles-wallet.md)するを参照してください。
::：

## ダウンロードとインストール

IC SDKの最新バージョンは、ローカルコンピュータのターミナルシェルから直接ダウンロードできます。すでにIC SDKをインストールしている場合は、このセクションをスキップして、[新しいプロジェクトの作成から](#net-new-project)始めることができます。

:::caution
この手順は**macOS**または**Linux**マシン用です。Windows については、[こちらを](/docs/current/developer-docs/setup/install/index)参照してください。
::：

ダウンロードとインストール

- #### ステップ1：ローカルコンピューターでターミナルシェルを開きます。
  
  例えば、アプリケーション \> ユーティリティを開き、**ターミナルを**ダブルクリックするか、<span class="keycombo">⌘+スペースバー</span> を押して検索を開き、`terminal` と入力します。

- #### ステップ2：以下のコマンドを実行してIC SDKパッケージをダウンロードし、インストールします：
  
  ```
    sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
  ```
  
  このコマンドは、DFINITY 実行コマンドラインインターフェイス（CLI）とその依存関係をローカルコンピュータにインストールする前に、ライセンス契約を読み、同意するよう促します。

- #### ステップ 3:`y` と入力し、Return を押してインストールを続行します。
  
  コマンドは、ローカルコンピュータにインストールされているコンポーネントに関する情報を表示します。

## IC SDKが使用できることを確認します。

インストール・スクリプトがエラーなしで実行されると、IC上で動作するプログラムの開発を開始するために必要なすべてがローカル・コンピュータ上で利用可能になります。

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

Dapps **プロジェクトとして** Internet Computer を開始します。 親コマンドとそのサブコマンドを使用してプロジェクトを作成します。`dfx` 

このガイドでは、デフォルト・サンプルのdapp から開始し、プロジェクト内のスターター・ファイルを使用してdapp を作成する方法を説明します。新しいプロジェクトを作成すると、`dfx` コマンドラインインターフェイスはデフォルトのプロジェクトディレクトリ構造をワークスペースに追加します。プロジェクト・ディレクトリを構成するテンプレート・ファイルについては、「[デフォルト・プロジェクトの探索](/docs/developer-docs/backend/motoko/explore-templates.md)」で説明します。

最初のdapp で新しいプロジェクトを作成するには、以下の手順に従います：

- #### ステップ 1: まだ開いていなければ、ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ2：次のコマンドを実行して、`hello` という名前の新しいプロジェクトを作成します：
  
  ```
    dfx new hello
  ```
  
  `dfx new hello` コマンドを実行すると、`hello` プロジェクトディレクトリ、テンプレートファイル、`hello` Git リポジトリが作成されます。
  
  `hello` の代わりに別のプロジェクト名を使う場合は、その名前をメモしておいてください。この手順では、`hello` の代わりにそのプロジェクト名を使う必要があります。

- #### ステップ3：次のコマンドを実行してプロジェクト・ディレクトリに移動します：
  
  ```
    cd hello
  ```

## ICメインネットへの接続を確認します。

Internet Computer ブロックチェーンメインネットにアクセスするために使用できる予約済みネットワークエイリアスがあります。ネットワークエイリアスは内部的に定義されたシステム設定なので、デフォルトではプロジェクトで設定する必要はありません。

ICへの接続を確認するには

- #### ステップ1：必要に応じて、プロジェクトのルート・ディレクトリにいることを確認します。

- #### ステップ2: ネットワーク・エイリアス`ic` に対して以下のコマンドを実行して、ICの現在のステータスとICに接続できることを確認します：
  
  ```
    dfx ping ic
  ```

- #### ステップ3：`dfx ping ic` コマンドがICに関する情報を返すことを確認します。
  
  たとえば、次のような出力が表示されるはずです：
  
  ```
    {
      "ic_api_version": "0.18.0"  "impl_hash": "d639545e0f38e075ad240fd4ec45d4eeeb11e1f67a52cdd449cd664d825e7fec"  "impl_version": "8dc1a28b4fb9605558c03121811c9af9701a6142"  "replica_health_status": "healthy"  "root_key": [48, 129, 130, 48, 29, 6, 13, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 1, 2, 1, 6, 12, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 2, 1, 3, 97, 0, 129, 76, 14, 110, 199, 31, 171, 88, 59, 8, 189, 129, 55, 60, 37, 92, 60, 55, 27, 46, 132, 134, 60, 152, 164, 241, 224, 139, 116, 35, 93, 20, 251, 93, 156, 12, 213, 70, 217, 104, 95, 145, 58, 12, 11, 44, 197, 52, 21, 131, 191, 75, 67, 146, 228, 103, 219, 150, 214, 91, 155, 180, 203, 113, 113, 18, 248, 71, 46, 13, 90, 77, 20, 80, 95, 253, 116, 132, 176, 18, 145, 9, 28, 95, 135, 185, 136, 131, 70, 63, 152, 9, 26, 11, 170, 174]
    }
  ```

## 開発者 ID と元帳アカウントの確認

すべての ICP トークンの取引は、Internet Computer ブロックチェーン上で動作する[台帳canister](../../references/glossary.md#ledger)に記録されます。台帳canister は、すべての ICP トークン保有者の**口座識別子と** **残高で**構成されています。

自分の台帳アカウントに保有する ICP トークンを送金する前に、台帳に自分の身元を確認し、取引を完了するための開発者 ID を承認する、安全で適切に署名されたメッセージを送信する必要があります。

ICP トークンの保管をどのように設定しているかによって、台帳に接続してトランザクションを完了するために必要なハードウェア、ソフトウェア、手順は異なります。たとえば、ハードウェア ウォレットから、ハードウェア セキュリティ モジュール（HSM）アプライアンスを使用して、Network Nervous System （NNS）フロントエンド アプリケーションを介して、または IC SDK`dfx` コマンドライン インターフェイスを使用して、台帳に接続してトランザクションを開始することができます。各アプローチは、台帳に署名してメッセージを送信し、アカウント保有者としてのアイデンティティを表現するための異なるインターフェイスを提示します。

### 開発者 ID について

IC SDK を初めて使用するとき、`dfx` コマンドライン ツールにより、`default` 開発者 ID が作成されます。この ID は、**プリンシパルデータ**型と、プリンシパルのテキスト表現（しばしば**プリンシパル識別子と**呼ばれます）で表されます。この ID の表現は、ビットコインやイーサリアムのアドレスに似ています。

しかし、開発者 ID に関連付けられたプリンシパルは、通常、元帳の**口座識別子とは**異なります。プリンシパル識別子とアカウント識別子は関連しており、どちらもあなたの ID をテキストで表しますが、フォーマットは異なります。

### 台帳に接続してアカウント情報を取得

このガイドでは、元帳に接続するハードウェアウォレットや外部アプリケーションがない場合、開発者 ID を使用して元帳のアカウント ID を取得し、元帳のアカウント ID から開発者 ID が管理するcycles ウォレットcanister に ICP トークンを転送します。

自分のアカウントを台帳で調べるには：

- #### ステップ 1：次のコマンドを実行して、現在使用している開発者 ID を確認します：
  
  ```
    dfx identity whoami
  ```
  
  ほとんどの場合、現在デフォルトの開発者 ID を使用していることがわかります。たとえば、以下のようになります：
  
  ```
    default
  ```

- #### ステップ 2: 以下のコマンドを実行して、現在の ID のプリンシパルのテキスト表示を表示します：
  
  ```
    dfx identity get-principal
  ```
  
  このコマンドは、以下のような出力を表示します：
  
  ```
    tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe
  ```

- #### ステップ 3: 以下のコマンドを実行して、開発者 ID のアカウント識別子を取得します：
  
  ```
    dfx ledger account-id
  ```
  
  このコマンドは、開発者 ID に関連付けられた元帳のアカウント識別子を表示します。たとえば、次のような出力が表示されます：
  
  ```
    03e3d86f29a069c6f2c5c48e01bc084e4ea18ad02b0eec8fccadf4487183c223
  ```

- #### ステップ 4: 以下のコマンドを実行して、アカウントの残高を確認します：
  
  ```
    dfx ledger --network ic balance
  ```
  
  このコマンドは、元帳アカウントの ICP トークンの残高を表示します。たとえば、次のような出力が表示されます：
  
  ```
    10.00000000 ICP
  ```

## cycles ウォレットの作成

アカウント情報と現在の ICP トークン残高が確認できたので、ICP トークンの一部をcycles に変換してcycles ウォレットに移動できます。

ICP トークンをcycles ウォレットに移すには：

- #### ステップ 1: 以下のようなコマンドを実行して元帳アカウントから ICP トークンを移し、cycles で新しいcanister を作成します：
  
  ```
    dfx ledger --network ic create-canister <principal-identifier> --amount <icp-tokens>
  ```
  
  このコマンドは、`--amount` 引数に指定した ICP トークンの数をcycles に変換し、cycles を指定したプリンシパルによって制御される新しいcanister 識別子に関連付けます。
  
  たとえば、以下のコマンドは .25 ICP トークンをcycles に変換し、新しいcanister のコントローラとしてデフォルト ID のプリンシパル識別子を指定します：
  
  ```
    dfx ledger --network ic create-canister tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe --amount .25
  ```
  
  トランザクションが成功すると、台帳にイベントが記録され、次のような出力が表示されます：
  
  ```
    Transfer sent at BlockHeight: 20
    Canister created with id: "gastn-uqaaa-aaaae-aaafq-cai"
  ```

- #### ステップ 2: 以下のようなコマンドを実行して、新しく作成したcanister プレースホルダにcycles ウォレットコードをインストールします：
  
  ```
    dfx identity --network ic deploy-wallet <canister-identifer>
  ```
  
  たとえば、次のようなコマンドを実行します：
  
  ```
    dfx identity --network ic deploy-wallet gastn-uqaaa-aaaae-aaafq-cai
  ```
  
  このコマンドは以下のような出力を表示します：
  
  ```
    Creating a wallet canister on the IC network.
    The wallet canister on the "ic" network for user "default" is "gastn-uqaaa-aaaae-aaafq-cai"
  ```

## cycles ウォレットの検証

ICP トークンをcycles に変換した後、cycles ウォレットcanister を検証し、現在のcycles 残高を確認できます。

cycles ウォレットを検証するには：

- #### ステップ 1: 次のコマンドを実行して、デプロイしたcycles ウォレットのcanister 識別子を確認します：
  
  ```
    dfx identity --network ic get-wallet
  ```
  
  このコマンドは、以下のような出力でcycles ウォレットのcanister 識別子を表示します：
  
  ```
    gastn-uqaaa-aaaae-aaafq-cai
  ```

- #### ステップ 2: 以下のようなコマンドを実行して、cycles ウォレットcanister が適切に設定され、cycles の残高を保持していることを確認します：
  
  ```
    dfx wallet --network ic balance
  ```
  
  このコマンドはcycles ウォレットの残高を返します。コマンドは  ウォレットの残高を返します：
  
  ```
    15430.122 TC (trillion cycles).
  ```
  
  以下のような URL を使って、ウェブブラウザでデフォルトのcycles ウォレットにアクセスすることもできます：
  
  ```
    https://<WALLET-CANISTER-ID>.icp0.io
  ```
  
  アプリケーションに初めてアクセスすると、「匿名デバイス」を使用しているという通知が表示され、ID の認証、ウォレットへのアクセスの許可、およびデバイスの登録が求められます。

- #### ステップ 3:**認証を**クリックしてインターネット ID サービスに進みます。

- #### ステップ 4: ID を登録済みの場合は**ユーザー番号を**入力するか、新規ユーザーとしてサービスに登録します。
  
  Internet Identity サービスの詳細、および複数の認証デバイスと認証方法の登録方法については、[Internet Identity サービスの使用](../../references/ii-spec.md)方法を参照してください。

- #### ステップ 5：ユーザー番号と、登録した認証方法（セキュリティキーや指紋など）を使用して認証します。

- #### ステップ 6：［**Proceed（続行**）］をクリックして、デフォルトのcycles ウォレット アプリケーションにアクセスします。

- #### ステップ**7：Register Device（デバイスの登録**）ページに表示されるコマンドをコピーしてターミナルで実行し、このセッションで使用するデバイスを登録します。
  
  例えば、以下のようなコマンドでcycles ウォレットcanister の`authorize` メソッドを呼び出します：
  
  ```
    dfx canister --network ic call "gastn-uqaaa-aaaae-aaafq-cai" authorize '(principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe")'
  ```
  
  コピーしたコマンドに正しいネットワーク (`ic`) エイリアスがあることを確認してください。コピーしたコマンドに正しいネットワーク (cycles ) エイリアスがあることを確認してください。canister 識別子 (この例では`gastn-uqaaa-aaaae-aaafq-cai`) を、自分の ID に関連付けられた ウォレットとして認識する必要があります。しかし、これが IC 上の最初のウォレットである場合、認証されるプリンシパルを認識できない可能性があります。この場合、別のプリンシパルを使用することが期待される動作です。
  
  `authorize` コマンドの実行後にブラウザが更新されると、プリンシパルアカウントのcycles ウォレットが表示されます。

- #### ステップ 8: ブラウザでcycles の残高とアクティビティを表示します。
  
  例えば
  
  ![cycles wallet](_attachments/cycles-wallet.png)
  
  デフォルトのcycles ウォレットで使用できるコマンドと方法の詳細については、[デフォルトのcycles ウォレットを使用する](/docs/developer-docs/setup/cycles/cycles-wallet.md)を参照してください。

## アプリケーションの登録、ビルド、デプロイ

cycles ウォレットの残高を確認したら、サンプルアプリケーションを登録、ビルド、デプロイできます。

Internet Computer ブロックチェーンメインネットに最初のアプリケーションをデプロイするには：

- #### ステップ 1: ターミナルシェルで、プロジェクトのルートディレクトリにいることを確認します。

- #### ステップ2：必要に応じて、以下のコマンドを実行して、`node` モジュールがプロジェクト・ディレクトリで利用可能であることを確認します：
  
  ```
    npm install
  ```
  
  このステップの詳細については、[プロジェクトでノードが利用可能であることを確認するを](/developer-docs/frontend/index.md#troubleshoot-node)参照してください。

- #### ステップ 3: 次のコマンドを実行して、最初のアプリケーションを登録、ビルド、およびデプロイします：
  
  ```
    dfx deploy --network ic
  ```
  
  `--network` オプションは、dapp をデプロイするためのネットワークエイリアスまたは URL を指定します。 このオプションは、Internet Computer ブロックチェーンメインネットにインストールするために必要です。
  
  `dfx deploy` コマンドの出力には、実行する操作に関する情報が表示されます。
  
  たとえば、このステップでは、`hello_backend` メインプログラム用の識別子と、`hello_frontend` フロントエンドのユーザーインターフェイス用の識別子の2つと、次のようなインストール情報が登録されます：
  
  ```
    Deploying all canisters.
    Creating canisters...
    Creating canister "hello_backend"...
    "hello" canister created on network "ic" with canister id: "5o6tz-saaaa-aaaaa-qaacq-cai"
    Creating canister "hello_frontend"...
    "hello_assets" canister created on network "ic" with canister id: "5h5yf-eiaaa-aaaaa-qaada-cai"
    Building canisters...
    Building frontend...
    Installing canisters...
    Installing code for canister hello_backend, with canister_id 5o6tz-saaaa-aaaaa-qaacq-cai
    Installing code for canister hello_frontend, with canister_id 5h5yf-eiaaa-aaaaa-qaada-cai
    Authorizing our identity (default) to the asset canister...
    Uploading assets to asset canister...
      /index.html 1/1 (472 bytes)
      /index.html (gzip) 1/1 (314 bytes)
      /index.js 1/1 (260215 bytes)
      /index.js (gzip) 1/1 (87776 bytes)
      /main.css 1/1 (484 bytes)
      /main.css (gzip) 1/1 (263 bytes)
      /sample-asset.txt 1/1 (24 bytes)
      /logo.png 1/1 (25397 bytes)
      /index.js.map 1/1 (842511 bytes)
      /index.js.map (gzip) 1/1 (228404 bytes)
      /index.js.LICENSE.txt 1/1 (499 bytes)
      /index.js.LICENSE.txt (gzip) 1/1 (285 bytes)
    Deployed canisters.
  ```
  
  操作を完了するのに十分な数の ICP トークンをcycles に変換しなかった場合は、次のようなコマンドを実行してcycles をcycles ウォレットに追加できます：
  
  ```
    dfx ledger --network ic top-up gastn-uqaaa-aaaae-aaafq-cai --amount 1.005
  ```
  
  このコマンドは、追加の`1.005` ICP トークンを`gastn-uqaaa-aaaae-aaafq-cai` cycles ウォレット識別子のcycles に変換します。このコマンドは、次のような出力を返します：
  
  ```
    Transfer sent at BlockHeight: 81520
    Canister was topped up!
  ```

- #### ステップ 4: 以下のコマンドを実行して、`hello_backend` canister と定義済みの`greet` 関数を呼び出します：
  
  ```
    dfx canister --network ic call hello_backend greet '("everyone": text)'
  ```
  
  この例を詳しく見てみましょう：
  
  - `--network ic` オプションを使用すると、呼び出したいcanister が`ic` にデプロイされていることを示します。`ic` ネットワークエイリアスは、Internet Computer ブロックチェーンメインネットにアクセスするための内部的に予約されたエイリアスです。
  
  - `--network ic` オプションは、操作サブコマンド（この場合は`dfx canister call` コマンド）の前になければならないことに注意してください。
  
  - `hello_backend` 引数には、呼び出したいcanister の名前を指定します。
  
  - `greet` 引数には、`hello` canister で呼び出したい関数名を指定します。
  
  - テキスト文字列`everyone` は、`greet` 関数に渡したい引数です。

- #### ステップ5：コマンドが`greet` 関数の戻り値を表示することを確認します。
  
  例えば
  
  ```
    ("Hello, everyone!")
  ```

- #### ステップ6：`dfx wallet balance` コマンドを再実行するか、ブラウザを更新すると、新しいcycle 残高と最近のアクティビティが表示されます。

## dapp フロントエンドのテスト

dapp がデプロイされたことを確認し、コマンドラインを使用してその動作をテストしたので、ウェブブラウザを使用してフロントエンドにアクセスできることを確認しましょう。

dapp フロントエンドにアクセスするには

- #### ステップ1: ブラウザを開きます。

- #### ステップ2:`hello_frontend` 識別子と`boundary.icp0.io` 接尾辞からなる URL を使ってdapp のフロントエンドに移動します。
  
  canister の識別子をメモしていない場合は、次のコマンドを実行して調べることができます：
  
  ```
    dfx canister --network ic id hello_assets
  ```
  
  例えば、完全なURLは以下のようになります：
  
  ```
    https://gsueu-yaaaa-aaaae-aaagq-cai.icp0.io
  ```
  
  この URL に移動すると、テンプレート・アプリケーションの HTML 入力ページが表示されます。例えば
  
  ![HTML page with prompt](_attachments/net-frontend-prompt.png)

- #### ステップ 3: あいさつ文を入力し、「**Click Me**」をクリックしてあいさつ文を返します。

## まとめ

Internet Computer ブロックチェーン上にdapp をデプロイする方法を確認したので、独自のプログラムを開発してデプロイする準備が整いました。

## リソース

Motoko の使用方法や、Internet Computer ブロックチェーン用のdapps の開発方法について学ぶのに役立つ、より詳細な例やガイドがドキュメント全体にあります。

次に行くべき場所について、いくつか提案します：

- サンプルdapps を探索するための[IC の構築](../../samples/overview.md)。

- さまざまなICのコンセプトとサービスについて学ぶには、「[コンセプト](../../concepts/index.md)」。

- IC内で使用される様々な用語の定義を学ぶための[IC用語集](../../references/glossary.md)。

- [Motoko 概要](/motoko/main/overview.md) Motoko を使用するための機能と構文について学びます。

- [Candidとは何か](/developer-docs/backend/candid/candid-concepts.md)：Candidインターフェース記述言語がどのようにサービスの相互運用性とコンポーザビリティを実現するかについて学びます。

<!---


# Deploying to the Internet Computer mainnet 

## Overview

This scenario assumes that you are installing the IC SDK for the first time and deploying the default project on the Internet Computer blockchain mainnet.

If you are only deploying projects in a local development environment, see the [local development](./deploy-locally.md) scenario.

To get started, let’s build and deploy a simple 'Hello, world!' dapp that has just one function—called `greet`. The `greet` function accepts one text argument and returns the result with a greeting similar to **Hello, everyone!** in a terminal if you run the dapp using the command-line or in an HTML page if you access the dapp in a browser.

## Prerequisites

Before you download and install this release of the IC SDK, verify the following:

-   [x] You have an internet connection and access to a shell terminal on your local computer.

-   [x] You have `node.js` installed if you want to access the default frontend for the default project.

-   [x] You have ICP tokens or cycles available for you to use.

:::info
You must have **cycles** available to complete this guide. To get cycles, you must either convert ICP tokens to cycles or be provided cycles from another source, for example, from a canister controlled by another developer or from a third-party cycles provider. This guide assumes that you have an account with ICP tokens available and illustrates how to convert ICP tokens into cycles, then transfer those cycles to a **cycles wallet** that you control.

For information about how to get ICP tokens, see [how you can get ICP tokens](../setup/cycles/converting_icp_tokens_into_cycles.md).

For an introduction to using the Network Nervous System application to manage ICP tokens, see [Network Nervous System dapp quick start](../../tokenomics/token-holders/nns-app-quickstart).

For information about using your default cycles wallet after you have created it, see [use the default cycles wallet](/developer-docs/setup/cycles/cycles-wallet.md).
:::

## Download and install

You can download the latest version of the IC SDK directly from within a terminal shell on your local computer. If you have previously installed the IC SDK, you can skip this section and start with [create a new project](#net-new-project).

:::caution
These instructions are for **macOS** or **Linux** machines. For Windows instructions, please see [here](/docs/current/developer-docs/setup/install/index).
:::

To download and install:

- #### Step 1:  Open a terminal shell on your local computer.

    For example, open Applications > Utilities, then double-click **Terminal** or press <span class="keycombo">⌘+spacebar</span> to open Search, then type `terminal`.

- #### Step 2:  Download and install the IC SDK package by running the following command:

        sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"

    This command prompts you to read and accept the license agreement before installing the DFINITY execution command-line interface (CLI) and its dependencies on your local computer.

- #### Step 3:  Type `y` and press Return to continue with the installation.

    The command displays information about the components being installed on the local computer.

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

Dapps for the Internet Computer start as **projects**. You create projects using the `dfx` parent command and its subcommands.

For this guide, we’ll start with the default sample dapp to illustrate creating a dapp using the starter files in a project. When you create a new project, the `dfx` command-line interface adds a default project directory structure to your workspace. We cover the template files that make up a project directory in the [explore the default project](/docs/developer-docs/backend/motoko/explore-templates.md) guide.

To create a new project for your first dapp:

- #### Step 1:  Open a terminal shell on your local computer, if you don’t already have one open.

- #### Step 2:  Create a new project named `hello` by running the following command:

        dfx new hello

    The `dfx new hello` command creates a new `hello` project directory, template files, and a new `hello` Git repository for your project.

    If you use a different project name instead of `hello`, make note of the name you used. You’ll need to use that project name in place of the `hello` project name throughout these instructions.

- #### Step 3:  Change to your project directory by running the following command:

        cd hello

## Check the connection to the IC mainnet

There is a reserved network alias that you can use to access the Internet Computer blockchain mainnet. The network alias is a system setting that’s defined internally, so there’s nothing you need to configure in your projects by default.

To check your connection to the IC:

- #### Step 1:  Check that you are in the root directory for your project, if needed.

- #### Step 2:  Check the current status of the IC and your ability to connect to it by running the following command for the network alias `ic`:

        dfx ping ic

- #### Step 3:  Verify that the `dfx ping ic` command returns information about the IC.

    For example, you should see output similar to the following:

        {
          "ic_api_version": "0.18.0"  "impl_hash": "d639545e0f38e075ad240fd4ec45d4eeeb11e1f67a52cdd449cd664d825e7fec"  "impl_version": "8dc1a28b4fb9605558c03121811c9af9701a6142"  "replica_health_status": "healthy"  "root_key": [48, 129, 130, 48, 29, 6, 13, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 1, 2, 1, 6, 12, 43, 6, 1, 4, 1, 130, 220, 124, 5, 3, 2, 1, 3, 97, 0, 129, 76, 14, 110, 199, 31, 171, 88, 59, 8, 189, 129, 55, 60, 37, 92, 60, 55, 27, 46, 132, 134, 60, 152, 164, 241, 224, 139, 116, 35, 93, 20, 251, 93, 156, 12, 213, 70, 217, 104, 95, 145, 58, 12, 11, 44, 197, 52, 21, 131, 191, 75, 67, 146, 228, 103, 219, 150, 214, 91, 155, 180, 203, 113, 113, 18, 248, 71, 46, 13, 90, 77, 20, 80, 95, 253, 116, 132, 176, 18, 145, 9, 28, 95, 135, 185, 136, 131, 70, 63, 152, 9, 26, 11, 170, 174]
        }

## Confirm your developer identity and ledger account

All ICP token transactions are recorded in a [ledger canister](../../references/glossary.md#ledger) running on the Internet Computer blockchain. The ledger canister consists of **account identifiers** and **balances** for all ICP token holders.

Before you can transfer any ICP tokens you hold in your ledger account, you need to send a secure and properly-signed message that verifies your identity to the ledger and authorizes your developer identity to complete the transaction.

Depending on how you have set up custody for holding your ICP tokens, the hardware, software, and steps required to connect to the ledger and complete a transaction can vary. For example, you might connect to the ledger and start a transaction from a hardware wallet, using a hardware security module (HSM) appliance, through the Network Nervous System (NNS) frontend application, or using the IC SDK `dfx` command-line interface. Each approach presents a different interface for signing and sending messages to the ledger and representing your identity as an account holder.

### About your developer identity

The first time you use the IC SDK, the `dfx` command-line tool creates a `default` developer identity for you. This identity is represented by a **principal** data type and a textual representation of the principal often referred to as your **principal identifier**. This representation of your identity is similar to a Bitcoin or Ethereum address.

However, the principal associated with your developer identity is typically not the same as your **account identifier** in the ledger. The principal identifier and the account identifier are related—both provide a textual representation of your identity—but they use different formats.

### Connect to the ledger to get account information

For the purposes of this guide—where there’s no hardware wallet or external application to connect to the ledger—we’ll use your developer identity to retrieve your ledger account identifier, then transfer ICP tokens from the ledger account identifier to a cycles wallet canister controlled by your developer identity.

To look up your account in the ledger:

- #### Step 1:  Confirm the developer identity you are currently using by running the following command:

        dfx identity whoami

    In most cases, you should see that you are currently using default developer identity. For example:

        default

- #### Step 2:  View the textual representation of the principal for your current identity by running the following command:

        dfx identity get-principal

    This command displays output similar to the following:

        tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe

- #### Step 3:  Get the account identifier for your developer identity by running the following command:

        dfx ledger account-id

    This command displays the ledger account identifier associated with your developer identity. For example, you should see output similar to the following:

        03e3d86f29a069c6f2c5c48e01bc084e4ea18ad02b0eec8fccadf4487183c223

- #### Step 4:  Check your account balance by running the following command:

        dfx ledger --network ic balance

    This command displays the ICP token balance from the ledger account. For example, you should see output similar to the following:

        10.00000000 ICP

## Creating a cycles wallet

Now that you have confirmed your account information and current ICP token balance, you can convert some of those ICP tokens to cycles and move them into a cycles wallet.

To transfer ICP tokens to create a cycles wallet:

- #### Step 1:  Create a new canister with cycles by transferring ICP tokens from your ledger account by running a command similar to the following:

        dfx ledger --network ic create-canister <principal-identifier> --amount <icp-tokens>

    This command converts the number of ICP tokens you specify for the `--amount` argument into cycles, and associates the cycles with a new canister identifier controlled by the principal you specify.

    For example, the following command converts .25 ICP tokens into cycles and specifies the principal identifier for the default identity as the controller of the new canister:

        dfx ledger --network ic create-canister tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe --amount .25

    If the transaction is successful, the ledger records the event and you should see output similar to the following:

        Transfer sent at BlockHeight: 20
        Canister created with id: "gastn-uqaaa-aaaae-aaafq-cai"

- #### Step 2:  Install the cycles wallet code in the newly-created canister placeholder by running a command similar to the following:

        dfx identity --network ic deploy-wallet <canister-identifer>

    For example:

        dfx identity --network ic deploy-wallet gastn-uqaaa-aaaae-aaafq-cai

    This command displays output similar to the following:

        Creating a wallet canister on the IC network.
        The wallet canister on the "ic" network for user "default" is "gastn-uqaaa-aaaae-aaafq-cai"

## Validate your cycles wallet

After you convert ICP tokens to cycles, you can validate the cycles wallet canister and check your current cycles balance.

To validate your cycles wallet:

- #### Step 1: Verify the canister identifier for the cycles wallet you deployed by running the following command:

        dfx identity --network ic get-wallet

    The command displays the canister identifier for your cycles wallet with output similar to the following:

        gastn-uqaaa-aaaae-aaafq-cai

- #### Step 2:  Check that your cycles wallet canister is properly configured and holds a balance of cycles by running a command similar to the following:

        dfx wallet --network ic balance

    The command returns the balance for the your cycles wallet. For example:

        15430.122 TC (trillion cycles).

    You can also access your default cycles wallet in a web browser by using a URL similar to the following:

        https://<WALLET-CANISTER-ID>.icp0.io

    The first time you access the application, you see a notice that you are using an 'Anonymous Device' and are prompted to authenticate your identity, authorize access to the wallet, and register your device.

- #### Step 3:  Click **Authenticate** to continue to the Internet Identity service.

- #### Step 4:  Enter your **User Number** if you have previously registered an identity or register with the service as a new user.

    For more information about the Internet Identity service and how to register multiple authentication devices and methods, see [how to use the Internet Identity service](../../references/ii-spec.md).

- #### Step 5:  Authenticate using your user number and the authentication method—for example, a security key or fingerprint—you have registered.

- #### Step 6:  Click **Proceed** to access to the default cycles wallet application.

- #### Step 7:  Register the device you are using for this session by copying the command displayed in the **Register Device** page and running it in a terminal.

    For example, call the `authorize` method for the cycles wallet canister with a command similar to the following:

        dfx canister --network ic call "gastn-uqaaa-aaaae-aaafq-cai" authorize '(principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe")'

    Be sure that the command you copy has the correct network (`ic`) alias. You should recognize the canister identifier—in this example, `gastn-uqaaa-aaaae-aaafq-cai`—as the cycles wallet associated with your identity. If this is your first wallet on the IC, however, you might not recognize the principal being authorized. The use of a different principal is the expected behavior in this case.

    When the browser refreshes after running the `authorize` command, the cycles wallet for your principal account is displayed.

- #### Step 8:  View your cycles balance and activity in the browser.

    For example:

    ![cycles wallet](_attachments/cycles-wallet.png)

    For more information about the commands and methods available for working with the default cycles wallet, see [use the default cycles wallet](/docs/developer-docs/setup/cycles/cycles-wallet.md).

## Register, build, and deploy the application

After you have validated your cycles wallet balance, you can register, build, and deploy your sample application.

To deploy your first application on the Internet Computer blockchain mainnet:

- #### Step 1:  In your terminal shell, check that you are still in the root directory for your project.

- #### Step 2:  Ensure that `node` modules are available in your project directory, if needed, by running the following command:

        npm install

    For more information about this step, see [ensuring node is available in a project](/developer-docs/frontend/index.md#troubleshoot-node).

- #### Step 3:  Register, build, and deploy your first application by running the following command:

        dfx deploy --network ic

    The `--network` option specifies the network alias or URL for deploying the dapp. This option is required to install on the Internet Computer blockchain mainnet.

    The `dfx deploy` command output displays information about the operations it performs.

    For example, this step registers two identifiers—one for the `hello_backend` main program and one for the `hello_frontend` frontend user interface—and installation information similar to the following:

        Deploying all canisters.
        Creating canisters...
        Creating canister "hello_backend"...
        "hello" canister created on network "ic" with canister id: "5o6tz-saaaa-aaaaa-qaacq-cai"
        Creating canister "hello_frontend"...
        "hello_assets" canister created on network "ic" with canister id: "5h5yf-eiaaa-aaaaa-qaada-cai"
        Building canisters...
        Building frontend...
        Installing canisters...
        Installing code for canister hello_backend, with canister_id 5o6tz-saaaa-aaaaa-qaacq-cai
        Installing code for canister hello_frontend, with canister_id 5h5yf-eiaaa-aaaaa-qaada-cai
        Authorizing our identity (default) to the asset canister...
        Uploading assets to asset canister...
          /index.html 1/1 (472 bytes)
          /index.html (gzip) 1/1 (314 bytes)
          /index.js 1/1 (260215 bytes)
          /index.js (gzip) 1/1 (87776 bytes)
          /main.css 1/1 (484 bytes)
          /main.css (gzip) 1/1 (263 bytes)
          /sample-asset.txt 1/1 (24 bytes)
          /logo.png 1/1 (25397 bytes)
          /index.js.map 1/1 (842511 bytes)
          /index.js.map (gzip) 1/1 (228404 bytes)
          /index.js.LICENSE.txt 1/1 (499 bytes)
          /index.js.LICENSE.txt (gzip) 1/1 (285 bytes)
        Deployed canisters.

    If you didn’t convert enough ICP tokens to cycles to complete the operation, you can add cycles to your cycles wallet by running a command similar to the following:

        dfx ledger --network ic top-up gastn-uqaaa-aaaae-aaafq-cai --amount 1.005

    This command converts an additional `1.005` ICP tokens to cycles for the `gastn-uqaaa-aaaae-aaafq-cai` cycles wallet identifier. The command returns output similar to the following:

        Transfer sent at BlockHeight: 81520
        Canister was topped up!

- #### Step 4:  Call the `hello_backend` canister and the predefined `greet` function by running the following command:

        dfx canister --network ic call hello_backend greet '("everyone": text)'

    Let’s take a closer look at this example:

    -   Using the `--network ic` option indicates that the canister you want to call is deployed on the `ic`. The `ic` network alias is an internally-reserved alias for accessing the Internet Computer blockchain mainnet.

    -   Note that the `--network ic` option must precede the operation subcommand, which, in this case, is the `dfx canister call` command.

    -   The `hello_backend` argument specifies the name of the canister you want to call.

    -   The `greet` argument specifies the name of the function you want to call in the `hello` canister.

    -   The text string `everyone` is the argument that you want to pass to the `greet` function.

- #### Step 5:  Verify the command displays the return value of the `greet` function.

    For example:

        ("Hello, everyone!")

- #### Step 6:  Rerun the `dfx wallet balance` command or refresh the browser to see your new cycle balance and recent activity.

## Test the dapp frontend

Now that you have verified that your dapp has been deployed and tested its operation using the command line, let’s verify that you can access the frontend using your web browser.

To access the dapp frontend:

- #### Step 1:  Open a browser.

- #### Step 2:  Navigate to the frontend for the dapp using a URL that consists of the `hello_frontend` identifier and the `boundary.icp0.io` suffix.

    If you didn’t make a note of the canister identifier, you can look it up by running the following command:

        dfx canister --network ic id hello_assets

    For example, the full URL should look similar to the following:

        https://gsueu-yaaaa-aaaae-aaagq-cai.icp0.io

    Navigating to this URL displays the HTML entry page for the template application. For example:

    ![HTML page with prompt](_attachments/net-frontend-prompt.png)

- #### Step 3:  Type a greeting, then click **Click Me** to return the greeting.

## Conclusion

Now that you have seen how to deploy a dapp on the Internet Computer blockchain, you are ready to develop and deploy programs of your own.

## Resources

You can find more detailed examples and guides to help you learn about how to use Motoko and how to develop dapps for the Internet Computer blockchain throughout the documentation.

Here are some suggestions for where to go next:

-   [Building on the IC](../../samples/overview.md) to explore sample dapps.

-   [Concepts](../../concepts/index.md) to learn about different IC concepts and services.  

-   [IC glossary](../../references/glossary.md) to learn the definitions of various terms used within the IC. 

-   [Motoko overview](/motoko/main/overview.md) to learn about the features and syntax for using Motoko.

-   [What is Candid?](/developer-docs/backend/candid/candid-concepts.md) to learn how the Candid interface description language enables service interoperability and composability.


-->
