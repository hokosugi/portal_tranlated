---

title: Canister smart contracts serve the Web
abstract:
shareImage: /img/how-it-works/web-content.jpg
slug: smart-contracts-serve-the-web
---
# スマートコントラクトがウェブに貢献

Internet Computer は、フロントエンド、バックエンド、データなど、完全なdapp をホストできる唯一のブロックチェーンです。どのユーザーもdapp をcanister （スマートコントラクト）としてInternet Computer 上にデプロイすることができます。Canisters はコードとステートを束ねた計算ユニットです。Canisters はデータの保存、HTML、CSS、Javascript ページの配信、API リクエストへの応答が可能です。Canisters は驚くほど高速で、200ms 以内にウェブページを配信できます。Canisters は最大 32 GB のデータを驚くほど低コストで保存できます（年間 1 GB あたり 5 ドル）。Internet Computer 上でホストされているdapps のブラウジングは、クラウド上でホストされている Web2 アプリのブラウジングと同じくらいシームレスです。これらすべてのactor、開発者はクラウドサービスを必要とせず、大規模なソーシャルメディア・アプリケーションも完全にオンチェーンで展開することができます。[ Internet Computer 上にデプロイされたdapps ](https://internetcomputer.org/ecosystem/) をいくつかお試しください。

## ワークフロー

Internet Computer 上にcanister としてデプロイされたウェブサイトにクライアントがアクセスする方法を説明します。アーキテクチャには4つの主要コンポーネントが含まれます。

- クライアント - ユーザーが所有するデバイス。ユーザーがウェブサイトを閲覧する際、クライアント・デバイスは HTTP リクエストを送信します。
- HTTPゲートウェイ - HTTP[ゲートウェイプロトコルを](/docs/current/references/ic-interface-spec/#http-gateway)実装するソフトウェア。HTTPリクエストをcanisters が理解できる形式に変換します。canister がレスポンスを送り返すと、HTTPゲートウェイはレスポンスをHTTPレスポンスに変換します。HTTPゲートウェイは、クライアント、バウンダリノード、または独立したサーバーのいずれかで実行できます。
- バウンダリノード - バウンダリノードは、Internet Computer のアーキテクチャを追跡します。特に、バウンダリノードは、サブネットのリスト、各サブネット上のノードのリスト、各サブネットで実行されるcanisters などを追跡します。canister クエリを受信すると、バウンダリ・ノードはcanister を実行しているブロックチェーン・ノードの 1 つにリクエストをルーティングできます。
- Canister - 開発者はdapp をcanister としてホストすることができます。Canister は多くのメソッドで構成されています。誰でもcanister にクエリを送信できます。クエリは、実行するcanister メソッドとcanister メソッドの入力で構成されます。Internet Computer はユーザーから送信されたクエリを受信し、対応するcanister メソッドを実行し、ユーザーにレスポンスを返します。

<figure>
<img src="/img/how-it-works/web_access.png" alt="Architecture: HTTP Gateway and Boundary nodes help in forwarding HTTP Request to canisters" title="HTTP Gateway converts the format of messages and Boundary nodes route the message to appropriate subnet" align="center" style="width:1000px">
<figcaption align="center">
HTTP Gateway converts the format of HTTP Requests to canister queries, and canister responses to HTTP responses.<br>
Boundary nodes route canister queries to appropriate subnet.
</figcaption>
</figure>

<!-- After a developer deploys an app as a canister, he gets the canister id of the created canister. Any user can then access the website for the app at a URL of the form http://\<canister id\>.ic0.app or http://\<canister id\>.raw.ic0.app. When the user enters the above URL on his browser, the browser contacts DNS service, which resolves the ic0.app domain to an IP address of a boundary node. The browser then makes a HTTP request to the boundary node.  -->

## ウェブアプリのデプロイInternet Computer

canister がウェブコンテンツを提供したい場合、HTTP リクエスト（URL、http メソッド、ヘッダー）を消費し、HTTP レスポンス（ステータス、ヘッダー、ボディ）を出力するメソッドを実装する必要があります。canister メソッドは、HTTP レスポンスの一部として、HTML、CSS、Javascript のコンテンツを返すことができます。詳しくは[Internet Computer Interface Spec](/docs/current/references/ic-interface-spec/#ic-http_request)を参照してください。

また、既存の静的ウェブアプリ（React や Angular などのフレームワークを使用して構築されたもの）を、「アセットcanister」を作成することで、最小限の追加コードでInternet Computer にホストする簡単な方法もあります。アセットcanister は、静的なウェブサイトをホストするための多くの定型的なコードが私たちのために処理されることを除けば、通常のcanister と同様に機能します。静的ウェブサイトをホストするには、canister を作成し、そのタイプを "asset" と指定し、ウェブアプリのソースフォルダを指定するだけです。アセットcanister がInternet Computer にデプロイされると、ウェブサイトは http://\<canister id\>.ic0.app と http://\<canister id\>.raw.ic0.app からアクセスできるようになります。[チュートリアル](/docs/current/samples/host-a-website/)または[ビデオを](https://www.youtube.com/watch?v=JAQ1dkFvfPI)ご覧ください。

## HTTPゲートウェイプロトコル

ブラウザはHTTP(s)プロトコルのみで通信し、canister 。ブラウザとInternet Computer protocolsの間のギャップを埋めるために、ブラウザとInternet Computer の間に位置するソフトウェアである[HTTPゲートウェイを](/docs/current/references/ic-interface-spec/#http-gateway)利用します。ブラウザはhttpリクエストをhttpゲートウェイに送信します。ゲートウェイはまずhttpリクエストのURLを解釈し、対応するcanister IDを抽出します。次に、httpリクエストをcanister クエリに変換し、境界ノードに送信します。canister が応答を送り返すと、http ゲートウェイは応答を解釈し、署名を検証し、http 応答に変換してブラウザに送信します。

HTTPゲートウェイプロトコルを実装する方法はたくさんあります。現在、2つの実装があります。

- ゲートウェイプロトコルは[サービスワーカーとして](https://web.dev/learn/pwa/service-workers/)実装されています。ユーザーが http://\<canister id\>.ic0.app のようなURLを入力すると、ブラウザはクエリーを解決するためにDNSサービスを呼び出します。現在、DNS は ic0.app ドメインをInternet Computer の[バウンダリノードに](/how-it-works/boundary-nodes/)マッピングします。ブラウザがバウンダリノードにリクエストを行うと、HTTP ゲートウェイプロトコルを実装したサービスワーカーで応答します。ブラウザはサービスワーカーをインストールします。それ以降、ユーザーが http://\<canister id\>.ic0.app にリクエストするたびに、ブラウザはリクエストをサービスワーカーに渡します。
- バウンダリノードもHTTPゲートウェイプロトコルを実装しています。ユーザーが http://\<canister id\>.raw.ic0.app のような URL を入力すると、ブラウザは http ゲートウェイとして動作するバウンダリノードに http リクエストを送信します。
  HTTP ゲートウェイプロトコルを実装する方法は他にもいくつかあります。ゲートウェイはブラウザの拡張機能として実装できます。chromiumブラウザを変更して、ブラウザの一部としてHTTP Gatewayを含めることもできます。

## SEO

Internet Computer 上で動作するdapps は、クローラーがオンチェーンで直接アクセスできるため、Web 2.0 の世界にシームレスに統合されます。これにより、dapps は検索エンジンにインデックスされ、ソーシャルプラットフォーム上でプレビューやカードを生成するためにメタデータを読み取ることができます。Internet Computer の検索エンジン最適化（SEO）機能の使用に関するチュートリアルは、こちらの[ブログ記事を](https://medium.com/dfinity/how-to-configure-dapps-for-social-platform-previews-and-seo-62a55ee63d33)ご覧ください。

[ウェブコンテンツの配信](/capabilities/serve-web-content/)

[IC 上でのフロントエンドdapp の構築](https://medium.com/dfinity/building-a-front-end-dapp-on-the-internet-computer-55985f0a595b)

[IC での静的ウェブサイトのホスティング](/docs/current/samples/host-a-website/)

[HTTPゲートウェイプロトコル](/docs/current/references/ic-interface-spec/#http-gateway)

[Web Serving Wiki の記事](https://wiki.internetcomputer.org/wiki/Web_Serving)

[![Watch youtube video](https://i.ytimg.com/vi/JAQ1dkFvfPI/maxresdefault.jpg)](https://www.youtube.com/watch?v=JAQ1dkFvfPI)

[![Watch youtube video](https://i.ytimg.com/vi/b_nc6yx5_DQ/maxresdefault.jpg)](https://www.youtube.com/watch?v=b_nc6yx5_DQ)

<!---


# Smart Contracts serve the web

The Internet Computer is the only blockchain that can host a full dapp – frontend, backend and data. Any user can deploy their dapp as a canister (smart contract) on the Internet Computer. Canisters are computational units that bundle together code and state. Canisters can store data, deliver HTML, CSS and Javascript pages, and answer API requests. Canisters are incredibly fast and can deliver webpages within 200ms. Canisters can store up to 32 GB of data at an incredibly low cost ($5 per GB per annum). Browsing dapps hosted on the Internet Computer is as seamless as browsing Web2 apps hosted on cloud. All these factors enable developers to deploy even large-scale social media applications entirely on-chain without needing any cloud services. Try out a few [dapps deployed on the Internet Computer](https://internetcomputer.org/ecosystem/).

## Workflow

We now describe how a client can access a website deployed as a canister on the Internet Computer. The architecture involves 4 key components.

- Client - A device owned by the user. When the user browses a website, the client device sends a HTTP request.
- HTTP Gateway - A HTTP Gateway is a software that implements [HTTP Gateway protocol](/docs/current/references/ic-interface-spec/#http-gateway). It converts HTTP requests into a format understandable by canisters. When the canister sends back a response, the HTTP Gateway converts the response into a HTTP response. The HTTP gateway can be run either on the client, on the boundary nodes or on independent servers.
- Boundary Node - Boundary nodes keep track of the architecture of the Internet Computer. In particular, boundary nodes keep track of the list of subnets, list of nodes on each subnet, the canisters run by each subnet, etc. On receiving a canister query, boundary nodes can route the request to one of the blockchain nodes running the canister.
- Canister - Developers can host their dapp as a canister. Canister consists of a bunch of methods. Anyone can send queries to the canister. A query consists of the canister method to be executed and the inputs for the canister method. The Internet Computer receives the queries sent by the users, executes the corresponding canister method and returns the response to the user.

<figure>
<img src="/img/how-it-works/web_access.png" alt="Architecture: HTTP Gateway and Boundary nodes help in forwarding HTTP Request to canisters" title="HTTP Gateway converts the format of messages and Boundary nodes route the message to appropriate subnet" align="center" style="width:1000px">
<figcaption align="center">
HTTP Gateway converts the format of HTTP Requests to canister queries, and canister responses to HTTP responses.<br>
Boundary nodes route canister queries to appropriate subnet.
</figcaption>
</figure>

<!-- After a developer deploys an app as a canister, he gets the canister id of the created canister. Any user can then access the website for the app at a URL of the form http://\<canister id\>.ic0.app or http://\<canister id\>.raw.ic0.app. When the user enters the above URL on his browser, the browser contacts DNS service, which resolves the ic0.app domain to an IP address of a boundary node. The browser then makes a HTTP request to the boundary node.  -!->

## Deploying web apps on the Internet Computer

If a canister wishes to serve web content, it should implement a method that consumes a HTTP request (url, http method and headers) and outputs a HTTP response (status, headers and body). The canister method could return HTML, CSS and Javascript content as part of the HTTP response. Refer to [Internet Computer Interface Spec](/docs/current/references/ic-interface-spec/#ic-http_request) for more details.

There’s also an easy way to host existing static web apps (even those built using frameworks such as React and Angular) on the Internet Computer with minimal extra code by creating an “asset canister”. An asset canister works similar to a regular canister, except that a lot of boilerplate code to host static websites is taken care of for us. To host a static website, we simply need to create a canister, specify its type as “asset” and specify the source folder of the web app. Once the asset canister is deployed to the Internet Computer, the website can be accessed at http://\<canister id\>.ic0.app and http://\<canister id\>.raw.ic0.app. See [tutorial](/docs/current/samples/host-a-website/) or [watch the video](https://www.youtube.com/watch?v=JAQ1dkFvfPI).

## HTTP gateway protocol

The browser only communicates with HTTP(s) protocol and doesn’t know how to query a canister. To fill the gap between the browser and Internet Computer protocols, we utilize a [HTTP Gateway](/docs/current/references/ic-interface-spec/#http-gateway), which is a software that sits in between the browser and the Internet Computer. The browser sends a http request to the http gateway. The gateway first interprets the URL in the http request and extracts the corresponding canister id. It then converts the http request into a canister query and sends it to the boundary nodes. When the canister sends back a response, the http gateway interprets the response, verifies the signatures, converts into a http response and sends it to the browser.

There are many ways to implement the HTTP Gateway protocol. Currently, there are 2 implementations.

- The gateway protocol is implemented as a [service worker](https://web.dev/learn/pwa/service-workers/). When the user enters a URL such as http://\<canister id\>.ic0.app, the browser calls the DNS service to resolve the query. Currently, the DNS maps the ic0.app domain to the [boundary nodes](/how-it-works/boundary-nodes/) of the Internet Computer. When the browser makes a request to a boundary node, it responds with a service worker that implements HTTP Gateway protocol. The browser then installs the service worker. From then on, whenever the user makes a request to http://\<canister id\>.ic0.app, the browser passes on the request to the service worker.
- Boundary nodes also implement the HTTP Gateway protocol. When the user enters a URL such as http://\<canister id\>.raw.ic0.app, the browser sends the http request to the boundary node, which acts as a http gateway.
  There are a few other ways of implementing the HTTP Gateway protocol. The gateway can be implemented as a browser extension. The chromium browser could also be modified to include HTTP Gateway as part of the browser.

## SEO

The dapps running on the Internet Computer seamlessly integrate into the Web 2.0 world as crawlers are able to access them directly on-chain. This allows dapps to be indexed by search engines and for their metadata to be read in order to generate previews and cards on social platforms. A tutorial on using the Search Engine Optimization (SEO) features of the Internet Computer can be found in this [blog post](https://medium.com/dfinity/how-to-configure-dapps-for-social-platform-previews-and-seo-62a55ee63d33).

[Serving web content](/capabilities/serve-web-content/)

[Building a front-end dapp on the IC](https://medium.com/dfinity/building-a-front-end-dapp-on-the-internet-computer-55985f0a595b)

[Hosting a static website on the IC](/docs/current/samples/host-a-website/)

[HTTP Gateway Protocol](/docs/current/references/ic-interface-spec/#http-gateway)

[Web Serving Wiki Article](https://wiki.internetcomputer.org/wiki/Web_Serving)

[![Watch youtube video](https://i.ytimg.com/vi/JAQ1dkFvfPI/maxresdefault.jpg)](https://www.youtube.com/watch?v=JAQ1dkFvfPI)

[![Watch youtube video](https://i.ytimg.com/vi/b_nc6yx5_DQ/maxresdefault.jpg)](https://www.youtube.com/watch?v=b_nc6yx5_DQ)

-->
