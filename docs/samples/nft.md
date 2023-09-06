# NFT の発行

この例では、NFTcanister の実装を示します。NFT（non-fungible tokens）とは、任意の
メタデータ（通常は何らかの画像）を持つ一意のトークンで、トレーディングカードに相当するデジタルを形成します。Internet Computer （例：[EXT](https://github.com/Toniq-Labs/extendable-token)、[IC-NFT](https://github.com/rocklabs-io/ic-nft)）にはいくつかの異なる
NFT規格がありますが、このチュートリアルでは[DIP-721を](https://github.com/Psychedelic/DIP721)使用します。[YouTubeに](https://youtu.be/1po3udDADp4)簡単な紹介があります。

canister は規格の基本的な実装で、発行、バーン、通知インターフェイスの拡張をサポートしています。

サンプルコードは[Rustの](https://github.com/dfinity/examples/tree/master/rust/dip721-nft-container) [samplesリポジトリと](https://github.com/dfinity/examples)[Motoko](https://github.com/dfinity/examples/tree/master/motoko/dip721-nft-container).

コマンドラインの長さに制限があるため、画像や動画のような大きなファイルを`dfx` でNFTに発行することはできません。そのため、
、シンプルなNFTを発行するための[コマンドラインミントツールが](https://github.com/dfinity/experimental-minting-tool)用意されています。

## 概要

D[IP-721](https://github.com/Psychedelic/DIP721)規格ではほとんどの[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)操作が規定されているため、NFTcanister はそれほど複雑ではありませんが、dapp の開発に関する 3 つの重要な概念を説明するためにInternet Computer を使用することができます：

- #### 1.canister アップグレードのための安定したメモリ。

Internet Computer は[直交永続性を](/motoko/main/motoko.md#orthogonal-persistence)採用しているため、開発者は一般的にデータの保存について深く考える必要はありません。
しかし、canister コードをアップグレードする際には、canister データを明示的に扱う必要があります。NFTcanister の例では、`pre_upgrade` と`post_upgrade` を使って安定したメモリをどのように扱うかを示しています。

- #### 2.認定データ。

一般に、関数がデータを読み取るだけの場合、canister のステートを変更するのではなく、
[アップデートコールの代わりにクエリーコールを](https://smartcontracts.org/docs/current/concepts/canisters-code.md#query-and-update-methods)使用することが有益です。
しかし、クエリーコールはコンセンサスを経由しないため、可能な限り[認証応答](https://internetcomputer.org/docs/current/developer-docs/security/general-security-best-practices)
を使用する必要があります。Rust実装のHTTPインターフェースは、認証済みデータをどのように処理できるかを示しています。

- #### 3.アセットに対する制御の委譲

NFTcanister の例には、このようなケースがすべて含まれており、その方法を示しています。

## アーキテクチャ

[DIP-721](https://github.com/Psychedelic/DIP721)で必要とされる基本的な機能は実装が非常に簡単であるため、このセクションでは上記のアイデアがどのように扱われ、実装されるかについてのみ説明します。

### canister アップグレードのための安定したストレージ

canister コードアップグレード中、異なるcanister 呼び出し間でメモリは永続化されません。
そのため、アップグレードが行われる前に、すべてのデータを安定したメ モリに書き込む必要があり、これは通常`pre_upgrade` 関数で行われます。アップグレード後は、`post_upgrade` 関数で、安定したメモリからメモリ
にデータをロードするのが通常です。`post_upgrade` 関数は、アップグレードが行われた後にシステムから呼び出されます。
アップグレードの一部（`post_upgdrade` を含む）でエラーが発生した場合、アップグレードはすべて元に戻されます。

Rust CDK (Canister Development Kit) は現在、安定したメモリに1つの値しかサポートしていません。そのため、気になるものをすべて保持できるオブジェクトを作成する必要があります。
さらに、すべてのデータ型を安定したメモリに格納できるわけではありません。[CandidType 特性](https://docs.rs/candid/latest/candid/types/trait.CandidType.html)
 を実装したものだけが (通常は[CandidType derive マクロによって](https://docs.rs/candid/latest/candid/derive.CandidType.html)) 安定したメモリに書き込めます。

canister のステートには、`CandidType` を実装していない`RbTree` が含まれているため、`CandidType` を実装したデータ構造（この場合は`Vec` ）に変換する必要があります。
幸いなことに、`RbTree` と`Vec` の両方がイテレータへの変換/イテレータからの変換を可能にする関数を実装しているため、変換は非常に簡単に行うことができます。
変換後は、アップグレード中のデータを格納するために、別の`StableState` オブジェクトが使用されます。

### 認証データ

`<canister-id>.raw.icp0.io` ではなく`<canister-id>.icp0.io` を介して HTTP でアセットを提供するには、
 レスポンスに、そのコンテンツを検証するための[証明書を含める](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification)必要があります。
このような証明書の取得は、コンセンサスを経由する必要があるため、クエリーコール中に行うことはできません。

証明書の内容は非常に限られています。この記事を書いている時点では、canisters 、証明書を取得するために提出できるデータは32バイト以下である。
その少量のデータを最大限に活用するために、`HashTree` (前節の`RbTree` も`HashTree`)が使用される。
 `HashTree` 、ツリー型のデータ構造で、ツリー全体を32バイトの小さなハッシュにまとめる(ハッシュする)ことができる。
ツリーのコンテンツが変更されるたびに、ハッシュも変更される。このようなツリーのハッシュが認証されている場合、ツリーのコンテンツが認証されていると見なすことができます。
NFTの例canister でデータがどのように認証されているかを見るには、`http.rs` の関数`add_hash` をご覧ください。

レスポンスが検証されるためには、a)提供されたコンテンツがツリーの一部であり、b)そのコンテン ツを含むツリーが実際に認証されたハッシュにハッシュできることが確認されなければなりませ ん。
関数`witness` は、a)とb)を満たすことが検証可能な最小限のコンテンツを含むツリーを作成する役割を担います。
この最小限のツリーが構築されると、証明書と最小限のハッシュツリーが`IC-Certificate` ヘッダーの一部として送信されます。

証明書の仕組みについてのより詳細な説明は、[こちらの説明ビデオを](https://internetcomputer.org/how-it-works/response-certification)ご覧ください。

### 資産の管理

[DIP-721では](https://github.com/Psychedelic/DIP721)、NFTに対する複数の管理レベルを規定しています：

- **所有者**：NFTの所有者。NFTの譲渡、オペレーターの追加・削除、NFTのバーンが可能。
- **オペレーター**：委任された所有者のようなもの。オペレーターはNFTを所有しませんが、所有者と同様の操作が可能です。
- **カストディアン**：NFTコレクション/canister の作成者。NFTに対して何でも（譲渡、オペレータの追加/削除、バーン、バーン解除）できますが、新しいものを発行したり、コレクションのシンボルや説明を変更することもできます。

NFTの例canister は、これら3つのレベルでのアクセス制御を非常にシンプルなものにしています：

- 制御の各レベルについて、プリンシパルの別個のリスト（またはセット）が保持されます。
- 制御レベルごとに、プリンシパルの個別のリスト（またはセット）が保持されます。これらの3つのレベルは、誰かが認証を必要とすることを行おうとするたびに手動でチェックされます。
- ユーザーが特定の関数を呼び出す権限がない場合、エラーが返されます。

NFTのバーンは特殊なケースです。NFTをバーンするとは、NFTを削除するか（DIP-721では意図されていません）、所有権を`null` （または同様の値）に設定することを意味します。
 Internet Computer では、この存在しないプリンシパルを[管理canister](https://smartcontracts.org/docs/current/references/ic-interface-spec.md#ic-management-canister) と呼びます。
リンクから引用します：「IC管理canister は単なるファサードであり、実際にはcanister （分離ステート、ワズムコードなどを持つ）としては存在しません。」そのアドレスは`aaaaa-aa` 。
この管理canister アドレスを使用して、そのプリンシパルを構築し、管理canister をバーンNFTのオーナーに設定することができます。

## NFTサンプルコードチュートリアル

## Motoko バリアント

### 前提条件

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールします。

- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールしてください。

- #### ステップ1：examplesリポジトリをクローンします：

<!-- end list -->

    git clone git@github.com:dfinity/examples.git

- #### ステップ2: DIP721プロジェクトのルートに移動します：

<!-- end list -->

    cd examples/motoko/dip-721-nft-container

- #### ステップ 3:Internet Computer のローカルインスタンスを実行します：

<!-- end list -->

```
dfx start --background 
```

:::info
新規インストールでない場合は、`--clean` フラグを付けて`start` を実行する必要があるかもしれません。
::：

    dfx start --clean --background

- #### ステップ 4: DIP721 NFTcanister をローカル IC に配置します。

このコマンドは、以下の初期化引数を指定して DIP721 NFTcanister をデプロイします：

    dfx deploy --argument "(
      principal\"$(dfx identity get-principal)\", 
      record {
        logo = record {
          logo_type = \"image/png\";
          data = \"\";
        };
        name = \"My DIP721\";
        symbol = \"DFXB\";
        maxLimit = 10;
      }
    )"

#### このコマンドで実行されること

- `principal`コレクションの初期カストディアン。カストディアンとは、コレクションを管理できるユーザー、つまり「Admin」ユーザーのことです。
  
  :::info
  `"$(dfx identity get-principal)"` は、あなたのマシンで dfx が使用するデフォルトの ID を、`deploy` に渡される引数に自動的に補間します。
  ::：

- `logo`:このNFTコレクションを表す画像。

- `name`:NFT コレクションの名前。

- `symbol`:トークンを識別するための一意の短い記号。

- `maxLimit`:このコレクションで許可される NFT の最大数。

以下のような出力が表示されます：

    Deployed canisters.
    URLs:
      Backend canister via Candid interface:
        dip721_nft_container: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai&id=be2us-64aaa-aaaaa-qaabq-cai

- #### ステップ5：NFTをミントします。

次のコマンドを使用してNFTを発行します：

    dfx canister call dip721_nft_container mintDip721 \
    "(
      principal\"$(dfx identity get-principal)\", 
      vec { 
        record {
          purpose = variant{Rendered};
          data = blob\"hello\";
          key_val_data = vec {
            record { key = \"description\"; val = variant{TextContent=\"The NFT metadata can hold arbitrary metadata\"}; };
            record { key = \"tag\"; val = variant{TextContent=\"anime\"}; };
            record { key = \"contentType\"; val = variant{TextContent=\"text/plain\"}; };
            record { key = \"locationType\"; val = variant{Nat8Content=4:nat8} };
          }
        }
      }
    )"

これが成功すると、次のようなメッセージが表示されます：

    (variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })

- #### ステップ6：NFTの転送

DIP721 インターフェースは、`transferFromDip721` または`safeTransferFromDip721` メソッドを使用して NFT を他の`principal` 値に転送できます。

まず、DFXを使用して別のIDを作成します。これが NFT を受け取るプリンシパルとなります。

    dfx identity new --disable-encryption alice
    ALICE=$(dfx --identity alice identity get-principal)

`ALICE` の ID が作成され、環境変数として設定されていることを確認します：

    echo $ALICE

プリンシパルが印刷されます。

    o4f3h-cbpnm-4hnl7-pejut-c4vii-a5u5u-bk2va-e72lb-edvgw-z4wuq-5qe

デフォルトユーザーから`ALICE` に NFT を転送します。

`from`: NFT を所有するプリンシパル
`to`: NFT を転送するプリンシパル
`token_id`: 転送するトークンの ID。

    dfx canister call dip721_nft_container transferFromDip721 "(principal\"$(dfx identity get-principal)\", principal\"$ALICE\", 0)"

NFT を`ALICE` からデフォルトユーザーに戻します。

    dfx canister call dip721_nft_container safeTransferFromDip721 "(principal\"$ALICE\", principal\"$(dfx identity get-principal)\", 0)"

2 番目の転送が機能するのは、呼び出し元がカストディアンのリストに含まれているため、つまりデフォルト ユーザーが NFT コレクションを変更する管理者権限を持っているためです。

### その他のメソッド

- #### バランス

<!-- end list -->

    dfx canister call dip721_nft_container balanceOfDip721 "(principal\"$(dfx identity get-principal)\")"

を出力します：

    (1 : nat64)

- #### getMaxLimitDip721

<!-- end list -->

    dfx canister call dip721_nft_container getMaxLimitDip721

出力

    (10 : nat16)

- #### getMetadataDip721

トークン ID を指定してください。
トークン ID は、`mintDip721` を実行したときに指定されたものです。例えば、`(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })` この場合、トークン ID は 0 です。

    dfx canister call dip721_nft_container getMetadataDip721 "0"

出力されます：

    (
      variant {
        Ok = vec {
          record {
            data = blob "hello";
            key_val_data = vec {
              record {
                key = "description";
                val = variant {
                  TextContent = "The NFT metadata can hold arbitrary metadata"
                };
              };
              record { key = "tag"; val = variant { TextContent = "anime" } };
              record {
                key = "contentType";
                val = variant { TextContent = "text/plain" };
              };
              record {
                key = "locationType";
                val = variant { Nat8Content = 4 : nat8 };
              };
            };
            purpose = variant { Rendered };
          };
        }
      },
    )

- #### getMetadataForUserDip721

<!-- end list -->

    dfx canister call dip721_nft_container getMetadataForUserDip721 "(principal\"$(dfx identity get-principal)\")"

出力

    (
      variant {
        Ok = record {
          token_id = 0 : nat64;
          metadata_desc = vec {
            record {
              data = blob "hello";
              key_val_data = vec {
                record {
                  key = "description";
                  val = variant {
                    TextContent = "The NFT metadata can hold arbitrary metadata"
                  };
                };
                record { key = "tag"; val = variant { TextContent = "anime" } };
                record {
                  key = "contentType";
                  val = variant { TextContent = "text/plain" };
                };
                record {
                  key = "locationType";
                  val = variant { Nat8Content = 4 : nat8 };
                };
              };
              purpose = variant { Rendered };
            };
          };
        }
      },
    )

- #### getTokenIdsForUserDip721

<!-- end list -->

    dfx canister call dip721_nft_container getTokenIdsForUserDip721 "(principal\"$(dfx identity get-principal)\")"

出力

    (vec { 0 : nat64 })

- #### ロゴDip721

<!-- end list -->

    dfx canister call dip721_nft_container logoDip721

出力

    (record { data = ""; logo_type = "image/png" })

- #### nameDip721

<!-- end list -->

    dfx canister call dip721_nft_container nameDip721

出力

    ("My DIP721")

- #### 対応インターフェースDip721

<!-- end list -->

    dfx canister call dip721_nft_container supportedInterfacesDip721

出力

    (vec { variant { TransferNotification }; variant { Burn }; variant { Mint } })

- #### シンボルDip721

<!-- end list -->

    dfx canister call dip721_nft_container symbolDip721

出力

    ("DFXB")

- #### 合計供給量Dip721

<!-- end list -->

    dfx canister call dip721_nft_container totalSupplyDip721

出力

    (1 : nat64)

- #### ownerOfDip721

トークン ID を指定してください。
トークン ID は、`mintDip721` を実行したときに指定されたものです。例えば、`(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })` この場合、トークン ID は 0 です。

    dfx canister call dip721_nft_container ownerOfDip721 "0"

出力してください：

    (
      variant {
        Ok = principal "5wuse-ejxao-gkqq6-4dhl5-hn5ps-2mgop-2se4s-w4zle-agr6j-svlhq-3qe"
      },
    )

これが`mintDip721` を実行したのと同じプリンシパルであることを確認します：

    dfx identity get-principal

## Rust バリアント

canister
インターフェースはプログラム的であることを意図していますが、RustバージョンにはHTTP機能が追加されているため、 でメタデータファイルを見ることができます。 これには6つのNFTが含まれているため、 から までのアイテムを見ることができます。`<canister URL>/<NFT ID>/<file ID>`
 `<canister URL>/0/0` `<canister URL>/5/0`

### 前提条件

- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールしてください。

- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールします。

- \[x\]`wasm32-unknown-unknown` ターゲット。これらは`rustup target add wasm32-unknown-unknown` と一緒にインストールできます。

- #### ステップ 1: プロジェクトのファイル用の Github リポジトリをクローンし、そのディレクトリに移動します：

<!-- end list -->

``` sh
git clone https://github.com/dfinity/examples
cd examples/rust/dip721-nft-container
```

- #### ステップ 2:canister をインストールする前にローカルレプリカを起動します：

<!-- end list -->

``` sh
dfx start --background --clean
```

- #### ステップ 3:canister をインストールします。

コマンドでcanister をデプロイします：

``` sh
dfx deploy --no-wallet --argument \
"(record {
    name = \"Numbers One Through Fifty\";
    symbol = \"NOTF\";
    logo = opt record {
        data = \"$(base64 -i ./logo.png)\";
        logo_type = \"image/png\";
    };
    custodians = opt vec { principal \"$(dfx identity get-principal)\" };
})"
```

canister は、以下のフィールドを持つレコードパラメーターを期待します：

- `custodians`:canister を管理することを許可されたユーザのリスト。 設定されていない場合、呼び出し元がデフォルトとなります。`dfx` を使用していて、`--no-wallet` を指定していない場合、それはあなたの財布のプリンシパルであり、あなた自身のものではありませんので、注意してください！

- `name`:NFTコレクションの名前。必須。

- `symbol`:NFT コレクションを識別する短いスラッグ。必須。

- `logo`:NFTコレクションのロゴ。`data` （base-64エンコードされたロゴ）と`logo_type` （ロゴファイルのMIMEタイプ）のフィールドを持つレコードとして表されます。未設定の場合、デフォルトはInternet Computer のロゴになります。

- #### ステップ4：canister と対話します。

標準機能以外に、5つの追加機能があります：

- `set_name` `set_symbol` `set_logo` `set_custodian`これらの関数は、対応するフィールドのコレクション情報を、初期化されたときから更新します。
- `is_custodian`この関数は、指定されたユーザーがカストディアンであるかどうかをチェックします。

canister は認証済みのHTTPインターフェースもサポートしています。`/<nft>/<id>` にアクセスすると、`nft`'のメタデータファイル \#`id` が返され、`/<nft>` は最初の非プレビューファイルを返します。

`ownerOfDip721` のような関数の結果は、悪意のある1つのノードによって任意に変更される可能性があります。クエリコールされた情報が依存している場合、例えば誰かが特定のNFTの所有者にICPを送り、その所有者から購入する可能性がある場合、これらのコールは代わりにアップデートコールとして実行されるべきです。`dfx` に`--update` フラグを渡すか、`agent-rs` の`Agent::update` 関数を使用することで、強制的にアップデートコールを実行できます。

- #### ステップ5: NFTの発行。

端末コマンドの長さにはサイズが制限されているため、画像や動画をベースとしたNFTを`dfx` 。そのため、シングルファイルのNFTを作成するための実験的な[発行](https://github.com/dfinity/experimental-minting-tool)ツールがあります。

このツールを使用するには、コマンドで発行ツールをインストールします：

    cargo install --git https://github.com/dfinity/experimental-minting-tool --locked

例として、デフォルトのロゴをミントするには、次のコマンドを実行します：

``` sh
minting-tool local "$(dfx canister id dip721_nft_container)" --owner "$(dfx identity get-principal)" --file ./logo.png --sha2-auto
```

このコマンドの出力は次のようになります：

    Successfully minted token 0 to x4d3z-ufpaj-lpxs4-v7gmt-v56ze-aub3k-bvifl-y4lsq-soafd-d3i4k-fqe (transaction id 0)

発行は、`custodians` パラメータまたは`set_custodians` 関数で許可された人に制限されます。`--file` canister cycles のコンテンツはオンチェーンに保存されるため、任意のユーザーがトークンを発行できないようにすることが重要です。自分で にデータをアップロードしすぎないように注意してください。canister 

#### デモ

このRustサンプルにはデモスクリプト（`demo.sh` ）が付属しており、数人のユーザー間でNFTの発行と取引を行うワークフローの例が実行されます。これは、基本的なNFT操作がどのように行われるかを確認するために使用できます。より詳細な説明については、\[規格\]\[DIP721\]をお読みください。

## リソース

- [Rust](https://rustup.rs)。
- [DIP721](https://github.com/Psychedelic/DIP721)。
- 発行[ツール](https://github.com/dfinity/experimental-minting-tool)。

<!---
# NFT minting

This example demonstrates implementing an NFT canister. NFTs (non-fungible tokens) are unique tokens with arbitrary
metadata, usually an image of some kind, to form the digital equivalent of trading cards. There are a few different
NFT standards for the Internet Computer (e.g [EXT](https://github.com/Toniq-Labs/extendable-token), [IC-NFT](https://github.com/rocklabs-io/ic-nft)), but for the purposes of this tutorial we use [DIP-721](https://github.com/Psychedelic/DIP721). You can see a quick introduction on [YouTube](https://youtu.be/1po3udDADp4).

The canister is a basic implementation of the standard, with support for the minting, burning, and notification interface extensions.

The sample code is available in the [samples repository](https://github.com/dfinity/examples) in [Rust](https://github.com/dfinity/examples/tree/master/rust/dip721-nft-container) and [Motoko](https://github.com/dfinity/examples/tree/master/motoko/dip721-nft-container).

Command-line length limitations would prevent you from minting an NFT with a large file, like an image or video, via `dfx`. To that end,
there is a [command-line minting tool](https://github.com/dfinity/experimental-minting-tool) provided for minting simple NFTs.

## Overview
The NFT canister is not very complicated since the [DIP-721](https://github.com/Psychedelic/DIP721) standard specifies most [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations,
but we can still use it to explain three important concepts concerning dapp development for the Internet Computer:

- #### 1. Stable memory for canister upgrades.
The Internet Computer employs [orthogonal persistence](/motoko/main/motoko.md#orthogonal-persistence), so developers generally do not need to think a lot about storing their data.
When upgrading canister code, however, it is necessary to explicitly handle canister data. The NFT canister example shows how stable memory can be handled using `pre_upgrade` and `post_upgrade`.

- #### 2. Certified data.
Generally, when a function only reads data, instead of modifying the state of the canister, it is
beneficial to use a [query call instead of an update call](https://smartcontracts.org/docs/current/concepts/canisters-code.md#query-and-update-methods).
But, since query calls do not go through consensus, [certified responses](https://internetcomputer.org/docs/current/developer-docs/security/general-security-best-practices)
should be used wherever possible. The HTTP interface of the Rust implementation shows how certified data can be handled.

- #### 3. Delegating control over assets.
For a multitude of reasons, users may want to give control over their assets to other identities, or even delete (burn) an item.
The NFT canister example contains all those cases and shows how it can be done.

## Architecture
Since the basic functions required in [DIP-721](https://github.com/Psychedelic/DIP721) are very straightforward to implement, this section only discusses how the above ideas are handled and implemented.

### Stable storage for canister upgrades
During canister code upgrades, memory is not persisted between different canister calls. Only memory in stable memory is carried over.
Because of that it is necessary to write all data to stable memory before the upgrade happens, which is usually done in the `pre_upgrade` function.
This function is called by the system before the upgrade happens. After the upgrade, it is normal to load data from stable memory into memory
during the `post_upgrade` function. The `post_upgrade` function is called by the system after the upgrade happened.
In case an error occurs during any part of the upgrade (including `post_upgdrade`), the entire upgrade is reverted.

The Rust CDK (Canister Development Kit) currently only supports one value in stable memory, so it is necessary to create an object that can hold everything you care about.
In addition, not every data type can be stored in stable memory; only ones that implement the [CandidType trait](https://docs.rs/candid/latest/candid/types/trait.CandidType.html)
(usually via the [CandidType derive macro](https://docs.rs/candid/latest/candid/derive.CandidType.html)) can be written to stable memory. 

Since the state of our canister includes an `RbTree` which does not implement the `CandidType`, it has to be converted into a data structure (in this case a `Vec`) that implements `CandidType`.
Luckily, both `RbTree` and `Vec` implement functions that allow converting to/from iterators, so the conversion can be done quite easily.
After conversion, a separate `StableState` object is used to store data during the upgrade.

### Certified data
To serve assets via HTTP over `<canister-id>.icp0.io` instead of `<canister-id>.raw.icp0.io`, responses have to
[contain a certificate](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification) to validate their content.
Obtaining such a certificate can not happen during a query call since it has to go through consensus, so it has to be created during an update call.

A certificate is very limited in its content. At the time of writing, canisters can submit no more than 32 bytes of data to be certified.
To make the most out of that small amount of data, a `HashTree` (the `RbTree` from the previous section is also a `HashTree`) is used.
A `HashTree` is a tree-shaped data structure where the whole tree can be summarized (hashed) into one small hash of 32 bytes.
Whenever some content of the tree changes, the hash also changes. If the hash of such a tree is certified, it means that the content of the tree can be considered certified.
To see how data is certified in the NFT example canister, look at the function `add_hash` in `http.rs`.

For the response to be verified, it has to be checked that a) the served content is part of the tree, and b) the tree containing that content actually can be hashed to the certified hash.
The function `witness` is responsible for creating a tree with minimal content that still can be verified to fulfill a) and b).
Once this minimal tree is constructed, certificate and minimal hash tree are sent as part of the `IC-Certificate` header.

For a much more detailed explanation how certification works, see [this explanation video](https://internetcomputer.org/how-it-works/response-certification).

### Managing control over assets
[DIP-721](https://github.com/Psychedelic/DIP721) specifies multiple levels of control over the NFTs:
- **Owner**: this person owns an NFT. They can transfer the NFT, add/remove operators, or burn the NFT.
- **Operator**: sort of a delegated owner. The operator does not own the NFT, but can do the same actions an owner can do.
- **Custodian**: creator of the NFT collection/canister. They can do anything (transfer, add/remove operators, burn, and even un-burn) to NFTs, but also mint new ones or change the symbol or description of the collection.

The NFT example canister keeps access control in these three levels very simple: 
- For every level of control, a separate list (or set) of principals is kept.
- Those three levels are then manually checked every single time someone attempts to do something for which they require authorization.
- If a user is not authorized to call a certain function an error is returned.

Burning an NFT is a special case. To burn an NFT means to either delete the NFT (not intended in DIP-721) or to set ownership to `null` (or a similar value).
On the Internet Computer, this non-existing principal is called the [management canister](https://smartcontracts.org/docs/current/references/ic-interface-spec.md#ic-management-canister).
Quote from the link: "The IC management canister is just a facade; it does not actually exist as a canister (with isolated state, Wasm code, etc.)." and its address is `aaaaa-aa`.
Using this management canister address, we can construct its principal and set the management canister as the owner of a burned NFT.

## NFT sample code tutorial

## Motoko variant

### Prerequisites 

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download and install [git.](https://git-scm.com/downloads)

- #### Step 1: Clone the examples repo:

```
git clone git@github.com:dfinity/examples.git
```

- #### Step 2: Navigate to DIP721 project root:

```
cd examples/motoko/dip-721-nft-container
```

- #### Step 3: Run a local instance of the Internet Computer:

```
dfx start --background 
```

:::info
If this is not a new installation, you may need to run `start` with the `--clean` flag.
:::

```
dfx start --clean --background
```

- #### Step 4: Deploy a DIP721 NFT canister to your local IC.
This command deploys the DIP721 NFT canister with the following initialization arguments:

```
dfx deploy --argument "(
  principal\"$(dfx identity get-principal)\", 
  record {
    logo = record {
      logo_type = \"image/png\";
      data = \"\";
    };
    name = \"My DIP721\";
    symbol = \"DFXB\";
    maxLimit = 10;
  }
)"
```

#### What this does
- `principal`: the initial custodian of the collection. A custodian is a user who can administrate the collection i.e. an "Admin" user. 

  :::info
  `"$(dfx identity get-principal)"` automatically interpolates the default identity used by dfx on your machine into the argument that gets passed to `deploy`.
  :::

- `logo`: The image that represents this NFT collection.
- `name`: The name of the NFT collection.
- `symbol`: A short, unique symbol to identify the token. 
- `maxLimit`: The maximum number of NFTs that are allowed in this collection.

You will receive output that resembles the following:

```
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    dip721_nft_container: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai&id=be2us-64aaa-aaaaa-qaabq-cai
```

- #### Step 5: Mint an NFT.

Use the following command to mint an NFT:

```
dfx canister call dip721_nft_container mintDip721 \
"(
  principal\"$(dfx identity get-principal)\", 
  vec { 
    record {
      purpose = variant{Rendered};
      data = blob\"hello\";
      key_val_data = vec {
        record { key = \"description\"; val = variant{TextContent=\"The NFT metadata can hold arbitrary metadata\"}; };
        record { key = \"tag\"; val = variant{TextContent=\"anime\"}; };
        record { key = \"contentType\"; val = variant{TextContent=\"text/plain\"}; };
        record { key = \"locationType\"; val = variant{Nat8Content=4:nat8} };
      }
    }
  }
)"
```

If this succeeds, you should see the following message:

```
(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })
```

- #### Step 6: Transferring an NFT.
The DIP721 interface supports transferring an NFT to some other `principal` values via the `transferFromDip721` or `safeTransferFromDip721` methods.

First, create a different identity using DFX. This will become the principal that you receives the NFT

```
dfx identity new --disable-encryption alice
ALICE=$(dfx --identity alice identity get-principal)
```

Verify the identity for `ALICE` was created and set as an environment variable:
```
echo $ALICE
```

You should see a principal get printed
```
o4f3h-cbpnm-4hnl7-pejut-c4vii-a5u5u-bk2va-e72lb-edvgw-z4wuq-5qe
```

Transfer the NFT from the default user to `ALICE`. 

Here the arguments are:
`from`: principal that owns the NFT
`to`: principal to transfer the NFT to
`token_id`: the id of the token to transfer

```
dfx canister call dip721_nft_container transferFromDip721 "(principal\"$(dfx identity get-principal)\", principal\"$ALICE\", 0)"
```

Transfer the NFT from from `ALICE` back to the default user.

```
dfx canister call dip721_nft_container safeTransferFromDip721 "(principal\"$ALICE\", principal\"$(dfx identity get-principal)\", 0)"
```
Note the second transfer works because the caller is in the list of custodians, i.e. the default user has admin rights to modify the NFT collection.

### Other methods

- #### balanceOfDip721

```
dfx canister call dip721_nft_container balanceOfDip721 "(principal\"$(dfx identity get-principal)\")"
```

Output:

```
(1 : nat64)
```

- #### getMaxLimitDip721

```
dfx canister call dip721_nft_container getMaxLimitDip721
```

Output:

```
(10 : nat16)
```

- #### getMetadataDip721

Provide a token ID. 
The token ID was provided to you when you ran `mintDip721`, e.g. `(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })` So, the token ID is 0 in this case.

```
dfx canister call dip721_nft_container getMetadataDip721 "0"
```

Output:

```
(
  variant {
    Ok = vec {
      record {
        data = blob "hello";
        key_val_data = vec {
          record {
            key = "description";
            val = variant {
              TextContent = "The NFT metadata can hold arbitrary metadata"
            };
          };
          record { key = "tag"; val = variant { TextContent = "anime" } };
          record {
            key = "contentType";
            val = variant { TextContent = "text/plain" };
          };
          record {
            key = "locationType";
            val = variant { Nat8Content = 4 : nat8 };
          };
        };
        purpose = variant { Rendered };
      };
    }
  },
)
```


- #### getMetadataForUserDip721

```
dfx canister call dip721_nft_container getMetadataForUserDip721 "(principal\"$(dfx identity get-principal)\")"
```

Output:

```
(
  variant {
    Ok = record {
      token_id = 0 : nat64;
      metadata_desc = vec {
        record {
          data = blob "hello";
          key_val_data = vec {
            record {
              key = "description";
              val = variant {
                TextContent = "The NFT metadata can hold arbitrary metadata"
              };
            };
            record { key = "tag"; val = variant { TextContent = "anime" } };
            record {
              key = "contentType";
              val = variant { TextContent = "text/plain" };
            };
            record {
              key = "locationType";
              val = variant { Nat8Content = 4 : nat8 };
            };
          };
          purpose = variant { Rendered };
        };
      };
    }
  },
)
```

- #### getTokenIdsForUserDip721

```
dfx canister call dip721_nft_container getTokenIdsForUserDip721 "(principal\"$(dfx identity get-principal)\")"
```

Output:

```
(vec { 0 : nat64 })
```

- #### logoDip721

```
dfx canister call dip721_nft_container logoDip721
```

Output:

```
(record { data = ""; logo_type = "image/png" })
```

- #### nameDip721

```
dfx canister call dip721_nft_container nameDip721
```

Output:

```
("My DIP721")
```

- #### supportedInterfacesDip721

```
dfx canister call dip721_nft_container supportedInterfacesDip721
```

Output:

```
(vec { variant { TransferNotification }; variant { Burn }; variant { Mint } })
```

- #### symbolDip721

```
dfx canister call dip721_nft_container symbolDip721
```

Output:
```
("DFXB")
```

- #### totalSupplyDip721

```
dfx canister call dip721_nft_container totalSupplyDip721
```

Output:

```
(1 : nat64)
```

- #### ownerOfDip721
Provide a token ID. 
The token ID was provided to you when you ran `mintDip721`, e.g. `(variant { Ok = record { id = 1 : nat; token_id = 0 : nat64 } })` So, the token ID is 0 in this case.

```
dfx canister call dip721_nft_container ownerOfDip721 "0"
```

Output:

```
(
  variant {
    Ok = principal "5wuse-ejxao-gkqq6-4dhl5-hn5ps-2mgop-2se4s-w4zle-agr6j-svlhq-3qe"
  },
)
```

Verify that this is the same principal that you ran `mintDip721` with:

```
dfx identity get-principal
```

## Rust variant

A running instance of the Rust canister for demonstration purposes is available as [t5l7c-7yaaa-aaaab-qaehq-cai](https://t5l7c-7yaaa-aaaab-qaehq-cai.icp0.io).
The interface is meant to be programmatic, but the Rust version additionally contains HTTP functionality so you can view a metadata file at `<canister URL>/<NFT ID>/<file ID>`.
It contains six NFTs, so you can look at items from `<canister URL>/0/0` to `<canister URL>/5/0`.

### Prerequisites

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download and install [git.](https://git-scm.com/downloads)
- [x] `wasm32-unknown-unknown` targets; these can be installed with `rustup target add wasm32-unknown-unknown`.

- #### Step 1: Clone the Github repo for the project's files and navigate into the directory:

```sh
git clone https://github.com/dfinity/examples
cd examples/rust/dip721-nft-container
```

- #### Step 2: Start the local replica before installing the canister:

```sh
dfx start --background --clean
```

- #### Step 3: Install the canister. 

Deploy the canister with the command:
```sh
dfx deploy --no-wallet --argument \
"(record {
    name = \"Numbers One Through Fifty\";
    symbol = \"NOTF\";
    logo = opt record {
        data = \"$(base64 -i ./logo.png)\";
        logo_type = \"image/png\";
    };
    custodians = opt vec { principal \"$(dfx identity get-principal)\" };
})"
```

The canister expects a record parameter with the following fields:

- `custodians`: A list of users allowed to manage the canister. If unset, it will default to the caller. If you're using `dfx`, and haven't specified `--no-wallet`, that's your wallet principal, not your own, so be careful!
- `name`: The name of your NFT collection. Required.
- `symbol`: A short slug identifying your NFT collection. Required.
- `logo`: The logo of your NFT collection, represented as a record with fields `data` (the base-64 encoded logo) and `logo_type` (the MIME type of the logo file). If unset, it will default to the Internet Computer logo.

- #### Step 4: Interact with the canister.

Aside from the standard functions, it has five extra functions:

- `set_name`, `set_symbol`, `set_logo`, and `set_custodian`: these functions update the collection information of the corresponding field from when it was initialized.
- `is_custodian`: this function checks whether the specified user is a custodian.

The canister also supports a certified HTTP interface; going to `/<nft>/<id>` will return `nft`'s metadata file #`id`, with `/<nft>` returning the first non-preview file.

Remember that query functions are uncertified; the result of functions like `ownerOfDip721` can be modified arbitrarily by a single malicious node. If queried information is depended on, for example if someone might send ICP to the owner of a particular NFT to buy it from them, those calls should be performed as update calls instead. You can force an update call by passing the `--update` flag to `dfx` or using the `Agent::update` function in `agent-rs`.

- #### Step 5: Mint an NFT. 

Due to size limitations on the length of a terminal command, an image- or video-based NFT would be impossible to send via `dfx`. To that end, there is an experimental [minting tool](https://github.com/dfinity/experimental-minting-tool) you can use to mint a single-file NFT. 

To use this tool, install the minting tool with the command:

```
cargo install --git https://github.com/dfinity/experimental-minting-tool --locked
```

As an example, to mint the default logo, you would run the following command:

```sh
minting-tool local "$(dfx canister id dip721_nft_container)" --owner "$(dfx identity get-principal)" --file ./logo.png --sha2-auto
```

The output of this command should look like this:

```
Successfully minted token 0 to x4d3z-ufpaj-lpxs4-v7gmt-v56ze-aub3k-bvifl-y4lsq-soafd-d3i4k-fqe (transaction id 0)
```

Minting is restricted to anyone authorized with the `custodians` parameter or the `set_custodians` function. Since the contents of `--file` are stored on-chain, it's important to prevent arbitrary users from minting tokens, or they will be able to store arbitrarily-sized data in the contract and exhaust the canister's cycles. Be careful not to upload too much data to the canister yourself, or the contract will no longer be able to be upgraded afterwards.

#### Demo

This Rust example comes with a demo script, `demo.sh`, which runs through an example workflow with minting and trading an NFT between a few users. This is primarily designed to be read rather than run so that you can use it to see how basic NFT operations are done. For a more in-depth explanation, read the [standard][DIP721].

## Resources
- [Rust](https://rustup.rs).
- [DIP721](https://github.com/Psychedelic/DIP721).
- [Minting tool](https://github.com/dfinity/experimental-minting-tool).
-->
