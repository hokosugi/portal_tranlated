# 参考資料

## 概要

このドキュメントでは、Candid がサポートする型に関する詳細なリファレンス情報と、Candid 仕様書および Candid Rust クレートに関するドキュメントへのリンクを提供します。

- サポートさ[れる](#supported-types)型

- [Candid 仕様](https://github.com/dfinity/candid)

- [Candid Rustクレート](https://docs.rs/candid)

Candid を使い始める前にさらに情報をお探しの場合は、以下の関連リソースをご覧ください：

- Candidが[アプリケーション・インターフェースの共通言語を提供する方法（ビデオ）](https://www.youtube.com/watch?v=O2KaWRtsqHg)。

## サポートされる型

このセクションでは、Candid がサポートするすべての型をリストします。各タイプについて、リファレンスには以下の情報が含まれています：

- 型の構文および型のテキスト表現の構文。

- 各型のアップグレード規則は、型の可能な**サブ**タイプと**スーパータイプの**観点から示されています。

- Rust、Motoko 、Javascriptでの対応する型。

サブタイプとは、メソッドの**結果を**変更できる型のことです。スーパータイプとは、メソッドの**引数を**変更できる型のことです。

このリファレンスでは、それぞれの型に関連する特定のサブタイプとスーパータイプのみをリストアップしていることに注意してください。どの型にも適用できるサブタイプとスーパータイプに関する一般的な情報は繰り返しません。例えば、このリファレンスでは`empty` をサブタイプとして挙げていません。同様に、`reserved` と`opt t` はどの型の上位型でもあるため、特定の型の上位型としてリストされていません。`empty` 、`reserved` 、`opt t` の型のサブタイプの規則の詳細については、以下のセクションを参照してください：

- [`opt t`](#type-opt)

- [`reserved`](#type-reserved)

- [`empty`](#type-empty)

## 型テキスト

`text` 型は人間が読めるテキストに使われます。より正確には、その値はunicodeコードポイントのシーケンスです（サロゲート部分を除く）。

#### 型のシンタックス

`text`

#### テキスト構文

``` candid
""
"Hello"
"Escaped characters: \n \r \t \\ \" \'"
"Unicode escapes: \u{2603} is ☃ and \u{221E} is ∞"
"Raw bytes (must be utf8): \E2\98\83 is also ☃"
```

#### 対応するMotoko 型

`Text`

#### 対応する Rust 型

`String` または`&str`

#### 対応するJavaScriptの値

`"String"`

## ブロブ型

`blob` 型はバイナリデータ、つまりバイト列に使用できます。`blob` 型を使用して記述されたインターフェースは、`vec nat8` を使用して記述されたインターフェースと互換性があります。

#### 型の構文

`blob`

#### テキスト構文

`blob <text>`

ここで`<text>` は、すべての文字が utf8 エンコードされたテキストリテラルと、任意のバイト列 (`"\CA\FF\FE"`) を表します。

テ キ ス ト 型に関す る 詳 し い情報は[text](#type-text) を参照 し て く だ さ い。

#### サブタイプ

`vec nat8`および`vec nat8` のすべてのサブタイプ。

#### スーパータイプ

`vec nat8`および`vec nat8` のすべてのスーパータイプ。

#### 対応するMotoko 型

`Blob`

#### 対応するRust型

`Vec<u8>` または`&[u8]`

#### 対応する JavaScript の値

`[ 1, 2, 3, 4, ... ]`

## nat 型

`nat` 型は、すべての自然数 (非負) を含みます。これは束縛されず、任意の大きな数を表すことができます。オンワイヤーエンコーディングは LEB128 ですので、小さな数も効率的に表現できます。

#### 型の構文

`nat`

#### テキスト構文

``` candid
1234
1_000_000
0xDEAD_BEEF
```

#### スーパータイプ

`int`

#### 対応するMotoko 型

`Nat`

#### 対応するRust型

`candid::Nat` または`u128`

#### 対応する JavaScript の値

`+BigInt(10000)` または`10000n`

## int 型

`int` 型はすべての整数を含みます。これは束縛されず、任意の大小の数を表すことができます。オンワイヤーエンコーディングは SLEB128 です。

#### 型の構文

`int`

#### テキスト構文

``` candid
1234
-1234
+1234
1_000_000
-1_000_000
+1_000_000
0xDEAD_BEEF
-0xDEAD_BEEF
+0xDEAD_BEEF
```

#### サブタイプ

`nat`

#### 対応するMotoko 型

`Int`

#### 対応するRust型

`candid::Int` または`i128`

#### 対応するJavaScriptの値

`+BigInt(-10000)` または`-10000n`

## 型 natN および intN

型`nat8`,`nat16`,`nat32`,`nat64`,`int8`,`int16`,`int32` および`int64` は、そのビット数の表現を持つ数値を表し、より「低レベル」なインターフェイスで使用することができます。

`natN` の範囲は`{0 …​ 2^N-1}` で、`intN` の範囲は`-2^(N-1) …​ 2^(N-1)-1` です。

オンワイヤー表現は、まさにそのビット数です。したがって、小さな値の場合、`nat64` よりも`nat` の方がスペース効率が高くなります。

#### タイプ構文

`nat8` `nat16`, , , , , または`nat32` `nat64` `int8` `int16` `int32` `int64`

#### テキスト構文

`nat8`,`nat16`,`nat32`,`nat64` については`nat` と同じ。

`int8`,`int16`,`int32`,`int64` については`int` と同じ。

型アノテーションを使用して、異なる整数型を区別できます。

``` candid
100 : nat8
-100 : int8
(42 : nat64)
```

#### 対応するMotoko 型

`natN` はデフォルトで に変換されますが、必要に応じて に変換することもできます。`NatN` `WordN` 

`intN` は に変換されます。`IntN`

#### 対応する Rust 型

対応するサイズの符号付き整数と符号なし整数。

| 長さ | 符号あり | 符号なし |
| --- | --- | --- |
| 8 ビット | i8 | u8 |
| 16ビット | i16 | u16 |
| 32ビット | i32 | u32 |
| 64ビット | i64 | u64 |

#### 対応するJavaScriptの値

8ビット、16ビット、32ビットは数値型に変換されます。

`int64` と は JavaScript の プリミティブに変換されます。`nat64` `BigInt` 

## float32 と float64 型

型`float32` と`float64` は、単精度 (32 ビット) と倍精度 (64 ビット) の IEEE 754 浮動小数点数を表します。

#### 型の構文

`float32`,`float64`

#### テキスト構文

`int` と同じ構文に加え、浮動小数点リテラルは以下の通り：

``` candid
1245.678
+1245.678
-1_000_000.000_001
34e10
34E+10
34e-10
0xDEAD.BEEF
0xDEAD.BEEFP-10
0xDEAD.BEEFp+10
```

#### 対応するMotoko 型

`float64` は に対応します。`Float`

`float32` 現在、 には表現がMotoko**ありません**。 を使用する Candid インターフェースは、 プログラムから提供したり、 プログラムから使用したりすることはできません。`float32` Motoko 

#### 対応するRust型

`f32`,`f64`

#### 対応する JavaScript の値

浮動小数点数

## 型 bool

`bool` 型は論理データ型で、`true` または`false` の値のみを持つことができます。

#### 型の構文

`bool`

#### テキスト構文

`true`,`false`

#### 対応するMotoko 型

`Bool`

#### 対応する Rust 型

`bool`

#### 対応するJavaScriptの値

`true`,`false`

## null型

`null` 型は値`null` の型であり、したがってすべての`opt t` 型のサブタイプです。また、列挙をモデル化するために[バリアントを](#type-variant)使用する場合の慣用的な選択でもあります。

#### 型の構文

`null`

#### テキスト構文

`null`

#### 上位型

すべての`opt t` 型。

#### 対応するMotoko 型

`Null`

#### 対応する Rust 型

`()`

#### 対応する JavaScript の値

`null`

## 型 vec t

`vec` 型はベクトル（シーケンス、リスト、配列）を表します。`vec t` 型の値には、`t` 型のゼロ個以上の値のシーケンスが含まれます。

#### 型の構文

`vec bool` `vec nat8`, など。`vec vec text`

#### テキスト構文

``` candid
vec {}
vec { "john@doe.com"; "john.doe@example.com" };
```

#### サブタイプ

- `t` が`t'` のサブタイプである場合、`vec t` は`vec t'` のサブタイプです。

- `blob` は のサブタイプです。`vec nat8`

#### スーパータイプ

- `t` が`t'` のスーパータイプである場合、`vec t` は`vec t'` のスーパータイプです。

- `blob` は の上位型。`vec nat8`

#### 対応するMotoko 型

`[T]`ここで、Motoko の型`T` は`t` に対応します。

#### 対応する Rust 型

`Vec<T>` または 、ここで Rust 型 は に対応します。`&[T]` `T` `t`

`vec t` は または に変換できます。`BTreeSet` `HashSet`

`vec record { KeyType; ValueType }` は あるいは に対応します。`BTreeMap` `HashMap`

#### 対応するJavaScriptの値

`Array`例えば`[ "text", "text2", …​ ]`

## 型 opt t

`opt t` 型は`t` 型のすべての値に加え、特別な`null` 値を含みます。これは、ある値がオプションであることを表現するために使用されます。つまり、データは`t` 型の値として存在するかもしれませんし、値`null` として存在しないかもしれません。

`opt` 型は入れ子にすることができ（例えば`opt opt text` ）、値`null` と`opt null` は別個の値です。

`opt` 型はCandidインタフェースの進化において重要な役割を果たし、以下に説明するような特別なサブタイプの規則を持っています。

#### 型の構文

`opt bool` `opt nat8` 、 など。`opt opt text`

#### テキスト構文

``` candid
null
opt true
opt 8
opt null
opt opt "test"
```

#### サブタイプ

`opt` を使ったサブタイプの正規ルールは以下のとおりです：

- `t` が`t'` のサブタイプである場合、`opt t` は`opt t'` のサブタイプです。

- `null` は のサブタイプ。`opt t'`

- `t` が のサブタイプである場合 ( 自体が , , である場合を除く)。`opt t` `t` `null` `opt …` `reserved`

さらに、アップグレードや高次のサービスに関連する技術的な理由から、型が一致しない場合は、**すべての**型は`opt t` のサブタイプであり、`null` が生成されます。しかし、ユーザはこのルールを直接利用しないことをお勧めします。

#### スーパータイプ

- `t` が`t'` のスーパータイプである場合，`opt t` は`opt t'` のスーパータイプです．

#### 対応するMotoko タイプ

`?T`ここで、Motoko の型`T` は`t` に対応します。

#### 対応する Rust 型

`Option<T>`対応する Rust 型`T` は`t` に対応します。

#### 対応するJavaScriptの値

`null` は に対応します。`[]`

`opt 8` は に対応します。`[8]`

`opt opt "test"` は に対応します。`[["test"]]`

## 型レコード { n : t, ... }.

`record` 型は、ラベル付けされた値のコレクションです。たとえば、次のコードは、`street` 、`city` 、`country` のテキスト・フィールドと、`zip_code` の数値フィールドを持つレコードの型に、`address` という名前を与えます。

``` candid
type address = record {
  street : text;
  city : text;
  zip_code : nat;
  country : text;
};
```

レコード型宣言におけるフィールドの順序は重要ではありません。各フィールドは異なる型を持つことができます（ベクトルとは異なります）。レコード・フィールドのラベルは、この例のように32ビットの自然数であることもできます：

``` candid
type address2 = record {
  288167939 : text;
  1103114667 : text;
  220614283 : nat;
  492419670 : text;
};
```

実際、テキスト・ラベルはその**フィールド・ハッシュとして**扱われ、ちなみに、`address` と`address2` は Candid にとって同じ型です。

ラベルを省略した場合、Candidは自動的に順次増加するラベルを割り当てます。この動作により、通常ペアやタプルを表現するために使用される以下の短縮構文が導かれます。型`record { text; text; opt bool }` 。`record { 0 : text; 1: text; 2: opt bool }`

#### 型の構文

``` candid
record {}
record { first_name : text; second_name : text }
record { "name with spaces" : nat; "unicode, too: ☃" : bool }
record { text; text; opt bool }
```

#### テキスト構文

``` candid
record {}
record { first_name = "John"; second_name = "Doe" }
record { "name with spaces" = 42; "unicode, too: ☃" = true }
record { "a"; "tuple"; null }
```

#### サブタイプ

レコードのサブタイプは、追加のフィールド（型は問わない）を持つレコード型であり、いくつかのフィールドの型がサブタイプに変更されたり、オプションのフィールドが削除されたりします。ただし、メソッドの結果でオプショナル・フィールドを削除するのは悪い習慣です。フィールドの型を`opt empty` に変更することで、このフィールドが使われなくなったことを示すことができます。

例えば、以下の型のレコードを返す関数があるとします：

``` candid
record {
  first_name : text; middle_name : opt text; second_name : text; score : int
}
```

のレコードを返す関数がある場合、それを次の型のレコードを返す関数に変更することができます：

``` candid
record {
  first_name : text; middle_name : opt empty; second_name : text; score : nat; country : text
}
```

ここで、`middle_name` フィールドを非推奨とし、`score` の型を変更し、`country` フィールドを追加しました。

#### スーパータイプ

レコードのスーパータイプは、いくつかのフィールドが削除されたり、いくつかのフィールドの型がスーパータイプに変更されたり、オプションのフィールドが追加されたレコード型です。

後者は、引数レコードを追加フィールドで拡張できるようにするものです。古いインタフェースを使用しているクライアントは、そのレコードにフィールドを含めません。これは、アップグレードされたサービスで期待される場合、`null` としてデコードされます。

例えば、型：

``` candid
record { first_name : text; second_name : text; score : nat }
```

のレコードを期待する関数がある場合、それを型のレコードを期待する関数に進化させることができます：

``` candid
record { first_name : text; score: int; country : opt text }
```

#### 対応するMotoko タイプ

レコード型がタプル（つまり、0から始まる連続したラベル）を参照するように見える場合、Motoko タプル型（たとえば、`(T1, T2, T3)` ）が使用されます。そうでない場合は、Motoko レコード`({ first_name :Text, second_name : Text })` が使用されます。

フィールド名がMotoko の予約名である場合、アンデスコアが付加されます。つまり、`record { if : bool }` は`{ if_ : Bool }` に対応します。

その場合でも）フィールド名が有効なMotoko 識別子でない場合、代わりに**フィールド・**ハッシュが使用されます：`record { ☃ : bool }` は`{ 11272781 : Boolean }` に対応します。

#### 対応する Rust 型

ユーザ定義の`struct` と`#[derive(CandidType, Deserialize)]` 特性。

`#[serde(rename = "DifferentFieldName")]` 属性を使用してフィールド名を変更できます。

レコード型がタプルの場合、`(T1, T2, T3)` のようなタプル型に変換できます。

#### 対応するJavaScriptの値

レコード・タイプがタプルの場合、値は配列に変換され、例えば`["Candid", 42]` 。

そうでない場合は、レコードオブジェクトに変換されます。たとえば、`{ "first name": "Candid", age: 42 }` 。

フィールド名がハッシュの場合、`_hash_` をフィールド名として使用します。例えば、`{ _1_: 42, "1": "test" }` 。

## 型バリアント { n : t, ... } 。

`variant` 型は、指定されたケース（**タグ**）のちょうど1つの値を表します。つまり

``` candid
type shape = variant {
  dot : null;
  circle : float64;
  rectangle : record { width : float64; height : float64 };
  "💬" : text;
};
```

は、ドット、円（半径を持つ）、長方形（寸法を持つ）、吹き出し（テキストを含む）のいずれかです。吹き出しはユニコードのラベル名(💬)の使用を示しています。

バリアントのタグはレコードのラベルと同じように、実際には数字で、文字列タグはそのハッシュ値を参照しています。

多くの場合、タグの一部または全部はデータを持ちません。その場合、`dot` のように、`null` 型を使うのが慣用的です。実際、Candidはバリアントで`: null` 型のアノテーションを省略できるようにすることで、これを奨励しています：

``` candid
type season = variant { spring; summer; fall; winter }
```

と等価です：

``` candid
type season = variant {
  spring : null; summer: null; fall: null; winter : null
}
```

と等価で、列挙を表すのに使われます。

`variant {}` 型は合法ですが、値を持ちません。そのような意図であれば、[`empty` 型の](#type-empty)方が適切かもしれません。

#### 型の構文

``` candid
variant {}
variant { ok : nat; error : text }
variant { "name with spaces" : nat; "unicode, too: ☃" : bool }
variant { spring; summer; fall; winter }
```

#### テキスト構文

``` candid
variant { ok = 42 }
variant { "unicode, too: ☃" = true }
variant { fall }
```

#### サブタイプ

バリアント型のサブタイプはいくつかのタグが削除されたバリアント型で、 いくつかのタグの型自体がサブタイプに変更されたものです。

メソッドの結果の variant に新しいタグを**追加**できるようにするには、 variant 自体が`opt …` でラップされていれば可能です。これは前もって計画する必要があります！インターフェイスを設計するときは、：

``` candid
service: {
  get_member_status (member_id : nat) -> (variant {active; expired});
}
```

と書く代わりに、これを使うのがよいでしょう：

``` candid
service: {
  get_member_status (member_id : nat) -> (opt variant {active; expired});
}
```

こうすることで、後で`honorary` メンバーシップのステータスを追加する必要が生じたときに、 ステータスのリストを拡張することができます。古いクライアントは未知のフィールドを`null` として受け取ります。

#### スーパータイプ

バリアントタイプのスーパータイプはタグが追加されたバリアントで、 いくつかのタグのタイプがスーパータイプに変更されたものです。

#### 対応するMotoko タイプ

バリアントタイプは例えばMotoko バリアントタイプとして表現されます：

``` motoko
type Shape = {
  #dot : ();
  #circle : Float;
  #rectangle : { width : Float; height : Float };
  #_2669435721_ : Text;
};
```

タグの型が`null` の場合、これはMotoko の`()` に対応することに注意してください。

#### 対応する Rust 型

ユーザ定義の`enum` と`#[derive(CandidType, Deserialize)]` 特性。

`#[serde(rename = "DifferentFieldName")]` 属性を使用してフィールド名を変更できます。

#### 対応する JavaScript の値

単一のエントリを持つレコードオブジェクト。例えば、`{ dot: null }` 。

フィールド名がハッシュの場合、`_hash_` をフィールド名として使用します。例えば、`{ _2669435721_: "test" }` 。

## タイプ func (...) → (...)

Candidは、サービスが他のサービスやそのメソッドへの参照を受け取ったり提供したりするような、高次のユースケースをサポートするように設計されています。`func` ：これは関数の**シグネチャ**（引数と結果の型、注釈）を示し、この型の値はそのシグネチャを持つ関数への参照です。

サポートされているアノテーションは以下のとおりです：

- `query` は、参照される関数がクエリ・メソッドであることを示し、 のステートを変更せず、安価な「クエリコール」メカニズムを使用して呼び出すことができることを意味します。canister

- `oneway` は、この関数がレスポンスを返さないことを示します。

パラメータの命名の詳細については、[引数と結果の命名を](/developer-docs/backend/candid/candid-concepts.md#service-naming)参照してください。

#### 型の構文

``` candid
func () -> ()
func (text) -> (text)
func (dividend : nat, divisor : nat) -> (div : nat, mod : nat);
func () -> (int) query
func (func (int) -> ()) -> ()
```

#### テキスト構文

現在のところ、プリンシパルで識別されるサービスのパブリックメソッドのみがサポートされています：

``` candid
func "w7x7r-cok77-xa".hello
func "w7x7r-cok77-xa"."☃"
func "aaaaa-aa".create_canister
```

#### サブタイプ

サブタイプ： 関数型に以下の変更を加えると、[サービスのアップグレードの](/developer-docs/backend/candid/candid-concepts.md#upgrades)ルールで説明されているように、サブタイプに変更されます：

- 結果型のリストが拡張されます。

- 結果型のリストが拡張されます。

- パラメータ型リストは省略可能な引数で拡張することができます(型`opt …`)。

- 既存のパラメータ型を****スーパータイプに****変更することができます！言い換えれば、関数型は引数型に****対変型さ****れます。

- 既存の結果型をサブタイプに変更することができます。

#### スーパータイプ

関数型に対する以下の変更は、それをスーパータイプに変更します：

- 結果型のリストが短くなります。

- 結果型リストが省略可能な引数で拡張されます (`opt …` 型)。

- パラメータ型のリストが拡張されます。

- 既存のパラメータ型を**サブタイプ**に変更することができます！言い換えれば、関数型は引数型****に対して変数を****持ちます。

- 既存の結果型をスーパータイプに変更することができます。

#### 対応するMotoko 型

候補関数型は，`shared` Motoko 関数に対応し，結果型は`async` でラップされます（ただし，`oneway` のアノテーションが付けられている場合は，結果型は単に`()` となります）．引数や結果はタプルになります：

``` candid
type F0 = func () -> ();
type F1 = func (text) -> (text);
type F2 = func (text, bool) -> () oneway;
type F3 = func (text) -> () oneway;
type F4 = func () -> (text) query;
```

はMotoko に対応します。

``` Motoko
type F0 = shared () -> async ();
type F1 = shared Text -> async Text;
type F2 = shared (Text, Bool) -> ();
type F3 = shared (text) -> ();
type F4 = shared query () -> async Text;
```

#### 対応するRust型

`candid::IDLValue::Func(Principal, String)`[IDLValue](https://docs.rs/candid/0.6.15/candid/parser/value/enum.IDLValue.html) を参照してください。

#### 対応するJavaScriptの値

`[Principal.fromText("aaaaa-aa"), "create_canister"]`

## 型サービス：{...}

サービスでは、（[`func` ](#type-func) 型を使用して）個々の関数への参照だけでなく、サービス全体への参照を渡したい場合があります。この場合、Candid型を使用してそのようなサービスの完全なインターフェイスを宣言することができます。

サービスタイプの構文の詳細については、[Candidサービスの説明を](/developer-docs/backend/candid/candid-concepts.md#candid-service-descriptions)参照してください。

#### 型の構文

``` candid
service: {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

#### テキスト構文

``` candid
service: "w7x7r-cok77-xa"
service: "zwigo-aiaaa-aaaaa-qaa3a-cai"
service: "aaaaa-aa"
```

#### サブタイプ

サービスタイプのサブタイプは、追加のメソッドを持つ可能性があり、既存のメソッドのタイプがサブタイプに変更されるサービスタイプです。

これは、[サービスのアップグレードにおける](/developer-docs/backend/candid/candid-concepts.md#upgrades)アップグレードルールについて説明したのとまったく同じ原理です。

#### スーパータイプ

サービスタイプのスーパータイプは，いくつかのメソッドが削除される可能性があり，既存のメソッドのタイプがスーパータイプに変更されるサービスタイプです．

#### 対応するMotoko タイプ

Candid のサービスタイプはMotoko の`actor` タイプに直接対応しています：

``` motoko
actor {
  add : shared Nat -> async ()
  subtract : shared Nat -> async ();
  get : shared query () -> async Int;
  subscribe : shared (shared Int -> async ()) -> async ();
}
```

#### 対応するRust型

`candid::IDLValue::Service(Principal)`[IDLValueを](https://docs.rs/candid/0.6.15/candid/parser/value/enum.IDLValue.html)参照してください。

#### 対応するJavaScriptの値

`Principal.fromText("aaaaa-aa")`

## プリンシパル型

Internet Computer は、canisters 、ユーザー、その他のエンティティを識別するための共通スキームとして**プリンシパルを**使用します。

#### 型の構文

`principal`

#### テキスト構文

``` candid
principal "w7x7r-cok77-xa"
principal "zwigo-aiaaa-aaaaa-qaa3a-cai"
principal "aaaaa-aa"
```

#### 対応するMotoko 型

`Principal`

#### 対応する Rust 型

`candid::Principal` または`ic_types::Principal`

#### 対応するJavaScriptの値

`Principal.fromText("aaaaa-aa")`

## 予約された型

`reserved` 型は、`reserved` という1つの(無意味な)値を持つ型であり、他のすべての型のスーパータイプです。

`reserved` 型はメソッドの引数を除去するために使用できます。次のシグネチャを持つメソッドを考えてみましょう：

``` candid
service: {
  foo : (first_name : text, middle_name : text, last_name : text) -> ()
}
```

`middle_name`Candidは、このシグネチャをこのように変更することを妨げません：

``` candid
service: {
  foo : (first_name : text, last_name : text) -> ()
}
```

もしクライアントが古いインターフェイスを使ってあなたに話しかけた場合、あなたは黙って`last_name` を無視し、`middle_name` を`last_name` として受け取るでしょう。メソッドの引数名は単なる慣習であり、メソッドの引数はその位置によって識別されることを覚えておいてください。

代わりに

``` candid
service: {
  foo : (first_name : text, middle_name : reserved, last_name : text) -> ()
}
```

を使えば、`foo` が第二引数を取っていたことを示すことができます。

この落とし穴を避けるには、引数が変化することが予想される関数や、引数が型ではなく位置でしか識別できない関数は、単一のレコードを取るように宣言するパターンを採用します。例えば

``` candid
service: {
  foo : (record { first_name : text; middle_name : text; last_name : text}) -> ()
}
```

さて、シグネチャをこう変えます：

``` candid
service: {
  foo : (record { first_name : text; last_name : text}) -> ()
}
```

というシグネチャに変更すると、正しい処理が行われ、削除された引数のレコードを保持しておく必要さえなくなります。

:::info

一般的に、メソッドから引数を削除することは推奨されません。通常は、引数を省略した新しいメソッドを導入する方が望ましいです。

:::

#### 型の構文

`reserved`

#### テキスト構文

`reserved`

#### サブタイプ

すべての型

#### 対応するMotoko 型

`Any`

#### 対応する Rust 型

`candid::Reserved`

#### 対応するJavaScriptの値

任意の値

## 空の型

`empty` 型は値を持たない型であり、他のどの型のサブタイプでもあります。

`empty` 型の実用的な使用例は比較的まれです。これは、メソッドを「決して正常に返さない」とマークするために使用できます。例えば

``` candid
service: {
  always_fails () -> (empty)
}
```

#### 型の構文

`empty`

#### テキスト構文

この型には値がないので、ありません。

#### スーパータイプ

すべての型

#### 対応するMotoko 型

`None`

#### 対応する Rust 型

`candid::Empty`

#### 対応するJavaScriptの値

この型には値がないので、ありません。

<!---
# Candid reference

## Overview

This document provides detailed reference information about Candid supported types and links to the Candid specification and documentation for the Candid Rust crate.

-   [Supported types](#supported-types).

-   [Candid specification](https://github.com/dfinity/candid).

-   [Candid Rust crate](https://docs.rs/candid).

If you are looking for more information before you start working with Candid, check out the following related resources:

-   [How Candid provides a common language for application interfaces (video)](https://www.youtube.com/watch?v=O2KaWRtsqHg).


## Supported types

This section lists all the types supported by Candid. For each type, the reference includes the following information:

-   Type syntax and the syntax for the textual representation of the type.

-   Upgrade rules for each type are given in terms of the possible **subtypes** and **supertypes** of a type.

-   Corresponding types in Rust, Motoko and Javascript.

Subtypes are the types you can change your method **results** to. Supertypes are the types that you can change your method **arguments** to.

You should note that this reference only lists the specific subtypes and supertypes that are relevant for each type. It does not repeat common information about subtypes and supertypes that can apply to any type. For example, the reference does not list `empty` as a subtype because it can be a subtype of any other type. Similarly, the types `reserved` and `opt t` are not listed as supertypes of specific types because they are supertypes of any type. For details about the subtyping rules for the `empty`, `reserved`, and `opt t` types, see the following sections:

-   [`opt t`](#type-opt)

-   [`reserved`](#type-reserved)

-   [`empty`](#type-empty)

## Type text

The `text` type is used for human readable text. More precisely, its values are sequences of unicode code points (excluding surrogate parts).

#### Type syntax  
`text`

#### Textual syntax  
``` candid
""
"Hello"
"Escaped characters: \n \r \t \\ \" \'"
"Unicode escapes: \u{2603} is ☃ and \u{221E} is ∞"
"Raw bytes (must be utf8): \E2\98\83 is also ☃"
```

#### Corresponding Motoko type  
`Text`

#### Corresponding Rust type  
`String` or `&str`

#### Corresponding JavaScript values  
`"String"`

## Type blob

The `blob` type can be used for binary data, that is, sequences of bytes. Interfaces written using the `blob` type are interchangeable with interfaces that are written using `vec nat8`.

#### Type syntax  
`blob`

#### Textual syntax  
`blob <text>`

where `<text>` represents a text literal with all characters representing their utf8 encoding, and arbitrary byte sequences (`"\CA\FF\FE"`).

For more information about text types, see [text](#type-text).

#### Subtypes  
`vec nat8`, and all subtypes of `vec nat8`.

#### Supertypes  
`vec nat8`, and all supertypes of `vec nat8`.

#### Corresponding Motoko type  
`Blob`

#### Corresponding Rust type  
`Vec<u8>` or `&[u8]`

#### Corresponding JavaScript values  
`[ 1, 2, 3, 4, ... ]`

## Type nat

The `nat` type contains all natural (non-negative) numbers. It is unbounded, and can represent arbitrary large numbers. The on-wire encoding is LEB128, so small numbers are still efficiently represented.

#### Type syntax  
`nat`

#### Textual syntax  
``` candid
1234
1_000_000
0xDEAD_BEEF
```

#### Supertypes  
`int`

#### Corresponding Motoko type  
`Nat`

#### Corresponding Rust type  
`candid::Nat` or `u128`

#### Corresponding JavaScript values  
`+BigInt(10000)` or `10000n`

## Type int

The `int` type contains all whole numbers. It is unbounded and can represent arbitrary small or large numbers. The on-wire encoding is SLEB128, so small numbers are still efficiently represented.

#### Type syntax  
`int`

#### Textual syntax  
``` candid
1234
-1234
+1234
1_000_000
-1_000_000
+1_000_000
0xDEAD_BEEF
-0xDEAD_BEEF
+0xDEAD_BEEF
```

#### Subtypes  
`nat`

#### Corresponding Motoko type  
`Int`

#### Corresponding Rust type  
`candid::Int` or `i128`

#### Corresponding JavaScript values  
`+BigInt(-10000)` or `-10000n`

## Type natN and intN

The types `nat8`, `nat16`, `nat32`, `nat64`, `int8`, `int16`, `int32` and `int64` represent numbers with a representation of that many bits, and can be used in more “low-level” interfaces.

The range of `natN` is `{0 …​ 2^N-1}`, and the range of `intN` is `-2^(N-1) …​ 2^(N-1)-1`.

The on-wire representation is exactly that many bits long. So for small values, `nat` is more space-efficient than `nat64`.

#### Type syntax  
`nat8`, `nat16`, `nat32`, `nat64`, `int8`, `int16`, `int32` or `int64`

#### Textual syntax  
Same as `nat` for `nat8`, `nat16`, `nat32`, and `nat64`.

Same as `int` for `int8`, `int16`, `int32` and `int64`.

We can use type annotation to distinguish different integer types.

``` candid
100 : nat8
-100 : int8
(42 : nat64)
```

#### Corresponding Motoko type  
`natN` translates by default to `NatN`, but can also correspond to `WordN` when required.

`intN` translate to `IntN`.

#### Corresponding Rust type  
Signed and unsigned integers of corresponding size.

| Length | Signed | Unsigned |
|--------|--------|----------|
| 8-bit  | i8     | u8       |
| 16-bit | i16    | u16      |
| 32-bit | i32    | u32      |
| 64-bit | i64    | u64      |

#### Corresponding JavaScript values  
8-bit, 16-bit and 32-bit translate to the number type.

`int64` and `nat64` translate to the `BigInt` primitive in JavaScript.

## Type float32 and float64

The types `float32` and `float64` represent IEEE 754 floating point numbers in single precision (32 bit) and double precision (64 bit).

#### Type syntax  
`float32`, `float64`

#### Textual syntax  
The same syntax as `int`, plus floating point literals as follows:

``` candid
1245.678
+1245.678
-1_000_000.000_001
34e10
34E+10
34e-10
0xDEAD.BEEF
0xDEAD.BEEFP-10
0xDEAD.BEEFp+10
```

#### Corresponding Motoko type  
`float64` corresponds to `Float`.

`float32` does **not** currently have a representation in Motoko. Candid interfaces using `float32` cannot be served from or used from Motoko programs.

#### Corresponding Rust type  
`f32`, `f64`

#### Corresponding JavaScript values  
float number

## Type bool

The `bool` type is a logical data type that can have only the values `true` or `false`.

#### Type syntax  
`bool`

#### Textual syntax  
`true`, `false`

#### Corresponding Motoko type  
`Bool`

#### Corresponding Rust type  
`bool`

#### Corresponding JavaScript values  
`true`, `false`

## Type null

The `null` type is the type of the value `null`, thus a subtype of all the `opt t` types. It is also the idiomatic choice when using [variants](#type-variant) to model enumerations.

#### Type syntax  
`null`

#### Textual syntax  
`null`

#### Supertypes  
All `opt t` types.

#### Corresponding Motoko type  
`Null`

#### Corresponding Rust type  
`()`

#### Corresponding JavaScript values  
`null`

## Type vec t

The `vec` type represents vectors (sequences, lists, arrays). A value of type `vec t` contains a sequence of zero or more values of type `t`.

#### Type syntax  
`vec bool`, `vec nat8`, `vec vec text`, and so on.

#### Textual syntax  
``` candid
vec {}
vec { "john@doe.com"; "john.doe@example.com" };
```

#### Subtypes  
-   Whenever `t` is a subtype of `t'`, then `vec t` is a subtype of `vec t'`.

-   `blob` is a subtype of `vec nat8`.

#### Supertypes  
-   Whenever `t` is a supertype of `t'`, then `vec t` is a supertype of `vec t'`.

-   `blob` is a supertype of `vec nat8`.

#### Corresponding Motoko type  
`[T]`, where the Motoko type `T` corresponds to `t`.

#### Corresponding Rust type  
`Vec<T>` or `&[T]`, where the Rust type `T` corresponds to `t`.

`vec t` can translate to `BTreeSet` or `HashSet`.

`vec record { KeyType; ValueType }` can translate to `BTreeMap` or `HashMap`.

#### Corresponding JavaScript values  
`Array`, e.g. `[ "text", "text2", …​ ]`

## Type opt t

The `opt t` type contains all the values of type `t`, plus the special `null` value. It is used to express that some value is optional, meaning that data might be present as some value of type `t`, or might be absent as the value `null`.

The `opt` type can be nested (for example, `opt opt text`), and the values `null` and `opt null` are distinct values.

The `opt` type plays a crucial role in the evolution of Candid interfaces, and has special subtyping rules as described below.

#### Type syntax  
`opt bool`, `opt nat8`, `opt opt text`, and so on.

#### Textual syntax  
``` candid
null
opt true
opt 8
opt null
opt opt "test"
```

#### Subtypes  
The canonical rules for subtyping with `opt` are:

-   Whenever `t` is a subtype of `t'`, then `opt t` is a subtype of `opt t'`.

-   `null` is a subtype of `opt t'`.

-   `t` is a subtype of `opt t` (unless `t` itself is `null`, `opt …` or `reserved`).

In addition, for technical reasons related to upgrading and higher-order services, **every** type is a subtype of `opt t`, yielding `null` if the types do not match. Users are advised, however, to not directly make use of that rule.

#### Supertypes  
-   Whenever `t` is a supertype of `t'`, then `opt t` is a supertype of `opt t'`.

#### Corresponding Motoko type  
`?T`, where the Motoko type `T` corresponds to `t`.

#### Corresponding Rust type  
`Option<T>`, where the Rust type `T` corresponds to `t`.

#### Corresponding JavaScript values  
`null` translates to `[]`.

`opt 8` translates to `[8]`.

`opt opt "test"` translates to `[["test"]]`.

## Type record { n : t, … }

A `record` type is a collection of labeled values. For example, the following code gives the name `address` to the type of records that have the textual fields `street`, `city` and `country` and a numerical field of `zip_code`.

``` candid
type address = record {
  street : text;
  city : text;
  zip_code : nat;
  country : text;
};
```

The order of fields in the record type declaration does not matter. Each field can have a different type (unlike vectors). The label of a record field can also be a 32-bit natural number, as in this example:

``` candid
type address2 = record {
  288167939 : text;
  1103114667 : text;
  220614283 : nat;
  492419670 : text;
};
```

In fact, textual labels are treated as their **field hash**, and incidentally, `address` and `address2` are—to Candid—the same types.

If you omit the label, Candid automatically assigns sequentially-increasing labels. This behavior leads to the following shortened syntax, which is typically used to represent pairs and tuples. The type `record { text; text; opt bool }` is equivalent to `record { 0 : text; 1: text; 2: opt bool }`

#### Type syntax  
``` candid
record {}
record { first_name : text; second_name : text }
record { "name with spaces" : nat; "unicode, too: ☃" : bool }
record { text; text; opt bool }
```

#### Textual syntax  
``` candid
record {}
record { first_name = "John"; second_name = "Doe" }
record { "name with spaces" = 42; "unicode, too: ☃" = true }
record { "a"; "tuple"; null }
```

#### Subtypes  
Subtypes of a record are record types that have additional fields (of any type), where some field’s types are changed to subtypes, or where optional fields are removed. It is, however, bad practice to remove optional fields in method results. You can change a field’s type to `opt empty` to indicate that this field is no longer used.

For example, if you have a function returning a record of of the following type:

``` candid
record {
  first_name : text; middle_name : opt text; second_name : text; score : int
}
```

you can evolve that to a function returning a record of the following type:

``` candid
record {
  first_name : text; middle_name : opt empty; second_name : text; score : nat; country : text
}
```

where we have deprecated the `middle_name` field, change the type of `score` and added the `country` field.

#### Supertypes  
Supertypes of a record are record types with some fields removed, some fields’ types changed to supertypes, or with optional fields added.

The latter is what allows you to extend your argument records with additional fields. Clients using the old interface will not include the field in their record, which will decode, when expected in the upgraded service, as `null`.

For example, if you have a function expecting a record of type:

``` candid
record { first_name : text; second_name : text; score : nat }
```

you can evolve that to a function expecting a record of type:

``` candid
record { first_name : text; score: int; country : opt text }
```

#### Corresponding Motoko type  
If the record type looks like it could refer to a tuple (that is, consecutive labels starting at 0), a Motoko tuple type (for example `(T1, T2, T3)`) is used. Else, a Motoko record `({ first_name :Text, second_name : Text })` is used.

If the field name is a reserved name in Motoko, an undescore is appended. So `record { if : bool }` corresponds to `{ if_ : Bool }`.

If (even then) the field name is not a valid Motoko identifier, the **field** hash is used instead: `record { ☃ : bool }` corresponds to `{ 11272781 : Boolean }`.

#### Corresponding Rust type  
User defined `struct` with `#[derive(CandidType, Deserialize)]` trait.

You can use the `#[serde(rename = "DifferentFieldName")]` attribute to rename field names.

If the record type is a tuple, it can be translated to a tuple type such as `(T1, T2, T3)`.

#### Corresponding JavaScript values  
If the record type is a tuple, the value is translated to an array, for example, `["Candid", 42]`.

Else it translates to a record object. For example, `{ "first name": "Candid", age: 42 }`.

If the field name is a hash, we use `_hash_` as the field name, for example, `{ _1_: 42, "1": "test" }`.

## Type variant { n : t, … }

A `variant` type represents a value that is from exactly one of the given cases, or **tags**. So a value of the type:

``` candid
type shape = variant {
  dot : null;
  circle : float64;
  rectangle : record { width : float64; height : float64 };
  "💬" : text;
};
```

is either a dot, or a circle (with a radius), or a rectangle (with dimensions), or a speech bubble (with some text). The speech bubble illustrates use of a unicode label name (💬).

The tags in variants are, just like the labels in records, actually numbers, and string tags refer to their hash value.

Often, some or all of the the tags do not carry data. It is idiomatic to then use the `null` type, as in the `dot` above. In fact, Candid encourages this by allowing you to omit the `: null` type annotation in variants, so:

``` candid
type season = variant { spring; summer; fall; winter }
```

is equivalent to:

``` candid
type season = variant {
  spring : null; summer: null; fall: null; winter : null
}
```

and used to represent enumerations.

The type `variant {}` is legal, but has no values. If that is the intention, the [`empty` type](#type-empty) may be more appropriate.

#### Type syntax  
``` candid
variant {}
variant { ok : nat; error : text }
variant { "name with spaces" : nat; "unicode, too: ☃" : bool }
variant { spring; summer; fall; winter }
```

#### Textual syntax  
``` candid
variant { ok = 42 }
variant { "unicode, too: ☃" = true }
variant { fall }
```

#### Subtypes  
Subtypes of a variant type are variant types with some tags removed, and the type of some tags themselves changed to a subtype.

If you want to be able to **add** new tags in variants in a method result, you can do so if the variant is itself wrapped in `opt …`. This requires planning ahead! When you design an interface, instead of writing:

``` candid
service: {
  get_member_status (member_id : nat) -> (variant {active; expired});
}
```

it is better to use this:

``` candid
service: {
  get_member_status (member_id : nat) -> (opt variant {active; expired});
}
```

This way, if you later need to add a `honorary` membership status, you can expand the list of statuses. Old clients will receive unknown fields as `null`.

#### Supertypes  
Supertypes of a variant types are variants with additional tags, and maybe the type of some tags changed to a supertype.

#### Corresponding Motoko type  
Variant types are represented as Motoko variant types, for example:

``` motoko
type Shape = {
  #dot : ();
  #circle : Float;
  #rectangle : { width : Float; height : Float };
  #_2669435721_ : Text;
};
```

Note that if the type of a tag is `null`, this corresponds to `()` in Motoko, to preserve the mapping between the respective idiomatic ways to model enumerations as variants.

#### Corresponding Rust type  
User defined `enum` with `#[derive(CandidType, Deserialize)]` trait.

You can use the `#[serde(rename = "DifferentFieldName")]` attribute to rename field names.

#### Corresponding JavaScript values  
A record object with a single entry. For example, `{ dot: null }`.

If the field name is a hash, we use `_hash_` as the field name, for example, `{ _2669435721_: "test" }`.

## Type func (…) → (…)

Candid is designed to support higher-order use cases, where a service may receive or provide references to other services or their methods, for example, as callbacks. The `func` type is central to this: It indicates the function’s **signature** (argument and results types, annotations), and values of this type are references to functions with that signature.

The supported annotations are:

-   `query` indicates that the referenced function is a query method, meaning it does not alter the state of its canister, and that it can be invoked using the cheaper “query call” mechanism.

-   `oneway` indicates that this function returns no response, intended for fire-and-forget scenarios.

For more information about parameter naming, see [Naming arguments and results](/developer-docs/backend/candid/candid-concepts.md#service-naming).

#### Type syntax  
``` candid
func () -> ()
func (text) -> (text)
func (dividend : nat, divisor : nat) -> (div : nat, mod : nat);
func () -> (int) query
func (func (int) -> ()) -> ()
```

#### Textual syntax  
Currently, only public methods of services, which are identified by their principal, are supported:

``` candid
func "w7x7r-cok77-xa".hello
func "w7x7r-cok77-xa"."☃"
func "aaaaa-aa".create_canister
```

#### Subtypes  
The following modifications to a function type change it to a subtype as discussed in the rules for [service upgrades](/developer-docs/backend/candid/candid-concepts.md#upgrades):

-   The result type list may be extended.

-   The parameter type list may be shortened.

-   The parameter type list may be extended with optional arguments (type `opt …`).

-   Existing parameter types may be changed to to a ****supertype**** ! In other words, the function type is ****contravariant**** in the argument type.

-   Existing result types may be changed to a subtype.

#### Supertypes  
The following modifications to a function type change it to a supertype:

-   The result type list may be shortened.

-   The result type list may be extended with optional arguments (type `opt …`).

-   The parameter type list may be extended.

-   Existing parameter types may be changed to to a **subtype** ! In other words, the function type is ****contravariant**** in the argument type.

-   Existing result types may be changed to a supertype.

#### Corresponding Motoko type  
Candid function types correspond to `shared` Motoko functions, with the result type wrapped in `async` (unless they are annotated with `oneway`, then the result type is simply `()`). Arguments resp. results become tuples, unless there is exactly one, in which case it is used directly:

``` candid
type F0 = func () -> ();
type F1 = func (text) -> (text);
type F2 = func (text, bool) -> () oneway;
type F3 = func (text) -> () oneway;
type F4 = func () -> (text) query;
```

corresponds in Motoko to

``` Motoko
type F0 = shared () -> async ();
type F1 = shared Text -> async Text;
type F2 = shared (Text, Bool) -> ();
type F3 = shared (text) -> ();
type F4 = shared query () -> async Text;
```

#### Corresponding Rust type  
`candid::IDLValue::Func(Principal, String)`, see [IDLValue](https://docs.rs/candid/0.6.15/candid/parser/value/enum.IDLValue.html).

#### Corresponding JavaScript values  
`[Principal.fromText("aaaaa-aa"), "create_canister"]`

## Type service: {…}

Services may want to pass around references to not just individual functions (using the [`func` type](#type-func)), but references to whole services. In this case, Candid types can be used to declare the complete interface of such a service.

See [Candid service descriptions](/developer-docs/backend/candid/candid-concepts.md#candid-service-descriptions) for more details on the syntax of a service type.

#### Type syntax  
``` candid
service: {
  add : (nat) -> ();
  subtract : (nat) -> ();
  get : () -> (int) query;
  subscribe : (func (int) -> ()) -> ();
}
```

#### Textual syntax  
``` candid
service: "w7x7r-cok77-xa"
service: "zwigo-aiaaa-aaaaa-qaa3a-cai"
service: "aaaaa-aa"
```

#### Subtypes  
The subtypes of a service type are those service types that possibly have additional methods, and where the type of an existing method is changed to a subtype.

This is exactly the same principle as discussed for upgrade rules in [service upgrades](/developer-docs/backend/candid/candid-concepts.md#upgrades).

#### Supertypes  
The supertypes of a service type are those service types that may have some methods removed, and the type of existing methods are changed to a supertype.

#### Corresponding Motoko type  
Service types in Candid correspond directly to `actor` types in Motoko:

``` motoko
actor {
  add : shared Nat -> async ()
  subtract : shared Nat -> async ();
  get : shared query () -> async Int;
  subscribe : shared (shared Int -> async ()) -> async ();
}
```

#### Corresponding Rust type  
`candid::IDLValue::Service(Principal)`, see [IDLValue](https://docs.rs/candid/0.6.15/candid/parser/value/enum.IDLValue.html).

#### Corresponding JavaScript values  
`Principal.fromText("aaaaa-aa")`

## Type principal

The Internet Computer uses **principals** as the common scheme to identify canisters, users, and other entities.

#### Type syntax  
`principal`

#### Textual syntax  
``` candid
principal "w7x7r-cok77-xa"
principal "zwigo-aiaaa-aaaaa-qaa3a-cai"
principal "aaaaa-aa"
```

#### Corresponding Motoko type  
`Principal`

#### Corresponding Rust type  
`candid::Principal` or `ic_types::Principal`

#### Corresponding JavaScript values  
`Principal.fromText("aaaaa-aa")`

## Type reserved

The `reserved` type is a type with one (uninformative) value `reserved`, and is the supertype of all other types.

The `reserved` type can be used to remove method arguments. Consider a method with the following signature:

``` candid
service: {
  foo : (first_name : text, middle_name : text, last_name : text) -> ()
}
```

and assume you no longer care about the `middle_name`. Although Candid will not prevent you from changing the signature to this:

``` candid
service: {
  foo : (first_name : text, last_name : text) -> ()
}
```

it would be disastrous: If a client talks to you using the old interface, you will silently ignore the `last_name` and take the `middle_name` as the `last_name`. Remember that method parameter names are just convention, and method arguments are identified by their position.

Instead, you can use:

``` candid
service: {
  foo : (first_name : text, middle_name : reserved, last_name : text) -> ()
}
```

to indicate that `foo` used to take a second argument, but you no longer care about that.

You can avoid this pitfall by adopting the pattern any function that is anticipated to have changing arguments, or whose arguments can only be distinguished by position, not type, is declared to take a single record. For example:

``` candid
service: {
  foo : (record { first_name : text; middle_name : text; last_name : text}) -> ()
}
```

Now, changing the signature to this:

``` candid
service: {
  foo : (record { first_name : text; last_name : text}) -> ()
}
```

does the right thing, and you don’t even need to keep a record of the removed argument around.

:::info

In general, it is not recommended to remove arguments from methods. Usually, it is preferable to introduce a new method that omits the argument.

:::

#### Type syntax  
`reserved`

#### Textual syntax  
`reserved`

#### Subtypes  
All types

#### Corresponding Motoko type  
`Any`

#### Corresponding Rust type  
`candid::Reserved`

#### Corresponding JavaScript values  
Any value

## Type empty

The `empty` type is the type without values, and is the subtype of any other type.

Practical use cases for the `empty` type are relatively rare. It could be used to mark a method as “never returns successfully”. For example:

``` candid
service: {
  always_fails () -> (empty)
}
```

#### Type syntax  
`empty`

#### Textual syntax  
None, as this type has no values

#### Supertypes  
All types

#### Corresponding Motoko type  
`None`

#### Corresponding Rust type  
`candid::Empty`

#### Corresponding JavaScript values  
None, as this type has no values.

-->
