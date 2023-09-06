---

title: Asset certification
abstract:
shareImage: /img/how-it-works/asset-certification.jpg
slug: asset-certification
---
# 資産の認証

Internet Computer とやりとりするユーザーは、受信したレスポンスが実際にInternet Computer から送られてきたものであり、改ざんされていないことを確認できる必要があります。従来、インターネットでは、この問題は公開鍵暗号を使って解決されてきました。サービスを実行するサーバーは秘密鍵を持っており、それを使ってすべてのレスポンスに署名します。その後、ユーザーはサーバーの公開鍵を使ってレスポンスの署名を検証することができます。

Web2のウェブサーバーが公開鍵と秘密鍵のペアを保持するように、Internet Computer ブロックチェーン全体も公開鍵と秘密鍵のペアを保持します。さらに、Internet Computer の各サブネットも独自の公開鍵と秘密鍵のペアを維持します。新しいサブネットが形成されると、NNSはサブネットの証明書を発行します。この証明書には、サブネットの公開鍵とInternet Computer の公開鍵との署名が含まれています。サブネットがユーザのメッセージに応答すると、その応答には、サブネットの公開鍵による応答への署名と、NNSがサブネットに発行した証明書を含む証明書チェーンが含まれます。ユーザーは、Web2の証明書チェーンを検証するのと同様に、Internet Computer の公開鍵を使用して証明書チェーンを検証できます。

各ブロックチェーンノードはサブネット秘密鍵の一部のみを共有します。その結果、各ノードは単独でメッセージに署名することはできません。しかし、サブネットのノードの少なくとも2/3がメッセージに同意すれば、そのノードは一緒に秘密鍵を組み合わせてメッセージに署名することができます。署名されたメッセージは、サブネットの公開鍵を使って簡単に検証することができます。検証が成功すれば、canister を実行しているブロックチェーンノードの少なくとも2/3がそのメッセージの配信に合意したことになります。Internet Computer 、秘密鍵共有の生成と維持、秘密鍵共有を使用したメッセージの署名に使用される技術は、[チェーンキー技術と](/how-it-works/chain-key-technology/)呼ばれています。

Internet Computer は2種類のメッセージをサポートしています：クエリーコールとアップデートコールです。クエリーコールはHTTP`GET` リクエストに似ており、Internet Computer のステートを変更しない。クエリーコールはコンセンサスプロトコルを経由しない。ユーザーはサブネット内のどのブロックチェーンノードに対してもクエリーコールを行うことができ、その（悪意のある可能性のある）ブロックチェーンノードのみがクエリーに回答します。証明書の生成にはサブネットの少なくとも2/3のノードのコンセンサスが必要なため、Internet Computer 、クエリーコールに応答しても証明書は発行されません。

効率上の理由から、canisters はクエリーコールを通じてウェブページをクライアントに配信します。しかし、クライアントは受信したコンテンツを検証する必要があるため、Internet Computer は[Certified Variables](/how-it-works/response-certification/) という概念を導入しています。一言で言えば、canister 、データの一部に対して事前に証明書を作成し、それをレプリケートされたステート内に格納することを選択できます。どのようなユーザでも、後でクエリーコールによって証明書とともにデータにアクセスすることができます。

アプリのすべてのアセット（HTML、CSS、Javascriptファイル、画像、動画など）を事前に証明するために、証明された変数の概念を使用できます。アセット認証には2つの方法があります。1)canister の開発者は、すべてのアセットを管理および認証するコードを明示的に記述できます。2)canister の開発者は、canister を作成し、タイプを「asset」に設定し、すべてのアセットを含むフォルダを指定することで、「assetcanister 」を作成できます。アセットcanister は、すべてのアセットを管理し認証するための定型的なコードが用意されていることを除けば、通常のcanister です。

canister が証明書とともにレスポンスを発行する場合、クライアントにレスポンスを渡す前に[HTTP ゲートウェイを](/how-it-works/smart-contracts-serve-the-web)使用して証明書を検証することができます。

証明書の詳細については、[Certified Variablesを](/how-it-works/response-certification/)参照してください。

[アセット認証 Wiki記事](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification)

[RustCanister 開発セキュリティのベストプラクティス](/docs/current/references/security/rust-canister-development-security-best-practices#asset-certification)

<!---


# Asset certification

A user interacting with the Internet Computer needs to be able to confirm that the responses they receive are actually coming from the Internet Computer and have not been tampered with. Traditionally, on the Internet, this problem is solved using public-key cryptography. The server running the service has a secret key and uses that to sign all its responses. A user can then verify the signature on the response using the server’s public key.

Just like a web server in Web2 maintains a public-key/secret-key pair, the Internet Computer blockchain as a whole maintains a public-key/secret-key pair. Additionally, each individual subnet in the Internet Computer also maintains its own public-key/secret-key pair. When a new subnet is formed, the NNS issues a certificate for the subnet which contains a signature of the subnet's public key with the Internet Computer's public key. When the subnet responds to a user's message, the response contains a certificate chain, which includes a signature on the response by the subnet's public key and the certificate issued by the NNS to the subnet. The user can verify the certificate chain using the Internet Computer's public key similar to verifying a certificate chain in Web2. 

Each blockchain node shares only a piece of its subnet secret key. As a result, each node is incapable of signing a message by itself. But if at least 2/3rd of the nodes of a subnet agree on a message, they together can combine their secret key pieces to sign the message. The signed message can be verified easily using the subnet's public key. If the verification succeeds, it means that at least 2/3rd of the blockchain nodes running the canister agreed to deliver that message. The technology used by the Internet Computer to generate and maintain the secret key shares, and sign messages using the secret key shares is called [chain-key technology](/how-it-works/chain-key-technology/).

The Internet Computer supports two types of messages: Query calls and Update calls. Query calls are similar to HTTP `GET` requests and do not modify the state of the Internet Computer. The query calls do not go through the consensus protocol. The user can make a query call to any blockchain node in the subnet, and only that (possibly malicious) blockchain node answers the query. As generating a certificate requires consensus from at least 2/3rd of the nodes of the subnet, the Internet Computer doesn't issue a certificate when responding to query calls. 

For efficiency reasons, the canisters deliver web pages to the client via query calls. However, as the client needs to verify the received content, the Internet Computer introduces the notion of [Certified Variables](/how-it-works/response-certification/). In a nutshell, a canister can a-priori choose to create a certificate for a piece of data and store it in the replicated state. Any user can later access the data along with its certificate via query calls. 

We can use the notion of the certified variables to certify all the assets (HTML, CSS, Javascript files, images, videos, etc.) of an app a-priori. There are 2 ways of performing the asset certification. 1) The canister developer can explicitly write code to manage and certify all the assets. 2) The canister developer can create an "asset canister", by creating a canister with type set to "asset" and specifying the folder containing all the assets. The asset canister is a regular canister, except that the boilerplate code for managing and certifying all the assets is taken care of for us. 

When a canister issues a response along with its certificate, a [HTTP Gateway](/how-it-works/smart-contracts-serve-the-web) can be used to verify the certificate before passing on the response to the client. 

For more information on certification, check [Certified Variables](/how-it-works/response-certification/).

[Asset Certification Wiki Article](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification)

[Rust Canister Development Security Best Practices](/docs/current/references/security/rust-canister-development-security-best-practices#asset-certification)

-->
