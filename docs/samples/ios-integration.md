# IOS統合

## 概要

[IOS 統合](https://github.com/dfinity/examples/tree/master/motoko/ios-notifications)は、Internet Computer でホストされているdapp を複数のプラットフォームと統合するための可能なソリューションを紹介する、ネイティブアプリ統合の実験的なdapp です。この例では、iOSアプリを作成しました。

純粋に IC 上で実行され、[Internet Identity](/docs/current/references/ii-spec)を使用しているdapp と、認証やプッシュ通知の受信など、ネイティブの感覚をユーザーに提供できる iOS ネイティブアプリとの簡単な統合の例を作成することを目的としました。

## アーキテクチャ

IOS 統合の基本機能は、4 つの主要コンポーネントで構成されています：

- まず、[Internet Identity](/docs/current/references/ii-spec)と統合され、基本的なルーティング機能を持つdapp を作成しました。ユーザーが認証されていない間は、ログインページのみが表示され、認証されるとアバウトページとホームページ間をナビゲートできます。

- 次に、dapp のラッパーとして機能する新しい IOS ネイティブアプリケーショ ンを作成し、ユーザーにネイティブな操作感を提供しました。

- 第三に、dapp にプロキシ・ページを追加し、[Internet Identity](/docs/current/references/ii-spec)を使用してユーザーを安全に認証できるようにし、認証されたセッションを有効期限が切れるまでウェブビューに保持します。

- 第 4 に、dapp は、システムからのプッシュ通知を受信し、指定した URL を開くように構成されています。これにより、dapp の特定のセクションにディープリンクするメカニズムとして、通知を送信することができます。

## 前提条件

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。
- https://github.com/dfinity/examples/ \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。
- \[x\][Node.jsを](https://nodejs.org/en/download/)インストールします。
- \[x\][xcodeを](https://apps.apple.com/us/app/xcode/id497799835)インストールします。

### ステップ1：ローカル開発。

はじめに、`dfx` のローカル開発環境を以下の手順で起動します：

``` bash
cd examples/motoko/ios-notifications/dapp-demo
dfx start --background --clean
```

### ステップ 2: 依存パッケージをインストールします：

    npm install

### ステップ 3:canisters をデプロイします：

    dfx deploy

### ステップ 4: フロントエンドを開始します：

    npm start

これで、dapp にアクセスできるようになります。`http://localhost:4943/?canisterId={YOUR_LOCAL_CANISTER_ID}`.

> `YOUR_LOCAL_CANISTER_ID` は、 コマンドの出力で利用できるようになります。`dfx deploy` 

### ステップ5: Internet Identityの使用。

このdapp を[インターネットIDと](https://internetcomputer.org/docs/current/developer-docs/integrations/internet-identity/integrate-identity)統合することで、認証が可能になります。IOS統合をサポートするために、ブラウザIndexedDBで利用可能な`delegation` および`key` を使用します。

IOS認証の手順は以下のとおり：

1.  ユーザーが認証するためにクリックします(これにより、`window.open` が呼び出されます)。
2.  dapp がリクエストをインターセプトし、新しい[ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) を開きます。
    - 確認ダイアログが表示され、dapp がインターネット ID ドメインを使用して認証することをユーザーに通知します。
3.  認証が完了すると、[ユニバーサルリンクを](https://developer.apple.com/documentation/xcode/supporting-universal-links-in-your-app)持つデバイス内でのみ発生するローカル コールバックが作成されます。
4.  dapp はこのコールバックを受信し、`delegation` と`key` をローカルの[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) に注入します。
5.  ウェブビューはリロードされ、ユーザーは認証されます。認証はIndexedDBを使用するため、ユーザーがdapp （セッションの有効期限は保持され、最大で30日間です）を閉じた後も動作し続けます。

#### これがどのように処理されるかの例です：

``` ts
async handleMultiPlatformLogin(): Promise<void> {
    const key = await this.storage.get(KEY_STORAGE_KEY) ?? undefined;
    const delegation = await this.storage.get(KEY_STORAGE_DELEGATION) ?? undefined;
    const identityParam: IdentityParam = { key, delegation };
    const preloadParam = Buffer.from(JSON.stringify(identityParam), "ascii").toString("base64");
    const url = Auth.currentURL();

    switch(this.loginType()) {
        case AuthLoginType.Ios:
            const iosCallback = new URL(url.searchParams.get(AuthLoginType.Ios) ?? "");
            iosCallback.searchParams.append(Auth.identityPreloadProp, preloadParam);
            // the redirect here triggers the app universal link
            // such as dappexample://auth?_identity=...
            // and this is what the app intercepts and handles
            window.location.href = iosCallback.toString();
            break;
        default:
            // desktop is enabled by default and doesn't need a special condition
            break;
    }
}
```

### ステップ6：通知

IOSアプリは、リモートAPNサーバーからの通知を受信できるように準備されています。この例の範囲では、独自の通知サーバーをセットアップしていません。代わりに、`send-notification.sh` スクリプトを使用して、独自のアップル開発者キーで通知をトリガーすることができます。

以下は、IOS通知を表示する手順です：

1.  アプリが起動したら、UNUserNotificationCenterを使ってユーザーにプッシュ通知の許可を要求します。
2.  権限が付与されると、リモート通知の登録要求が行われます。
3.  リモート呼び出しでデバイスIDが利用可能になります。
    - 開発目的のために、この値を xcode コンソールに出力します。
4.  正しい`env` 変数で`send-notification.sh` スクリプトを実行すると、通知がデバイスに表示されます。
    - シミュレータはリモートで登録できないので、このステップには物理的なIOSデバイスが必要です。
5.  通知をクリックすると、dapp 、aboutページが開きます。

## セキュリティに関する考慮事項

- Internet Identity と統合する場合は、必ずユニバーサルリンクをセットアップして使用してください。カスタムアプリのスキームなど、委任を渡す他の形式には、他人があなたのdapp になりすますことができるなど、セキュリティ上のリスクがあることが知られています。
- この例では、ネイティブアプリでキーペアを生成していますが、リクエストの署名はdapp で行われるため、秘密鍵はまだそこで利用可能です。本番アプリケーションでは、この鍵がネイティブアプリで生成され、[安全なエンクレーブに](https://developer.apple.com/documentation/security/certificate_key_and_trust_services/keys/protecting_keys_with_the_secure_enclave)保存されていることを確認し、webkit を介してdapp に`sign` メソッドを公開します。これにより、dapp が秘密鍵にアクセスできないようにします。

## 参考文献

詳細については、[README ファイルを](https://github.com/dfinity/examples/blob/master/motoko/ios-notifications/README.md)参照してください。

<!---
# IOS integration

## Overview
[IOS integration](https://github.com/dfinity/examples/tree/master/motoko/ios-notifications) is an experimental dapp with a native app integration that showcases a possible solution for integrating a dapp hosted in the Internet Computer with multiple platforms. For this example we've created an iOS app.

We aimed to create an example of a simple integration of a dapp running purely on the IC and is using [Internet Identity](/docs/current/references/ii-spec) with a native iOS application that can let the user have a native feel such as authenticating and receive push notifications.

## Architecture 

The basic functionality of the IOS integration consists of four main components:

- First, we created a dapp that is integrated with [Internet Identity](/docs/current/references/ii-spec) and has a basic routing functionality. While the user is not authenticated it can only see the login page and when authenticated can navigate between the about and home page.

- Second, we created a new IOS native application that serves as a wrapper for the dapp and creates a native feel for the user.

- Third, a proxy page was added in the dapp to enable the user to securely authenticate using [Internet Identity](/docs/current/references/ii-spec) and keep the authenticated session in the webview until it expires, even when the user exits the app and re-opens it the session persists.

- Fourth, the dapp is configured to receive push notifications from the system and open a specified URL, this allows for notifications to be sent serving as a mechanism to deep link into a specific section of the dapp. 

## Prerequisites
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/
- [x] Install [Node.js](https://nodejs.org/en/download/).
- [x] [xcode](https://apps.apple.com/us/app/xcode/id497799835).

### Step 1: Local development.

To get started, start a local `dfx` development environment with the following steps:

```bash
cd examples/motoko/ios-notifications/dapp-demo
dfx start --background --clean
```

### Step 2: Install the dependency packages:

```
npm install
```

### Step 3: Deploy your canisters:

```
dfx deploy
```

### Step 4: Start the front-end:

```
npm start
```

You can now access the dapp at `http://localhost:4943/?canisterId={YOUR_LOCAL_CANISTER_ID}`.

> `YOUR_LOCAL_CANISTER_ID` will be made available to you in the output of the `dfx deploy` command.

### Step 5: Using Internet Identity.

The integration of this dapp with the [Internet Identity](https://internetcomputer.org/docs/current/developer-docs/integrations/internet-identity/integrate-identity) enables authentication. To support the IOS integration,  it uses the `delegation` and `key` made available in the browser IndexedDB. 

The steps for IOS authentication are:

1. User clicks to authenticate (this triggers the `window.open` to be called).
2. The dapp intercepts the request and opens a new [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession).
    - This show's a confirmation dialog, informing the user that the dapp would like to authenticate using the Internet Identity domain.
3. After authentication happens, a local callback that only happens inside the device with the [universal link](https://developer.apple.com/documentation/xcode/supporting-universal-links-in-your-app) is made.
4. The dapp receives this callback and injects the `delegation` and `key` into the local [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview).
5. The webview reloads and the user is now authenticated, since authentication uses IndexedDB, it continues to work after the user closes the dapp (expiration time of the session is kept, max is 30 days).

#### An example of how this can be handled:

```ts
async handleMultiPlatformLogin(): Promise<void> {
    const key = await this.storage.get(KEY_STORAGE_KEY) ?? undefined;
    const delegation = await this.storage.get(KEY_STORAGE_DELEGATION) ?? undefined;
    const identityParam: IdentityParam = { key, delegation };
    const preloadParam = Buffer.from(JSON.stringify(identityParam), "ascii").toString("base64");
    const url = Auth.currentURL();

    switch(this.loginType()) {
        case AuthLoginType.Ios:
            const iosCallback = new URL(url.searchParams.get(AuthLoginType.Ios) ?? "");
            iosCallback.searchParams.append(Auth.identityPreloadProp, preloadParam);
            // the redirect here triggers the app universal link
            // such as dappexample://auth?_identity=...
            // and this is what the app intercepts and handles
            window.location.href = iosCallback.toString();
            break;
        default:
            // desktop is enabled by default and doesn't need a special condition
            break;
    }
}
```

### Step 6: Notifications

The IOS app is prepared to receive notifications from remote APN servers. For the scope of this example we haven't setup our own notification server. Instead, you can use the `send-notification.sh` script to trigger the notification with your own apple developer keys.

These are the steps to show an IOS notification:

1. When the app starts we use UNUserNotificationCenter to request the user for push notification permissions.
2. With granted permissions a request to register for remote notifications is made.
3. A device ID is made available with the remote call.
    - For development purposes we print this value to the xcode console.
4. Execute the `send-notification.sh` script with the correct `env` variables and the notification will appear in your device.
    - A physical IOS device is required for this step since the simulator can't register remotely.
5. By clicking the notification the dapp will open in the about page.

## Security considerations

- When integrating with Internet Identity make sure to setup and use universal links, other forms of passing the delegation such as a custom app scheme is known to have security risks such as allowing others to impersonate your dapp.
-  For this example we are generating the keypair in the native app but the private key is still available there since the signing of the request happens on the dapp, in your production application make sure this is generated by your native app and stored in a [secure enclave](https://developer.apple.com/documentation/security/certificate_key_and_trust_services/keys/protecting_keys_with_the_secure_enclave), and expose a `sign` method to the dapp through webkit, this will ensure that your dapp does not have access to your private key.

## References

For further details, please refer to the [README file](https://github.com/dfinity/examples/blob/master/motoko/ios-notifications/README.md).

-->
