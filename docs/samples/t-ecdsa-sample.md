# 閾値ECDSA署名コード・ウォークスルー

## 概要

しきい値[ECDSA](../developer-docs/integrations/t-ecdsa/index.md)APIを紹介するための最小限の例canister スマートコントラクトを紹介します。

この例canister は、入力文字列から派生した鍵で ECDSA 署名を作成する署名オラクルです。

具体的には

- サンプルcanister は、メッセージを提供するリクエストを受け取ります。
- サンプルcanister はメッセージをハッシュし、派生パスに鍵派生文字列を使用します。
- サンプルcanister 、上記を使用して閾値ECDSA[サブネットに](https://wiki.internetcomputer.org/wiki/Subnet_blockchain)署名を要求します(閾値ECDSAは閾値ECDSA署名を生成することに特化したサブネットです)。

このチュートリアルでは、[IC SDKの](../developer-docs/setup/index.md)ダウンロードから始まり、ICメインネット上でのコードのデプロイと試用まで、開発の完全な概要を説明します。

:::info
このチュートリアルでは、canister のサンプルコードをプログラミング言語で記述したバージョンに焦点を当てています。 [Motoko](../developer-docs/backend/motoko/index.md)プログラミング言語で書かれたバージョンのサンプルコードに焦点を当てていますが、Motoko の特別な知識は必要ありません。同じリポジトリに[Rust](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa)バージョンもあり、デプロイも同じコマンドに従います。
::：

## 前提条件

- \[x\][IC SDKを](../developer-docs/setup/index.md)ダウンロードして[インストールして](../developer-docs/setup/index.md)ください。
- \[x\][examplesリポジトリを](https://github.com/dfinity/examples)クローンしてください。

## ステップ 1: スタート

`threshold-ecdsa` のサンプルコードは[examples リポジトリの](https://github.com/dfinity/examples)[`/motoko`](https://github.com/dfinity/examples/tree/master/motoko/threshold-ecdsa)または [`/rust`](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa)サブディレクトリにあります。ローカルでの開発には、少なくとも[IC SDK](../developer-docs/setup/index.md)バージョン0.11.0が必要です。

### canister をローカルにデプロイしてテストしてください。

このチュートリアルではcanister のMotoko バージョンを使用します：

``` bash
cd examples/motoko/threshold-ecdsa
dfx start --background
npm install
dfx deploy
```

#### 何をするかというと

- `dfx start --background` IC SDKを介してICのローカルインスタンスを起動します。
- `dfx deploy` ユーザーのディレクトリにあるコードを として IC のローカルバージョンにデプロイします。canister 

成功すると、このように表示されます：

``` bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    ecdsa_example_motoko: http://127.0.0.1:4943/?canisterId=t6rzw-2iaaa-aaaaa-aaama-cai&id=st75y-vaaaa-aaaaa-aaalq-cai
```

このURLをウェブブラウザで開くと、canister が公開するパブリックメソッドを示すウェブUIが表示されます。canister は`public_key` と`sign` のメソッドを公開しているので、それらは Web UI にレンダリングされます：

![Candid UI](./_attachments/tecdsa-candid-ui.png)

## ステップ 2: IC メインネットへのcanister のデプロイ

このcanister をICメインネットにデプロイするには、2つのことを行う必要があります：

- cycles （他のブロックチェーンでは「ガス」に相当）を取得します。これはすべてのcanisters に必要です。
- 正しいキーIDを持つようにサンプルのソースコードを更新します。これはこのcanister に固有のものです。

### デプロイするためにcycles を取得します。

Internet Computer にデプロイするには [cycles](../developer-docs/setup/cycles).[cycles の蛇口から](/developer-docs/setup/cycles/cycles-faucet.md) cycles を無料で入手できます。

### 正しいキーIDでソースコードを更新

サンプルコードをデプロイするには、canister 、正しい環境の正しいキーIDが必要です。具体的には、サンプル・コードの`src/ecdsa_example_motoko/main.mo` ファイルの`key_id` の値を置き換える必要があります。メインネットにデプロイする前に、`key_id` の正しい名前を使用するようにコードを修正する必要があります。

オプションは3つあります：

- `dfx_test_key`ローカルバージョンのICにデプロイする際に使用するデフォルトのキーID（IC SDK経由）。
- `test_key_1`メインネットで使用されるマスター・**テスト**鍵ID。
- `key_1`メインネットで使用されるマスター・**プロダクション・**キーID。

例えば、`src/ecdsa_example_motoko/main.mo` のデフォルトコードには以下の行が含まれており、ローカルにデプロイすることができます：

:::caution
次の例は、より大きなコード・ファイルの一部である 2 つの**コード・スニペット**です。
::：

``` motoko
let { public_key } = await ic.ecdsa_public_key({
  canister_id = null;
  derivation_path = [ caller ];
  key_id = { curve = #secp256k1; name = "dfx_test_key" };
});
```

``` motoko
let { signature } = await ic.sign_with_ecdsa({
  message_hash;
  derivation_path = [ caller ];
  key_id = { curve = #secp256k1; name = "dfx_test_key" };
});
```

`"test_key_1"` `"key_1"`
:::caution
ICメインネットにデプロイするには、`key_id` のフィールドの値を`"dfx_test_key"` の値で置き換える必要があります：

### IC SDKを使ったメインネットへのデプロイ

メイン[ネットに](../developer-docs/setup/deploy-mainnet.md)デプロイするには、以下のコマンドを実行します：

``` bash
npm install
dfx deploy --network ic
```

成功すると以下のようになります：

``` bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    ecdsa_example_motoko: https://a3gq9-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=736w4-cyaaa-aaaal-qb3wq-cai
```

上記の例では、`ecdsa_example_motoko` は https://a3gq9-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=736w4-cyaaa-aaaal-qb3wq-cai という URL を持ち、メインネットにデプロイされたcanister の Candid Web UI を提供します。

## ステップ3： 公開鍵の取得

### CandidウェブUIの使用

canister をローカルまたはメインネットにデプロイした場合、パブリックメソッドにアクセスできる Candid ウェブ UI への URL があるはずです。`public-key` メソッドを呼び出すことができます：

![Public key method](./_attachments/tecdsa-candid-public-key.png)

以下の例では、このメソッドは公開鍵として`03c22bef676644dba524d4a24132ea8463221a55540a27fc86d690fda8e688e31a` を返します。

``` json
{
  "Ok":
  {
    "public_key_hex": "03c22bef676644dba524d4a24132ea8463221a55540a27fc86d690fda8e688e31a"
  }  
}
```

### コードウォークスルー

`main.mo`ECDSA 公開鍵を取得する方法を示す以下のMotoko コードが表示されます。

``` motoko
  //declare "ic" to be the management canister, which is evoked by `actor("aaaaa-aa")`. This is how we will obtain an ECDSA public key 
  let ic : IC = actor("aaaaa-aa");

  public shared (msg) func public_key() : async { #Ok : { public_key: Blob }; #Err : Text } {
    let caller = Principal.toBlob(msg.caller);
    
    try {

      //request the management canister to compute an ECDSA public key
      let { public_key } = await ic.ecdsa_public_key({

          //When `null`, it defaults to getting the public key of the canister that makes this call
          canister_id = null;
          derivation_path = [ caller ];
          //this code uses the mainnet test key
          key_id = { curve = #secp256k1; name = "test_key_1" };
      });
      
      #Ok({ public_key })
    
    } catch (err) {
    
      #Err(Error.message(err))
    
    }

  };
```

上記のコードでは、canister が[IC 管理canister](../references/ic-interface-spec/#ic-management-canister)(`aaaaa-aa`) の`ecdsa_public_key` メソッドを呼び出しています。

:::info
[IC管理canister](../references/ic-interface-spec/#ic-management-canister)は単なるファサードであり、実際にはcanister （分離ステート、Wasmコードなどを持つ）としては存在しません。これは、canisters 、ICのシステムAPIを（あたかも単一のcanister ）呼び出すための人間工学的な方法です。以下のコードでは、管理canister を使用してECDSA公開鍵を作成します。`let ic : IC = actor("aaaaa-aa")` は、上記のコードでIC管理canister を宣言しています。
::：

### Canister ルート公開鍵

canister のルート公開鍵を取得するために、API の派生パスは単に空のままにしておくことができます。

### 鍵の導出

- BIP-32の鍵導出階層において、ルート鍵以下のcanister'の公開鍵を取得するためには、導出パスを指定する必要があります。一般的なドキュメントで説明されているように、 派生パスの配列の各要素は、ビッグエンディアンで 4 バイトにエンコードされた 32 ビットの整数、 あるいは任意の長さのバイト配列のいずれかです。この要素を使用して、派生階層の対応するレベルのキーを導出します。
- 上のコード例では、`msg.caller` プリンシパルから抽出したバイトを`derivation_path` で使用しています。これにより、canister の`public_key()` メソッドを呼び出すさまざまな人が、それぞれの公開鍵を取得できるようになります。

## ステップ4：署名

閾値ECDSA署名の計算は、この機能の中核となる機能である。**Canisters 、 ECDSA鍵自体は保持しない**が、鍵は専用サブネットが保持するマスター鍵から導出される。canister はcanister の管理APIを通じて署名の計算を要求することができます。リクエストは指定された鍵を保持するサブネットにルーティングされ、サブネットは閾値暗号を使用してリクエストされた署名を計算します。これにより、サブネットはcanister ルート鍵、または署名プロトコルの一部として、共有秘密と要求元のcanister のプリンシパル識別子からさらに派生して得られた鍵を導出します。したがって、canister は、canister ルート鍵またはそこから派生した鍵に対してのみ署名の作成を要求することができる。つまり、canisters 、ECDSA秘密鍵を使用して署名を作成するタイミングを決定するという点で、ECDSA秘密鍵を「制御」していることになりますが、秘密鍵そのものを保持しているわけではありません。

``` motoko
  public shared (msg) func sign(message_hash: Blob) : async { #Ok : { signature: Blob };  #Err : Text } {
    assert(message_hash.size() == 32);
    let caller = Principal.toBlob(msg.caller);
    try {
      Cycles.add(10_000_000_000);
      let { signature } = await ic.sign_with_ecdsa({
          message_hash;
          derivation_path = [ caller ];
          key_id = { curve = #secp256k1; name = "dfx_test_key" };
      });
      #Ok({ signature })
    } catch (err) {
      #Err(Error.message(err))
    }
  };
```

## ステップ5：署名の検証

この例の完全性を期すため、作成された署名が、同じcanister および 派生パスに対応する公開鍵で検証できることを示します。

以下は、[secp256k1](https://www.npmjs.com/package/secp256k1)npmパッケージを使用して、この検証をJavascriptで行う方法を示しています：

``` javascript
let { ecdsaVerify } = require("secp256k1")

let public_key = ... // Uint8Array type, the result of calling the above canister "public_key" function.
let hash = ...       // 32-byte Uint8Array representing a binary hash (e.g. sha256).
let signature = ...  // Uint8Array type, the result of calling the above canister "sign" function on `hash`.

let verified = ecdsaVerify(signature, hash, public_key)
```

`ecdsaVerify` 関数を呼び出すと、必ず`true` が返されます。

同様の検証は、`secp256k1` 曲線をサポートする暗号ライブラリの助けを借りて、他の多くの言語でも行うことができます。

## 結論

このチュートリアルでは、次のようなスマート・コントラクトのサンプルをデプロイしました：

- **canisters 自身がECDSA鍵を**保持していないにもかかわらず、ECDSA秘密鍵で署名。
- 公開鍵のリクエスト。
- 署名検証の実行。

<!---
# Threshold ECDSA signing code walkthrough

## Overview

We present a minimal example canister smart contract for showcasing the [threshold ECDSA](../developer-docs/integrations/t-ecdsa/index.md) API. 

The example canister is a signing oracle that creates ECDSA signatures with keys derived from an input string. 

More specifically:

- The sample canister receives a request that provides a message.
- The sample canister hashes the message and uses the key derivation string for the derivation path. 
-  The sample canister uses the above to request a signature from the threshold ECDSA [subnet](https://wiki.internetcomputer.org/wiki/Subnet_blockchain) (the threshold ECDSA is a subnet specializing in generating threshold ECDSA signatures).

This tutorial gives a complete overview of the development, starting with downloading of the [IC SDK](../developer-docs/setup/index.md), up to the deployment and trying out of the code on the IC mainnet.

:::info
This walkthrough focuses on the version of the sample canister code written in [Motoko](../developer-docs/backend/motoko/index.md) programming language, but no specific knowledge of Motoko is needed to follow along. There is also a [Rust](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa) version available in the same repo and follows the same commands for deploying.
:::

## Prerequisites
-   [x] Download and [install the IC SDK](../developer-docs/setup/index.md) if you do not already have it.
-   [x] Clone the [examples repository](https://github.com/dfinity/examples).

## Step 1: Getting started

Sample code for `threshold-ecdsa` is provided in the [examples repository](https://github.com/dfinity/examples), under either [`/motoko`](https://github.com/dfinity/examples/tree/master/motoko/threshold-ecdsa) or [`/rust`](https://github.com/dfinity/examples/tree/master/rust/threshold-ecdsa) sub-directories. It requires at least [IC SDK](../developer-docs/setup/index.md) version 0.11.0 for local development.

### Deploy and test the canister locally 

This tutorial will use the Motoko version of the canister:

```bash
cd examples/motoko/threshold-ecdsa
dfx start --background
npm install
dfx deploy
```

#### What this does
- `dfx start --background` starts a local instance of the IC via the IC SDK
- `dfx deploy` deploys the code in the user's directory as a canister on the local version of the IC

If successful, you should see something like this:

```bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    ecdsa_example_motoko: http://127.0.0.1:4943/?canisterId=t6rzw-2iaaa-aaaaa-aaama-cai&id=st75y-vaaaa-aaaaa-aaalq-cai
```

If you open the URL in a web browser, you will see a web UI that shows the public methods the canister exposes. Since the canister exposes `public_key` and `sign` methods, those are rendered in the web UI:

 ![Candid UI](./_attachments/tecdsa-candid-ui.png)


## Step 2: Deploying the canister on the IC mainnet

To deploy this canister on the IC mainnet, one needs to do two things:

- Acquire cycles (equivalent of "gas" in other blockchains). This is necessary for all canisters.
- Update the sample source code to have the right key ID. This is unique to this canister.

### Acquire cycles to deploy

Deploying to the Internet Computer requires [cycles](../developer-docs/setup/cycles). You can get free cycles from the [cycles faucet](/developer-docs/setup/cycles/cycles-faucet.md).

### Update source code with the right key ID

To deploy the sample code, the canister needs the right key ID for the right environment. Specifically, one needs to replace the value of the `key_id` in the `src/ecdsa_example_motoko/main.mo` file of the sample code. Before deploying to the mainnet, one should modify the code to use the right name of the `key_id`.

There are three options:

* `dfx_test_key`: a default key ID that is used in deploying to a local version of IC (via IC SDK).
* `test_key_1`: a master **test** key ID that is used on the mainnet.
* `key_1`: a master **production** key ID that is used on the mainnet.

For example, the default code in `src/ecdsa_example_motoko/main.mo` includes the following lines and can be deployed locally:

:::caution
The following example are two **code snippets** that are part of a larger code file. These snippets may return an error if run on their own.
:::

```motoko
let { public_key } = await ic.ecdsa_public_key({
  canister_id = null;
  derivation_path = [ caller ];
  key_id = { curve = #secp256k1; name = "dfx_test_key" };
});
```

```motoko
let { signature } = await ic.sign_with_ecdsa({
  message_hash;
  derivation_path = [ caller ];
  key_id = { curve = #secp256k1; name = "dfx_test_key" };
});
```

:::caution
To deploy to the IC mainnet, one needs to replace the value in `key_id` fields with the values `"dfx_test_key"` to instead have either `"test_key_1"` or `"key_1"` depending on the desired intent.
:::

### Deploy to the mainnet via IC SDK

To [deploy on the mainnet](../developer-docs/setup/deploy-mainnet.md), run the following commands:

```bash
npm install
dfx deploy --network ic
```
If successful, you should see something like this:

```bash
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    ecdsa_example_motoko: https://a3gq9-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=736w4-cyaaa-aaaal-qb3wq-cai
```

In example above, `ecdsa_example_motoko` has the URL https://a3gq9-oaaaa-aaaab-qaa4q-cai.raw.icp0.io/?id=736w4-cyaaa-aaaal-qb3wq-cai and serves up the Candid web UI for this particular canister deployed on the mainnet.

## Step 3: Obtaining public keys

### Using the Candid web UI

If you deployed your canister locally or to the mainnet, you should have a URL to the Candid web UI where you can access the public methods. We can call the `public-key` method:

![Public key method](./_attachments/tecdsa-candid-public-key.png)

In the example below, the method returns `03c22bef676644dba524d4a24132ea8463221a55540a27fc86d690fda8e688e31a` as the public key.

```json
{
  "Ok":
  {
    "public_key_hex": "03c22bef676644dba524d4a24132ea8463221a55540a27fc86d690fda8e688e31a"
  }  
}
```


### Code walkthrough
Open the file `main.mo`, which will show the following Motoko code that demonstrates how to obtain an ECDSA public key. 

```motoko
  //declare "ic" to be the management canister, which is evoked by `actor("aaaaa-aa")`. This is how we will obtain an ECDSA public key 
  let ic : IC = actor("aaaaa-aa");

  public shared (msg) func public_key() : async { #Ok : { public_key: Blob }; #Err : Text } {
    let caller = Principal.toBlob(msg.caller);
    
    try {

      //request the management canister to compute an ECDSA public key
      let { public_key } = await ic.ecdsa_public_key({

          //When `null`, it defaults to getting the public key of the canister that makes this call
          canister_id = null;
          derivation_path = [ caller ];
          //this code uses the mainnet test key
          key_id = { curve = #secp256k1; name = "test_key_1" };
      });
      
      #Ok({ public_key })
    
    } catch (err) {
    
      #Err(Error.message(err))
    
    }

  };
```

In the code above, the canister calls the `ecdsa_public_key` method of the [IC management canister](../references/ic-interface-spec/#ic-management-canister) (`aaaaa-aa`). 


:::info
The [IC management canister](../references/ic-interface-spec/#ic-management-canister) is just a facade; it does not actually exist as a canister (with isolated state, Wasm code, etc.). It is an ergonomic way for canisters to call the system API of the IC (as if it were a single canister). In the code below, we use the management canister to create an ECDSA public key. `let ic : IC = actor("aaaaa-aa")` declares the IC management canister in the code above.
:::

### Canister root public key

For obtaining the canister's root public key, the derivation path in the API can be simply left empty.

### Key derivation

-   For obtaining a canister's public key below its root key in the BIP-32 key derivation hierarchy, a derivation path needs to be specified. As explained in the general documentation, each element in the array of the derivation path is either a 32-bit integer encoded as 4 bytes in big endian or a byte array of arbitrary length. The element is used to derive the key in the corresponding level at the derivation hierarchy.
-   In the example code above, we use the bytes extracted from the `msg.caller` principal in the `derivation_path`, so that different callers of `public_key()` method of our canister will be able to get their own public keys.

## Step 4: Signing

Computing threshold ECDSA signatures is the core functionality of this feature. **Canisters do not hold ECDSA keys themselves**, but keys are derived from a master key held by dedicated subnets. A canister can request the computation of a signature through the management canister API. The request is then routed to a subnet holding the specified key and the subnet computes the requested signature using threshold cryptography. Thereby, it derives the canister root key or a key obtained through further derivation, as part of the signature protocol, from a shared secret and the requesting canister's principal identifier. Thus, a canister can only request signatures to be created for their canister root key or a key derived from it. This means, canisters "control" their private ECDSA keys in that they decide when signatures are to be created with them, but don't hold a private key themselves.

```motoko
  public shared (msg) func sign(message_hash: Blob) : async { #Ok : { signature: Blob };  #Err : Text } {
    assert(message_hash.size() == 32);
    let caller = Principal.toBlob(msg.caller);
    try {
      Cycles.add(10_000_000_000);
      let { signature } = await ic.sign_with_ecdsa({
          message_hash;
          derivation_path = [ caller ];
          key_id = { curve = #secp256k1; name = "dfx_test_key" };
      });
      #Ok({ signature })
    } catch (err) {
      #Err(Error.message(err))
    }
  };
```

## Step 5: Signature verification

For completeness of the example, we show that the created signatures can be verified with the public key corresponding to the same canister and derivation path.

The following shows how this verification can be done in Javascript, with the [secp256k1](https://www.npmjs.com/package/secp256k1) npm package:

```javascript
let { ecdsaVerify } = require("secp256k1")

let public_key = ... // Uint8Array type, the result of calling the above canister "public_key" function.
let hash = ...       // 32-byte Uint8Array representing a binary hash (e.g. sha256).
let signature = ...  // Uint8Array type, the result of calling the above canister "sign" function on `hash`.

let verified = ecdsaVerify(signature, hash, public_key)
```

The call to `ecdsaVerify` function should always return `true`.

Similar verifications can be done in many other languages with the help of cryptographic libraries support the `secp256k1` curve.

## Conclusion

In this walkthrough, we deployed a sample smart contract that:

* Signed with private ECDSA keys even though **canisters do not hold ECDSA keys themselves**.
* Requested a public key.
* Performed signature verification.

-->
