# 外部エージェントとICの統合

## 概要

Internet Computer エコシステムでは、IC のパブリックインタフェースを呼び出すために使用されるライブラリを**エージェントと**呼びます。エージェントにはいくつかの重要な責務があり、選択した言語で作業するのに便利です。

canister をローカルマシンまたはInternet Computer 上で稼働させている場合、canister スマートコントラクトと対話する2つの主な方法があります。
インターフェース仕様に従った**エージェントを**使用して、v2 API を使用してcanister と対話することも、canister の HTTP インターフェースを使用することもできます。

## 利用可能なエージェント

このセクションでは、言語順に以下のエージェントについて説明します：

- JavaScript / TypeScript
  - [JavaScript/TypeScriptエージェントDFINITY](./javascript-intro.md)
- Rust
  - [による Rust エージェントDFINITY](./ic-agent-dfinity.md)

これらに加えて、コミュニティがサポートしているエージェントがいくつかあります：

- .NET
  - [`ICP.NET` Gekctek](https://github.com/Gekctek/ICP.NET)
- Dart
  - [`agent_dart` ](https://github.com/AstroxNetwork/agent_dart)by[AstroX](https://github.com/AstroxNetwork/agent_dart)(Flutterによるモバイル開発をサポート)
  - [`ic_dart_tools` Levi Feldmanによる](https://github.com/levifeldman/ic_tools_dart)
- Go
  - [`IC-Go` ミックスラボ](https://github.com/mix-labs/IC-Go)
  - [`agent-go` Aviate Labsによる](https://github.com/aviate-labs/agent-go)
- Java
  - [`ic4j-agent` IC4J](https://github.com/ic4j/ic4j-agent)(Androidをサポート)
- Python
  - [`ic-py` ロックラボ](https://github.com/rocklabs-io/ic-py)
- C
  - [`agent-c` Zondax](https://github.com/Zondax/icp-client-cpp)(IC Rust AgentのCラッパー)
- Ruby
  - [`ic_agent` Terry.Tu](https://github.com/tuminfei/ic_agent)

他の言語でのエージェント作成に興味がある方は、<https://dfinity.org/grants> までご連絡ください。

## エージェントが行うこと

### データの構造化

Internet Computer に対する`call` は、`update` または`query` の2つの一般的な形式を取ることができます。**エージェントは**、`/api/v2/canister/<effective_canister_id>/call` に`POST` リクエストを送信し、以下のコンポーネントを含みます：

- `request_type`
- 認証
  - `sender` `nonce` `ingress_expiry`
- `canister_id`
- `method_name`
- `request_id` - `update` リクエストタイプの呼び出しに必要
- `arg` - 残りのペイロード

canister の Candid インターフェースを知ることで、**エージェントは** `"arg"` をクライアントアプリケーションからのデータでアセンブルします。上記のすべてのコンポーネントは、次に証明書にアセンブルされ、CBORエンコードされたバッファに変換されます。

更新リクエストの場合、エージェントは残りのフィールドもハッシュし、一意の`request_id` として渡します。この`request_id` は、ICが更新に関するコンセンサスを得る間のポーリングに使用されます。

**エージェントは**CBORエンコードされた証明書を受け取り、`POST` リクエストのボディに添付します。canister はそのリクエストに対して非同期に動作し、canister レスポンスの準備ができるまで、**エージェントは** `read_state` リクエストでポーリングを開始できます。

### データのデコード

ICからデータが返されると、**エージェントは**ペイロードから証明書を取り出し、それを検証します。証明書はNNSサブネットのパブリック`rootKey` を使って本物であることを確認できます。ネットワークはCBORエンコードされたバッファで応答し、**エージェントは**それをデコードし、意味言語固有の型を使用して有用な構造に変換することができます。例えば、canister から返された型が`text` の場合、JavaScript の`string` に変換されます。

### 認証の管理

Internet Computer への呼び出しには、常に暗号化されたIDが必要です。そのIDは**匿名か**、暗号署名を使用して**認証さ**れます。IDは必須であるため、canisters 、呼に付加されたIDを使用して、 その呼にどのように応答するかを決定することができます。これにより、コントラクトはそのIDを他の目的に使用することができ るようになります。

#### 受け入れ可能なID

ICは、以下の種類の署名をIDに使用した呼を受け付けます：

- Ed25519およびECDSA署名。
  - Ed25519署名とECDSA署名。
- Ed25519または曲線P-256上のECDSA（secp256r1としても知られる）。
  - ハッシュ関数として SHA-256 を使用。
  - secp256k1のKoblitz曲線を使用。

これらの ID を`principal` としてエンコードする場合、エージェントは、ID が自己認証か匿名かを示すサフィックスバイトを付加します。

上記のいずれかの曲線を使用する自己認証 ID のサフィックスは 2 である。

匿名IDは1バイトの4.テキストエンコーディングでは`"2vxsx-fae"` に解決されます。

<!---
# Integrating external agents with the IC

## Overview

In the Internet Computer ecosystem, a library that is used to make calls to the IC public interface is called an **agent**. An agent has a few key responsibilities, which make it convenient to work with in your language of choice.

If you have a canister running, either on your local machine or live on the Internet Computer, you will have two main ways to interact with your canister smart contract.
You can talk to the canister using the v2 API using an **agent** that follows the interface specification, or you can use the canister's HTTP interface.

## Available agents

This section of the docs covers the following agents, ordered by languages:

- JavaScript / TypeScript
  - [JavaScript/TypeScript agent by DFINITY](./javascript-intro.md)
- Rust
  - [Rust agent by DFINITY](./ic-agent-dfinity.md)

In addition to those, there are several other community-supported agents:

- .NET
  - [`ICP.NET` by Gekctek](https://github.com/Gekctek/ICP.NET)
- Dart
  - [`agent_dart` by AstroX](https://github.com/AstroxNetwork/agent_dart) (supports mobile development with Flutter)
  - [`ic_dart_tools` by Levi Feldman](https://github.com/levifeldman/ic_tools_dart)
- Go
  - [`IC-Go` by MixLabs](https://github.com/mix-labs/IC-Go)
  - [`agent-go` by Aviate Labs](https://github.com/aviate-labs/agent-go)
- Java
  - [`ic4j-agent` by IC4J](https://github.com/ic4j/ic4j-agent) (supports Android)
- Python
  - [`ic-py` by Rocklabs](https://github.com/rocklabs-io/ic-py)
- C
  - [`agent-c` by Zondax](https://github.com/Zondax/icp-client-cpp) (C Wrapper for IC Rust Agent)
- Ruby
  - [`ic_agent` by Terry.Tu](https://github.com/tuminfei/ic_agent)

If you're interested in building an agent in another language please reach out to us via [https://dfinity.org/grants](https://dfinity.org/grants).

## What an agent does

### Structuring data

A `call` to the Internet Computer can take two common forms - an `update` or a `query`. The **agent** submits a `POST` request to `/api/v2/canister/<effective_canister_id>/call`, and includes the following components:

- `request_type`
- Authentication
  - `sender`, `nonce`, and `ingress_expiry`
- `canister_id`
- `method_name`
- `request_id` - required for `update` request type calls
- `arg` - the rest of the payload

By knowing the Candid interface of the canister, the **agent** will assemble the `"arg"` with data from the client application, ensuring it matches the Candid interface for the method it will be calling. All of the above components are then assembled into a certificate, which is transformed into a CBOR-encoded buffer.

For update requests, the agent also hashes the rest of the fields, and passes it in as a unique `request_id`. That `request_id` is used for polling while the IC reaches consensus on the update.

The **agent** takes the CBOR-encoded certificate and attaches it to the body of the `POST` request. The canister will work on that request asynchronously, and then the **agent** can begin polling with `read_state` requests, until the canister response is ready.

### Decoding data

Once the data has been returned from the IC, the **agent** takes the certificate from the payload and verifies it. The certificate can be verified as genuine using the public `rootKey` of the NNS subnet. The network will respond with a CBOR-encoded buffer, which the **agent** can then decode, and transform into a useful structure using semantic language-specific types. For example, if the type returned from the canister is `text`, that will get turned into a JavaScript `string`, and so on.

### Managing authentication

Calls to the Internet Computer always need to have a cryptographic identity attached. That identity will either be **anonymous** or **authenticated**, using a cryptographic signature. Since identities are required, canisters can use the identity attached to a call to decide how to respond to that call. This enables contracts to use those identities for other purposes.

#### Accepted identities

The IC accepts calls using the following types of signatures in identities:

- Ed25519 and ECDSA signatures.
  - Plain signatures are supported for the schemes.
- Ed25519 or ECDSA on curve P-256 (also known as secp256r1).
  - using SHA-256 as a hash function.
  - using the Koblitz curve in secp256k1.

When encoding these identities as a `principal`, agents attach a suffix byte, indicating whether the identity is self-authenticating or anonymous.

A self-authenticating identity using one of the above curves will have a suffix of 2.

While the anonymous identity is a single byte 4. It resolves to `"2vxsx-fae"`, in its textual encoding.

-->
