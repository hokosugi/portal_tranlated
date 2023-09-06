# 18: スケーラブルdapp の例

## 概要

[CanCanサンプルアプリケーションは](https://github.com/dfinity/cancan)、独自のアプリケーションのモデルとして使用できるいくつかの機能を示す、簡素化された動画共有サービスです。たとえば、CanCan サンプル アプリケーションを調べることで学べることは、次のとおりです：

- コンテンツをフラグメントに分割してアップロードおよび保存し、クエリを使用してフラグメントを取得および再組み立てして効率的なストリーミングを行うことで、スケーラブルなアプリケーションを構築する方法。

- 異なるバックエンド言語で書かれたcanisters を使用するアプリケーションの相互運用性を設定する方法。

- 異なるユーザーがアップロードした動画を保存するための基本的な認証モデルを実装する方法。

- デスクトップアプリやモバイルアプリ向けに、より洗練されたユーザーインターフェイス機能を実装するフロントエンドを構築する方法。

## アップロードされたコンテンツを複数に分割する方法canisters

canisters はWebAssembly モジュールをコンパイルしたものなので、一定の既知の制限があります。たとえば、現在WebAssembly モジュールには、最大 4GB のメモリと、許可されるオブジェクト呼び出し数の上限があります。

CanCanのようなビデオ共有のサンプル・アプリケーションでは、このような制限があるため、複数のcanisters 、データを小さな塊に分割して保存・検索する必要があります。

### 分散ハッシュテーブルの実装

Internet Computer 向けにスケーラブルな動画共有サービスを構築する最初の試みでは、アップロードまたはストリーミングされる動画のデータ チャンクを事前に定義されたセット（canisters ）に分散する単純な get および put 関数を備えたバックエンド サービスとして、分散ハッシュ テーブル（DHT）を使用しました。プロジェクトの初期段階では、このアプローチで概念実証を行い、動画データを適切にトランスコードして保存および検索できることを検証するのに十分でした。

しかし、分散ハッシュ テーブルは、保存のためにデータを入力し、視聴のためにデータを取得できるcanisters の特定の数に依存していたため、アプリケーションのスケーラビリティには限界がありました。さらに、分散ハッシュテーブルのバックエンド・サービスのオリジナルの実装には、ノードが利用できなくなったりデータが失われたりする原因となる一般的なネットワーク接続の問題に対応するためのコードが含まれていました。

## スケーラビリティの簡素化

Internet Computer protocol はサブネット内のノード間で複製されたステートに依存しているため、他のプラットフォームやプロトコルで動作するアプリケーションでは一般的に利用できない、フォールトトレランスとフェイルオーバーに関する特定の保証をネイティブに提供します。

Internet Computer 、canisters 、リクエストの受信と応答が可能であることを保証できることがわかったため、オリジナルの分散ハッシュテーブルバックエンドサービスは、BigMapと呼ばれる、よりシンプルでスケーラブルなバックエンドサービスに置き換えられました。

BigMapは、Internet Computer 。BigMapライブラリをバックエンドサービスとして使用することで、CanCanサンプルアプリケーションは、動的にデータをチャンクし、シリアライズし、複数のcanisters 。

このライブラリは、任意の数のcanisters を使用して拡張できる、アプリケーション固有のインメモリ データ抽象化のためのビルディング ブロックを提供します。各canister の容量はまだ限られていますが、アプリケーションは必要なcanisters をインスタンス化し、`manifest` と呼ばれるインデックス ファイルで、各ユーザの動画の完全なコンテンツを構成するフラグメントを追跡します。

プラットフォームとしてのInternet Computer がスケーラビリティ、レプリケーション、フェイルオーバー、フォールト トレランスを提供するため、`BigMap` サービスに必要なコードは、従来の分散ハッシュ テーブルよりもはるかにシンプルです。

## 相互運用性の実証

CanCanサンプル・アプリケーションのリポジトリに含まれるBigMapサービスは、Rustプログラミング言語で記述されています。しかし、CanCanサンプル・アプリケーションは、異なる言語で書かれたcanisters 間の相互運用性も実証しています。

この場合、`BigMap` 機能は Rust プログラミング言語を使用して実装され、その他のサービス（ビデオコンテンツのエンコードとデコード、認証のためのユーザプリンシパルの管理など）はMotoko を使用して実装されています。

サンプルアプリケーションのさまざまな部分をcanisters としてデプロイすることで、それらの間の相互作用がシームレスなユーザーエクスペリエンスを提供します。

CanCan リポジトリでご覧いただける`BigMap` サービスは Rust で記述されていますが、実際には以下のことを実証するために Rust とMotoko の両方のプログラミング言語で実装されています：

- canisters としてデプロイされたMotoko コードと Rust コードの両方をInternet Computer 上で実行できます。

- CanCanサンプル・アプリケーションの動作に影響を与えることなく、バックエンド言語を切り替えることができます。

- Candid言語は、JavaScript、Rust、Motoko に依存せず、BigMap APIを記述するための共通言語を提供するため、どちらの言語実装もシームレスに動作します。

## 認証モデル

LinkedUpサンプルアプリケーションと同様に、CanCanサンプルアプリケーションは、公開鍵と秘密鍵のペア、ブラウザベースのローカルストレージ、および`Principal` データ型を使用してユーザーを認証します。

## フロントエンド機能の実装

CanCanサンプルアプリケーションでは、ReactライブラリとTypeScriptを組み合わせてフロントエンドのユーザーインターフェースを実装しています。

### データモデルの概要

このアプリケーションは、ユーザーに関する情報と動画に関する情報を保存します。ほとんどのブラウザをサポートするため、動画はバイト配列にシリアライズされ、動画データは[Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)オブジェクトと呼ばれる 500kb 単位のバイト セグメントに格納されます。動画が要求されると、マニフェストは動画を再生するために必要なチャンクのリストをロードし、チャンクを連結してから規格の`<video>` 要素に動画を表示します。

### ユーザー プロファイル

ユーザー プロファイルは`profiles/{username}` として保存され、次のデータ モデルを使って定義されます：

    export interface Profile {
      username: string;         // maya
      following: string[];      // [`alice`, `bob]`
      followers: string[];      // ['palice']
      uploadedVideos: string[]; // ['profiles/maya/videos/a5b54646-2ea3-4e0e-82d1-da3ab8148df2']
      likedVideos: string[];    // ['profiles/bob/videos/b74e4eb0-dea8-4a4a-a1ae-d4593dc86930']
      avatar?: string;          // ?ImageData (TODO)
    }

### 動画

アップロードされた動画は、`profiles/{username}/videos/{videoId}` および`public/videos` (プラットフォーム上のすべての既存動画の配列) に格納された一意の識別子によって識別されます。

動画のメタデータは`profiles/{username}/videos/{videoId}/metadata` に保存されます。個々の動画の断片は`profiles/{username}/videos/{videoId}/chunks/chunk.{0-10}` に保存されます。

動画は`profiles/{username}` として保存され、以下のデータモデルを使用して定義されます：

    export interface Video {
      src: string;       // 'profiles/maya/videos/f5a44646-2ea3-4e0f-83d2-da3ab8148df2'
      userId: string;    // 'maya'
      createdAt: string; // Date.now()
      caption: string;   // 'cool movie, punk'
      tags: string[];    // ['outside', 'grilling', 'beveragino']
      likes: string[];    // ['sam', 'kelly']
      viewCount: number; // 102
      name: string; // 'grilling'
    }

### 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしてください。

さらに、このガイドには以下のパッケージが必要です：

- \[x\][Node.js](https://nodejs.org) をダウンロードしてインストールしてください。
- \[x\][Python](https://www.python.org) をダウンロードしてインストールしてください。
- \[x\][Vessel@0.6.0](https://github.com/dfinity/vessel/releases/tag/v0.6.0) をダウンロードしてインストールします。

## CanCanのデプロイdapp

[vesselが](https://github.com/dfinity/vessel)バージョン0.6.\*でインストールされていることを再確認し、このリポジトリをクローンして`cancan` ディレクトリに移動します。

``` shell
vessel --version
# vessel 0.6.0

git clone git@github.com:dfinity/cancan.git
cd cancan
```

以下のコマンドを実行して、ローカルのInternet Computer レプリカを起動します：

``` shell
dfx start --clean
```

次に、`dfx.json` ファイルを編集して、最新バージョンの dfx を使用します：

```
  "dfx": "0.14.1"
```

同じディレクトリの別のターミナルタブで次のコマンドを実行します：

``` shell
npm install
```

`./bootstrap.sh` スクリプトの内容を次のように置き換えます：

    #!/bin/bash
    
    set -e
    echo "Running bootstrap script..."
    
    # Support bootstrapping CanCan from any folder
    BASEDIR=$(
      cd "$(dirname "$0")"
      pwd
    )
    cd "$BASEDIR"
    
    host="localhost"
    address="http://$host"
    
    echo "dfx build"
    npm run deploy
    
    # echo "Running seed script..."
    # echo "\nThis command may prompt you to install node to run.\nPlease accept and continue."
    # npm run seed -- 2
    
    URL="http://$(dfx canister id cancan_ui).$host:4943/"
    
    echo "Open at $URL"
    
    case $(uname) in
    Linux) xdg-open $URL || true ;;
    *) open $URL || true ;;
    esac

次に、`package-set.dhall` ファイルを編集して、アップストリームURLを更新します：

    let upstream =
      https://github.com/dfinity/vessel-package-set/releases/download/mo-0.7.5-20230118/package-set.dhall

次に、ブートストラップスクリプトを実行します：

    ./bootstrap.sh

これにより、`cancan_ui_backend` というローカルのcanister がデプロイされます。フロントエンドを開くには、`dfx canister id cancan_ui_frontend` を実行してフロントエンドcanister ID を取得します。それからブラウザを開き、`http://<cancan_ui-canister-id>.localhost:8000/sign-in` に移動します。

高速なリフレッシュとホットリロードで開発サーバーを実行するには、アプリのルートディレクトリでこのコマンドを使用できます：

``` shell
npm run start
```

デフォルトのブラウザは`localhost:3000` でタブを開きます（またはフォーカスします）。`/?canisterId=${cancan_ui_canister_id}` に、`cancan_ui_canister_id` が（現在の）`ryjl3-tyaaa-aaaaa-aaaba-cai` であることを追加します。

これで、フロントエンドのコードに変更を加えても、すぐに更新を確認することができます。時折、CSSルールを追加しても更新がトリガーされず、ユーザーが手動で更新しないと変更が表示されないことがあります。

## 次のステップ

このガイドを完成させるために、さらにいくつかの[サンプルdapps](sample-apps.md) をチェックしてください。

<!---
# 18: Scalable dapp example

## Overview

The [CanCan sample application](https://github.com/dfinity/cancan) is a simplified video-sharing service that demonstrates several features that you can use as models for your own applications. For example, here are a few things you can learn by exploring the CanCan sample application:

-   How to build a scalable application by splitting content into fragments for upload and storage then using queries to retrieve and reassemble the fragments for efficient streaming.

-   How to configure interoperability for an application that uses canisters written in different backend languages.

-   How to implement a basic authentication model for storing videos uploaded by different users.

-   How to build a frontend that implements more sophisticated user interface features for desktop or mobile apps.

## Splitting uploaded content into multiple canisters

Because canisters are compiled WebAssembly modules, they have certain known limitations. For example, currently WebAssembly modules have a maximum of 4GB for memory and an upper limit on the number of object calls allowed.

For a video-sharing sample applications like CanCan, these limitations mean that multiple canisters are required and that data must be broken into smaller chunks for storage and retrieval.

### Implementing a distributed hash table

The initial attempt to build a scalable video-sharing service for the Internet Computer used a distributed hash table (DHT) as a backend service with simple get and put functions that distributed data—chunks of the video to be uploaded or streamed—into a predefined set of canisters. In the early phases of the project, this approach was sufficient for a proof-of-concept and verifying that the video data could be properly transcoded for storage and retrieval.

However, the scalability of the application was limited because the distributed hash table relied on a specific number of canisters that it could populate with data for storage and retrieve data from for viewing. In addition, the original implementation of the distributed hash table backend service included code to accommodate common network connectivity issues that could cause nodes to be unavailable or lose data.

## Simplifying scalability

Because the Internet Computer protocol relies on replicated state across nodes in a subnet, it provides certain guarantees about fault tolerance and failover natively that are not generally available to applications running on other platforms or protocols.

With the realization that the Internet Computer could ensure that canisters were available to receive and respond to requests,the original distributed hash table backend service was replaced with a simpler but more scalable backend service called BigMap.

BigMap provides a simple, plug-in library for building scalable applications using key-value storage on the Internet Computer. By using the BigMap library as a backend service, the CanCan sample application can dynamically chunk, serialize, and distribute data to multiple canisters.

The library offers building blocks for application-specific, in-memory data abstractions that scale using any number of canisters. Each canister still has limited capacity, but the application instantiates the canisters it needs and keeps track of the fragments that make up the full video content for each user’s videos in an index file called the `manifest`.

The code required for the `BigMap` service is much simpler than a traditional distributed hash table because the Internet Computer as a platform provides scalability, replication, failover, and fault tolerance.

## Demonstrating interoperability

The BigMap service included in the CanCan sample application repository is written in the Rust programming language. However, the CanCan sample application also demonstrates interoperability between canisters written in different languages.

In this case, the `BigMap` functionality is implemented using the Rust programming language and other services—such as encoding and decoding of video content and the management of user principals for authentication—are implemented using Motoko.

By deploying different parts of the sample application as canisters, the interaction between them provides a seamless user experience.

Although the `BigMap` service you see in the CanCan repository is written in Rust, the service was actually implemented in both Rust and Motoko programming languages to demonstrate the following:

-   You can run both Motoko code and Rust code deployed as canisters on the Internet Computer.

-   You can switch between backend languages without affecting the operation of the CanCan sample application.

-   Both language implementation work seamlessly because the Candid language provides a common language for describing the BigMap API, independent of JavaScript, Rust, or Motoko.

## Authentication model

Much like the LinkedUp sample application, the CanCan sample application uses the public-private key pair, browser-based local storage, and the `Principal` data type to authenticate users.

## Implementing frontend features

The CanCan sample application uses the React library in combination with TypeScript to implement frontend user interface.

### Data model overview

The application stores information about users and information about videos. To support most browsers, the videos are serialized into byte arrays with video data stored in 500kb segments of bytes that are referred to as [Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) objects. When a video is requested, the manifest loads the list of chunks required to play the video and concatenates the chunks before displaying the video in a standard `<video>` element.

### User profiles

User profiles are stored as `profiles/{username}` and are defined using the following data model:

```
export interface Profile {
  username: string;         // maya
  following: string[];      // [`alice`, `bob]`
  followers: string[];      // ['palice']
  uploadedVideos: string[]; // ['profiles/maya/videos/a5b54646-2ea3-4e0e-82d1-da3ab8148df2']
  likedVideos: string[];    // ['profiles/bob/videos/b74e4eb0-dea8-4a4a-a1ae-d4593dc86930']
  avatar?: string;          // ?ImageData (TODO)
}
```

### Videos

Uploaded videos are identified by a unique identifier stored at `profiles/{username}/videos/{videoId}` and in `public/videos` (an array of all existing videos on the platform).

Metadata for videos is stored at `profiles/{username}/videos/{videoId}/metadata` Individual video fragments are stored at `profiles/{username}/videos/{videoId}/chunks/chunk.{0-10}`.

Videos are stored as `profiles/{username}` and are defined using the following data model:

```
export interface Video {
  src: string;       // 'profiles/maya/videos/f5a44646-2ea3-4e0f-83d2-da3ab8148df2'
  userId: string;    // 'maya'
  createdAt: string; // Date.now()
  caption: string;   // 'cool movie, punk'
  tags: string[];    // ['outside', 'grilling', 'beveragino']
  likes: string[];    // ['sam', 'kelly']
  viewCount: number; // 102
  name: string; // 'grilling'
}
```

### Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

Additionally, the following packages are required for this guide:
- [x] Download and install [Node.js](https://nodejs.org).
- [x] Download and install [Python](https://www.python.org).
- [x] Download and install [Vessel@0.6.0](https://github.com/dfinity/vessel/releases/tag/v0.6.0).

## Deploying the CanCan dapp

Double-check you have [vessel](https://github.com/dfinity/vessel) installed at version 0.6.*, then clone this repository and navigate to the `cancan` directory.


```shell
vessel --version
# vessel 0.6.0

git clone git@github.com:dfinity/cancan.git
cd cancan
```

Start a local Internet Computer replica by running the following command:

```shell
dfx start --clean
```

Then, edit the `dfx.json` file to use the latest version of dfx:

```
  "dfx": "0.14.1"
```

Execute the following command in another terminal tab in the same directory:

```shell
npm install
```

Replace the contents of the `./bootstrap.sh` script with the following:

```
#!/bin/bash

set -e
echo "Running bootstrap script..."

# Support bootstrapping CanCan from any folder
BASEDIR=$(
  cd "$(dirname "$0")"
  pwd
)
cd "$BASEDIR"

host="localhost"
address="http://$host"

echo "dfx build"
npm run deploy

# echo "Running seed script..."
# echo "\nThis command may prompt you to install node to run.\nPlease accept and continue."
# npm run seed -- 2

URL="http://$(dfx canister id cancan_ui).$host:4943/"

echo "Open at $URL"

case $(uname) in
Linux) xdg-open $URL || true ;;
*) open $URL || true ;;
esac
```

Next, edit the `package-set.dhall` file to update the upstream URL:

```
let upstream =
  https://github.com/dfinity/vessel-package-set/releases/download/mo-0.7.5-20230118/package-set.dhall
```


Then, run the bootstrap script:

```
./bootstrap.sh
```

This will deploy a local canister called `cancan_ui_backend`. To open the front-end, get the frontend canister id by running `dfx canister id cancan_ui_frontend`. Then open your browser, and navigate to `http://<cancan_ui-canister-id>.localhost:8000/sign-in`.

To run a development server with fast refreshing and hot-reloading, you can use this command in the app's root directory:

```shell
npm run start
```

Your default browser will open (or focus) a tab at `localhost:3000`, to which you must then append `/?canisterId=${cancan_ui_canister_id}`, where `cancan_ui_canister_id` is typically (at current) `ryjl3-tyaaa-aaaaa-aaaba-cai`.

Now you can make changes to any frontend code and see instant updates, in many cases not even requiring a page refresh, so UI state is preserved between changes. Occasionally adding a CSS rule won't trigger an update, and the user has to manually refresh to see those changes.

## Next steps

To complete this guide, check out some additional [sample dapps](sample-apps.md).
-->
