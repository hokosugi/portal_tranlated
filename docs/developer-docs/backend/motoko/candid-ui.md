# 17: Candid UIを使用したブラウザでの機能テスト

## 概要

canister インターフェース記述言語（しばしば Candid またはより一般的には IDL と呼ばれます）は、canister スマートコントラクトの署名を指定するための共通言語を提供します。

Candidは、異なる言語で書かれた、または異なるツールを使用してアクセスされたcanister スマートコントラクトと対話するための統一された方法を提供します。
例えば、Candidは、基礎となるプログラムがネイティブのRust、JavaScript、またはその他のプログラミング言語であるかどうかにかかわらず、サービスの一貫したビューを提供します。
Candidはまた、`dfx` コマンドラインインターフェースやNetwork Nervous System dapp などの異なるツールが、サービスのための共通の記述を共有することを可能にします。

actor の型シグネチャに基づき、Candid はテストやデバッグのためにcanister 関数を呼び出すことができる Web インターフェースも提供します。

## Candid UI の使用

`dfx deploy` または`dfx canister install` コマンドを使用してローカルcanister 実行環境にプロジェクトをデプロイした後、ブラウザで Candid ウェブインターフェースエンドポイントにアクセスすることができます。

このウェブインターフェイス（Candid UI）は、サービスの説明をフォームで公開し、フロントエンドのコードを記述することなく、関数を素早く表示してテストしたり、異なるデータタイプを入力して実験したりすることを可能にします。

Candidウェブ・インターフェースを使用してcanister 関数をテストするには、まずコマンドを実行して現在のプロジェクトに関連するCandid UIcanister 識別子を見つけます：

    dfx canister id __Candid_UI

このコマンドは以下のような出力で Candid UI のcanister 識別子を表示します：

    r7inp-6aaaa-aaaaa-aaabq-cai

Candid UIcanister 識別子をクリップボードにコピーします。

ローカルのcanister 実行環境を停止した場合、以下のコマンドを実行してローカルで再起動します：

    dfx start --background

ブラウザを開き、`dfx.json` 設定ファイルで指定されたアドレスとポート番号に移動します。

デフォルトでは、ローカルのcanister 実行環境は`127.0.0.1:4943` アドレスとポート番号にバインドされます。

必要な`canisterId` パラメータと、`dfx canister id __Candid_UI` コマンドによって返された Candid UIcanister 識別子を URL に追加します。

例えば、完全な URL は以下のようになりますが、`dfx canister id __Candid_UI` コマンドによって返された`CANDID-UI-CANISTER-IDENTIFIER` が含まれます：

    http://127.0.0.1:4943/?canisterId=<CANDID-UI-CANISTER-IDENTIFIER>

例えば、上記の Candid UI のcanister 識別子の例では、以下のようになります：

    http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai

canister`.did`
このフィールドは、対話したい の 識別子を参照していることに注意してください（最後のステップで使用した Candid UI の 識別子とは異なります）。canister canister canister 

テストしたいcanister のcanister 識別子を \[**Provide acanister ID**\] フィールドに指定し、\[**Go\]**をクリックしてサービスの説明を表示します。

使用するcanister 識別子がわからない場合は、`dfx canister id` コマンドを実行して、特定のcanister 名の識別子を調べることができます。たとえば、`my_counter` という名前のcanister のcanister 識別子を取得するには、次のようにします：

    dfx canister id my_counter

dapp で定義されている関数呼び出しと型のリストを確認します。

関数の適切な型の値を入力するか、\[**Random\]**をクリックして値を生成し、\[**Call\]**または \[**Query**\] をクリックして結果を確認します。

:::info
データ型によっては、Candid インターフェースに関数をテストするための追加の構成設定が表示される場合があることに注意してください。
::：

例えば、関数が配列を受け取る場合、値を入力する前に配列の項目数を指定する必要があるかもしれません。

![Calculator functions](_attachments/candid-calc.png)

## 次のステップ

次のステップでは、[スケーラブルなdapp の例を](scalability-cancan.md)探ります。

<!---
# 17: Using the Candid UI to test functions in a browser

## Overview
The canister interface description language, often referred to as Candid or more generally as the IDL, provides a common language for specifying the signature of a canister smart contract.

Candid provides a unified way for you to interact with canister smart contracts that are written in different languages or accessed using different tools.
For example, Candid provides a consistent view of a service whether the underlying program is native Rust, JavaScript, or any other programming language. 
Candid also enables different tools, such as the `dfx` command-line interface and the Network Nervous System dapp, to share a common description for a service.

Based on the type signature of the actor, Candid also provides a web interface that allows you to call canister functions for testing and debugging.

## Using Candid UI

After you have deployed your project in the local canister execution environment using the `dfx deploy` or `dfx canister install` command, you can access the Candid web interface endpoint in a browser. 

This web interface, the Candid UI, exposes the service description in a form, enabling you to quickly view and test functions and experiment with entering different data types without writing any front-end code.

To use the Candid web interface to test canister functions, first find the Candid UI canister identifier associated with the current project by running the command:

```
dfx canister id __Candid_UI
```

This command displays the canister identifier for the Candid UI with output similar to the following:

```
r7inp-6aaaa-aaaaa-aaabq-cai
```

Copy the Candid UI canister identifier so that it is available in the clipboard. 

If you've stopped the local canister execution environment, restart it locally by running the following command:

```
dfx start --background
```

Open a browser and navigate to the address and port number specified in the `dfx.json` configuration file. 

By default, the local canister execution environment binds to the `127.0.0.1:4943` address and port number.

Add the required `canisterId` parameter and the Candid UI canister identifier returned by the `dfx canister id __Candid_UI` command to the URL. 

For example, the full URL should look similar to the following but with the `CANDID-UI-CANISTER-IDENTIFIER` that was returned by the `dfx canister id __Candid_UI` command:

```
http://127.0.0.1:4943/?canisterId=<CANDID-UI-CANISTER-IDENTIFIER>
```

For instance, with the example canister identifier for the Candid UI as shown above, this could look as follows:

```
http://127.0.0.1:4943/?canisterId=r7inp-6aaaa-aaaaa-aaabq-cai
```

The browser then displays a form for you to specify a canister identifier or choose a Candid description (`.did`) file. 
Note that this field refers to the canister identifier of the canister you would like to interact with (as opposed to the canister identifier for the Candid UI that we used in the last step).

Specify the canister identifier of the canister you would like to test in the **Provide a canister ID** field, then click **Go** to display the service description.
    
If you aren’t sure which canister identifier to use, you can run the `dfx canister id` command to look up the identifier for a specific canister name. For instance, to get the canister identifier for a canister named `my_counter`, you would use:

```
dfx canister id my_counter
```

Review the list of function calls and types defined in the dapp.

Type a value of the appropriate type for a function or click **Random** to generate a value, then click **Call** or **Query** to see the result.

:::info
Note that depending on the data type, the Candid interface might display additional configuration settings for testing functions.
:::

For example, if a function takes an array, you might need to specify the number of items in the array before entering values.

![Calculator functions](_attachments/candid-calc.png)

## Next steps

In the next step, we'll explore a [scalable dapp example](scalability-cancan.md).
-->
