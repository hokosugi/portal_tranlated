# キャンディッドとは？

## 概要

Candidは**インターフェース記述言語**です。その主な目的は、**サービスの**パブリックインターフェースを記述することです。 **canister**Internet ComputerCandidの主な利点の1つは、言語にとらわれず、Motoko 、Rust、JavaScriptなどの異なるプログラミング言語で書かれたサービスとフロントエンド間の相互運用を可能にすることです。

Candidの典型的なインターフェース記述は以下のようになります：

``` candid
service counter : {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

この例では、`counter` のサービスは以下のパブリックメソッドで構成されています：

- `add` と`subtract` メソッドはカウンターの値を変更します。

- `get` メソッドはカウンタの現在値を読み取ります。

- `subscribe` メソッドは、カウンタの値が変更されるたびに通知コールバック・メソッドを呼び出すなど、別の関数を呼び出すために使用できます。

この例が示すように、すべてのメソッドには一連の引数型と結果型があります。メソッドには、この例で示した`query` 表記のように、Internet Computer に固有の注釈を含めることもできます。

このシンプルなインターフェイスの説明から、コマンドラインやウェブベースのフロントエンドから直接この`counter` サービスと対話したり、Rust プログラムや他のプログラミング言語やスクリプト言語からプログラム的に対話したりすることが可能です。

相互運用性に加え、Candidは既存のクライアントを壊すことなく行える変更を正確に指定することで、サービスインターフェースの進化をサポートします。例えば、既存のクライアントとの互換性を失うことなく、サービスに新しいオプションパラメータを安全に追加することができます。

## なぜ新しいIDLを作成するのですか？

一見すると、JSON、XML、Protobufなどの他の技術で十分だと思うかもしれません。しかし、Candidはこれらの他のテクノロジーにはないユニークな機能の組み合わせを提供します。Candidが特にInternet Computer のdapps の開発に適している特徴は以下の通りです：

- JSON、XML、Protobufのような多くの言語は、個々の値をバイトや文字にマッピングする方法のみを記述します。これらのデータ記述言語は、サービス全体を記述しません。これらの言語は、それらのデータ型を使用するメソッドではなく、転送したいデータ型に焦点を当てます。

- Candid の実装では、Candid の値をホスト言語の型や値に直接マッピングします。Candid では、開発者は抽象的な`Candid` 値を構築したり分解したりすることはありません。

- Candid は、サービスとそのインターフェースを健全かつ構成的な方法でアップグレードする方法のルールを定義します。

- Candidは本質的に高階言語です。キャンディッドでは、サービスやメソッドへの参照を含め、プレーンなデータ以上のものを渡すことができます。Candidの安全なアップグレードのサポートは、このような高次の使用を考慮に入れています。

- Candid には、`query` アノテーションのような、特定のInternet Computer 機能のサポートが組み込まれています。

## Candidの型と値

Candidは、ほとんどの用途を標準的にカバーする型のセットを持つ、強く型付けされたシステムです。これには以下があります：

- 非有界整数型 (`nat`,`int`).

- 有界整数型 (`nat8`,`nat16`,`nat32`,`nat64`,`int8`,`int16`,`int32`,`int64`).

- 浮動小数点型 (`float32`,`float64`).

- ブール型 (`bool`).

- テキストデータ (`text`) およびバイナリデータ (`blob`) の型。

- コンテナ型（変種を含む）(`opt`,`vec`,`record`,`variant`)。

- 参照型 (`service`,`func`,`principal`)。

- 特殊な`null`,`reserved`,`empty` 型。

すべての型は[リファレンスの](/references/candid-ref.md)セクションで詳しく説明されています。

この型のセットの背後にある哲学は、情報が符号化され、引き渡され、復号化されることができるように、データの**構造を**記述するのに十分であるということです。例えば、数値が偶数であること、ベクトルが一定の長さを持つこと、ベクトルの要素がソートされていることを表現する方法はありません。

Candidは、Motoko 、Rust、JavaScript、またはその他の言語でコードを書いているかどうかにかかわらず、各ホスト言語に適した合理的で標準的な選択に基づくデータ型の自然なマッピングを可能にするために、この型のセットをサポートしています。

## Candidサービスの説明

Candid のデータ型に慣れたら、それらを使用してサービスを記述することができます。Candid サービス記述ファイル（`.DID` ファイル）は、手書きまたはサービス実装から生成することができます。

特定のホスト言語用のサービス記述を生成する方法を調べる前に、サンプルサービス記述の構造とその構成部分を詳しく見てみましょう。

最も単純なサービス記述は、パブリックメソッドを持たないサービスを指定するもので、次のようになります：

``` candid
service : {}
```

このサービスはあまり役に立たないので、単純な`ping` メソッドを追加しましょう：

``` candid
service : {
  ping : () -> ();
}
```

この例では、`ping` という単一のパブリックメソッドをサポートするサービスを記述します。メソッド名は任意の文字列を使用することができ、プレーンな識別子でない場合は引用符 (`"method with spaces"`) で囲むことができます。

メソッドは**一連の**引数と結果の型を宣言します。この`ping` メソッドの場合、引数は渡されず、結果も返されないので、引数にも結果にも空のシーケンス \`() \` が使われます。

最も単純なケースを見たところで、もう少し複雑なサービスの記述について考えてみましょう。このサービスは`reverse` と`divMod` の2つのメソッドから構成され、各メソッドには引数と結果の型のシーケンスが含まれます：

``` candid
service : {
  reverse : (text) -> (text);
  divMod : (dividend : nat, divisor : nat) -> (div : nat, mod : nat);
}
```

メソッド`reverse` は、型`text` のパラメータを1つ受け取り、型`text` の値を1つ返します。

メソッド`divMod` は、すべて型`nat` の 2 つの値を期待し、返します。

## 引数と結果の命名

前の例では、`divMod` メソッドのシグニチャに、引数と結果の値の名前が含まれています。メソッドの引数や結果に名前をつけるのは、純粋にドキュメンテーションのためです。使用する名前は、メソッドの型や渡される値を変更するものではありません。その代わり、引数や結果は名前とは関係なく、その**位置によって**識別されます。

特に、Candidは、型を：

``` candid
  divMod : (dividend : nat, divisor : nat) -> (mod : nat, div : nat);
```

に変更することや、`mod` を最初に返すメソッドを期待するサービスに上記の`divMod` を渡すことを妨げることはありません。

このように、意味的に関連する名前付き**レコード**フィールドとは大きく異なります。

## 複雑な型の再利用

サービス内の複数のメソッドが同じ複合型を参照することがよくあります。その場合、型に名前を付けて複数回再利用することができます。例えば

``` candid
type address = record {
  street : text;
  city : text;
  zip_code : nat;
  country : text;
};
service address_book : {
  set_address: (name : text, addr : address) -> ();
  get_address: (name : text) -> (opt address) query;
}
```

これらの型定義は、**既存の**型を単に省略したものであり、新しい型を定義したものではありません。関数のシグネチャで`address` を使おうが、レコードを書き出そうが関係ありません。また、名前は異なるが同等の定義を持つ2つの省略形は、同じ型を記述し、互換性があります。つまり、Candidは**構造的な**型付けを行います。

## クエリ・メソッドの指定

最後の例では、`get_address` メソッドに`query` アノテーションが使用されていることにお気づきかもしれません。例えば

``` candid
service address_book : {
  set_address: (name : text, addr : address) -> ();
  get_address: (name : text) -> (opt address) query;
}
```

このアノテーションは、`get_address` メソッドが IC**クエリーコールとして**呼び出されることを示します。[クエリおよび更新メソッドで](/concepts/canisters-code.md#query-and-update-methods)説明したように、クエリはコンセンサスを経ずにcanister から情報を取得する効率的な方法を提供します。したがって、メソッドをクエリとして識別できることは、IC との対話に Candid を使用する主な利点の 1 つです。

## エンコードとデコード

Candidのポイントは、バイナリ形式にエンコードされ、基礎となる転送方法（Internet Computer への、または 内のメッセージなど）によって転送され、反対側でデコードされた引数を渡す、サービスメソッドのシームレスな呼び出しを可能にすることです。

Candidユーザーとして、このバイナリーフォーマットの詳細を心配する必要はありません。新しいホスト言語をサポートするなど）Candid を自分で**実装**する予定がある場合は、[Candid の仕様を](https://github.com/dfinity/candid)参照して詳細を確認してください。しかし、フォーマットのいくつかの側面は知っておく価値があります：

- Candidバイナリ・フォーマットは`DIDL…` （16進数では`4449444c…` ）で始まります。低レベルのログ出力でこれを見た場合、Candidでエンコードされた値である可能性が非常に高いです。

- メソッドのパラメータと結果は型のシーケンスであるため、Candidのバイナリフォーマットは常に値の*シーケンスを*エンコードします。

- バイナリフォーマットは非常にコンパクトです。125,000エントリーの`(vec nat64)` は1,000,007バイトです。

- バイナリは自己記述的で、その中の値の型の(凝縮された)型記述を含みます。これによって、受信側は、メッセージが異なる、互換性のない型として送信されたかどうかを検出することができます。

- 送信側が引数を受信側が期待する型としてシリアライズしている限り、デシリアライズは成功します。

## サービスのアップグレード

新しいメソッドが追加されたり、既存のメソッドがより多くのデータを返したり、引数が追加されたりします。通常、開発者は既存のクライアントを壊すことなくこれを行いたいと考えます。

Candidは、新しいサービスタイプが以前のインターフェイス記述を使用している他のすべての関係者と通信できるようになるタイミングを示す正確なルールを定義することで、このような進化をサポートします。基礎となるフォーマリズムは*サブタイプ化*です。

サービスは以下の方法で安全に進化することができます：

- 新しいメソッドを追加することができます。

- 新しいメソッドを追加することができます。既存のメソッドは、追加の値を返すことができます。古いクライアントは、単に追加の値を無視します。

- 既存のメソッドはパラメータリストを短縮できます。古いクライアントは余分な引数を送ることができますが、無視されます。

- 既存のメソッドは、オプションの引数 (`opt …` 型) でパラメータ・リストを拡張できます。その引数を渡さない古いクライアントからのメッセージを読むときは、`null` の値が想定されます。

- 既存のパラメータ型は*変更*することができますが、変更前の型の*スーパータイプに*限ります。

- 既存の結果型は*変更する*ことができますが、変更前の型の*サブタイプにのみ*変更することができます。

ある型のスーパータイプとサブタイプについては、その型の[リファレンス・](/references/candid-ref.md)セクションを参照してください。

サービスがどのように進化していくのか、具体的な例を見てみましょう。以下のAPIを持つサービスを考えてみましょう：

``` candid
service counter : {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

このサービスは以下のインターフェースに進化することができます：

``` candid
type timestamp = nat;
service counter : {
  set : (nat) -> ();
  add : (int) -> (new_val : nat);
  subtract : (nat, trap_on_underflow : opt bool) -> (new_val : nat);
  get : () -> (nat, last_change : timestamp) query;
  subscribe : (func (nat) -> (unregister : opt bool)) -> ();
}
```

## Candidテキスト値

Candidの主な目的は、例えばMotoko 、Rust、JavaScriptなどのホスト言語で書かれたプログラムをICに接続することです。したがって、ほとんどの場合、プログラムのデータをCandidの値として扱う必要はありません。その代わりに、JavaScript のようなホスト言語で使い慣れた JavaScript の値を使用して作業し、Candid を使用してそれらの値を Rust またはMotoko で記述されたcanister に透過的に転送します。値を受信したcanister は、それらをネイティブの Rust またはMotoko の値として扱います。

しかし、ロギング、デバッグ、コマンドラインでのサービスとのやり取りなど、Candidの値を人間が読める形式で直接確認することが有用な場合もあります。このようなシナリオでは、Candid値の**テキスト表示を**使用できます。

構文は Candid 型の構文に似ています。例えば、Candid 値の典型的なテキスト表示は以下のようになります：

``` candid
(record {
  first_name = "John";
  last_name = "Doe";
  age = 14;
  membership_status = variant { active };
  email_addresses =
    vec { "john@doe.com"; "john.doe@example.com" };
})
```

:::info
Candidの**バイナリ**形式には実際のフィールド名は含まれず、単に数値**ハッシュが**含まれます。そのため、期待される型の知識なしにこのような値をきれいに印刷しても、レコードやバリアントのフィールド名は含まれません。上記の値は以下のように出力されます：

``` candid
(record {
   4846783 = 14;
   456245371 = variant {373703110};
   1443915007 = vec {"john@doe.com"; "john.doe@example.com"};
   2797692922 = "John"; 3046132756 = "Doe"
})
```

:::

## サービス説明の生成

[上記の](#candid-service-descriptions)セクションでは、ゼロから Candid サービス説明を書く方法を学びました。しかし、多くの場合それは必要ありません。サービスを実装するために使用する言語によっては、コードから Candid サービス記述を生成することができます。

例えば、Motoko では、canister のように記述することができます：

``` motoko
actor {
  var v : Int = 0;
  public func add(d : Nat) : async () { v += d; };
  public func subtract(d : Nat) : async () { v -= d; };
  public query func get() : async Int { v };
  public func subscribe(handler : func (Int) -> async ()) { … }
}
```

このプログラムをコンパイルすると、Motoko コンパイラーは上記のインターフェイスを持つ Candid サービス記述ファイルを自動的に生成します。

Rust や C のような他の言語でも、例えば Rust ネイティブ型を使用するなど、その言語のネイティブ型を使用してサービスを開発することができます。しかし、Rustのような言語でサービスを開発した後、Candidでサービス記述を自動生成する方法は今のところありません。そのため、RustやCでサービスのプログラムを書く場合は、Candidの[仕様に](https://github.com/dfinity/candid)記載されている規約に従って、Candidのインターフェイス記述を手動で記述する必要があります。

Rustプログラム用のCandidサービス記述の書き方の例については、[Rust CDKの例と](https://github.com/dfinity/cdk-rs/tree/next/examples) [Rustチュートリアルを](../rust/index.md)参照してください。

使用するホスト言語に関係なく、ホスト言語の型と Candid の型のマッピングを知っておくことが重要です。[サポートされている型の](/references/candid-ref.md)リファレンスセクションでは、Motoko 、Rust、JavaScriptのCandid型マッピングが説明されています。

<!---
# What is Candid?

## Overview
Candid is an **interface description language**. Its primary purpose is to describe the public interface of a **service**, usually in the form of a program deployed as a **canister** that runs on the Internet Computer. One of the key benefits of Candid is that it is language-agnostic, and allows inter-operation between services and frontends written in different programming languages, including Motoko, Rust, and JavaScript.

A typical interface description in Candid might look like this:

``` candid
service counter : {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

In this example, the described service `counter` consists of the following public methods:

- The `add` and `subtract` methods change the value of a counter. 

- The `get` method reads the current value of a counter. 

- The `subscribe` method can be used to invoke another function, for example, to invoke a notification callback method each time the counter value changes.

As this example illustrates, every method has a sequence of argument and result types. A method can also include annotations, like the `query` notation shown in this example, that are specific to the Internet Computer.

Given this simple interface description, it is possible for you to interact with this `counter` service directly from the command line or through a web-based frontend or programmatically from a Rust program or through another programming or scripting language.

In addition to interoperability, Candid supports the evolution of service interfaces by precisely specifying the changes that can be made without breaking existing clients. For example, you can safely add new optional parameters to a service without losing compatibility for existing clients.

## Why create a new IDL?

At first glance, you might think that other technologies, such as JSON, XML, or Protobuf, would suffice. However, Candid provides a unique combination of features that are not found in these other technologies. The features that make Candid particularly well-suited for developing dapps for the Internet Computer include the following:

-   Many languages like JSON, XML, and Protobuf only describe how to map individual values to bytes or characters. These data description languages do not describe services as a whole. These languages focus on the data types you want to transfer instead of the methods that make use of those data types.

-   Candid implementations map the Candid value directly to types and values of the host language. With Candid, developers do not construct or deconstruct some abstract `Candid` value.

-   Candid defines rules for how services and their interface can be upgraded in a sound and compositional way.

-   Candid is inherently a higher-order language. With Candid, you can pass more than plain data, including references to services and methods. Candid support for safe upgrades takes such higher-order use into account.

-   Candid has built-in support for specific Internet Computer features, such as the `query` annotation.

## Candid types and values

Candid is a strongly typed system with a set of types that canonically cover most uses. It has:

-   Unbounded integral number types (`nat`, `int`).

-   Bounded integral number (`nat8`,`nat16`, `nat32`, `nat64`, `int8`,`int16`, `int32`, `int64`).

-   Floating point types (`float32`, `float64`).

-   The Boolean type (`bool`).

-   Types for textual (`text`) and binary (`blob`) data.

-   Container types, including variants (`opt`, `vec`, `record`, `variant`).

-   Reference types (`service`, `func`, `principal`).

-   The special `null`, `reserved` and `empty` types.

All types are described in detail in the [reference](/references/candid-ref.md) section.

The philosophy behind this set of types is that they are sufficient to describe the **structure** of data, so that information can be encoded, passed around and decoded, but intentionally do not describe **semantic** constraints beyond what’s needed to describe the representation. For example, there’s no way to express that a number should be even, that a vector has a certain length, or that the elements of a vector are sorted.

Candid supports this set of types to allow a natural mapping of data types based on reasonable, canonical choices suitable for each host language, whether you are writing your code in Motoko, Rust, JavaScript, or some other language.

## Candid service descriptions

Once you are familiar with the Candid types, you can use them to describe a service. A Candid service description file (a `.DID` file) can either be written by hand or generated from a service implementation.

Before you explore how to generate service descriptions for a specific host language, let’s take a closer look at the structure of a sample service description and its constituent parts.

The simplest service description specifies a service with no public methods and would look like this:

``` candid
service : {}
```

This service is not very useful, so let’s add a simple `ping` method:

``` candid
service : {
  ping : () -> ();
}
```

This example describes a service that supports a single public method called `ping`. Method names can be arbitrary strings, and you can quote them (`"method with spaces"`) if they are not plain identifiers.

Methods declare a **sequence** of arguments and result types. In the case of this `ping` method, no arguments are passed and no results are returned, so the empty sequence \`() \` is used for both arguments and results.

Now that you’ve seen the simplest case, let’s consider a slightly more complex service description. This service consists of two methods, `reverse` and `divMod`, and each method includes a sequence of argument and result types:

``` candid
service : {
  reverse : (text) -> (text);
  divMod : (dividend : nat, divisor : nat) -> (div : nat, mod : nat);
}
```

The method `reverse` expects a single parameter of type `text` and returns one value of type `text`.

The method `divMod` expects and returns two values, all of type `nat`.

## Naming arguments and results

In the previous example, the signature for the `divMod` method includes names for the argument and result values. Naming the arguments or results for a method is purely for documentation purposes. The name you use does not change the method’s type or the values being passed. Instead, arguments and results are identified by their **position**, independent of the name.

In particular, Candid does not prevent you from changing the type to:

``` candid
  divMod : (dividend : nat, divisor : nat) -> (mod : nat, div : nat);
```

or passing the above `divMod` to a service expecting a method that returns `mod` first.

This is thus very different from named **record** fields, which are semantically relevant.

## Reusing complex types

Often, multiple methods in a service may refer to the same complex type. In that case, the type can be named and reused multiple times. For example:

``` candid
type address = record {
  street : text;
  city : text;
  zip_code : nat;
  country : text;
};
service address_book : {
  set_address: (name : text, addr : address) -> ();
  get_address: (name : text) -> (opt address) query;
}
```

These type definitions merely abbreviate an **existing** type, they do not define a new type. It does not matter whether you use `address` in the function signature, or write out the records. Also, two abbreviations with different names but equivalent definitions, describe the same type and are interchangeable. In other words, Candid uses **structural** typing.

## Specifying a query method

In the last example, you might have noticed the use of the `query` annotation for the `get_address` method. For example:

``` candid
service address_book : {
  set_address: (name : text, addr : address) -> ();
  get_address: (name : text) -> (opt address) query;
}
```

This annotation indicates that the `get_address` method can be invoked as an IC **query call**. As discussed in [query and update methods](/concepts/canisters-code.md#query-and-update-methods), a query provides an efficient way to retrieve information from a canister without going through consensus, so being able to identify a method as a query is one of the key benefits of using Candid to interact with the IC.

## Encoding and decoding

The point of Candid is to allow seamless invocation of service methods, passing arguments encoded to a binary format and transferred by an underlying transportation method (such as messages into or within the Internet Computer), and decoded on the other side.

As a Candid user, you do not have to worry about the details of this binary format. If you plan to **implement** Candid yourself (for example, to support a new host language), you can consult the [Candid specification](https://github.com/dfinity/candid) for details. However, some aspects of the format are worth knowing:

-   The Candid binary format starts with `DIDL…` (or, in hex, `4449444c…`). If you see this in some low-level log output, you are very likely observing a Candid-encoded value.

-   The Candid binary format always encodes *sequences* of values, because methods parameters and results are sequences of types.

-   The binary format is quite compact. A `(vec nat64)` with 125 000 entries takes 1 000 007 bytes.

-   The binary is self-describing, and includes a (condensed) type description of type of the values therein. This allows the receiving side to detect if a message was sent at as a different, incompatible type.

-   As long as the sender serializes the arguments as the type that the receiving side expects, deserialization will succeed.

## Service upgrades

Services evolve over time; they gain new methods, existing methods return more data, or expect additional arguments. Usually, developers want to do that without breaking existing clients.

Candid supports such evolution by defining precise rules that indicate when the new service type will still be able to communicate with all other parties that are using the previous interface description. The underlying formalism is that of *subtyping*.

Services can safely evolve in the following ways:

-   New methods can be added.

-   Existing methods can return additional values, that is, the sequence of result types can be extended. Old clients will simply ignore additional values.

-   Existing methods can shorten their parameter list. Old clients may still send the extra arguments, but they will be ignored.

-   Existing methods can extend their parameter list with optional arguments (type `opt …`). When reading messages from old clients, who do not pass that argument, a `null` value is assumed.

-   Existing parameter types may be *changed*, but only to a *supertype* of the previous type.

-   Existing result types may be *changed*, but only to a *subtype* of the previous type.

For information about the supertypes and subtypes of a given type, see the corresponding [reference](/references/candid-ref.md) section for that type.

Let’s look at a concrete example of how a service might evolve. Consider a service with the following API:

``` candid
service counter : {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

This service can evolve to the following interface:

``` candid
type timestamp = nat;
service counter : {
  set : (nat) -> ();
  add : (int) -> (new_val : nat);
  subtract : (nat, trap_on_underflow : opt bool) -> (new_val : nat);
  get : () -> (nat, last_change : timestamp) query;
  subscribe : (func (nat) -> (unregister : opt bool)) -> ();
}
```

## Candid textual values

The main purpose of Candid is to connect programs written in some host language—Motoko, Rust, or JavaScript, for example—with the IC. In most cases, therefore, you do not have to deal with your program data as Candid values. Instead, you work with a host language like JavaScript using familiar JavaScript values then rely on Candid to transparently transport those values to a canister written in Rust or Motoko. The canister receiving the values treats them as native Rust or Motoko values.

However, there are some cases, for example, when logging, debugging, or interacting with a service on the command-line, where it is useful to see the Candid values directly in a human-readable form. In these scenarios, you can use the **textual presentation** for Candid values.

The syntax is similar to that for Candid types. For example, a typical textual presentation for a Candid value might look like this:

``` candid
(record {
  first_name = "John";
  last_name = "Doe";
  age = 14;
  membership_status = variant { active };
  email_addresses =
    vec { "john@doe.com"; "john.doe@example.com" };
})
```

:::info 
The Candid **binary** format does not include the actual field names, merely numeric **hashes**. So pretty-printing such a value without knowledge of the expected type will not include the field names of records and variants. The above value might then be printed as follows:

``` candid
(record {
   4846783 = 14;
   456245371 = variant {373703110};
   1443915007 = vec {"john@doe.com"; "john.doe@example.com"};
   2797692922 = "John"; 3046132756 = "Doe"
})
```

:::

## Generating service descriptions

In the [section above](#candid-service-descriptions), you learned how to write a Candid service description from scratch. But often, that is not even needed. Depending on the language you use to implement your service, you can get the Candid service description generated from your code.

For example, in Motoko, you can write a canister like this:

``` motoko
actor {
  var v : Int = 0;
  public func add(d : Nat) : async () { v += d; };
  public func subtract(d : Nat) : async () { v -= d; };
  public query func get() : async Int { v };
  public func subscribe(handler : func (Int) -> async ()) { … }
}
```

When you compile this program, the Motoko compiler automatically generates a Candid service description file with the interface shown above.

In other languages, like Rust or C, you can still develop your service using the types that are native to that language, for example, using native Rust types. After you develop a service in a language like Rust, however, there’s currently no way to automatically generate the service description in Candid. Therefore, if you write a program for a service in Rust or C, you need to write the Candid interface description manually following the conventions described in the [Candid specification](https://github.com/dfinity/candid).

For examples of how to write Candid service descriptions for Rust programs, see the [Rust CDK examples](https://github.com/dfinity/cdk-rs/tree/next/examples) and the [Rust tutorials](../rust/index.md).

Regardless of the host language you use, it is important to know the mapping between host language types and Candid types. In the [supported types](/references/candid-ref.md) reference section, you’ll find Candid type mapping described for Motoko, Rust, and JavaScript.

-->
