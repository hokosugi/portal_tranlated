# DNS設定ガイド

## 概要

このガイドでは、2つの人気のある
レジストラのドメインのDNSレコードを設定する方法を説明します：[Namecheapと](#namecheap) [GoDaddy](#godaddy)です。

## Namecheap

以下では、ドメインの頂点を設定するために必要な手順の概要を説明し、カスタムドメインとして使用するNamecheapのサブドメインを
。必要な
設定を説明するために、ドメイン`ic-domain.live` とサブドメイン`example.ic-domain.live`
を使用し、ID`y5jqt-wqaaa-aaaam-abcoq-cai` でcanister を指すように設定します。

- #### ステップ 1: ドメインを Namecheap で購入した後、Namecheap ダッシュボードでドメインの管理ペインを開きます。

- #### ステップ 2: \[**Advanced DNS**\] タブを開きます。

### エイペックス

ドメインの頂点（例：`ic-domain.live` ）を設定するには、\[**新しいレコードを追加**\]をクリックして以下のレコードを追加します：

- ホストフィールドを`@` に、ターゲットフィールドを`icp1.io` に設定した`ALIAS` レコードを作成します；
- ホスト・フィールドを`_acme-challenge` に、ターゲット・フィールドを`_acme-challenge.ic-domain.live.icp2.io` に設定した`CNAME` レコードを作成；
- ホスト・フィールドを`_canister-id` に、値フィールドをcanister ID`y5jqt-wqaaa-aaaam-abcoq-cai` に設定した`TXT` レコードを作成します。

結果として、以下のスクリーンショットのような構成になるはずです：

![DNS Configuration for ](namecheap-apex.png)

### サブドメイン

サブドメイン（例：`example.ic-domain.live` ）を設定するには、［**新しいレコードを追加**］をクリックして次のレコードを追加します：

- ホストフィールドを`example` に、ターゲットフィールドを`icp1.io` に設定した`CNAME` レコードを作成します；
- ホスト・フィールドを`_acme-challenge.example` に、ターゲット・フィールドを`_acme-challenge.example.ic-domain.live.icp2.io` に設定した`CNAME` レコードを作成；
- ホスト・フィールドを`_canister-id.example` に、値フィールドをcanister ID`y5jqt-wqaaa-aaaam-abcoq-cai` に設定した`TXT` レコードを作成します。

結果として、以下のスクリーンショットのような構成になるはずです：

![DNS Configuration for ](namecheap-subdomain.png)

これで、カスタムドメインをバウンダリノードに登録する準備がすべて整いましたので、[一般的なカスタムドメインの手順の](custom-domain.md#custom-domains-on-the-boundary-nodes)ステップ2に進むことができます。

## GoDaddy

以下では、Internet Computer にホストされているcanister をポイントするために使用される、GoDaddy でドメインを設定する方法を説明します。例として、
ドメイン`ic-domain.online` とサブドメイン`example.ic-domain.online`
を設定し、ID`y5jqt-wqaaa-aaaam-abcoq-cai` でcanister をポイントします。

- #### ステップ1: GoDaddyでドメインを購入した後、アカウントを開き、**My Productsに**移動します。

- #### ステップ2: ドメインの横にある**DNS**ボタンをクリックします：
  
  ![Domain Management Overview](godaddy-overview.png)

### エイペックス

残念ながら、GoDaddyはドメインの頂点に`CNAME` レコード（またはその代替、`ALIAS` または`ANAME` ）の設定をサポートしていません。

主に2つの方法があります：

- 境界ノードのIPアドレスを直接設定する方法。
- 別のDNSプロバイダー（Cloudflareなど）に依存する方法。

IPアドレスを直接設定する方法は、別のDNSプロバイダー（
）に依存する方法に比べて単純ですが、耐障害性とパフォーマンスが低下します。

### IPアドレスの直接設定

- #### ステップ1: まず、バウンダリノード(`icp1.io`)のIPアドレスを検索する必要があります。

そのために、オンラインDNSルックアップサービス（例：[nslookup.io](https://nslookup.io)）
を使用し、IPv4アドレスとIPv6アドレス（それぞれ`A` と`AAAA` レコード）を控えておきます。

    ![Resulting `A` and `AAAA` records from querying `icp1.io` on nslookup.io](nslookup-results.png)

- #### ステップ2: GoDaddyアカウントの**DNS管理**ペインで、次のDNSレコードを追加します：
  
  - 名前フィールド
    を「@」に設定し、値フィールドをIPv4アドレス（例：`193.118.63.173` ）に設定して、各IPv4アドレスに`A` レコードを作成します；
  - 名前フィールド
    を"@"に、値フィールドをIPv6アドレスに設定して、IPv6アドレスごとに`AAAA` レコードを作成します（例：`2a0b:21c0:b002:2:5000:59ff:fead:c233` ）；
  - 名前フィールドを`_acme-challenge` に、値フィールドを`_acme-challenge.ic-domain.online.icp2.io` に設定した`CNAME` レコードを作成します；
  - 名前フィールドを`_canister-id` に、値フィールドをcanister ID`y5jqt-wqaaa-aaaam-abcoq-cai` に設定した`TXT` レコードを作成。
  
  結果として、以下のスクリーンショットのような構成になるはずです：
  
  ![DNS Configuration for ](godaddy-apex-ips.png)

### 代替DNSプロバイダーに頼る

DNSプロバイダーとしてCloudflareを使用したこの方法を説明します。ドメインの頂点に`CNAME` 、`ALIAS` 、`ANAME` レコード
をサポートする他のDNSプロバイダーでも、
同様に動作します。

- #### ステップ1： Cloudflareで無料アカウントを作成し、ダッシュボードのトップバーにある**Add siteを**クリックします。

- #### ステップ2： ドメイン（例：`ic-domain.online` ）を入力し、**サイトを追加を**クリックします。

- #### ステップ 3: 無料プランを選択し、次に進みます。

- #### ステップ4：以下のレコードを追加してドメインを設定します：
  
  - 名前フィールドを`@` に、
    ターゲットフィールドを`icp1.io` に設定した`CNAME` レコードを作成します；
  - ホストフィールドを`_acme-challenge` に、ターゲットフィールドを`_acme-challenge.ic-domain.online.icp2.io` に設定した`CNAME` レコードを作成します；
  - name フィールドを`_canister-id` に、content フィールドをcanister ID`y5jqt-wqaaa-aaaam-abcoq-cai` に設定した`TXT` レコードを作成します。

結果として、以下のスクリーンショットのような構成になるはずです：

![DNS Configuration for ](cloudflare-apex.png)

- #### ステップ6: 次のステップで、CloudflareはGoDaddyが使用するように設定する2つのネームサーバーをリストします。

2つのネームサーバーをメモしておきます（例:`brianna.ns.cloudflare.com` と`kaiser.ns.cloudflare.com` ）。

- #### ステップ 7: GoDaddyの**DNS管理**ペインで、**ネームサーバー**セクションの**変更**ボタンをクリックします。

- #### ステップ8: 開いたダイアログで、\[**Enter my own nameservers (advanced)\]**をクリックし、提供されたフィールドにCloudflareからのネームサーバーを入力します。最後に、**Saveを**クリックします。

![GoDaddy's dialog to configure the nameservers](godaddy-ns-dialog.png)

- #### ステップ9：ダイアログで、ネームサーバーを変更する意図があることを確認します。

結果として、以下のスクリーンショットのような構成になるはずです：

![Alternative nameserver configuration on GoDaddy](godaddy-ns-configured.png)

- #### ステップ10：Cloudflare管理ポータルに戻り、**Doneを**クリック**し、ネームサーバーを確認**します。

このステップには数時間かかることがあり、
成功するとメールで通知されます。

### サブドメイン

サブドメイン（例：`example.ic-domain.live` ）を設定するには、**Add new recordを**クリックして以下のレコードを追加します：

- ホストフィールドを`example` に、ターゲットフィールドを`icp1.io` に設定した`CNAME` レコードを作成します；
- ホスト・フィールドを`_acme-challenge.example` に、ターゲット・フィールドを`_acme-challenge.example.ic-domain.online.icp2.io` に設定した`CNAME` レコードを作成；
- ホスト・フィールドを`_canister-id.example` に、値フィールドをcanister ID`y5jqt-wqaaa-aaaam-abcoq-cai` に設定した`TXT` レコードを作成します。

結果として、以下のスクリーンショットのような構成になるはずです：

![DNS Configuration for ](godaddy-subdomain.png)

これで、カスタムドメインをバウンダリノードに登録する準備がすべて整いましたので、[一般的なカスタムドメインの説明の](custom-domain.md#custom-domains-on-the-boundary-nodes)ステップ2に進むことができます。

<!---
# DNS configuration guide

## Overview
This guide explains how to configure the DNS records of your domain for two popular
registrars: [Namecheap](#namecheap) and [GoDaddy](#godaddy).

## Namecheap

In the following, we outline the steps required to configure the apex of a domain and
a subdomain on Namecheap to be used as a custom domain. To illustrate the required
configuration, we use the domain `ic-domain.live` and the subdomain `example.ic-domain.live`
by configuring it to point to the canister with the ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

- #### Step 1: After purchasing your domain on Namecheap, open the management pane of your domain in the Namecheap dashboard.

- #### Step 2: Open the **Advanced DNS** tab.

### Apex
To configure the apex of the domain (e.g., `ic-domain.live`), add the following records byclicking on **Add new record**:
  * Create an `ALIAS` Record for which you set the host field to `@` and the target field to `icp1.io`;
  * Create a `CNAME` Record for which you set the host field to `_acme-challenge` and the target field to `_acme-challenge.ic-domain.live.icp2.io`;
  * Create a `TXT` Record for which you set the host field to `_canister-id` and the value field to the canister ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

  The resulting configuration should look similar to the following screenshot:

  ![DNS Configuration for `ic-domain.live` on Namecheap](namecheap-apex.png)

### Subdomain
To configure a subdomain (e.g., `example.ic-domain.live`), add the following records by clicking on **Add new record**:
  * Create a `CNAME` Record for which you set the host field to `example` and the target field to `icp1.io`;
  * Create a `CNAME` Record for which you set the host field to `_acme-challenge.example` and the target field to `_acme-challenge.example.ic-domain.live.icp2.io`;
  * Create a `TXT` Record for which you set the host field to `_canister-id.example` and the value field to the canister ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

  The resulting configuration should look similar to the following screenshot:

  ![DNS Configuration for `example.ic-domain.live` on Namecheap](namecheap-subdomain.png)

Now, you are all set to register your custom domain with the boundary nodes and you can continue with step 2 of the [general custom domains instructions](custom-domain.md#custom-domains-on-the-boundary-nodes).

## GoDaddy

In the following, we explain how you can configure your domain on GoDaddy to
be used to point to a canister hosted on the Internet Computer. As an illustration,
we configure the domain `ic-domain.online` and the subdomain `example.ic-domain.online`
to point to the canister with the ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

- #### Step 1: After purchasing your domain on GoDaddy, open your account and navigate to **My Products**.

- #### Step 2: Click on the **DNS** button next to the domain:

    ![Domain Management Overview](godaddy-overview.png)

### Apex

Unfortunately, GoDaddy does not support to configure a `CNAME` record (or one of its alternatives, `ALIAS` or `ANAME`) for the apex of the domain, we need to make use of a workaround.

  There are mainly two approaches:

  - Directly configuring the IP addresses of the boundary nodes.
  - Relying on a different DNS provider (e.g., Cloudflare).

Directly configuring the IP addresses is simpler compared to relying on another
DNS provider, but is less resilient and performant.

  ### Directly configure the IP addresses

  - #### Step 1: First, you need to look up the IP addresses of the boundary nodes (`icp1.io`).
  To this end, use an online DNS lookup service (e.g., [nslookup.io](https://nslookup.io))
  and take a note of the IPv4- and IPv6-addresses, the `A` and `AAAA` records, respectively.

    ![Resulting `A` and `AAAA` records from querying `icp1.io` on nslookup.io](nslookup-results.png)

  - #### Step 2: In the **DNS Management** pane in your GoDaddy account, add the following DNS records:

    * Create an `A` record for each IPv4-address by setting the name field
    to "@" and the value field to the IPv4-address (e.g., `193.118.63.173`);
    * Create an `AAAA` record for each IPv6-address by setting the name field
    to "@" and the value field to the IPv6-address (e.g., `2a0b:21c0:b002:2:5000:59ff:fead:c233`);
    * Create a `CNAME` record for which you set the name field to `_acme-challenge` and the value field to `_acme-challenge.ic-domain.online.icp2.io`;
    * Create a `TXT` Record for which you set the name field to `_canister-id` and the value field to the canister ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

    The resulting configuration should look similar to the following screenshot:

    ![DNS Configuration for `ic-domain.online` on GoDaddy](godaddy-apex-ips.png)

### Rely on an alternative DNS provider

We explain this approach using Cloudflare as DNS provider. It works similar
with any other DNS provider that supports `CNAME`, `ALIAS`, or `ANAME` records
for the apex of a domain.

  - #### Step 1: Create a free account with Cloudflare, click on **Add site** in the top bar of dashboard.

  - #### Step 2: Enter your domain (e.g., `ic-domain.online`) and click **Add site**.

  - #### Step 3: Choose the free plan and continue.

  - #### Step 4: Add the following records to configure your domain:
    * Create a `CNAME` record for which you set the name field to `@` and the
    target field to `icp1.io`;
    * Create a `CNAME` record for which you set the host field to `_acme-challenge` and the target field to `_acme-challenge.ic-domain.online.icp2.io`;
    * Create a `TXT` record for which you set the name field to `_canister-id` and the content field to the canister ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

  The resulting configuration should look similar to the following screenshot:

  ![DNS Configuration for `ic-domain.online` on Cloudflare](cloudflare-apex.png)

  - #### Step 6: In the next step, Cloudflare lists two nameservers that you should configure GoDaddy to use.
  Take note of the two nameservers (e.g., `brianna.ns.cloudflare.com` and `kaiser.ns.cloudflare.com`).

  - #### Step 7: In the **DNS Management** pane of GoDaddy, click on the **Change** button in the **Nameservers** section.

  - #### Step 8: In the dialog that opened, click on **Enter my own nameservers (advanced)** and fill in the nameservers from Cloudflare in the provided fields. To finish, click on **Save**.

  ![GoDaddy's dialog to configure the nameservers](godaddy-ns-dialog.png)

  - #### Step 9: Confirm in the dialog that you indeed intend to change the nameservers.
  The resulting configuration should look similar to the following screenshot:

  ![Alternative nameserver configuration on GoDaddy](godaddy-ns-configured.png)

  - #### Step 10: Back in the Cloudflare management portal, click on **Done, check nameservers**.
  This step can take several hours and you will be notified by email once it
  succeeded.


### Subdomain
To configure a subdomain (e.g., `example.ic-domain.live`), add the following records by clicking on **Add new record**:
  * Create a `CNAME` Record for which you set the host field to `example` and the target field to `icp1.io`;
  * Create a `CNAME` Record for which you set the host field to `_acme-challenge.example` and the target field to `_acme-challenge.example.ic-domain.online.icp2.io`;
  * Create a `TXT` Record for which you set the host field to `_canister-id.example` and the value field to the canister ID `y5jqt-wqaaa-aaaam-abcoq-cai`.

  The resulting configuration should look similar to the following screenshot:

  ![DNS Configuration for `example.ic-domain.online` on GoDaddy](godaddy-subdomain.png)


Now, you are all set to register your custom domain with the boundary nodes and you can continue with step 2 of the [general custom domains instructions](custom-domain.md#custom-domains-on-the-boundary-nodes).

-->
