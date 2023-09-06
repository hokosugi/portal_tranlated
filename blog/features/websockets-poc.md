---

title: "A step towards WebSockets on the Internet Computer"
description: Introducing a WebSocket proof-of-concept for the dev community to build on.
tags: [Devs]
image: /img/blog/moonshot.webp
---
取引所では
リアルタイムで価格が更新され、文書で共同作業する際には同僚の編集が即座に表示され
、ソーシャルメディアプラットフォームのコメントは継続的に更新されるべきです。

このようなユーザーエクスペリエンスを提供するために、最新のWeb2アプリケーションは通常WebSocketに依存しています。
、アプリケーションのフロントエンド（
）とバックエンド（ ）間で長時間の全二重接続を確立することができます。これによって、例えば
最新の為替レートなどのデータをリアルタイムで送信することができます。

ウェブソケットは、[HTTPSアウトコールの](https://internetcomputer.org/https-outcalls)次のステップと見なすことができます。
HTTPSアウトコールは、canisters 、一般に公開されたオフチェーンデータにアクセスすることを可能にしますが、
ウェブソケットは、あらゆるクライアントとの長寿命の双方向接続を提供します。

## 課題

WebSocketはweb2の世界では規格ですが、web3ではまだ使われていません。
。私たちは、Internet Computer ブロックチェーンのためにこれを変えることを目指しています。

残念なことに、これはInternet Computer の分散型
の性質のため、一筋縄ではいきません。バックエンドは単一の
集中型エンティティ上で実行されるのではなく、サブネット内の複数のレプリカ・ノード上で実行されます。WebSockets
をInternet Computer 上で実現するためには、主に以下の2つの課題に対処する必要があります：

*1対1の接続*- WebSocketは1対1の接続です。フロントエンドは、1つの集中型バックエンドとの接続を確立します（
）。しかし、Internet Computer 、
上のアプリケーションは、サブネット内の複数のレプリカ・ノードに分散して実行されます。WebSocketsを有効にするには、
、フロントエンドに単一のコンタクトポイント（
）を提供する統合ポイントが必要です。

*長寿命の接続*- WebSocket は長時間オープンされたままです。しかし、Internet Computer 上の
canisters はactor モデルに従い、
メッセージを受け取り、処理し、返信します。

開始し、経験を積むために、私たちは一歩ずつ物事を進めることにし、まず
WebSockets を
に完全に直接統合しようとするのではなく、概念実証を構築することにしましたInternet Computer 。

## アーキテクチャ

私たちの概念実証の中心は WebSocket ゲートウェイです。これは
専用のマシン上で動作し、次の図が示すように、フロントエンドであるクライアントのブラウザと
バックエンドであるInternet Computer 上のcanister の間に位置します。

![WebSockets architecture](../_assets/websockets-architecture.webp)

ゲートウェイはフロントエンドに WebSocket エンドポイントを提供し、接続を維持します。
一方、クエリーコールとアップデートコールを通じて、Internet Computer 上のバックエンドとインターフェイスします。

ゲートウェイはフロントエンドからのメッセージをアップデートコールとしてバックエンド
に送信し、フロントエンドの新しいメッセージをバックエンドに継続的にポーリングします。
ポーリングの代わりに HTTPS のアウトコールを使ってゲートウェイにメッセージを "プッシュ "することもできます。
しかし、メッセージの数が増えるにつれて、ポーリングはますます効率的になります。

集中障害点であるにもかかわらず、ゲートウェイはInternet Computer
 を利用して信頼できる役割を果たします。フロントエンドとバックエンドの両方が、ゲートウェイがメッセージを改ざんできないように
署名します。

## 試してみてください！

[Github](https://github.com/dfinity/ic-websocket-poc)
[で概念実証を](https://github.com/dfinity/ic-websocket-poc)公開しましたので、ぜひご自身で試してみてください！ご自身のプロジェクトや
のインスピレーションとしてお使いください！これは概念実証であり、本番環境で使用するためにはセキュリティと安定性を強化するためにさらなる
改良が必要であることにご注意ください。私たちは、
[開発者フォーラム](https://forum.dfinity.org/t/websockets-on-the-ic-a-proof-of-concept/20836)
[にスレッドを](https://forum.dfinity.org/t/websockets-on-the-ic-a-proof-of-concept/20836)立ち上げ、あなたが構築したすべてのアプリケーションとあなたのフィードバックを見ることを楽しみにしています。

<!---


Today, users expect web applications to be fast and snappy: an exchange should update
the prices in real-time, edits of colleagues should appear immediately when collaborating
on a document, and comments on social media platforms should update continuously.

To provide such a user-experience, modern web2 applications usually rely on WebSockets,
which allow establishing a long-lived, full-duplex connection between the frontend
and backend of an application. This allows for both sides to send data, for example,
the latest exchange rates, in real-time.

WebSockets can be seen as the next step after [HTTPS outcalls](https://internetcomputer.org/https-outcalls).
While HTTPS outcalls enable canisters to access publicly-available, off-chain data,
WebSockets provide long-lived, bi-directional connections with any client.

## Challenges

While WebSockets are a standard in the web2 world, they have not yet
found their way to web3. We aim to change this for the Internet Computer blockchain.

Unfortunately, this is not a straightforward undertaking due to the decentralized
nature of the Internet Computer, where the backend does not just run on a single
centralized entity, but on multiple replica nodes in a subnet. To enable WebSockets
on the Internet Computer, we need to address the following two main challenges:

_One-to-one connections_ - WebSockets are one-to-one connections: the frontend establishes
a connection with a single centralized backend. An application on the Internet Computer,
however, is running distributed across multiple replica nodes in a subnet. In order
to enable WebSockets, there needs to be a point of consolidation, which provides a
single contact point for the frontend.

_Long-lived connections_ - WebSockets remain open for long periods of time. The
canisters on the Internet Computer however follow the actor model, which accepts
a message, processes it and replies.

To get started and gain experience, we decided to take things one step at a time, and first
build a proof-of-concept instead of trying to directly integrate WebSockets fully
into the Internet Computer.

## Architecture

The centerpiece of our proof-of-concept is the WebSocket gateway. It runs on a
dedicated machine and sits between the frontend, the client’s browser, and the
backend, the canister on the Internet Computer as the following figure shows.

![WebSockets architecture](../_assets/websockets-architecture.webp)

The gateway provides a WebSocket endpoint for the frontend and maintains the connection,
while it interfaces with the backend on the Internet Computer through query and update calls.

The gateway sends messages coming from the frontend as update calls to the backend
and it continuously polls the backend for new messages for the frontend. Instead of
polling, one could have also used HTTPS outcalls to "push" the messages to the gateway.
However, with an increasing number of messages, polling is more and more efficient.

Despite being a centralized point of failure, the gateway draws on the Internet Computer
to fulfill its role trustlessly: both the frontend and the backend sign their messages
such that the gateway cannot tamper with them.

## Try it out!

We have released [the proof-of-concept on Github](https://github.com/dfinity/ic-websocket-poc)
and invite you to try it out yourself! Use it as an inspiration for your own projects and
build upon it! Please note that this is a proof-of-concept and requires further
improvements to enhance security and stability for any use in production. We have
started [a thread in the developer forum](https://forum.dfinity.org/t/websockets-on-the-ic-a-proof-of-concept/20836)
and are looking forward to seeing all the applications you built and your feedback.

-->
