---

title: "Internet Identity: Anonymizing Blockchain Authentication System"
abstract:
shareImage: /img/how-it-works/internet-identity.jpg
slug: web-authentication-identity
---
# インターネットアイデンティティ

[Internet Ident](https://identity.ic0.app/)ity は、Internet Computer 上に構築された、プライバシーを強化するブロックチェーンベースの認証フレームワークです。最新のブラウザやオペレーティングシステムで広く使用されている、安全な認証フレームワークである WebAuthn の API と統合されています。ユーザーは、パスキーを Internet Identity に接続し、パスワードや不便な 2FA の代わ りに、デバイス内の安全な TPM チップを認証に使用できます。また、Internet Identityは、YubiKeysやLedgerハードウェアウォレットのようなデバイスもサポートしています。

## プライバシー

連鎖鍵暗号方式を使用して、Internet Identity は、ユーザーが認証するdapp ごとに一意のプリンシパル ID を作成することで、プライベート認証を保証します。これにより、dapps をまたがるユーザーの追跡が防止され、各セッションがプライベートになります。

## インターネット ID の作成

インターネット ID をまだ持っていない場合は、<https://identity.ic0.app/> で作成できます。

<figure>
<img src="/img/how-it-works/ii-1.webp" alt="Internet Identity creation screen" title="Internet Identity creation screen" align="center" style="height:500px; width: auto">
</figure> 

Create Internet Identity」をクリックすると、パスキーの作成が要求されます。パスキーは、指紋センサー付きのノートパソコンや顔ID付きのスマートフォンなど、TPMチップを内蔵したデバイスであれば何でもかまいません。または、Internet Identity は、YubiKeys や Ledger デバイスなどのポータブル HSM をサポートしています。

<figure>
<img src="/img/how-it-works/ii-2.webp" alt="Create a passkey to connect with your Internet Identity" title="Create a passkey to connect with your Internet Identity" align="center" style="height:350px; width: auto">
</figure> 

Internet Identity が作成されると、ユーザーはそれを使用して、ICP ベースのdapps で安全かつプライベートに認証したり、パスキーを追加したりすることができます。一般に、複数のパスキーをインターネット ID に接続し、リカバリフレーズなどのリカバリ方法を設定することを推奨します。

<figure>
<img src="/img/how-it-works/ii-3.webp" alt="Internet Identity screen prompting the user to authorize access to Openchat" title="Internet Identity screen prompting the user to authorize access to Openchat" align="center" style="height:500px; width: auto">
</figure>

パスキーを追加すると、インターネットID番号が割り当てられます。簡単にアクセスできる安全な場所に保存しておく必要があります。ブラウザはこの番号を記憶していますが、キャッシュがクリアされると忘れてしまうため、その場合は手動で入力する必要があります。

<figure>
<img src="/img/how-it-works/ii-4.webp" alt="Internet Identity screen prompting the user to authorize access to Openchat" title="Internet Identity screen prompting the user to authorize access to Openchat" align="center" style="height:500px; width: auto">
</figure>

前述したように、デバイスの紛失や盗難に備えてリカバリフレーズを追加しておくことも重要です。インターネット ID はNetwork Nervous System (NNS)dapp にログインすることもできます。NNS はInternet Computer を管理する DAO で、ICP トークン保有者はトークンをステークすることでその管理 に参加できます。

[Internet Identityアプリ](https://identity.ic0.app/)

[インターネット ID ウィキ](https://wiki.internetcomputer.org/wiki/Internet_Computer_wiki#Internet_Identity_Introduction)

[インターネット ID 仕様](https://internetcomputer.org/docs/current/references/ii-spec/)

[オープンソース - インターネット ID](https://github.com/dfinity/internet-identity)

[ウェブ認証とアイデンティティInternet Computer](https://medium.com/dfinity/web-authentication-and-identity-on-the-internet-computer-a9bd5754c547)

[インターネット・アイデンティティ簡単な Web3 認証](https://medium.com/dfinity/internet-identity-the-end-of-usernames-and-passwords-ff45e4861bf7)

[インターネット・アイデンティティ・コードの検証チュートリアル](https://medium.com/dfinity/verifying-the-internet-identity-code-a-walkthrough-c1dd7a53f883)

[IC内部インターネット・アイデンティティ・ストレージ](https://mmapped.blog/posts/11-ii-stable-memory.html)

[![Watch youtube video](https://i.ytimg.com/vi/9eUTcCP_ELM/maxresdefault.jpg)](https://www.youtube.com/watch?v=9eUTcCP_ELM)

<!---


# Internet Identity

[Internet Identity](https://identity.ic0.app/) is a privacy-enhancing blockchain based authentication framework to built on the Internet Computer. It integrates with the APIs of WebAuthn, a widely used, secure authentication framework supported by modern browsers and operating systems. Users can connect passkeys to their Internet Identity, and use the secure TPM chip inside these devices for authentication instead of passwords or clunky 2FAs. Alternatively, Internet Identity supports devices like YubiKeys or Ledger hardware wallets.

## Privacy

Using chain-key cryptography, Internet Identity ensures private authentication by creating a unique principal id for each dapp the user authenticates with. This prevents the tracking of users across dapps, making each session private.

## Create an Internet Identity

If you don't yet have an Internet Identity, you can create one at [https://identity.ic0.app/](https://identity.ic0.app/).

<figure>
<img src="/img/how-it-works/ii-1.webp" alt="Internet Identity creation screen" title="Internet Identity creation screen" align="center" style="height:500px; width: auto">
</figure> 

If you click "Create Internet Identity", you are asked to create a passkey. A passkey can be any device that has a TPM chip inside it, such as a laptop with a fingerprint sensor, a smartphone with face ID. Alternatively, Internet Identity supports portable HSMs, such as YubiKeys or Ledger devices.

<figure>
<img src="/img/how-it-works/ii-2.webp" alt="Create a passkey to connect with your Internet Identity" title="Create a passkey to connect with your Internet Identity" align="center" style="height:350px; width: auto">
</figure> 


After the Internet Identity is created, users can already use it to securely and privately authenticate with ICP based dapps, or add more passkeys. It is generally advised to have multiple passkeys connected to your Internet Identity as well as a recovery method setup, such as a recovery phrase.

<figure>
<img src="/img/how-it-works/ii-3.webp" alt="Internet Identity screen prompting the user to authorize access to Openchat" title="Internet Identity screen prompting the user to authorize access to Openchat" align="center" style="height:500px; width: auto">
</figure>

Once you added a passkey, you will be assigned an Internet Identity number. You should save somewhere safe, where you can easily access it. While your browser remembers this number, it will forget it if its cache is cleared, in which case you will need to type it in manually.

<figure>
<img src="/img/how-it-works/ii-4.webp" alt="Internet Identity screen prompting the user to authorize access to Openchat" title="Internet Identity screen prompting the user to authorize access to Openchat" align="center" style="height:500px; width: auto">
</figure>

As mentioned previously, it is also important to add a recovery phrase in case your device gets lost or stolen. Your Internet Identity also allows you to login to the Network Nervous System (NNS) dapp. NNS is the DAO that governs the Internet Computer, and allows ICP token holders to participate in its governance by staking their tokens.


[Internet Identity App](https://identity.ic0.app/)

[Internet Identity Wiki](https://wiki.internetcomputer.org/wiki/Internet_Computer_wiki#Internet_Identity_Introduction)

[Internet Identity Specification](https://internetcomputer.org/docs/current/references/ii-spec/)

[Open Source - Internet Identity](https://github.com/dfinity/internet-identity)

[Web Authentication and Identity on the Internet Computer](https://medium.com/dfinity/web-authentication-and-identity-on-the-internet-computer-a9bd5754c547)

[Internet Identity: Easy Web3 Authentication](https://medium.com/dfinity/internet-identity-the-end-of-usernames-and-passwords-ff45e4861bf7)

[Verifying the Internet Identity Code: A Walkthrough](https://medium.com/dfinity/verifying-the-internet-identity-code-a-walkthrough-c1dd7a53f883)

[IC internals: Internet Identity storage](https://mmapped.blog/posts/11-ii-stable-memory.html)

[![Watch youtube video](https://i.ytimg.com/vi/9eUTcCP_ELM/maxresdefault.jpg)](https://www.youtube.com/watch?v=9eUTcCP_ELM)

-->
