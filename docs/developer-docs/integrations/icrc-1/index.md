# ICRC-1トークン規格

## 概要

[ICRC-](https://github.com/dfinity/ICRC-1/blob/main/standards/ICRC-1/README.md)1は、Internet Computer における**カンジブル・トークンの**規格です。

## データ

### アカウント

`principal` は複数のアカウントを持つことができます。`principal` の各アカウントは`subaccount` という 32 バイトの文字列で識別されます。したがって、アカウントは`(principal, subaccount)` のペアに対応します。

すべてのバイトが 0 に設定されたサブアカウントによって識別されるアカウントは、`principal` の*デフォルトアカウント*です。

``` candid "Type definitions" +=
type Subaccount = blob;
type Account = record { owner : principal; subaccount : opt Subaccount; };
```

## メソッド

### icrc1\_name<span id="name_method"></span>

トークンの名前（例えば、`MyToken` ）を返します。

``` candid "Methods" +=
icrc1_name : () -> (text) query;
```

### icrc1\_symbol<span id="symbol_method"></span>

トークンのシンボルを返します（例:`ICP` ）。

``` candid "Methods" +=
icrc1_symbol : () -> (text) query;
```

### icrc1\_decimals<span id="decimals_method"></span>

トークンが使用する小数点以下の桁数を返します (例:`8` は、トークンの金額を`100000000` で割ってユーザー表示にすることを意味します)。

``` candid "Methods" +=
icrc1_decimals : () -> (nat8) query;
```

### icrc1\_fee<span id="fee_method"></span>

デフォルトの送金手数料を返します

``` candid "Methods" +=
icrc1_fee : () -> (nat) query;
```

### icrc1\_metadata<span id="metadata_method"></span>

この元帳のメタデータ項目のリストを返します。
以下の「メタデータ」のセクションを参照してください。

``` candid "Type definitions" +=
type Value = variant { Nat : nat; Int : int; Text : text; Blob : blob };
```

``` candid "Methods" +=
icrc1_metadata : () -> (vec record { text; Value }) query;
```

### icrc1\_total\_supply

発行[口座を](#minting_account)除く全ての口座の総トークン数を返します。

``` candid "Methods" +=
icrc1_total_supply : () -> (nat) query;
```

### icrc1\_minting\_account

この元帳がトークンの鋳造と発行をサポートしている場合、[鋳造](#minting_account)口座を返します。

``` candid "Methods" +=
icrc1_minting_account : () -> (opt Account) query;
```

### icrc1\_balance\_of

引数として与えられた口座の残高を返します。

``` candid "Methods" +=
icrc1_balance_of : (Account) -> (nat) query;
```

### icrc1\_transfer<span id="transfer_method"></span>

`record { of = caller; subaccount = from_subaccount }` 口座から`to` 口座に`amount` トークンを転送します。
呼び出し側は転送のために`fee` トークンを支払います。

``` candid "Type definitions" +=
type TransferArgs = record {
    from_subaccount : opt Subaccount;
    to : Account;
    amount : nat;
    fee : opt nat;
    memo : opt blob;
    created_at_time : opt nat64;
};

type TransferError = variant {
    BadFee : record { expected_fee : nat };
    BadBurn : record { min_burn_amount : nat };
    InsufficientFunds : record { balance : nat };
    TooOld;
    CreatedInFuture : record { ledger_time: nat64 };
    Duplicate : record { duplicate_of : nat };
    TemporarilyUnavailable;
    GenericError : record { error_code : nat; message : text };
};
```

``` candid "Methods" +=
icrc1_transfer : (TransferArgs) -> (variant { Ok: nat; Err: TransferError; });
```

呼び出し元は`fee` を支払います。
呼び出し元が`fee` 引数を設定しない場合、元帳はデフォルトの転送手数料を適用します。
 `fee` 引数が元帳の手数料と一致しない場合、元帳は`variant { BadFee = record { expected_fee = ... } }` エラーを返さなければなりません (MUST)。

`memo` パラメーターは、元帳にとって意味を持たない任意の blob です。
元帳は、少なくとも 32 バイト長のメモを許可すべきです (SHOULD)。
元帳は、[トランザクションの重複排除に](#transaction_deduplication) `memo` 引数を使用すべきです (SHOULD)。

`created_at_time` パラメーターは、クライアントがトランザクションを構築した時間（UTCタイムゾーンでのUNIXエポックからのナノ秒）を示します。
台帳は、`created_at_time` 引数が過去または未来にありすぎるトランザクションを拒否し、`variant { TooOld }` と`variant { CreatedInFuture = record { ledger_time = ... } }` エラーを返すべきです（SHOULD）。

結果は、転送のトランザクションインデックスかエラーです。

### icrc1\_supported\_standards

この元帳が実装している規格のリストを返します。
以下の[「拡張」](#extensions)セクションを参照してください。

``` candid "Methods" +=
icrc1_supported_standards : () -> (vec record { name : text; url : text }) query;
```

呼び出しの結果には、常に少なくとも1つのエントリがなければなりません、

``` candid
record { name = "ICRC-1"; url = "https://github.com/dfinity/ICRC-1" }
```

## 拡張<span id="extensions"></span>

基本規格では、豊かなDeFiエコシステムの構築に不可欠ないくつかの台帳機能を意図的に除外しています：

- 例えば、スマートコントラクトのための信頼性の高いトランザクション通知。
- ブロック構造とブロックをフェッチするためのインターフェース。
- 署名済みトランザクション。

`icrc1_supported_standards`
このエンドポイントは、台帳によって実装されたすべての仕様（例えば、 や ）の名前を返します。`"ICRC-42"` `"DIP-20"`

## メタデータ

ウォレットとの統合を簡素化し、ユーザーエクスペリエンスを向上させるために、台帳はメタデータを公開できます。
クライアントは [`icrc1_metadata`](#metadata_method)メソッドを使用してメタデータ・エントリーをフェッチできます。
メタデータ・エントリーはすべてオプションです。

### キーの形式

メタデータのキーは任意のUnicode文字列で、`<namespace>:<key>` のパターンに従わなければなりません。`<namespace>` はコロンを含まない文字列です。
名前空間`icrc1` は、この規格で定義されたキーのために予約されています。

### 標準メタデータ項目

| キー | 値例 | 意味 |
| --- | --- | --- |
| `icrc1:symbol` | `variant { Text = "XTKN" }` | トークン通貨コード（[ISO-4217](https://en.wikipedia.org/wiki/ISO_4217) 参照）。クエリのコール結果と同じである必要があります。 [`icrc1_symbol`](#symbol_method)クエリーコール。 |
| `icrc1:name` | `variant { Text = "Test Token" }` | トークンの名前。存在する場合は、クエリのコール結果と同じである必要があります。 [`icrc1_name`](#name_method)クエリーコールの結果と同じでなければなりません。 |
| `icrc1:decimals` | `variant { Nat = 8 }` | トークンが使用する小数の数。例えば、8 は、トークンの金額を 10<sup>8</sup> で割って、ユーザ表現を得ることを意味します。存在する場合は、クエリ・コールの結果と同じである必要があります。 [`icrc1_decimals`](#decimals_method)クエリーコールの結果と同じでなければなりません。 |
| `icrc1:fee` | `variant { Nat = 10_000 }` | デフォルトの送金手数料。存在する場合は、クエリ・コールの結果と同じである必要があります。 [`icrc1_fee`](#fee_method)クエリーコールの結果と同じでなければなりません。 |

## トランザクションの重複排除<span id="transfer_deduplication"></span>

以下のシナリオを考えてみましょう：

- エージェントがIC上でホストされているICRC-1台帳にトランザクションを送信します。
- 台帳はトランザクションを受け入れました。
- エージェントは数分間ネットワーク接続を失い、トランザクションの結果を知ることができません。

ICRC-1 台帳は、エージェントのエラーリカバリーを簡素化するために、転送の重複排除を実装する**必要が**ありま す。
重複排除は、事前に設定された時間ウィンドウ`TX_WINDOW` （たとえば直近24時間） 内に送信されたすべてのトランザクションをカバーします。
クライアントとInternet Computer の間の時間ドリフトを考慮するために、台帳は重複排除ウィンドウを`PERMITTED_DRIFT` パラメーター（たとえば2分）で未来に延長する**ことができます**。

クライアントは、`created_at_time` および`memo` フィールドを使用して、重複排除アルゴリズムを制御できます。 [`transfer`](#transfer_method)コール引数の

- `created_at_time` フィールドは、トランザクション構築時間を、UTCタイムゾーンの UNIXエポックからのナノ秒数として設定する。
- `memo` フィールドは、元帳が`memo` フィールドの異なる値を持つ転送を重複排除しないことを除いて、元帳に意味を持ちません。

クライアントが`created_at_time` フィールドを設定した場合、台帳はトランザクションの重複排除に以下のアルゴリズムを使用すべきです (SHOULD)：

- `created_at_time` が設定され、台帳によって観測された`time() - TX_WINDOW - PERMITTED_DRIFT` *より前の*場合、台帳は`variant { TooOld }` エラーを返すべきです。
- `created_at_time` が設定されていて、台帳が観測した`time() + PERMITTED_DRIFT` *より*後である場合、台帳は`variant { CreatedInFuture = record { ledger_time = ... } }` エラーを返します。
- 台帳がインデックス`i` のトランザクションで構造的に等しい転送ペイロード (つまり、すべての転送引数フィールドと呼び出し元が同じ値) を観察した場合、`variant { Duplicate = record { duplicate_of = i } }` を返すべきです。
- そうでなければ、その転送は新規トランザクションである。

クライアントが`created_at_time` フィールドを設定しなかった場合、元帳はトランザクションを重複排除すべきではない（SHOULD NOT）。

## 発行アカウント<span id="minting_account"></span>

発行アカウントは、新しいトークンを作成でき、バーン・トークンの受信者として機能するユニークなアカウントです。

ミント取引には手数料はかかりません。

バーントランザクションは手数料無料ですが、最小バーン量要件がある場合があります。
クライアントが少なすぎる量をバーンしようとすると、台帳は以下のように応答する**はずです**：

    variant { Err = variant { BadBurn = record { min_burn_amount = ... } } }

発行アカウントは、通常の送金でバーンされた手数料の受取人でもあります。

## 口座のテキスト表現

このフォーマットは、[Internet Computer インターフェース仕様で](../../../references/ic-interface-spec.md#textual-ids)指定されているプリンシパルの**テキストエンコーディングに**依存しています。以下では、`Principal.toText` と`Principal.fromText` と呼びます。
このフォーマットは、以下の望ましい特性を持っています：

- 予約されていないプリンシパルのテキストエンコーディングは、元帳上のそのプリンシパルの デフォルト口座の有効なテキストエンコーディングです。
- デコード関数は帰納的です（つまり、異なる有効なエンコーディングは異なる口座に対応します）。
  この特性により、アプリケーションはテキスト表現をキーとして使用できます。
- テキストエンコーディングにタイプミスがあると、高い確率で無効になります。

### エンコード

アプリケーションは以下のようにアカウントをエンコードする**必要が**あります：

- デフォルトアカウント（サブアカウントはNULLまたは32個のゼロを持つブロブ）のエンコーディングは、所有者プリンシパルのエンコーディングです。
- デフォルトでないサブアカウントを持つ口座のエンコーディングは、所有者のプリンシパルバイト、先頭のゼロが省略されたサブアカウントバイト、先頭のゼロを除いたサブアカウントの長さ（1バイト）、および余分なバイト`7F`<sub>16</sub> を連結したテキストプリンシパルエンコーディングです。

擬似コードでは

``` sml
encodeAccount({ owner; subaccount }) = case subaccount of
  | None ⇒ Principal.toText(owner)
  | Some([32; 0]) ⇒ Principal.toText(owner)
  | Some(bytes) ⇒ Principal.toText(owner · shrink(bytes) · [|shrink(bytes)|, 0x7f])

shrink(bytes) = case bytes of
  | 0x00 :: rest ⇒ shrink(rest)
  | bytes ⇒ bytes
```

### デコード

アプリケーションは以下のようにテキスト表現をデコードする**必要が**あります：

- あたかもプリンシパルであるかのようにテキストを`raw_bytes` にデコードし、プリンシパルの長さチェックを無視します(いくつかのデコーダは、プリンシパルが最大29バイト長であることを許可します)。
- `raw_bytes` がバイト`7F`<sub>16</sub> で終わらない場合、`raw_bytes` を所有者とするアカウントと空のサブアカウントを返します。
- `raw_bytes` が`7F`<sub>16</sub> で終わっている場合：
  - **ステップ1：**最後の`7F`<sub>16</sub> バイトを削除します。
  - **ステップ2**：最後のバイト`N` を読み取り、それを削除。`N > 32` または`N = 0` の場合、エラーを発生。
  - ステップ**3**: 最後のNバイトを入力から除去。
    - 取り除かれたバイト列の最初のバイトがゼロの場合、エラーを発生。
    - 32バイトのサブアカウントを得るために、左側に(32 - N)個のゼロを付加します。
  - **ステップ 4:**入力配列の残りをオーナーとし、前のステップで構築したバイト配列をサブアカウントとするアカウントを返します。

擬似コードでは

``` sml
decodeAccount(text) = case Principal.fromText(text) of
  | (prefix · [n, 0x7f]) where Blob.size(prefix) < n ⇒ raise Error
  | (prefix · [n, 0x7f]) where n > 32 orelse n = 0 ⇒ raise Error
  | (prefix · suffix · [n, 0x7f]) where Blob.size(suffix) = n ⇒
    if suffix[0] = 0
    then raise Error
    else { owner = Principal.fromBlob(prefix); subaccount = Some(expand(suffix)) }
  | raw_bytes ⇒ { owner = Principal.fromBlob(raw_bytes); subaccount = None }

expand(bytes) = if Blob.size(bytes) < 32
                then expand(0x00 :: bytes)
                else bytes
```

<!---
# ICRC-1 token standard

## Overview
The [ICRC-1](https://github.com/dfinity/ICRC-1/blob/main/standards/ICRC-1/README.md) is a standard for **fungible tokens** on the Internet Computer.

## Data

### account

A `principal` can have multiple accounts. Each account of a `principal` is identified by a 32-byte string called `subaccount`. Therefore an account corresponds to a pair `(principal, subaccount)`.

The account identified by the subaccount with all bytes set to 0 is the _default account_ of the `principal`.

```candid "Type definitions" +=
type Subaccount = blob;
type Account = record { owner : principal; subaccount : opt Subaccount; };
```

## Methods

### icrc1_name <span id="name_method"></span>

Returns the name of the token (e.g., `MyToken`).

```candid "Methods" +=
icrc1_name : () -> (text) query;
```

### icrc1_symbol <span id="symbol_method"></span>

Returns the symbol of the token (e.g., `ICP`).

```candid "Methods" +=
icrc1_symbol : () -> (text) query;
```

### icrc1_decimals <span id="decimals_method"></span>

Returns the number of decimals the token uses (e.g., `8` means to divide the token amount by `100000000` to get its user representation).

```candid "Methods" +=
icrc1_decimals : () -> (nat8) query;
```

### icrc1_fee <span id="fee_method"></span>

Returns the default transfer fee.

```candid "Methods" +=
icrc1_fee : () -> (nat) query;
```

### icrc1_metadata <span id="metadata_method"></span>

Returns the list of metadata entries for this ledger.
See the "Metadata" section below.

```candid "Type definitions" +=
type Value = variant { Nat : nat; Int : int; Text : text; Blob : blob };
```

```candid "Methods" +=
icrc1_metadata : () -> (vec record { text; Value }) query;
```

### icrc1_total_supply

Returns the total number of tokens on all accounts except for the [minting account](#minting_account).

```candid "Methods" +=
icrc1_total_supply : () -> (nat) query;
```

### icrc1_minting_account

Returns the [minting account](#minting_account) if this ledger supports minting and burning tokens.

```candid "Methods" +=
icrc1_minting_account : () -> (opt Account) query;
```

### icrc1_balance_of

Returns the balance of the account given as an argument.

```candid "Methods" +=
icrc1_balance_of : (Account) -> (nat) query;
```

### icrc1_transfer <span id="transfer_method"></span>

Transfers `amount` of tokens from account `record { of = caller; subaccount = from_subaccount }` to the `to` account.
The caller pays `fee` tokens for the transfer.

```candid "Type definitions" +=
type TransferArgs = record {
    from_subaccount : opt Subaccount;
    to : Account;
    amount : nat;
    fee : opt nat;
    memo : opt blob;
    created_at_time : opt nat64;
};

type TransferError = variant {
    BadFee : record { expected_fee : nat };
    BadBurn : record { min_burn_amount : nat };
    InsufficientFunds : record { balance : nat };
    TooOld;
    CreatedInFuture : record { ledger_time: nat64 };
    Duplicate : record { duplicate_of : nat };
    TemporarilyUnavailable;
    GenericError : record { error_code : nat; message : text };
};
```

```candid "Methods" +=
icrc1_transfer : (TransferArgs) -> (variant { Ok: nat; Err: TransferError; });
```

The caller pays the `fee`.
If the caller does not set the `fee` argument, the ledger applies the default transfer fee.
If the `fee` argument does not agree with the ledger fee, the ledger MUST return `variant { BadFee = record { expected_fee = ... } }` error.

The `memo` parameter is an arbitrary blob that has no meaning to the ledger.
The ledger SHOULD allow memos of at least 32 bytes in length.
The ledger SHOULD use the `memo` argument for [transaction deduplication](#transaction_deduplication).

The `created_at_time` parameter indicates the time (as nanoseconds since the UNIX epoch in the UTC timezone) at which the client constructed the transaction.
The ledger SHOULD reject transactions that have `created_at_time` argument too far in the past or the future, returning `variant { TooOld }` and `variant { CreatedInFuture = record { ledger_time = ... } }` errors correspondingly.

The result is either the transaction index of the transfer or an error.

### icrc1_supported_standards

Returns the list of standards this ledger implements.
See the ["Extensions"](#extensions) section below.

```candid "Methods" +=
icrc1_supported_standards : () -> (vec record { name : text; url : text }) query;
```

The result of the call should always have at least one entry,

```candid
record { name = "ICRC-1"; url = "https://github.com/dfinity/ICRC-1" }
```

## Extensions <span id="extensions"></span>

The base standard intentionally excludes some ledger functions essential for building a rich DeFi ecosystem, for example:

  - Reliable transaction notifications for smart contracts.
  - The block structure and the interface for fetching blocks.
  - Pre-signed transactions.

The standard defines the `icrc1_supported_standards` endpoint to accommodate these and other future extensions.
This endpoint returns names of all specifications (e.g., `"ICRC-42"` or `"DIP-20"`) implemented by the ledger.

## Metadata

A ledger can expose metadata to simplify integration with wallets and improve user experience.
The client can use the [`icrc1_metadata`](#metadata_method) method to fetch the metadata entries. 
All the metadata entries are optional.

### Key format

The metadata keys are arbitrary Unicode strings and must follow the pattern `<namespace>:<key>`, where `<namespace>` is a string not containing colons.
Namespace `icrc1` is reserved for keys defined in this standard.

### Standard metadata entries

| Key | Example value | Semantics |
| --- | ------------- | --------- |
| `icrc1:symbol` | `variant { Text = "XTKN" }` | The token currency code (see [ISO-4217](https://en.wikipedia.org/wiki/ISO_4217)). When present, should be the same as the result of the [`icrc1_symbol`](#symbol_method) query call. |
| `icrc1:name` | `variant { Text = "Test Token" }` | The name of the token. When present, should be the same as the result of the [`icrc1_name`](#name_method) query call. |
| `icrc1:decimals` | `variant { Nat = 8 }` | The number of decimals the token uses. For example, 8 means to divide the token amount by 10<sup>8</sup> to get its user representation. When present, should be the same as the result of the [`icrc1_decimals`](#decimals_method) query call. |
| `icrc1:fee` | `variant { Nat = 10_000 }` | The default transfer fee. When present, should be the same as the result of the [`icrc1_fee`](#fee_method) query call. |

## Transaction deduplication <span id="transfer_deduplication"></span>

Consider the following scenario:

- An agent sends a transaction to an ICRC-1 ledger hosted on the IC.
- The ledger accepts the transaction.
- The agent loses the network connection for several minutes and cannot learn about the outcome of the transaction.

An ICRC-1 ledger **should** implement transfer deduplication to simplify the error recovery for agents.
The deduplication covers all transactions submitted within a pre-configured time window `TX_WINDOW` (for example, last 24 hours).
The ledger **may** extend the deduplication window into the future by the `PERMITTED_DRIFT` parameter (for example, 2 minutes) to account for the time drift between the client and the Internet Computer.

The client can control the deduplication algorithm using the `created_at_time` and `memo` fields of the [`transfer`](#transfer_method) call argument:
  * The `created_at_time` field sets the transaction construction time as the number of nanoseconds from the UNIX epoch in the UTC timezone.
  * The `memo` field does not have any meaning to the ledger, except that the ledger will not deduplicate transfers with different values of the `memo` field.

The ledger SHOULD use the following algorithm for transaction deduplication if the client set the `created_at_time` field:
  * If `created_at_time` is set and is _before_ `time() - TX_WINDOW - PERMITTED_DRIFT` as observed by the ledger, the ledger should return `variant { TooOld }` error.
  * If `created_at_time` is set and is _after_ `time() + PERMITTED_DRIFT` as observed by the ledger, the ledger should return `variant { CreatedInFuture = record { ledger_time = ... } }` error.
  * If the ledger observed a structurally equal transfer payload (i.e., all the transfer argument fields and the caller have the same values) at transaction with index `i`, it should return `variant { Duplicate = record { duplicate_of = i } }`.
  * Otherwise, the transfer is a new transaction.

If the client did not set the `created_at_time` field, the ledger SHOULD NOT deduplicate the transaction.

## Minting account <span id="minting_account"></span>

The minting account is a unique account that can create new tokens and acts as the receiver of burnt tokens.

Transfers **from** the minting account act as **min** transactions depositing fresh tokens on the destination account.
Mint transactions have no fee.

Transfers **to** the minting account act as **burn** transactions, removing tokens from the token supply.
Burn transactions have no fee but might have minimal burn amount requirements.
If the client tries to burn an amount that is too small, the ledger **should** reply with the following:

```
variant { Err = variant { BadBurn = record { min_burn_amount = ... } } }
```

The minting account is also the receiver of the fees burnt in regular transfers.

## Textual representation of accounts

We specify a **canonical textual format** that all applications should use to display ICRC-1 accounts.
This format relies on the textual encoding of principals specified in the [Internet Computer interface specification](../../../references/ic-interface-spec.md#textual-ids), referred to as `Principal.toText` and `Principal.fromText` below.
The format has the following desirable properties:

- A textual encoding of any non-reserved principal is a valid textual encoding of the default account of that principal on the ledger.
- The decoding function is injective (i.e., different valid encodings correspond to different accounts).
   This property enables applications to use text representation as a key.
- A typo in the textual encoding invalidates it with a high probability.

### Encoding

Applications **should** encode accounts as follows:

- The encoding of the default account (the subaccount is null or a blob with 32 zeros) is the encoding of the owner principal.
- The encoding of accounts with a non-default subaccount is the textual principal encoding of the concatenation of the owner principal bytes, the subaccount bytes with the leading zeros omitted, the length of the subaccount without the leading zeros (a single byte), and an extra byte `7F`<sub>16</sub>.

In pseudocode:

```sml
encodeAccount({ owner; subaccount }) = case subaccount of
  | None ⇒ Principal.toText(owner)
  | Some([32; 0]) ⇒ Principal.toText(owner)
  | Some(bytes) ⇒ Principal.toText(owner · shrink(bytes) · [|shrink(bytes)|, 0x7f])

shrink(bytes) = case bytes of
  | 0x00 :: rest ⇒ shrink(rest)
  | bytes ⇒ bytes
```

### Decoding

Applications **should** decode textual representation as follows:

- Decode the text as if it was a principal into `raw_bytes`, ignoring the principal length check (some decoders allow the principal to be at most 29 bytes long).
- If `raw_bytes` do not end with byte `7F`<sub>16</sub>, return an account with `raw_bytes` as the owner and an empty subaccount.
- If `raw_bytes` end with `7F`<sub>16</sub>:
     - **Step 1:** drop the last `7F`<sub>16</sub> byte.
     - **Step 2:** read the last byte `N` and drop it. If `N > 32` or `N = 0`, raise an error.
     - **Step 3:** take the last N bytes and strip them from the input.
        - If the first byte in the stripped sequence is zero, raise an error.
        - Prepend the bytes with (32 - N) zeros on the left to get a 32-byte subaccount.
     - **Step 4:** return an account with the owner being the rest of the input sequence as the owner and the subaccount being the byte array constructed in the previous step.

In pseudocode:

```sml
decodeAccount(text) = case Principal.fromText(text) of
  | (prefix · [n, 0x7f]) where Blob.size(prefix) < n ⇒ raise Error
  | (prefix · [n, 0x7f]) where n > 32 orelse n = 0 ⇒ raise Error
  | (prefix · suffix · [n, 0x7f]) where Blob.size(suffix) = n ⇒
    if suffix[0] = 0
    then raise Error
    else { owner = Principal.fromBlob(prefix); subaccount = Some(expand(suffix)) }
  | raw_bytes ⇒ { owner = Principal.fromBlob(raw_bytes); subaccount = None }

expand(bytes) = if Blob.size(bytes) < 32
                then expand(0x00 :: bytes)
                else bytes
```

-->
