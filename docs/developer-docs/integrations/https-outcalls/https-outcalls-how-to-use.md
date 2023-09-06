# HTTPアウトコールの使い方イントロダクション

このガイドでは、ICの[HTTPSアウトコール](../index.md)機能の使い方を説明します。この機能により、スマートコントラクトはブロックチェーンの外部にあるHTTP(S)サーバーに直接呼び出しを行い、その応答をスマートコントラクトのさらなる処理で使用することができます。

## キーコンセプト

### サポートされるメソッド

この機能は現在、HTTPリクエストの`GET` 、`HEAD` 、`POST` メソッドをサポートしています。

### IC管理canister

- [IC 管理Canister](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-management-canister)-canister が HTTPS アウトコールを使用するには、IC のシステム API を呼び出す必要があります。Canisters は、**IC 管理Canister** にメッセージを送信することで、システム API を呼び出すことができます。この意図は、システムAPIを他のcanister のように簡単に使用できるようにすることです。管理canister は、識別子`"aaaaa-aa"` を使用して呼び出されます。

:::info
IC管理canister は単なるファサードであり、実際にはcanister （分離ステート、Wasmコードなどを持つ）としては存在しません。
::：

### Cycles

- [Cycles](../../gas-cost.md)つまり、(たとえば、canister 通話間のように)発呼側の残高から暗黙的に差し引かれることはありません。

## HTTPアウトコールを送信するためのAPI

[Internet Computer インターフェイス仕様の](https://internetcomputer.org/docs/current/references/ic-interface-spec)とおり、canister は、[以下の構造によって](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-http_request) `http_request` メソッドを使用できます：

### リクエスト

リクエストには以下のパラメータを指定します：

- `url`要求されたURL。要求されるURL URLはhttps://www.ietf.org/rfc/rfc3986.txt\[RFC-3986\]に従って有効でなければならず、その長さは`8192` を超えてはなりません。URLはカスタムポート番号を指定できます。

- `max_response_bytes`レスポンスの最大サイズをバイト数で指定します。指定する場合、その値は`2MB` (`2,000,000B`) を超えてはなりません。呼はこのパラメータに基づいて課金されます。指定しない場合、`2MB` の最大値が使用されます。

- `method`: 現在のところ、`GET` 、`HEAD` 、`POST` のみがサポートされています。

- `headers`HTTP リクエストヘッダーとそれに対応する値のリスト。

- `body`リクエストボディの内容。

- `transform`生の応答をサニタイズされた応答に変換するオプションの関数と、サニタイズされる応答とともに、呼び出し時に関数に提供されるバイトエンコードされたコンテキスト。提供される場合、呼び出すcanister 自身がこの関数をエクスポートしなければなりません。

### 応答

返される応答(および指定された場合、`transform` 関数に提供される応答)は、以下のフィールドを含みます：

- `status`レスポンス・ステータス (200, 404 など)。

- `headers`HTTP レスポンス・ヘッダとそれに対応する値のリスト。

- `body`レスポンスの本文。

## サンプルコード

Motoko と Rust で`GET` と`POST`リクエストを行う具体的な例は、以下を参照してください：

- [ `GET` リクエストの最小サンプルコード](./https-outcalls-get.md)
- [ `POST` リクエストの最小サンプルコード](./https-outcalls-post.md)

<!---
# How to use HTTP outcalls: Intro

This guide shoes how to use the [HTTPS outcalls](../index.md) feature of the IC. This feature allows smart contracts to directly make calls to HTTP(S) servers external to the blockchain and use the response in the further processing of the smart contract, without the need of oracles.

## Key concepts

### Methods supported

The feature currently supports `GET`, `HEAD`, and `POST` methods for HTTP requests.

### IC management canister
* [The IC Management Canister](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-management-canister) - In order for a canister to use HTTPS outcalls, it needs to call into the system API of the IC. Canisters can call into the system API by sending messages to the **IC Management Canister**. The intent is to make using the system API as simple as if it were just another canister. Management canister is evoked by using the identifier `"aaaaa-aa"`.

:::info
The IC management canister is just a facade; it does not actually exist as a canister (with isolated state, Wasm code, etc.). 
:::

### Cycles

* [Cycles](../../gas-cost.md) used to pay for the call must be explicitly transferred with the call, i.e., they are not deducted from the caller's balance implicitly (e.g., as for inter-canister calls).

## The API for sending HTTP outcalls

As per the [Internet Computer interface specification](https://internetcomputer.org/docs/current/references/ic-interface-spec), a canister can use the `http_request` method by [following construction](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-http_request):

### The request
The following parameters should be supplied for in the request:

-   `url`: the requested URL. The URL must be valid according to https://www.ietf.org/rfc/rfc3986.txt[RFC-3986] and its length must not exceed `8192`. The URL may specify a custom port number.

-   `max_response_bytes`: optional; specifies the maximal size of the response in bytes. If provided, the value must not exceed `2MB` (`2,000,000B`). The call will be charged based on this parameter. If not provided, the maximum of `2MB` will be used.

-   `method`: currently, only `GET`, `HEAD`, and `POST` are supported.

-   `headers`: list of HTTP request headers and their corresponding values.

-   `body`: optional; the content of the request's body.

-   `transform`: an optional function that transforms raw responses to sanitized responses, and a byte-encoded context that is provided to the function upon invocation, along with the response to be sanitized. If provided, the calling canister itself must export this function.

### The response

The returned response (and the response provided to the `transform` function, if specified) contains the following fields:

-   `status`: the response status (e.g., 200, 404).

-   `headers`: list of HTTP response headers and their corresponding values.

-   `body`: the response's body.

## Sample code

To see concrete examples of making `GET` and `POST`requests in Motoko and Rust see:

* [Minimal sample code of making a `GET` request](./https-outcalls-get.md)
* [Minimal sample code of making a `POST` request](./https-outcalls-post.md)


-->
