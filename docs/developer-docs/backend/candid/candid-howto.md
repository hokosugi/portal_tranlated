# キャンディッドの使用

# 概要

[Candid のコンセプト](./candid-concepts.md)ページで説明したように、Candid はcanisters と対話するための言語にとらわれない方法を提供します。Candid を使用することで、IC SDK を使用した端末、ウェブブラウザ、JavaScript、Motoko 、Rust、またはその他の言語で記述されたプログラムのいずれから IC と対話するかに関係なく、入力引数の値を指定し、canister メソッドからの戻り値を表示することができます。Candidがどのようなもので、どのように機能するのかを理解したところで、このセクションではいくつかの一般的なシナリオでCandidを使用する方法を説明します。

具体的な例として、`counter` canister が既にネットワーク上にデプロイされており、以下の Candid インターフェースがあるとします：

``` candid
service Counter : {
  inc : (step: nat) -> (nat);
}
```

では、Candid の助けを借りて様々なシナリオでこのcanister と対話する方法を探ってみましょう。

## `.did` ファイル

Candidタイプは、Candidサービス記述ファイル（`.did` ファイル）を介してサービスを記述するために使用することができます。これは、手動で記述するか、サービス実装から生成することができます。

例えば、Motoko でcanister を記述すると、プログラムをコンパイルする際にコンパイラが自動的に Candid 記述を生成します。SDK を使用すると、通常、プロジェクトの`/declarations` ディレクトリに自動生成された`.did` ファイルが表示されます。これらのファイルは自動生成されるため、手動で編集しないことをお勧めします。プロジェクトの`.did` ファイルを変更しても、次の dfx ビルドで上書きされます。

Rustのような他の言語では、Candidインターフェースの記述を手動で書かなければなりません。型の助けを借りて、サービス記述ファイルに基づいてUIを自動生成し、ランダムテストを実行するツールを開発しました。

## ターミナルでサービスと対話

canisters 、ICと対話する最も一般的な方法の1つは、IC SDKを使用することです。

IC SDKの中で、`dfx` ツールは`dfx canister call` コマンドを提供し、特定のデプロイされたcanister（基本的にはIC上で実行されるスマートコントラクト）、および該当する場合はスマートコントラクトによって提供されるサービスのメソッドを呼び出します。

`dfx canister call` コマンドを実行する際、引数を[Candid テキスト値として](./candid-concepts.md#textual-values)指定することで、メソッドに引数を渡すことができます。

コマンドラインで Candid テキスト値を渡す場合、複数の引数値をカンマ (`,`) で区切って括弧で囲んで指定できます。例えば、`(42, true)` を指定すると、2 つの引数値を表します。最初の引数は数値`42` で、2 番目の引数はブール値`true` です。

次の例は、`dfx canister call` コマンドを使用して、`counter` canister サービスを呼び出し、`inc` メソッドの引数を渡す例です：

``` bash
$ dfx canister call counter inc '(42)'
(43)
```

より複雑な Candid 引数の作成方法については、[Candid リファレンスを](/references/candid-ref.md)参照してください。また、Candidの引数が長すぎてコマンドラインに収まらない場合は、`--argument-file` のフラグを使用してください。 [`dfx canister call`](/references/cli-reference/dfx-canister.md#dfx-canister-call).

また、引数を省略し、IC SDKにメソッドタイプに一致するランダムな値を生成させることもできます。例えば

``` bash
$ dfx canister call counter inc
Unspecified argument, sending the following random argument:
(1_543_454_453)

(1_543_454_454)
```

`dfx` および`dfx canister call` コマンドの使用に関する詳細は、[コマンドラインリファレンス](/references/cli-reference/index.md)および[dfxcanister](/references/cli-reference/dfx-canister.md)ドキュメントを参照してください。

## ブラウザからのサービスとの対話

Candid インターフェース記述言語は、canister の署名を指定するための共通言語を提供します。スマートコントラクトが提供するサービスのタイプ署名に基づいて、Candid はウェブインターフェース（Candid UI）を提供します。これにより、フロントエンドのコードを記述することなく、ウェブブラウザーからテストやデバッグのためにcanister 関数を呼び出すことができます。

Candidのウェブインターフェースを使用して`counter` canister をテストするには：

- #### ステップ 1:`dfx canister id __Candid_UI` コマンドを使用して`counter` canister に関連付けられている Candid UIcanister 識別子を見つけます。
  
  ``` bash
  dfx canister id __Candid_UI
  ```
  
  コマンドは、以下のような出力で Candid UI のcanister 識別子を表示します：
  
  ```
    r7inp-6aaaa-aaaaa-aaabq-cai
  ```

- #### ステップ 2: 以下のコマンドを実行して、canister 実行環境をローカルで開始します：
  
  ``` bash
  dfx start --background
  ```

- #### ステップ3： ブラウザを開き、`dfx.json` 構成ファイルで指定されたアドレスとポート番号に移動します。
  
  デフォルトでは、`local` canister 実行環境は`127.0.0.1:4943` アドレスとポート番号にバインドされます。

- #### ステップ4：必要な`canisterId` パラメータと、`dfx canister id` コマンドによって返される Candid UIcanister 識別子を追加します。
  
  例えば、完全なURLは以下のようになりますが、`dfx canister id` コマンドによって返された`CANDID-UI-CANISTER-IDENTIFIER` を使用します：
  
  ```
    http://127.0.0.1:4943/?canisterId=<CANDID-UI-CANISTER-IDENTIFIER>
  ```
  
  ブラウザは、canister 識別子を指定するか、または Candid description (`.did`) ファイルを選択するためのフォームを表示します。
  
  ![candid ui select id](_attachments/candid-ui-select-id.png)
  
  どのcanister 識別子を使用すればよいかわからない場合は、`dfx canister id` コマンドを実行して、特定のcanister 名の識別子を調べることができます。
  
  例えば、`counter` サービス記述の機能を表示したい場合は、次のコマンドを実行してcanister 識別子を検索できます：
  
  ```
    dfx canister id counter
  ```

- #### ステップ5：canister 識別子または記述ファイルを指定し、「**Go**」をクリックしてサービス記述を表示します。

- #### ステップ6：プログラムで定義されている関数呼び出しと型のリストを確認します。

- #### ステップ7： 関数に適切なタイプの値を入力するか、［**ランダム］**をクリックして値を生成し、［**コール**］または［**クエリー**］をクリックして結果を確認します。

任意のcanister の Candid インターフェースから Web インターフェースを作成するツールの詳細については、[Candid UI](https://github.com/dfinity/candid/tree/master/tools/ui)リポジトリを参照してください。

## Motoko からのサービスとの対話canister

Motoko `dfx build` で を記述する場合、 コンパイラは のトップレベル または のシグネチャを自動的に Candid 記述に変換します。canister Motoko canister `actor` `actor class` 

例えば、Motoko の`counter` canister を呼び出す`hello` canister を記述したい場合です：

``` motoko
import Counter "canister:Counter";
import Nat "mo:base/Nat";
actor {
  public func greet() : async Text {
    let result = await Counter.inc(1);
    "The current counter is " # Nat.toText(result)
  };
}
```

この例では、`counter` canister のインポート依存関係（`import Counter "canister:Counter"` 宣言）が`dfx build` コマンドによって処理されるとき、`dfx build` コマンドは、`counter` canister 識別子と Candid 記述がMotoko コンパイラに正しく渡されるようにします。Motoko コンパイラは次に、Candid 型を適切なネイティブのMotoko 型に変換します。この変換により、`counter` canister が異なる言語で実装されていても、インポートされたcanister のソース・コードがなくても、Motoko 関数のように`inc` メソッドをネイティブに呼び出すことができます。Candid とMotoko の間の型マッピングに関する追加情報については、「[サポートされる型](/references/candid-ref.md)」のリファレンス・セクションを参照してください。

Motoko コンパイラと`dfx build` コマンドは、他のcanisters やツールが`hello` canister とシームレスにやり取りできるように、`hello` canister の Candid 記述も自動生成します。生成された Candid 記述は、プロジェクトのビルドディレクトリ`.dfx/local/canisters/hello/hello.did` にあります。

## Rust からのサービスとの対話canister

Rust でcanister を記述する場合、`dfx build` コマンドはサービス記述が必要な場所で適切に参照されるようにします。ただし、Candidのサービス記述は、[Candidの仕様に](https://github.com/dfinity/candid/blob/master/spec/Candid.md#core-grammar)記載されている規約に従って手動で記述する必要があります。

例えば、`counter` canister を呼び出す`hello` canister を Rust で記述したい場合：

``` rust
use ic_cdk_macros::*;

#[import(canister = "counter")]
struct Counter;

#[update]
async fn greet() -> String {
    let result = Counter::inc(1.into()).await;
    format!("The current counter is {}", result)
}
```

`counter` canister の import マクロ -`#[import(canister = "counter")]` 宣言 - が`dfx build` コマンドで処理されるとき、`dfx build` コマンドは`counter` canister 識別子と Candid 記述が Rust CDK に正しく渡されるようにします。Rust CDKは、Candid型を適切なRustネイティブ型に変換します。この変換により、`counter` canister が異なる言語で実装されていても、インポートされたcanister のソースコードを持っていなくても、`inc` メソッドを Rust 関数のようにネイティブに呼び出すことができます。CandidとRustの間の型マッピングに関する追加情報については、[サポートされている型の](/references/candid-ref.md)リファレンスセクションを参照してください。

他のcanisters やツールが`hello` canister と相互作用するためには、手動で`.did` ファイルを作成する必要があります：

``` candid
service : {
    greet : () -> (text);
}
```

また、Candidのサービス記述を自動的に生成する実験的な機能もあります。

Rust で Candid サービスやcanisters を作成するための追加情報やライブラリについては、[Candid クレート](https://docs.rs/candid/)、[Rust CDK サンプル](https://github.com/dfinity/cdk-rs/tree/next/examples)、[Rust チュートリアルの](../rust/index.md)ドキュメントを参照してください。

## JavaScriptからサービスと対話

[dfinity](https://www.npmjs.com/package/@dfinity/agent)/agent[npm パッケージには](https://www.npmjs.com/package/@dfinity/agent)、Candid を使用してcanisters をインポートするためのサポートが含まれています。

例えば、`counter` canister を呼び出したい場合、以下のJavaScriptプログラムを書くことができます：

``` javascript
import counter from 'ic:canisters/counter';
import BigNumber from 'bignumber.js';
(async () => {
  const result = await counter.inc(new BigNumber(42));
  console.log("The current counter is " + result.toString());
})();
```

カウンタcanister のインポート依存性が`dfx build` コマンドと`webpack` 設定によって処理されるとき、この処理によってcanister 識別子と Candid の説明が JavaScript プログラムに正しく渡されることが保証されます。舞台裏では、Candid サービス記述は`dfx build` によって`.dfx/local/canister/counter/counter.did.js` にある JavaScript モジュールに変換されます。`dfinity/agent` `counter` canister が異なる言語で実装されていても、インポートされたcanister のソースコードを持っていなくても、`inc` メソッドをネイティブに呼び出すことができます。Candid と JavaScript 間の型マッピングに関する追加情報については、[サポートされている型の](/references/candid-ref.md)リファレンスセクションを参照してください。

## 新しい Candid 実装の作成

Motoko 、Rust、JavaScript 用の Candid 実装に加え、以下のホスト言語用の Candid ライブラリがコミュニティによってサポートされています：

- [アセンブリスクリプト](https://github.com/rckprtr/cdk-as/tree/master/packages/cdk/assembly/candid)

- [Elm](https://github.com/chenyan2002/ic-elm/)

- [Haskell](https://hackage.haskell.org/package/candid)

- [Java](https://github.com/ic4j/ic4j-candid)

- [Kotlin](https://github.com/seniorjoinu/candid-kt)

現在実装が提供されていない言語やツールをサポートするためにCandidの実装を作成したい場合は、[Candidの仕様を](https://github.com/dfinity/candid/blob/master/spec/Candid.md)参照してください。

新しい言語やツールのためにCandidの実装を追加する場合、公式の[Candidテストデータを](https://github.com/dfinity/candid/tree/master/test)使用して、実装がCandidと互換性があることをテストして検証することができます。

<!---
# Using Candid

# Overview

As discussed in the [Candid concepts](./candid-concepts.md) page, Candid provides a language-agnostic way to interact with canisters. By using Candid, you can specify input argument values and display return values from canister methods regardless of whether you interact with the IC from a terminal using the IC SDK, through a web browser, or from a program written in JavaScript, Motoko, Rust, or any other language. Now that you are familiar with what Candid is and how it works, this section provides instructions for how to use it in some common scenarios.

As a concrete example, let’s assume there is a `counter` canister already deployed on the network with the following Candid interface:

``` candid
service Counter : {
  inc : (step: nat) -> (nat);
}
```

Now, let’s explore how to interact with this canister in different scenarios with the help of Candid.

## The `.did` file

Candid types can be used to describe a service via a Candid service description file (`.did` file), which can either be manually written or generated from a service implementation. 

If you write a canister in Motoko, for example, the compiler automatically generates a Candid description when you compile the program. If you use the SDK, you will typically see the auto-generated `.did` files in the `/declarations` directory of your project. Since these files are are auto-generated, it is recommended they should not be manually edited. Even if you change the `.did` files in your project, they will be overwritten in the next dfx build.

In other languages, like Rust, you will have to write the Candid interface description manually. With the help of types, we developed tools to automatically generate UI and perform random testing based on the service description file. 

## Interact with a service in a terminal

One of the most common ways you interact with canisters and the IC is by using the IC SDK.

Within the IC SDK, the `dfx` tool provides the `dfx canister call` command to call a specific deployed canister—essentially a smart contract that runs on the IC—and, if applicable, a method of the service provided by the smart contract.

When you run the `dfx canister call` command, you can pass arguments to a method by specifying them as [Candid textual values](./candid-concepts.md#textual-values).

When you pass Candid textual values on the command-line, you can specify multiple argument values separated by commas (`,`) and wrapped inside parentheses. For example, specifying `(42, true)` represents two argument values, where the first argument is the number `42` and the second argument is a boolean value of `true`.

The following example illustrates using the `dfx canister call` command to call the `counter` canister service and pass arguments for the `inc` method:

``` bash
$ dfx canister call counter inc '(42)'
(43)
```

To figure out how to create more complex Candid arguments, please refer to the [Candid reference](/references/candid-ref.md). And for Candid arguments too long to fit the command line, please use the `--argument-file` flag of [`dfx canister call`](/references/cli-reference/dfx-canister.md#dfx-canister-call).

You can also omit the arguments and let the IC SDK generate a random value that matches the method type. For example:

``` bash
$ dfx canister call counter inc
Unspecified argument, sending the following random argument:
(1_543_454_453)

(1_543_454_454)
```

For more information about using `dfx` and the `dfx canister call` command, see [command-line reference](/references/cli-reference/index.md) and [dfx canister](/references/cli-reference/dfx-canister.md) documentation.

## Interact with a service from a browser

The Candid interface description language provides a common language for specifying the signature of a canister. Based on the type signature of the service offered by the smart contract, Candid provides a web interface—the Candid UI—that allows you to call canister functions for testing and debugging from a web browser without writing any frontend code.

To use the Candid web interface to test the `counter` canister:

- #### Step 1:  Find the Candid UI canister identifier associated with the `counter` canister using the `dfx canister id __Candid_UI` command.

    ``` bash
    dfx canister id __Candid_UI
    ```

    The command displays the canister identifier for the Candid UI with output similar to the following:

        r7inp-6aaaa-aaaaa-aaabq-cai

- #### Step 2:  Start the canister execution environment locally by running the following command:

    ``` bash
    dfx start --background
    ```

- #### Step 3:  Open a browser and navigate to the address and port number specified in the `dfx.json` configuration file.

    By default, the `local` canister execution environment binds to the `127.0.0.1:4943` address and port number.

- #### Step 4:  Add the required `canisterId` parameter and the Candid UI canister identifier returned by the `dfx canister id` command.

    For example, the full URL should look similar to the following but with the `CANDID-UI-CANISTER-IDENTIFIER` that was returned by the `dfx canister id` command:

        http://127.0.0.1:4943/?canisterId=<CANDID-UI-CANISTER-IDENTIFIER>

    The browser displays a form for you to specify a canister identifier or choose a Candid description (`.did`) file.

    ![candid ui select id](_attachments/candid-ui-select-id.png)

    If you aren’t sure which canister identifier to use, you can run the `dfx canister id` command to look up the identifier for a specific canister name.

    For example, if you want to view the functions for the `counter` service description, you could look up the canister identifier by running the following command:

        dfx canister id counter

- #### Step 5:  Specify a canister identifier or description file, then click **Go** to display the service description.

- #### Step 6:  Review the list of function calls and types defined in the program.

- #### Step 7:  Type a value of the appropriate type for a function or click **Random** to generate a value, then click **Call** or **Query** to see the result.

For more information about the tool that creates a Web interface from the Candid interface of any canister, see the [Candid UI](https://github.com/dfinity/candid/tree/master/tools/ui) repository.

## Interact with a service from a Motoko canister

If you are writing a canister in Motoko, the Motoko compiler automatically translates the signature of your canister’s top-level `actor` or `actor class` into a Candid description, and the `dfx build` command ensures that the service description is properly referenced where it needs to be.

For example, if you want to write a `hello` canister that calls the `counter` canister in Motoko:

``` motoko
import Counter "canister:Counter";
import Nat "mo:base/Nat";
actor {
  public func greet() : async Text {
    let result = await Counter.inc(1);
    "The current counter is " # Nat.toText(result)
  };
}
```

In this example, when the import dependency on the `counter` canister— the `import Counter "canister:Counter"` declaration—is processed by the `dfx build` command, the `dfx build` command ensures that the `counter` canister identifier and the Candid description are passed to the Motoko compiler correctly. The Motoko compiler then translates the Candid type into the appropriate native Motoko type. This translation enables you to call the `inc` method natively—as if it were a Motoko function—even if the `counter` canister is implemented in a different language and even if you do not have the source code for the imported canister. For additional information on the type mapping between Candid and Motoko, you can consult the [supported types](/references/candid-ref.md) reference section.

The Motoko compiler and `dfx build` command also auto-generate the Candid description for the `hello` canister to allow other canisters or tools to interact with the `hello` canister seamlessly. The generated Candid description is located in your project build directory at `.dfx/local/canisters/hello/hello.did`.

## Interact with a service from a Rust canister

If you write a canister in Rust, the `dfx build` command ensures that the service description is properly referenced where it needs to be. However, you need to write the Candid service description manually following the conventions described in the [Candid specification](https://github.com/dfinity/candid/blob/master/spec/Candid.md#core-grammar).

For example, if you want to write a `hello` canister that calls the `counter` canister in Rust:

``` rust
use ic_cdk_macros::*;

#[import(canister = "counter")]
struct Counter;

#[update]
async fn greet() -> String {
    let result = Counter::inc(1.into()).await;
    format!("The current counter is {}", result)
}
```

When the import macro on the `counter` canister— the `#[import(canister = "counter")]` declaration—is processed by the `dfx build` command, the `dfx build` command ensures that the `counter` canister identifier and the Candid description are passed to the Rust CDK correctly. The Rust CDK then translates the Candid type into the appropriate native Rust type. This translation enables you to call the `inc` method natively—as if it were a Rust function—even if the `counter` canister is implemented in a different language and even if you do not have the source code for the imported canister. For additional information on the type mapping between Candid and Rust, you can consult the [supported types](/references/candid-ref.md) reference section.

For other canisters and tools to interact with the `hello` canister, you need to manually create a `.did` file:

``` candid
service : {
    greet : () -> (text);
}
```

There is also an experimental feature to generate a Candid service description automatically, see this [test case](https://github.com/dfinity/candid/blob/master/rust/candid/tests/types.rs#L99) as an example.

For additional information and libraries to help you create Candid services or canisters in Rust, see the documentation for the [Candid crate](https://docs.rs/candid/), [Rust CDK examples](https://github.com/dfinity/cdk-rs/tree/next/examples) and the [Rust tutorials](../rust/index.md).

## Interact with a service from JavaScript

The [dfinity/agent npm package](https://www.npmjs.com/package/@dfinity/agent) includes support for importing canisters using Candid.

For example, if you want to call the `counter` canister, you can write the following JavaScript program:

``` javascript
import counter from 'ic:canisters/counter';
import BigNumber from 'bignumber.js';
(async () => {
  const result = await counter.inc(new BigNumber(42));
  console.log("The current counter is " + result.toString());
})();
```

When the import dependency of counter canister is processed by the `dfx build` command and the `webpack` configuration, this processing ensures that the canister identifier and the Candid description are passed to the JavaScript program correctly. Behind the scenes, the Candid service description is translated into a JavaScript module, located at `.dfx/local/canister/counter/counter.did.js`, by `dfx build`. The `dfinity/agent` package then translates the Candid type into native JavaScript values and enables you to call the `inc` method natively—as if it were a JavaScript function—even if the `counter` canister is implemented in a different language and even if you do not have the source code for the imported canister. For additional information on the type mapping between Candid and JavaScript, you can consult the [supported types](/references/candid-ref.md) reference section.

## Create a new Candid implementation

In addition to the Candid implementations for Motoko, Rust, and JavaScript, there are community-supported Candid libraries for the following host languages:

-   [AssemblyScript](https://github.com/rckprtr/cdk-as/tree/master/packages/cdk/assembly/candid)

-   [Elm](https://github.com/chenyan2002/ic-elm/)

-   [Haskell](https://hackage.haskell.org/package/candid)

-   [Java](https://github.com/ic4j/ic4j-candid)

-   [Kotlin](https://github.com/seniorjoinu/candid-kt)

If you want to create a Candid implementation to support a language or tool for which an implementation is not currently available, you should consult the [Candid specification](https://github.com/dfinity/candid/blob/master/spec/Candid.md).

If you add a Candid implementation for a new language or tool, you can use the official [Candid test data](https://github.com/dfinity/candid/tree/master/test) to test and verify that your implementation is compatible with Candid, even in slightly more obscure corner cases.

-->
