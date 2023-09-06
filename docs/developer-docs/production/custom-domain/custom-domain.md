# カスタムドメイン

## 概要

デフォルトでは、Internet Computer 上のすべてのcanisters は、`icp0.io`
とそのcanister ID からアクセスできます。このデフォルトドメインに加えて、カスタムドメインで
canister をホストすることもできます。このガイドではその方法を説明します。

カスタムドメインでcanister をホストするには、基本的に2つの方法があります：

- [境界ノードにドメインを登録](#custom-domains-on-the-boundary-nodes)します。
- [独自のインフラストラクチャで](#custom-domains-using-your-own-infrastructure)ドメインをホストする方法。

どちらの方法でも、お好きなレジストラでドメインを取得する必要があります。

この2つのアプローチは、使いやすさと設定のしやすさが異なります。
ドメインをバウンダリノードに登録する場合、
カスタムドメインの DNS レコードを設定するだけで、バウンダリノードが証明書の取得、
証明書の期限切れ前の更新、SEO、サービスワーカーの提供を行います。
ドメインを独自のインフラストラクチャでホスティングする場合、証明書の取得と更新を行い、サービスワーカーを提供する独自のインフラストラクチャを用意する必要があります。
ただし、ドメインの設定方法はより柔軟になります（例、
カスタムサービスワーカーを提供することもできます)。

:::info
 `HttpAgent` は、`icp0.io` や`ic0.app` のように自動的にホストを推測することができないため、カスタムドメインを使用する場合は、フロントエンドのコードで`host` を指定する必要があるかもしれません。エージェントを設定するには、以下のようになります：

``` ts
// Point to icp-api for the mainnet. Leaving host undefined will work for localhost
const host = isProduction ? 'https://icp-api.io' : undefined;
const agent = new HttpAgent({ host });
```

:::

## バウンダリノードのカスタムドメイン

以下では、まず
カスタムドメインをバウンダリノードに登録するために必要なすべての手順を示します。次に、どのように登録を更新し、
削除するかを説明します。

### 最初の登録

以下の手順に従って、バウンダリノードを使用して、カスタムドメイン
の下にcanister をホストすることができます。まず、必要な手順を説明します。次に、
[具体的な例でこれらの手順を](#concrete-example)説明し、
[トラブルシューティングの](#troubleshooting)手順を説明します。

- #### ステップ1：ドメインのDNSレコードを設定します（`CUSTOM_DOMAIN` で表します）。

`icp1.io` を指すドメインの`CNAME` エントリを追加し、ドメイン宛てのトラフィックがすべてバウンダリ ノードにリダイレクトされるようにします。
 canister ID を含む`TXT` エントリを、ドメインの`_canister-id`-サブドメイン（たとえば、`_canister-id.CUSTOM_DOMAIN` ）に追加します。
 `_acme-challenge` -サブドメイン（たとえば、`_acme-challenge.CUSTOM_DOMAIN` ）の`CNAME` エントリを追加し、バウンダリ ノードが証明書を取得するために、`_acme-challenge.CUSTOM_DOMAIN.icp2.io` を指します。

:::info
多くの場合、ドメインの先頭であるApexレコードに`CNAME` レコードを設定することはできません。この場合、DNSプロバイダーは、いわゆる`CNAME` フラット化をサポートします。このため、これらのDNSプロバイダーは、`CNAME` ～`icp1.io`.
::の代わりに使用できる、`ANAME` や`ALIAS` レコードなどのフラット化レコードタイプを提供しています：

-----

- #### ステップ2：カスタムドメインを含む`.well-known` の下のcanister に、`ic-domains` という名前のファイルを作成します。

デフォルトでは、`dfx` は、名前が`.` で始まるすべてのファイルとディレクトリをアセットcanister から除外します。したがって、`ic-domains` ファイルを含めるには、`.ic-assets.json` という追加のファイルを作成する必要があります。
 `dfx.json` の`sources` にリストされているディレクトリ内に、`.ic-assets.json` という名前の新しいファイルを作成します。

1つのcanister で複数のカスタム・ドメインとそのサブドメインを使用するには、`ic-domains`-ファイル内の改行で各ドメインをリストするだけです：

``` sh
custom-domain1.com
subdomain1.custom-domain1.com
subdomain2.custom-domain1.com
custom-domain2.com
subdomain1.custom-domain3.com
custom-domain4.com
```

上記の例では、`subdomain1` は、例えば、`www` を表します。

`.ic-assets.json`-ファイルに以下の設定を記述して、`.well-known` ディレクトリを含めるように設定します：

    [
        {
            "match": ".well-known",
            "ignore": false
        }
    ]

更新されたcanister をデプロイします。

-----

- #### ステップ3：以下のコマンドを実行し、`CUSTOM_DOMAIN` をカスタムドメインに置き換えて、ドメインをバウンダリノードに登録します。

<!-- end list -->

``` sh
curl -sLv -X POST \
    -H 'Content-Type: application/json' \
    https://icp0.io/registrations \
    --data @- <<EOF
{
    "name": "CUSTOM_DOMAIN"
}
EOF
```

クエリーコールが成功した場合、本文にリクエストIDを含むJSONレスポンスが返されます：

    {"id":"REQUEST_ID"}

呼び出しに失敗した場合は、失敗の理由を示すエラーメッセージが表示されます：

- **DNS CNAMEレコードがありません**:`_acme-challenge`-サブドメインの`CNAME` エントリがありません。
- **既存のDNS TXTチャレンジレコード**：DNSレコードに`_acme-challenge`-サブドメインの`TXT` エントリがすでに含まれています。これを削除して再試行してください。
- **DNS TXT レコードが見つかりません**:`_canister-id`-サブドメインの`TXT` エントリが見つかりません。
- **無効な DNS TXT レコード**:`TXT` エントリの内容が有効なcanister ID ではありません。
- 複数の DNS**TXT** レコード:`_canister-id`-サブドメインの`TXT` エントリが複数あります。これらを削除し、1つだけを残してください。
- **既知のドメインの取得に失敗**しました:`ic-domains`- ファイルは`.well-known/ic-domains` でアクセスできません。
- 既知のドメインの**リストからドメインが**見つかりません:`ic-domains`-ファイルからカスタムドメインが見つかりません。
- **頂点ドメインのレート制限を超えました**: 頂点ドメインの登録要求が多すぎます。後で再試行してください。1つのapexドメインおよび1時間あたり、最大5件の登録リクエストを送信できます。

-----

- #### ステップ4：登録処理には数分かかることがあります。

次のコマンドを実行し、`REQUEST_ID` を前のステップで受信した ID に置き換えて、登録リクエストの進行状況を追跡します。

``` sh
curl -sLv -X GET \
    https://icp0.io/registrations/REQUEST_ID
```

ステータスは以下のいずれかになります：

- `PendingOrder`登録要求が提出され、受け取り待ちです。
- `PendingChallengeResponse`証明書が発注されました。
- `PendingAcmeApproval`チャレンジが完了しました。
- `Available`登録リクエストは正常に処理されました。
- `Failed`登録申請が失敗しました。

-----

- #### ステップ5：登録要求が`available` 、すべてのバウンダリ・ノードで証明書が利用可能になるまで数分待ちます。

その後、カスタムドメインを使用してcanister にアクセスできるようになります。

## 具体例

canister のドメイン`foo.bar.com` をcanister ID`hwvjt-wqaaa-aaaam-qadra-cai` で登録したいとします。

### DNSコンフィギュレーション

| レコードタイプ | ホスト | 値 |
| --- | --- | --- |
| `CNAME` | foo.bar.com | icp1.io |
| `TXT` | canister-id.foo.bar.com | hwvjt-wqaaa-aaaam-qadra-cai |
| `CNAME` | \_acme-challenge.foo.bar.com。 | \_acme-challenge.foo.bar.com.icp2.io。 |

:::info
DNSプロバイダーによっては、メインドメインを指定する必要がありません。たとえば、`foo.bar.com` の代わりに`foo` を、`_canister-id.foo.bar.com` の代わりに`_canister-id.foo` を、`_acme-challenge.foo.bar.com` の代わりに`_acme-challenge.foo` を指定するだけです。
::：

### `.well-known/ic-domains`

- #### ステップ1：`.well-known` ディレクトリに、以下の内容の`ic-domains` ファイルを作成します：
      foo.bar.com
- #### ステップ 2:canister ソースのルートに`.ic-assets.json` ファイルを作成します：

<!-- end list -->

    [
        {
            "match": ".well-known",
            "ignore": false
        }
    ]

- #### ステップ 3: 更新されたcanister をデプロイします。

### 登録プロセスの開始

``` sh
curl -sLv -X POST \
    -H 'Content-Type: application/json' \
    https://icp0.io/registrations \
    --data @- <<EOF
{
    "name": "foo.bar.com"
}
EOF
```

:::info
[以下の文書では](dns-setup.md)、人気のある2つのドメインレジストラを例に、DNS
レコードを設定する詳細な手順を説明します。
::：

### トラブルシューティング

カスタムドメインを登録しようとして問題に遭遇した場合、
以下のチェックを行ってください：

- のようなツールを使用してDNS設定を確認します。 [`dig`](https://linux.die.net/man/1/dig)または [`nslookup`](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup).例えば、canister IDを持つ`TXT` レコードを確認するには、`dig TXT _canister-id.CUSTOM_DOMAIN` を実行します。特に、余分なエントリ（たとえば、`_canister-id`-サブドメインのための複数の`TXT` レコード）がないことを確認してください。
- `_acme-challenge`-サブドメインに`TXT` レコードがないことを確認します（`dig TXT _acme-challenge.CUSTOM_DOMAIN` など）。`TXT` レコードがある場合は、ドメインプロバイダーが過去に ACME チャレンジを行った際に残ったものである可能性が高いです。これらのレコードはドメイン管理ダッシュボードに表示されないことが多いので注意してください。これらのレコードを削除するには、ドメインプロバイダーから提供されているすべてのTLS/SSL証明書を無効にしてみてください。
- canister （ブラウザで`CANISTER_ID.icp0.io/.well-known/ic-domains` を開くか、ターミナルで`curl CANISTER_ID.icp0.io/.well-known/ic-domains` を使用するなど）から直接ダウンロードして、`ic-domains` ファイルを確認します。

## カスタムドメインの更新

ドメインを更新して別のcanister 、まず
、ドメインのDNSレコードを更新し、バウンダリノードに通知する必要があります：

- #### ステップ1：`TXT` エントリを更新し、ドメインのサブドメイン（例：`_canister-id` ）の新しいcanister IDを含めます（`_canister-id.CUSTOM_DOMAIN` ）。
- #### ステップ2：PUTリクエストと登録のID（`REQUEST_ID` ）を使用して、境界ノードに変更を通知します。

<!-- end list -->

``` sh
curl -sLv -X PUT \
    https://icp0.io/registrations/REQUEST_ID
```

:::info
登録の ID を忘れた場合は、ドメインの登録
リクエストを再度送信するだけで、境界ノードが対応する ID を返します。
::：

### カスタムドメインの削除

ドメインを削除したい場合は、DNSレコード
を削除し、バウンダリノードに通知するだけです：

- #### ステップ 1:`_canister-id`-subdomain (例:`_canister-id.CUSTOM_DOMAIN`) のcanister ID を含む`TXT` エントリと`_acme-challenge`-subdomain (例:`_acme-challenge.CUSTOM_DOMAIN`) の`CNAME` エントリを削除します。
- #### ステップ2: DELETEリクエストを使用して、削除をバウンダリノードに通知。

<!-- end list -->

``` sh
curl -sLv -X DELETE \
    https://icp0.io/registrations/REQUEST_ID
```

:::info
万が一、登録の ID を忘れてしまった場合は、ドメインに対して別の登録
リクエストを送信すれば、境界ノードが対応する ID を返します。
::：

## 独自のインフラを使用したカスタムドメイン

- #### ステップ1:canister をICにデプロイし、canister IDを記録します。
- #### ステップ 2:[公式 IC リポジトリを](https://github.com/dfinity/ic)クローンし、`ic/typescript/service-worker` の下にある[service worker フォルダに](https://github.com/dfinity/ic/tree/master/typescript/service-worker)移動します。
- #### canister `hostnameCanisterIdMap` ステップ 3:canister ID にドメインをマッピングします。 [`service-worker/src/sw/domains/static.ts`](https://github.com/dfinity/ic/blob/master/typescript/service-worker/src/sw/domains/static.ts).
- #### ステップ 4:`service-worker/README.md` の指示に従ってサービス ワーカーをビルドします。出力は次のようになります：
  - `index.html` 、
  - 最小化された`.js` ファイル
  - `.map` ファイルです。
- #### ステップ 5: サーバーまたは CDN からアセット（`index.html` 、`.js` 、`.map` ファイル）をホストし、カスタムドメイン名をこのサーバーに指定します。
- #### ステップ6：テスト

:::caution
Internet Identity（II）を使用してユーザーを認証するウェブサイトの場合：IIによって提供されるプリンシパルは、ログイン要求が開始されたドメインに依存します。そのため、canister URLを通じてユーザーを認証している場合に、カスタムドメインに切り替えようとすると、ユーザーは同じプリンシパルを持つことができなくなります。これを防ぐには、[Alternative Originsを](../../integrations/internet-identity/alternative-origins.md)設定してください。
::：

<!---
# Custom domains

## Overview

By default all canisters on the Internet Computer are accessible through `icp0.io`
and their canister ID. In addition to that default domain, one can also host a
canister under a custom domain. This guide explains how to do that.

There are, essentially, two approaches to host a canister under a custom domain:

- [Register the domain with the boundary nodes](#custom-domains-on-the-boundary-nodes).
- [Hosting the domain on your own infrastructure](#custom-domains-using-your-own-infrastructure).

For both approaches, you need to acquire a domain through your favorite registrar.

The two approaches differ in the ease of use and the configurability. When registering
the domain with the boundary nodes, you simply have to configure the DNS records of
the custom domain and the boundary nodes take care of obtaining a certificate,
renewing the certificate before its expiration, SEO and serving the service worker.
When hosting the domain on your own infrastructure, you need to obtain and renew
the certificates, and provide your own infrastructure that serves the service worker.
However, you are also more flexible in how you configure your domain (e.g., you
can serve a custom service worker).

:::info
You may need to specify a `host` in your frontend code when you are using a custom domain, as the `HttpAgent` may not be able to automatically infer the host like it can on `icp0.io` and `ic0.app`. To configure your agent, it will look something like this:

```ts
// Point to icp-api for the mainnet. Leaving host undefined will work for localhost
const host = isProduction ? 'https://icp-api.io' : undefined;
const agent = new HttpAgent({ host });
```
:::

## Custom domains on the boundary nodes

In the following, we first list all the steps necessary to register your
custom domain with the boundary nodes. Then, we explain how one can update and
remove a registration.

### First registration

By following the steps below, you can host your canister under your custom domain
using the boundary nodes. We first explain the necessary steps. Then, we provide
a [concrete example to illustrate these steps](#concrete-example), followed by some
instructions on [troubleshooting](#troubleshooting).

- #### Step 1: Configure the DNS record of your domain, which we denote with `CUSTOM_DOMAIN`.
Add a `CNAME` entry for your domain pointing to `icp1.io` such that all the traffic destined to your domain is redirected to the boundary nodes;
Add a `TXT` entry containing the canister ID to the `_canister-id`-subdomain of your domain (e.g., `_canister-id.CUSTOM_DOMAIN`);
Add a `CNAME` entry for the `_acme-challenge`-subdomain (e.g., `_acme-challenge.CUSTOM_DOMAIN`) pointing to `_acme-challenge.CUSTOM_DOMAIN.icp2.io` in order for the boundary nodes to acquire the certificate.

:::info
In many cases, it is not possible to set a `CNAME` record for the top of a domain, the Apex record. In this case, DNS providers support so-called `CNAME` flattening. To this end, these DNS providers offer flattened record types, such as `ANAME` or `ALIAS` records, which can be used instead of the `CNAME` to `icp1.io`.
:::

--------------------------------------

- #### Step 2: Create a file named `ic-domains` in your canister under `.well-known` containing the custom domain.
By default, `dfx` excludes all files and directories whose names start with a `.` from the asset canister. Hence, to include the `ic-domains`-file, you need to create an additional file, called `.ic-assets.json`.
Create a new file with the name `.ic-assets.json` inside a directory listed in `sources` in `dfx.json`.

To use multiple custom domains and their subdomains with a single canister, simply list each one of them on a newline in the `ic-domains`-file:
```sh
custom-domain1.com
subdomain1.custom-domain1.com
subdomain2.custom-domain1.com
custom-domain2.com
subdomain1.custom-domain3.com
custom-domain4.com
```

In the above example, `subdomain1` could, for example, stand for `www`.

Configure the `.well-known` directory to be included by writing the following configuration into the `.ic-assets.json`-file:

```
[
    {
        "match": ".well-known",
        "ignore": false
    }
]
```

Deploy the updated canister.

--------------------------------------

- #### Step 3: Register the domain with the boundary nodes by issuing the following command and replacing `CUSTOM_DOMAIN` with your custom domain.

```sh
curl -sLv -X POST \
    -H 'Content-Type: application/json' \
    https://icp0.io/registrations \
    --data @- <<EOF
{
    "name": "CUSTOM_DOMAIN"
}
EOF
```
If the call was successful, you will get a JSON response that contains the request ID in the body, which you can use to query the status of your registration request:
```
{"id":"REQUEST_ID"}
```
In case the call failed, you will get an error message indicating the reason for the failure:
* **Missing DNS CNAME record**: the `CNAME` entry for the `_acme-challenge`-subdomain is missing.
* **Existing DNS TXT challenge record**: the DNS record already contains a `TXT` entry for the `_acme-challenge`-subdomain. Remove it and try again.
* **Missing DNS TXT record**: the `TXT` entry for the `_canister-id`-subdomain is missing.
* **Invalid DNS TXT record**: the content of the `TXT` entry is not a valid canister ID.
* **More than one DNS TXT record**: there are multiple `TXT` entries for the `_canister-id`-subdomain. Remove them and keep only one.
* **Failed to retrieve known domains**: the `ic-domains`-file is not accessible under `.well-known/ic-domains`.
* **Domain is missing from list of known domains**: the custom domain is missing from the `ic-domains`-file.
* **Rate limit exceeded for apex domain**: too many registration requests have been submitted for the apex domain. Try again later. You can submit at most 5 registration requests per apex domain and hour.
--------------------------------------

- #### Step 4: Processing the registration can take several minutes.
Track the progress of your registration request by issuing the following command and replacing `REQUEST_ID` with the ID you received in the previous step.

```sh
curl -sLv -X GET \
    https://icp0.io/registrations/REQUEST_ID
```

The status will be one of the following:
* `PendingOrder`: the registration request has been submitted and is waiting to be picked up.
* `PendingChallengeResponse`: the certificate has been ordered.
* `PendingAcmeApproval`: the challenge has been completed.
* `Available`: the registration request has been successfully processed.
* `Failed`: the registration request failed.

--------------------------------------

- #### Step 5: Once your registration request becomes `available`, wait a few minutes for the certificate to become available on all boundary nodes.
After that, you should be able to access your canister using the custom domain.

## Concrete example

Imagine you wanted to register your domain `foo.bar.com` for your canister with the canister ID `hwvjt-wqaaa-aaaam-qadra-cai`.

### DNS configuration

| Record Type   | Host                        | Value                               |
|---------------|-----------------------------|-------------------------------------|
| `CNAME`       | foo.bar.com                 | icp1.io                             |
| `TXT`         | _canister-id.foo.bar.com    | hwvjt-wqaaa-aaaam-qadra-cai         |
| `CNAME`       | _acme-challenge.foo.bar.com | _acme-challenge.foo.bar.com.icp2.io |

:::info
Some DNS providers do not require you to specify the main domain. For example, you would just have to specify `foo` instead of `foo.bar.com`, `_canister-id.foo` instead of `_canister-id.foo.bar.com`, and `_acme-challenge.foo` instead of `_acme-challenge.foo.bar.com`.
:::

### `.well-known/ic-domains`
- #### Step 1: Create the `ic-domains` file with the following content in the `.well-known` directory:
    ```
    foo.bar.com
    ```
- #### Step 2: Create the `.ic-assets.json` file at the root of the canister source:

```
[
    {
        "match": ".well-known",
        "ignore": false
    }
]
```

- #### Step 3: Deploy the updated canister.

### Start the registration process

```sh
curl -sLv -X POST \
    -H 'Content-Type: application/json' \
    https://icp0.io/registrations \
    --data @- <<EOF
{
    "name": "foo.bar.com"
}
EOF
```

:::info
In the [following document](dns-setup.md), we provide detailed instructions to configure DNS
records on the example of two popular domain registrars.
:::

### Troubleshooting

When you are running into issues trying to register your custom domains, make the
following checks:

- Check your DNS configuration using a tool like [`dig`](https://linux.die.net/man/1/dig) or [`nslookup`](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup). To check, for example, the `TXT` record with the canister ID, you can run `dig TXT _canister-id.CUSTOM_DOMAIN`. In particular, make sure that there are no extra entries (e.g., multiple `TXT` records for the `_canister-id`-subdomain).
- Check that there are no `TXT` records for the `_acme-challenge`-subdomain (e.g., by using `dig TXT _acme-challenge.CUSTOM_DOMAIN`). If there are `TXT` records, then they are most likely left-over from previous ACME-challenges by your domain provider. Note that these records often do not show up in your domain management dashboard. Try disabling all TLS/SSL-certificate offerings from your domain provider to remove these records.
- Check the `ic-domains` file by downloading it directly from your canister (e.g., by opening `CANISTER_ID.icp0.io/.well-known/ic-domains` in your browser or using `curl CANISTER_ID.icp0.io/.well-known/ic-domains` in your terminal).

## Updating a custom domain

In case you want to update the domain to point to a different canister, you first
need to update the DNS record of your domain and then notify a boundary node:

- #### Step 1: Update the `TXT` entry to contain the new canister ID for the `_canister-id`-subdomain of your domain (e.g., `_canister-id.CUSTOM_DOMAIN`).
- #### Step 2: Notify a boundary node of the change using a PUT request and the ID of your registration (`REQUEST_ID`).

```sh
curl -sLv -X PUT \
    https://icp0.io/registrations/REQUEST_ID
```

:::info
In case you forgot the ID of your registration, you can just submit another registration
request for your domain and the boundary node will return the corresponding ID.
:::

### Removing a custom domain

In case you want to remove your domain, you just need to remove the DNS records
and notify a boundary node:

- #### Step 1: Remove the `TXT` entry containing the canister ID for `_canister-id`-subdomain (e.g., `_canister-id.CUSTOM_DOMAIN`) and the `CNAME` entry for the `_acme-challenge`-subdomain (e.g., `_acme-challenge.CUSTOM_DOMAIN`).
- #### Step 2: Notify a boundary node of the removal using a DELETE request.

```sh
curl -sLv -X DELETE \
    https://icp0.io/registrations/REQUEST_ID
```

:::info
In case you forgot the ID of your registration, you can just submit another registration
request for your domain and the boundary node will return the corresponding ID.
:::

## Custom domains using your own infrastructure

- #### Step 1: Deploy your canister to the IC and note the canister id.
- #### Step 2: Clone the [official IC repo](https://github.com/dfinity/ic) and navigate to the [service worker folder](https://github.com/dfinity/ic/tree/master/typescript/service-worker) located under `ic/typescript/service-worker`.
- #### Step 3: Map your domain to the canister ID by adding your domain-to-canister mapping to `hostnameCanisterIdMap` in the file [`service-worker/src/sw/domains/static.ts`](https://github.com/dfinity/ic/blob/master/typescript/service-worker/src/sw/domains/static.ts).
- #### Step 4: Build the service worker according to the instructions in `service-worker/README.md`. The output should be:
    - an `index.html`,
    - a minified `.js` file, and
    - a `.map` file.
- #### Step 5: Host the assets (`index.html`, `.js` and `.map` files) from a server or CDN and point your custom domain name at this server.
- #### Step 6: Test.

:::caution
For websites that use Internet Identity (II) to authenticate users: The principals provided by II depend on the domain from which the login request was started. So if you authenticate your users through the canister URL and want to switch over to a custom domain, users will not have the same principals anymore. You can prevent this by setting up [Alternative Origins](../../integrations/internet-identity/alternative-origins.md).
:::

-->
