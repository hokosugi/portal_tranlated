# HTTPアウトコールの使い方POST

## 概要

`POST` HTTPSリクエストを行うための最小限の例。このdapp の目的は、canister から HTTP リクエストを行う方法を示すことだけです。

サンプルコードはMotoko と Rust の両方で書かれています。このサンプルcanister は、いくつかの JSON を含む`POST` リクエストをフリーの API に送信し、ヘッダーとボディが正しく送信されたことを確認します。

**このcanister の主な目的は、開発者に冪等な`POST` リクエストを作成する方法を示すことです。**

この例は5分もかからずに完了します。

## 私たちが作っているもの

### の候補となる Web UIcanister

このチュートリアルのcanister は、呼び出されたときに HTTP`POST` リクエストをトリガーする**パブリックメソッドをひとつだけ**持ちます。canister はフロントエンドを持ちません（バックエンドのみ）が、他のcanisters と同様に、Candid ウェブ UI を介してパブリックメソッドと対話することができます：

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

メソッドを呼び出すと、canister はレスポンスボディに以下の JSON を含む HTTP`POST` リクエストを送信します：

``` json
{
    "name": "Grogu",
    "force_sensitive": "true"
}
```

![Candid web UI](../_attachments/https-post-candid-motoko-result.webp)

### HTTP POSTリクエストの検証

canister が期待した HTTP リクエストを送信したことを確認するために、このcanister は HTTP リクエストを検査できる[公開 API サービスに](https://public.requestbin.com/r/en8d7aepyq2ko)HTTP リクエストを送信しています。下の画像にあるように、`POST` リクエストヘッダとボディを検査して、canister が送信したものであることを確認できます。

![Public API to inspect POST request](../_attachments/https-post-requestbin-result.webp)

## `POST` リクエストに関する重要な注意

HTTPS のアウトコールはコンセンサスを経由するため、開発者はcanister からの HTTPs`POST` リクエストが宛先に何度も送信されることを予期しておく必要があります。Web3 コンポーネントを無視するとしても、同一の POST リクエストが複数回送信されることは、クライアントがさまざまな理由 (宛先サーバーが利用できないなど) でリクエストを再試行することが一般的な HTTP において、新しい問題ではありません。

HTTP の`POST` リクエストで推奨される方法は、ヘッダーに idempotency キーを追加することで、宛先サーバーはクライアントからの`POST` リクエストがどれなのかを知ることができます。

開発者は、宛先サーバーがidempotencyキーを理解し使用するように注意する必要があります。canister 、冪等キーを送るようにコード化することはできますが、それをどうするかは最終的には受信サーバー次第です。以下に、冪等[キーを使ったAPI](https://stripe.com/docs/api/idempotent_requests)サービスの例を示します。

## Motoko バージョン

### Motoko:コードの構造

本題に入る前に、これから触れるコードの構造を説明します：

メインファイルはこんな感じです：

``` motoko

//Import some custom types from `src/backend_canister/Types.mo` file
import Types "Types";

actor {

//method that uses the HTTP outcalls feature and returns a string
  public func foo() : async Text {

    //1. DECLARE IC MANAGEMENT CANISTER
    let ic : Types.IC = actor ("aaaaa-aa");

    //2. SETUP ARGUMENTS FOR HTTP GET request
    let request : Types.HttpRequestArgs = {
        //construct the request
    };

    //3. ADD CYCLES TO PAY FOR HTTP REQUEST
    //code to add cycles

    //4. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    let response : Types.HttpResponsePayload = await ic.http_request(request);

    //5. DECODE THE RESPONSE
    //code to decode response

    //6. RETURN RESPONSE OF THE BODY
    response
  };
};
```

また、`Types.mo` にいくつかのカスタムタイプを作成します。これは次のようになります：

``` motoko
module Types {

    //type declarations for HTTP requests, HTTP responses, IC management canister, etc...

}
```

### Motoko:ステップ・バイ・ステップ

新しいプロジェクトを作成するには

- #### ステップ 1: 以下のコマンドを実行して、新しいプロジェクトを作成します：

<!-- end list -->

``` bash
dfx new send_http_post
cd send_http_post
npm install
```

- #### ステップ 2:`src/send_http_post_backend/main.mo` ファイルをテキストエディタで開き、内容を次のように置き換えます：

<!-- end list -->

``` motoko
import Debug "mo:base/Debug";
import Blob "mo:base/Blob";
import Cycles "mo:base/ExperimentalCycles";
import Array "mo:base/Array";
import Nat8 "mo:base/Nat8";
import Text "mo:base/Text";

//import the custom types we have in Types.mo
import Types "Types";

actor {

//PULIC METHOD
//This method sends a POST request to a URL with a free API we can test.
  public func send_http_post_request() : async Text {

    //1. DECLARE IC MANAGEMENT CANISTER
    //We need this so we can use it to make the HTTP request
    let ic : Types.IC = actor ("aaaaa-aa");

    //2. SETUP ARGUMENTS FOR HTTP GET request

    // 2.1 Setup the URL and its query parameters
    //This URL is used because it allows us to inspect the HTTP request sent from the canister
    let host : Text = "en8d7aepyq2ko.x.pipedream.net";
    let url = "https://en8d7aepyq2ko.x.pipedream.net/";

    // 2.2 prepare headers for the system http_request call

    //idempotency keys should be unique so we create a function that generates them.
    let idempotency_key: Text = generateUUID();
    let request_headers = [
        { name = "Host"; value = host # ":443" },
        { name = "User-Agent"; value = "http_post_sample" },
        { name= "Content-Type"; value = "application/json" },
        { name= "Idempotency-Key"; value = idempotency_key }
    ];

    // The request body is an array of [Nat8] (see Types.mo) so we do the following:
    // 1. Write a JSON string
    // 2. Convert ?Text optional into a Blob, which is an intermediate reprepresentation before we cast it as an array of [Nat8]
    // 3. Convert the Blob into an array [Nat8]
    let request_body_json: Text = "{ \"name\" : \"Grogu\", \"force_sensitive\" : \"true\" }";
    let request_body_as_Blob: Blob = Text.encodeUtf8(request_body_json); 
    let request_body_as_nat8: [Nat8] = Blob.toArray(request_body_as_Blob); // e.g [34, 34,12, 0]


    // 2.3 The HTTP request
    let http_request : Types.HttpRequestArgs = {
        url = url;
        max_response_bytes = null; //optional for request
        headers = request_headers;
        //note: type of `body` is ?[Nat8] so we pass it here as "?request_body_as_nat8" instead of "request_body_as_nat8"
        body = ?request_body_as_nat8; 
        method = #post;
        transform = null; //optional for request
    };

    //3. ADD CYCLES TO PAY FOR HTTP REQUEST

    //IC management canister will make the HTTP request so it needs cycles
    //See: https://internetcomputer.org/docs/current/motoko/main/cycles
    
    //The way Cycles.add() works is that it adds those cycles to the next asynchronous call
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    Cycles.add(17_000_000_000);
    
    //4. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    //Since the cycles were added above, we can just call the IC management canister with HTTPS outcalls below
    let http_response : Types.HttpResponsePayload = await ic.http_request(http_request);
    
    //5. DECODE THE RESPONSE

    //As per the type declarations in `Types.mo`, the BODY in the HTTP response 
    //comes back as [Nat8s] (e.g. [2, 5, 12, 11, 23]). Type signature:
    
    //public type HttpResponsePayload = {
    //     status : Nat;
    //     headers : [HttpHeader];
    //     body : [Nat8];
    // };

    //We need to decode that [Na8] array that is the body into readable text. 
    //To do this, we:
    //  1. Convert the [Nat8] into a Blob
    //  2. Use Blob.decodeUtf8() method to convert the Blob to a ?Text optional 
    //  3. We use Motoko syntax "Let... else" to unwrap what is returned from Text.decodeUtf8()
    let response_body: Blob = Blob.fromArray(http_response.body);
    let ?decoded_text = Text.decodeUtf8(response_body) else return "No value returned";

    //6. RETURN RESPONSE OF THE BODY
    let response_url: Text = "https://public.requestbin.com/r/en8d7aepyq2ko/";
    let result: Text = decoded_text # ". See more info of the request sent at at: " # response_url;
    result
  };

  //PRIVATE HELPER FUNCTION
  //Helper method that generates a Universally Unique Identifier
  //this method is used for the Idempotency Key used in the request headers of the POST request.
  //For the purposes of this exercise, it returns a constant, but in practice it should return unique identifiers
  func generateUUID() : Text {
    "UUID-123456789";
  }
};
```

- #### ステップ3：`src/send_http_post_backend/Types.mo` ファイルをテキストエディタで開き、内容を次のように置き換えます：

<!-- end list -->

``` motoko
module Types {

    //1. Type that describes the Request arguments for an HTTPS outcall
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    public type HttpRequestArgs = {
        url : Text;
        max_response_bytes : ?Nat64;
        headers : [HttpHeader];
        body : ?[Nat8];
        method : HttpMethod;
        transform : ?TransformRawResponseFunction;
    };

    public type HttpHeader = {
        name : Text;
        value : Text;
    };

    public type HttpMethod = {
        #get;
        #post;
        #head;
    };

    public type HttpResponsePayload = {
        status : Nat;
        headers : [HttpHeader];
        body : [Nat8];
    };

    //2. HTTPS outcalls have an optional "transform" key. These two types help describe it.
    //"The transform function may, for example, transform the body in any way, add or remove headers, 
    //modify headers, etc. "
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    
    //2.1 This type describes a function called "TransformRawResponse" used in line 14 above
    //"If provided, the calling canister itself must export this function." 
    //In this minimal example for a GET request, we declare the type for completeness, but 
    //we do not use this function. We will pass "null" to the HTTP request.
    public type TransformRawResponseFunction = {
        function : shared query TransformArgs -> async HttpResponsePayload;
        context : Blob;
    };

    //2.2 This type describes the arguments the transform function needs
    public type TransformArgs = {
        response : HttpResponsePayload;
        context : Blob;
    };


    //3. Declaring the IC management canister which we use to make the HTTPS outcall
    public type IC = actor {
        http_request : HttpRequestArgs -> async HttpResponsePayload;
    };

}
```

- #### ステップ 4:dapp をローカルでテストします。

dapp をローカルにデプロイします：

``` bash
dfx start --background
dfx deploy
```

成功すれば、ターミナルは開くことができるcanister URLを返すはずです：

``` bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    send_http_post_backend: http://127.0.0.1:4943/?canisterId=dccg7-xmaaa-aaaaa-qaamq-cai&id=dfdal-2uaaa-aaaaa-qaama-cai
```

バックエンド用の率直なウェブUIを開き、`send_http_post_request()` メソッドを呼び出します：

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

## Rustバージョン

### Rustのバージョンコードの構造

以下は、Rustcanister (例:`lib.rs`) でcanister をどのように宣言するかです：

``` rust
//1. DECLARE IC MANAGEMENT CANISTER
use ic_cdk::api::management_canister::http_request::{
    http_request, CanisterHttpRequestArgument, HttpHeader, HttpMethod, HttpResponse, TransformArgs,
    TransformContext,
};

//Update method using the HTTPS outcalls feature
#[ic_cdk::update]
async fn foo() {
    //2. SETUP ARGUMENTS FOR HTTP GET request
    let request = CanisterHttpRequestArgument {
        //instantiate the request
    };

    //3. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    //Note: in Rust, `http_request()` already sends the cycles needed 
    //so no need for explicit Cycles.add() as in Motoko
    match http_request(request).await {
        
        //4. DECODE AND RETURN THE RESPONSE
        Ok((response,)) => {
            //Ok case 
        }
        Err((r, m)) => {
            //error case
        }
    }
}
```

### Rustステップバイステップ

- #### ステップ 1: 以下のコマンドを実行して、新しいプロジェクトを作成します：

<!-- end list -->

``` bash
dfx new --type=rust send_http_post_rust
cd send_http_post_rust
npm install
rustup target add wasm32-unknown-unknown
```

- #### ステップ 2:`/src/send_http_post_rust_backend/src/lib.rs` ファイルをテキストエディタで開き、内容を次のように置き換えます：

<!-- end list -->

``` rust
//1. IMPORT IC MANAGEMENT CANISTER
//This includes all methods and types needed
use ic_cdk::api::management_canister::http_request::{
    http_request, CanisterHttpRequestArgument, HttpHeader, HttpMethod, HttpResponse, TransformArgs,
    TransformContext,
};

//Update method using the HTTPS outcalls feature
#[ic_cdk::update]
async fn send_http_post_request() -> String {
    //2. SETUP ARGUMENTS FOR HTTP GET request

    // 2.1 Setup the URL
    let host = "en8d7aepyq2ko.x.pipedream.net";
    let url = "https://en8d7aepyq2ko.x.pipedream.net/";

    // 2.2 prepare headers for the system http_request call
    //Note that `HttpHeader` is declared in line 4
    let request_headers = vec![
        HttpHeader {
            name: "Host".to_string(),
            value: format!("{host}:443"),
        },
        HttpHeader {
            name: "User-Agent".to_string(),
            value: "demo_HTTP_POST_canister".to_string(),
        },
        //For the purposes of this exercise, Idempotency-Key" is hard coded, but in practice
        //it should be generated via code and unique to each POST request. Common to create helper methods for this
        HttpHeader {
            name: "Idempotency-Key".to_string(),
            value: "UUID-123456789".to_string(),
        },
    ];

    //note "CanisterHttpRequestArgument" and "HttpMethod" are declared in line 4.
    //CanisterHttpRequestArgument has the following types:

    // pub struct CanisterHttpRequestArgument {
    //     pub url: String,
    //     pub max_response_bytes: Option<u64>,
    //     pub method: HttpMethod,
    //     pub headers: Vec<HttpHeader>,
    //     pub body: Option<Vec<u8>>,
    //     pub transform: Option<TransformContext>,
    // }
    //see: https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/struct.CanisterHttpRequestArgument.html

    //Where "HttpMethod" has structure:
    // pub enum HttpMethod {
    //     GET,
    //     POST,
    //     HEAD,
    // }
    //See: https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/enum.HttpMethod.html

    //Since the body in HTTP request has type Option<Vec<u8>> it needs to look something like this: Some(vec![104, 101, 108, 108, 111]) ("hello" in ASCII)
    //where the vector of u8s are the UTF. In order to send JSON via POST we do the following:
    //1. Declare a JSON string to send
    //2. Convert that JSON string to array of UTF8 (u8)
    //3. Wrap that array in an optional
    let json_string: String = r#"{
        "name": "Grogu",
        "force_sensitive": "true",
        "language": "rust"
    }"#
    .to_string();
    //note: here, r#""# is used for raw strings in Rust, which allows you to include characters like " and \ without needing to escape them.
    //We could have used "serde_json" as well.
    let json_utf8: Vec<u8> = json_string.into_bytes();
    let request_body: Option<Vec<u8>> = Some(json_utf8);

    let request = CanisterHttpRequestArgument {
        url: url.to_string(),
        max_response_bytes: None, //optional for request
        method: HttpMethod::POST,
        headers: request_headers,
        body: request_body,
        transform: None, //optional for request
    };

    //3. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE

    //Note: in Rust, `http_request()` already sends the cycles needed
    //so no need for explicit Cycles.add() as in Motoko
    match http_request(request).await {
        //4. DECODE AND RETURN THE RESPONSE

        //See:https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/struct.HttpResponse.html
        Ok((response,)) => {
            //if successful, `HttpResponse` has this structure:
            // pub struct HttpResponse {
            //     pub status: Nat,
            //     pub headers: Vec<HttpHeader>,
            //     pub body: Vec<u8>,
            // }

            //We need to decode that Vec<u8> that is the body into readable text.
            //To do this, we:
            //  1. Call `String::from_utf8()` on response.body
            //  3. We use a switch to explicitly call out both cases of decoding the Blob into ?Text
            let str_body = String::from_utf8(response.body)
                .expect("Transformed response is not UTF-8 encoded.");

            //The API response will looks like this:
            // { successful: true }

            //Return the body as a string and end the method
            let response_url = "https://public.requestbin.com/r/en8d7aepyq2ko";
            let result: String = format!(
                "{}. See more info of the request sent at at: {}",
                str_body, response_url
            );
            result
        }
        Err((r, m)) => {
            let message =
                format!("The http_request resulted into error. RejectionCode: {r:?}, Error: {m}");

            //Return the error as a string and end the method
            message
        }
    }
}
```

- `send_http_post_request() -> String` `String` を返しますが、これは必須ではありません。このチュートリアルでは、テストを容易にするためにこの作業を行います。

- `lib.rs` ファイルは[http\_requestを](https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/fn.http_request.html)使用しています。これはRust CDKの便利なメソッドで、すでにcycles をIC管理canister に送信しています。これは、13ノードのサブネットとほとんどのケースで送信するcycles の数を知っています。HTTPS outcallでさらにcycles が必要な場合は、[http\_request\_with\_cycles](https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/fn.http_request_with_cycles.html)([)](https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/fn.http_request_with_cycles.html)メソッドを使用し、必要なcycles を明示的に呼び出す必要があります。

- 上記で使用したRust CDKメソッド`http_request` は、IC管理canister メソッドをラップしています。 [`http_request`](../../../references/ic-interface-spec#ic-http_request)をラップしていますが、厳密には同じではありません。

- #### ステップ3：`src/hello_http_rust_backend/hello_http_rust_backend.did` ファイルをテキストエディタで開き、内容を以下のように置き換えます：

`lib.rs` のメソッド`send_http_post_request()` と一致するように、Candidインターフェースファイルを更新します。

    service : {
        "send_http_post_request": () -> (text);
    }

- #### ステップ 4:dapp をローカルでテストします。

dapp をローカルにデプロイします：

``` bash
dfx start --background
dfx deploy
```

成功すれば、ターミナルは開くことができるcanister URLを返すはずです：

``` bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    send_http_post_rust_backend: http://127.0.0.1:4943/?canisterId=dxfxs-weaaa-aaaaa-qaapa-cai&id=dzh22-nuaaa-aaaaa-qaaoa-cai
```

バックエンド用の率直なウェブUIを開き、`send_http_post_request()` メソッドを呼び出します：

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

:::note
RustとMotoko の両方の最小限の例では、生のレスポンスを変換する**transform**関数を作成しませんでした。これは次のセクション
:: で検討します：

## その他のリソース

- Rust による[HTTP POST](https://github.com/dfinity/examples/tree/master/rust/send_http_post)リクエストのサンプルコード
- の HTTP[POST](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)リクエストのサンプルコード[ Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)

<!---
# How to use HTTP outcalls: POST

## Overview
A minimal example to make a `POST` HTTPS request. The purpose of this dapp is only to show how to make HTTP requests from a canister.

The sample code is in both Motoko and Rust. This sample canister sends a `POST` request with some JSON to a free API where we can verify the headers and body were sent correctly.

**The main intent of this canister is to show developers how to make idempotent `POST` requests.**

This example takes less than 5 minutes to complete.

## What we are building

### Candid web UI of canister

The canister in this tutorial will have only **one public method** which, when called, will trigger an HTTP `POST` request. The canister will not have a frontend (only a  backend), but like all canisters, we can interact with its public methods via the Candid web UI, which will look like this:

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

When we call the method, the canister will send an HTTP `POST` request with the following JSON in the response body:

```json
{
    "name": "Grogu",
    "force_sensitive": "true"
}
```

![Candid web UI](../_attachments/https-post-candid-motoko-result.webp)

### Verifying the HTTP POST request

In order to verify that our canister sent the HTTP request we expected, this canister is sending HTTP requests to a [public API service](https://public.requestbin.com/r/en8d7aepyq2ko) where the HTTP request can be inspected. As you can see the image below, the `POST` request headers and body can be inspected to make sure it is what the canister sent.

![Public API to inspect POST request](../_attachments/https-post-requestbin-result.webp)

## Important notes on `POST` requests

Because HTTPS outcalls go through consensus, a developer should expect any HTTPs `POST` request from a canister to be sent many times to its destination. Even if we ignore the Web3 component, multiple identical POST requests is not new problem in HTTP where it is common for clients to retry requests for a variety of reasons (e.g. destination server being unavailable).

The recommended way for HTTP `POST` requests is to add the idempotency keys in the header so the destination server knows which `POST` requests from the client are the same. 

Developers should be careful that the destination server understand and use idempotency keys. A canister can be coded to be send idempotency keys, but it is ultimately up to the recipient server to know what to do with then. Here is an [example of an API service that uses idempotency keys](https://stripe.com/docs/api/idempotent_requests).

## Motoko version

### Motoko: Structure of the code

Before we dive in, here is the structure the code we will touch:

Here is how our main file will look like:

```motoko

//Import some custom types from `src/backend_canister/Types.mo` file
import Types "Types";

actor {

//method that uses the HTTP outcalls feature and returns a string
  public func foo() : async Text {

    //1. DECLARE IC MANAGEMENT CANISTER
    let ic : Types.IC = actor ("aaaaa-aa");

    //2. SETUP ARGUMENTS FOR HTTP GET request
    let request : Types.HttpRequestArgs = {
        //construct the request
    };

    //3. ADD CYCLES TO PAY FOR HTTP REQUEST
    //code to add cycles

    //4. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    let response : Types.HttpResponsePayload = await ic.http_request(request);

    //5. DECODE THE RESPONSE
    //code to decode response

    //6. RETURN RESPONSE OF THE BODY
    response
  };
};
```

We will also create some custom types in `Types.mo`. This will look like this:

```motoko
module Types {

    //type declarations for HTTP requests, HTTP responses, IC management canister, etc...

}
```

### Motoko: Step by step

To create a new project:

- #### Step 1:  Create a new project by running the following command:

```bash
dfx new send_http_post
cd send_http_post
npm install
```

- #### Step 2:  Open the `src/send_http_post_backend/main.mo` file in a text editor and replace content with:


```motoko
import Debug "mo:base/Debug";
import Blob "mo:base/Blob";
import Cycles "mo:base/ExperimentalCycles";
import Array "mo:base/Array";
import Nat8 "mo:base/Nat8";
import Text "mo:base/Text";

//import the custom types we have in Types.mo
import Types "Types";

actor {

//PULIC METHOD
//This method sends a POST request to a URL with a free API we can test.
  public func send_http_post_request() : async Text {

    //1. DECLARE IC MANAGEMENT CANISTER
    //We need this so we can use it to make the HTTP request
    let ic : Types.IC = actor ("aaaaa-aa");

    //2. SETUP ARGUMENTS FOR HTTP GET request

    // 2.1 Setup the URL and its query parameters
    //This URL is used because it allows us to inspect the HTTP request sent from the canister
    let host : Text = "en8d7aepyq2ko.x.pipedream.net";
    let url = "https://en8d7aepyq2ko.x.pipedream.net/";

    // 2.2 prepare headers for the system http_request call

    //idempotency keys should be unique so we create a function that generates them.
    let idempotency_key: Text = generateUUID();
    let request_headers = [
        { name = "Host"; value = host # ":443" },
        { name = "User-Agent"; value = "http_post_sample" },
        { name= "Content-Type"; value = "application/json" },
        { name= "Idempotency-Key"; value = idempotency_key }
    ];

    // The request body is an array of [Nat8] (see Types.mo) so we do the following:
    // 1. Write a JSON string
    // 2. Convert ?Text optional into a Blob, which is an intermediate reprepresentation before we cast it as an array of [Nat8]
    // 3. Convert the Blob into an array [Nat8]
    let request_body_json: Text = "{ \"name\" : \"Grogu\", \"force_sensitive\" : \"true\" }";
    let request_body_as_Blob: Blob = Text.encodeUtf8(request_body_json); 
    let request_body_as_nat8: [Nat8] = Blob.toArray(request_body_as_Blob); // e.g [34, 34,12, 0]


    // 2.3 The HTTP request
    let http_request : Types.HttpRequestArgs = {
        url = url;
        max_response_bytes = null; //optional for request
        headers = request_headers;
        //note: type of `body` is ?[Nat8] so we pass it here as "?request_body_as_nat8" instead of "request_body_as_nat8"
        body = ?request_body_as_nat8; 
        method = #post;
        transform = null; //optional for request
    };

    //3. ADD CYCLES TO PAY FOR HTTP REQUEST

    //IC management canister will make the HTTP request so it needs cycles
    //See: https://internetcomputer.org/docs/current/motoko/main/cycles
    
    //The way Cycles.add() works is that it adds those cycles to the next asynchronous call
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    Cycles.add(17_000_000_000);
    
    //4. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    //Since the cycles were added above, we can just call the IC management canister with HTTPS outcalls below
    let http_response : Types.HttpResponsePayload = await ic.http_request(http_request);
    
    //5. DECODE THE RESPONSE

    //As per the type declarations in `Types.mo`, the BODY in the HTTP response 
    //comes back as [Nat8s] (e.g. [2, 5, 12, 11, 23]). Type signature:
    
    //public type HttpResponsePayload = {
    //     status : Nat;
    //     headers : [HttpHeader];
    //     body : [Nat8];
    // };

    //We need to decode that [Na8] array that is the body into readable text. 
    //To do this, we:
    //  1. Convert the [Nat8] into a Blob
    //  2. Use Blob.decodeUtf8() method to convert the Blob to a ?Text optional 
    //  3. We use Motoko syntax "Let... else" to unwrap what is returned from Text.decodeUtf8()
    let response_body: Blob = Blob.fromArray(http_response.body);
    let ?decoded_text = Text.decodeUtf8(response_body) else return "No value returned";

    //6. RETURN RESPONSE OF THE BODY
    let response_url: Text = "https://public.requestbin.com/r/en8d7aepyq2ko/";
    let result: Text = decoded_text # ". See more info of the request sent at at: " # response_url;
    result
  };

  //PRIVATE HELPER FUNCTION
  //Helper method that generates a Universally Unique Identifier
  //this method is used for the Idempotency Key used in the request headers of the POST request.
  //For the purposes of this exercise, it returns a constant, but in practice it should return unique identifiers
  func generateUUID() : Text {
    "UUID-123456789";
  }
};
```

- #### Step 3:  Open the `src/send_http_post_backend/Types.mo` file in a text editor and replace content with:

```motoko
module Types {

    //1. Type that describes the Request arguments for an HTTPS outcall
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    public type HttpRequestArgs = {
        url : Text;
        max_response_bytes : ?Nat64;
        headers : [HttpHeader];
        body : ?[Nat8];
        method : HttpMethod;
        transform : ?TransformRawResponseFunction;
    };

    public type HttpHeader = {
        name : Text;
        value : Text;
    };

    public type HttpMethod = {
        #get;
        #post;
        #head;
    };

    public type HttpResponsePayload = {
        status : Nat;
        headers : [HttpHeader];
        body : [Nat8];
    };

    //2. HTTPS outcalls have an optional "transform" key. These two types help describe it.
    //"The transform function may, for example, transform the body in any way, add or remove headers, 
    //modify headers, etc. "
    //See: https://internetcomputer.org/docs/current/references/ic-interface-spec/#ic-http_request
    
    //2.1 This type describes a function called "TransformRawResponse" used in line 14 above
    //"If provided, the calling canister itself must export this function." 
    //In this minimal example for a GET request, we declare the type for completeness, but 
    //we do not use this function. We will pass "null" to the HTTP request.
    public type TransformRawResponseFunction = {
        function : shared query TransformArgs -> async HttpResponsePayload;
        context : Blob;
    };

    //2.2 This type describes the arguments the transform function needs
    public type TransformArgs = {
        response : HttpResponsePayload;
        context : Blob;
    };


    //3. Declaring the IC management canister which we use to make the HTTPS outcall
    public type IC = actor {
        http_request : HttpRequestArgs -> async HttpResponsePayload;
    };

}
```

- #### Step 4: Test the dapp locally.

Deploy the dapp locally:

```bash
dfx start --background
dfx deploy
```

If successful, the terminal should return canister URLs you can open:

```bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    send_http_post_backend: http://127.0.0.1:4943/?canisterId=dccg7-xmaaa-aaaaa-qaamq-cai&id=dfdal-2uaaa-aaaaa-qaama-cai
```

Open the candid web UI for backend and call the `send_http_post_request()` method:

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

## Rust version

### Rust: Structure of the code

Here is how the management canister is declared in a Rust canister (e.g. `lib.rs`):

```rust
//1. DECLARE IC MANAGEMENT CANISTER
use ic_cdk::api::management_canister::http_request::{
    http_request, CanisterHttpRequestArgument, HttpHeader, HttpMethod, HttpResponse, TransformArgs,
    TransformContext,
};

//Update method using the HTTPS outcalls feature
#[ic_cdk::update]
async fn foo() {
    //2. SETUP ARGUMENTS FOR HTTP GET request
    let request = CanisterHttpRequestArgument {
        //instantiate the request
    };

    //3. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE
    //Note: in Rust, `http_request()` already sends the cycles needed 
    //so no need for explicit Cycles.add() as in Motoko
    match http_request(request).await {
        
        //4. DECODE AND RETURN THE RESPONSE
        Ok((response,)) => {
            //Ok case 
        }
        Err((r, m)) => {
            //error case
        }
    }
}
```

### Rust: Step by step

- #### Step 1:  Create a new project by running the following command:

```bash
dfx new --type=rust send_http_post_rust
cd send_http_post_rust
npm install
rustup target add wasm32-unknown-unknown
```

- #### Step 2: Open the `/src/send_http_post_rust_backend/src/lib.rs` file in a text editor and replace content with:

```rust
//1. IMPORT IC MANAGEMENT CANISTER
//This includes all methods and types needed
use ic_cdk::api::management_canister::http_request::{
    http_request, CanisterHttpRequestArgument, HttpHeader, HttpMethod, HttpResponse, TransformArgs,
    TransformContext,
};

//Update method using the HTTPS outcalls feature
#[ic_cdk::update]
async fn send_http_post_request() -> String {
    //2. SETUP ARGUMENTS FOR HTTP GET request

    // 2.1 Setup the URL
    let host = "en8d7aepyq2ko.x.pipedream.net";
    let url = "https://en8d7aepyq2ko.x.pipedream.net/";

    // 2.2 prepare headers for the system http_request call
    //Note that `HttpHeader` is declared in line 4
    let request_headers = vec![
        HttpHeader {
            name: "Host".to_string(),
            value: format!("{host}:443"),
        },
        HttpHeader {
            name: "User-Agent".to_string(),
            value: "demo_HTTP_POST_canister".to_string(),
        },
        //For the purposes of this exercise, Idempotency-Key" is hard coded, but in practice
        //it should be generated via code and unique to each POST request. Common to create helper methods for this
        HttpHeader {
            name: "Idempotency-Key".to_string(),
            value: "UUID-123456789".to_string(),
        },
    ];

    //note "CanisterHttpRequestArgument" and "HttpMethod" are declared in line 4.
    //CanisterHttpRequestArgument has the following types:

    // pub struct CanisterHttpRequestArgument {
    //     pub url: String,
    //     pub max_response_bytes: Option<u64>,
    //     pub method: HttpMethod,
    //     pub headers: Vec<HttpHeader>,
    //     pub body: Option<Vec<u8>>,
    //     pub transform: Option<TransformContext>,
    // }
    //see: https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/struct.CanisterHttpRequestArgument.html

    //Where "HttpMethod" has structure:
    // pub enum HttpMethod {
    //     GET,
    //     POST,
    //     HEAD,
    // }
    //See: https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/enum.HttpMethod.html

    //Since the body in HTTP request has type Option<Vec<u8>> it needs to look something like this: Some(vec![104, 101, 108, 108, 111]) ("hello" in ASCII)
    //where the vector of u8s are the UTF. In order to send JSON via POST we do the following:
    //1. Declare a JSON string to send
    //2. Convert that JSON string to array of UTF8 (u8)
    //3. Wrap that array in an optional
    let json_string: String = r#"{
        "name": "Grogu",
        "force_sensitive": "true",
        "language": "rust"
    }"#
    .to_string();
    //note: here, r#""# is used for raw strings in Rust, which allows you to include characters like " and \ without needing to escape them.
    //We could have used "serde_json" as well.
    let json_utf8: Vec<u8> = json_string.into_bytes();
    let request_body: Option<Vec<u8>> = Some(json_utf8);

    let request = CanisterHttpRequestArgument {
        url: url.to_string(),
        max_response_bytes: None, //optional for request
        method: HttpMethod::POST,
        headers: request_headers,
        body: request_body,
        transform: None, //optional for request
    };

    //3. MAKE HTTPS REQUEST AND WAIT FOR RESPONSE

    //Note: in Rust, `http_request()` already sends the cycles needed
    //so no need for explicit Cycles.add() as in Motoko
    match http_request(request).await {
        //4. DECODE AND RETURN THE RESPONSE

        //See:https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/struct.HttpResponse.html
        Ok((response,)) => {
            //if successful, `HttpResponse` has this structure:
            // pub struct HttpResponse {
            //     pub status: Nat,
            //     pub headers: Vec<HttpHeader>,
            //     pub body: Vec<u8>,
            // }

            //We need to decode that Vec<u8> that is the body into readable text.
            //To do this, we:
            //  1. Call `String::from_utf8()` on response.body
            //  3. We use a switch to explicitly call out both cases of decoding the Blob into ?Text
            let str_body = String::from_utf8(response.body)
                .expect("Transformed response is not UTF-8 encoded.");

            //The API response will looks like this:
            // { successful: true }

            //Return the body as a string and end the method
            let response_url = "https://public.requestbin.com/r/en8d7aepyq2ko";
            let result: String = format!(
                "{}. See more info of the request sent at at: {}",
                str_body, response_url
            );
            result
        }
        Err((r, m)) => {
            let message =
                format!("The http_request resulted into error. RejectionCode: {r:?}, Error: {m}");

            //Return the error as a string and end the method
            message
        }
    }
}
```

- `send_http_post_request() -> String` returns a `String`, but this is not necessary. In this tutorial, this is done for easier testing.
- The `lib.rs` file used [http_request](https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/fn.http_request.html) which is a convenient Rust CDK method that already sends cycles to the IC management canister under the hood. It knows how many cycles to send for a 13-node subnet and most cases. If your HTTPS outcall needs more cycles, you should use [http_request_with_cycles()](https://docs.rs/ic-cdk/latest/ic_cdk/api/management_canister/http_request/fn.http_request_with_cycles.html) method and explicitly call the cycles needed. 
- The Rust CDK method `http_request` used above wraps the IC management canister method [`http_request`](../../../references/ic-interface-spec#ic-http_request), but it is not strictly the same.

- #### Step 3: Open the `src/hello_http_rust_backend/hello_http_rust_backend.did` file in a text editor and replace content with:

We update the Candid interface file so it matches the method `send_http_post_request()` in `lib.rs`. 

```
service : {
    "send_http_post_request": () -> (text);
}
```

- #### Step 4: Test the dapp locally.

Deploy the dapp locally:

```bash
dfx start --background
dfx deploy
```

If successful, the terminal should return canister URLs you can open:

```bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    send_http_post_rust_backend: http://127.0.0.1:4943/?canisterId=dxfxs-weaaa-aaaaa-qaapa-cai&id=dzh22-nuaaa-aaaaa-qaaoa-cai
```

Open the candid web UI for backend and call the `send_http_post_request()` method:

![Candid web UI](../_attachments/https-post-candid-2-motoko.webp)

:::note
In both the Rust and Motoko minimal examples, we did not create a **transform** function so that it transforms the raw response. This is something we will explore in a following section
:::

## Additional resources

* Sample code of [HTTP POST requests in Rust](https://github.com/dfinity/examples/tree/master/rust/send_http_post)
* Sample code of [HTTP POST requests in Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)
-->
