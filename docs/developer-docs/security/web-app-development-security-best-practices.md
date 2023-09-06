---

sidebar_position: 3
sidebar_label: Web app development
---
# ウェブアプリ開発セキュリティのベストプラクティス

## 概要

このドキュメントには、Web アプリを開発する際のセキュリティのベストプラクティスに関する情報が含まれています。

## 認証

### よく監査された認証サービスとクライアント側の IC ライブラリを使用してください。

#### セキュリティ上の懸念

ウェブアプリにユーザー認証とcanister 呼び出しを実装することは、エラーが発生しやすく、危険です。例えば、canister 呼び出しをスクラッチから実装する場合、署名の作成や検証に関するバグがあるかもしれません。

#### 推奨事項

- 認証には[Internet Identity](https://github.com/dfinity/internet-identity)などを使用し、canister 呼び出しには[agent-js を](https://github.com/dfinity/agent-js)使用し、dApp から Internet Identity とやり取りするには[auth-client](https://github.com/dfinity/agent-js/tree/main/packages/auth-client)を使用します。

- もちろん、認証のためにIC上の別の認証フレームワークを検討することもオプションです。

### 適切なセッションタイムアウトの設定

#### セキュリティに関する懸念

現在、インターネット ID は、有効期限付きの委任を発行します。この有効期限は、[auth-clientで](https://github.com/dfinity/agent-js/tree/main/packages/auth-client)設定できます。委任の有効期限が切れると、ユーザーは再認証する必要があります。適切な値を設定することは、セキュリ ティとユーザビリティのトレードオフです。

#### 推奨

[OWASP の勧告を](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html#session-expiration)参照してください。セキュリティに敏感なアプリケーションでは、30分のタイムアウトを設定すべきです。

[auth-clientは](https://github.com/dfinity/agent-js/tree/main/packages/auth-client)アイドルタイムアウトをサポートしています。詳細は[こちらを](https://github.com/dfinity/agent-js/tree/main/packages/auth-client#idle-management)ご覧ください。

### 本番環境で agent-js の fetchRootKey を使用しないでください。

#### セキュリティ上の懸念

`agent.fetchRootKey()` は、テスト環境のステータスコールからルートサブネットの閾値公開鍵を取得するために[agent-js](https://github.com/dfinity/agent-js)で使用できます。この鍵は、 アップデートコールを通じて受信した認証データの閾値署名を検証するために使用されます。本番環境のウェブアプリでこの方法を使用すると、攻撃者は自分の公開鍵を提供することができ、更新応答のすべての真正性保証を無効にすることができます。canister 

#### 推奨事項

本番ビルドでは`agent.fetchRootKey()` を絶対に使用しないでください。このメソッドを呼び出さない場合、ハードコードされたメインネットのルートサブネット公開鍵が署名検証に使用されます。

## に固有ではありません。Internet Computer

このセクションのベストプラクティスは非常に一般的なもので、Internet Computer に特化したものではありません。このリストは決して完全なものではなく、過去に問題になった非常に具体的な懸念事項をいくつか挙げているだけです。

### フロントエンドでの入力の検証

#### セキュリティ上の懸念

信頼できない情報源(例えばユーザ)からのデータの入力妥当性確認が欠けていると、不正な形式のデータが永続化され、ユーザに送り返される可能性があります。これはDoS、インジェクション攻撃、フィッシングなどにつながる可能性があります。

#### 推奨事項

- canister でのデータ検証に加えて、フロントエンドですでにデータ検証を実施してください。 データ検証はできるだけ早い段階で行うべきです。

- [OWASP input validation cheat sheetを](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html#goals-of-input-validation)参照してください。

### 信頼できないドメインからJavaScript（と他のアセット）をロードしないでください。

#### セキュリティ上の懸念

`<canister-id>.icp0.io` 以外のドメインから信頼されていないJavaScriptをロードすることは、そのドメインを完全に信頼することを意味します。また、これらのドメインから読み込まれたアセット(`<canister-id>.raw.icp0.io` を含む)はアセット認証を使いません。

悪意のあるJavaScriptを配信した場合、例えばLocalStorageからagent-jsが管理する秘密鍵を読み取ることで、ウェブアプリ/アカウントを乗っ取ることができます。

信頼できないドメインから CSS のような他のアセットを読み込むことは、セキュリティリスクであることに注意してください[。](https://xsleaks.dev/docs/attacks/css-injection/)

#### 推奨

- 他のオリジンからの JavaScript と他のアセットのロードは避けるべきです。特に、セキュリティ上重要なアプリケーションでは、他のドメインが信頼できると仮定することはできません。

- ブラウザに配信されるすべてのコンテンツが、canister によって提供され、アセット認証を使って認証されていることを確認してください。これは特に、JavaScript、フォント、CSS などに当てはまります。

- [コンテンツセキュリティポリシーを](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)使用して、他のオリジンからのスクリプトやその他のコンテンツがまったく読み込まれないようにしてください。[コンテンツセキュリティポリシー（CSP）を含むセキュリティヘッダの定義](#define-security-headers-including-a-content-security-policy-csp)も参照してください。

### コンテンツセキュリティポリシー(CSP)を含むセキュリティヘッダの定義

#### セキュリティに関する懸念

セキュリティヘッダは、以下のような多くのセキュリティ上の懸念に対応するために使用できます：

- [クリックジャッキングの](https://owasp.org/www-community/attacks/Clickjacking)禁止。
- [TLSの強化](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)。
- [信頼できないドメインからのJavaScriptやその他のコンテンツがブラウザで実行できない](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)ようにする、など。

これらのヘッダを適切に設定しないと、具体的なセキュリティ上の問題を引き起こし、深層防御を欠くことになります。

#### 推奨事項

- <https://securityheaders.com/> を使用してサイトをチェックしてください。

- [HSTS](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)、[CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)、[X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options)、[referrer-policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)、permissions-policy[（ジェネレータ](https://www.permissionspolicy.com/)）を使用してください。

- これらのヘッダ（CSPを含む）は、[Internet Identityなどで](https://github.com/dfinity/internet-identity)うまく統合されています。

- CSPを[CSPエバリュエータで](https://csp-evaluator.withgoogle.com/)チェックしてください。

- [OWASPのセキュアヘッダプロジェクトも](https://owasp.org/www-project-secure-headers/)参照してください。

### 暗号: Web Crypto API を使って XSS から鍵を保護します。

#### セキュリティ上の懸念

ブラウザのストレージ([sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)や[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) など)に鍵を保存することは、これらの鍵が JavaScript コードによってアクセスされる可能性があるため、安全でないと考えられています。これは XSS 攻撃や、他のドメインから信頼できないスクリプトを読み込むときに起こる可能性があります ([信頼できないドメインから JavaScript を読み込ま](#dont-load-javascript-and-other-assets-from-untrusted-domains)ない も参照してください)。

#### 推奨

[Web Crypto API を](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API)使って、JavaScript から鍵の情報を隠します。`generateKey` で`extractable=false` [を使って](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/generateKey)ください。この例は暗号化されたノートの例にあります。これにより、JavaScript から秘密鍵にアクセスすることが不可能になります。

### 安全なウェブフレームワークの使用

#### セキュリティ上の懸念

最近のウェブフレームワークは、ウェブページにレンダリングされる潜在的なユーザ提供データを安全にエスケープ/サニタイズするので、XSS のような攻撃を非常に難しくしています。このようなフレームワークを使わないのは、XSS のような攻撃を避けるのが難しいので危険です。

#### 推奨

- XSSを避けるために、[Svelteの](https://svelte.dev/)ような安全なテンプレート機構を持つウェブフレームワークを使用してください。これは例えば[NNSdapp](https://github.com/dfinity/nns-dapp)プロジェクトで使われています。

- [Svelteの@htmlなど](https://svelte.dev/docs#template-syntax-html)、フレームワークの安全でない機能は使わないでください。

### ログアウトが有効であることを確認してください。

#### セキュリティ上の懸念

ユーザーによるログアウト操作が効果的でない場合、例えば共有デバイスやパブリックデバイスが使用されている場合、アカウントの乗っ取りにつながる可能性があります。

#### 推奨事項

- ログアウト時にすべてのセッションデータ（特に[sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)と[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)）をクリアし、[IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) などをクリアします。

- 一つのタブでログアウトがトリガーされた場合、同じオリジンを表示する他のブラウザタブもログアウトされることを確認してください。これは、agent-js が初期化されると秘密鍵をメモリに保持するため、agent-js が使用されている場合は自動的に発生しません。

### セキュリティ上重要な操作についてユーザに警告するプロンプトを使用し、ユーザに明示的に確認させます。

#### セキュリティ上の懸念

このままでは、ユーザが誤って機密性の高い操作を実行する可能性があります。

#### 推奨

- そのアクションの正確な結果を記述したセキュリティ警告をプロンプトに表示し、ユーザに明示的に確認させてください。

- 高いセキュリティ要求があるアプリケーションについては、トランザクション承認の使用を考慮してください。これは、例えばトークンやcycle の移動が含まれる場合に推奨されます。例えば、[NNSdapp](https://github.com/dfinity/nns-dapp)でハードウェア・ウォレットを使用することで、これを実現できます。

<!---

# Web app development security best practices

## Overview
This document contains information regarding security best practices for developing web apps.

## Authentication

### Use a well-audited authentication service and client side IC libraries

#### Security concern

Implementing user authentication and canister calls yourself in your web app is error prone and risky. For example, if canister calls are implemented from scratch, there may be bugs around signature creation or verification.

#### Recommendation

-   Consider using e.g. [Internet Identity](https://github.com/dfinity/internet-identity) for authentication, use [agent-js](https://github.com/dfinity/agent-js) for making canister calls, and the [auth-client](https://github.com/dfinity/agent-js/tree/main/packages/auth-client) for interacting with internet identity from your dApp.

-   It is of course also an option to consider alternative authentication frameworks on the IC for authentication.

### Set an appropriate session timeout

#### Security concern

Currently, Internet Identity issues delegations with an expiry time. This expiry time can be set in the [auth-client](https://github.com/dfinity/agent-js/tree/main/packages/auth-client). After a delegation expires, the user has to re-authenticate. Setting a good value is a trade-off between security and usability.

#### Recommendation

See the [OWASP recommendations](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html#session-expiration). A timeout of 30 min should be set for security sensitive applications.

The [auth-client](https://github.com/dfinity/agent-js/tree/main/packages/auth-client) supports idle timeouts. More details available [here](https://github.com/dfinity/agent-js/tree/main/packages/auth-client#idle-management).

### Don’t use fetchRootKey in agent-js in production

#### Security concern

`agent.fetchRootKey()` can be used in [agent-js](https://github.com/dfinity/agent-js) to fetch the root subnet threshold public key from a status call in test environments. This key is used to verify threshold signatures on certified data received through canister update calls. Using this method in a production web app gives an attacker the option to supply their own public key, invalidating all authenticity guarantees of update responses.

#### Recommendation

Never use `agent.fetchRootKey()` in production builds, only in test builds. Not calling this method will result in the hard coded root subnet public key of the mainnet being used for signature verification, which is the desired behavior in production.

## Nonspecific to the Internet Computer

The best practices in this section are very general and not specific to the Internet Computer. This list is by no means complete and only lists a few very specific concerns that have led to issues in the past.

### Validate input in the frontend

#### Security concern

Missing input validation of data from untrusted sources (e.g. users) can lead to malformed data being persisted and delivered back to users. This may lead to DoS, injection attacks, phishing, etc.

#### Recommendation

-   Perform data validation already in the front end, in addition to data validation in the canister. Data validation should happen as early as possible.

-   See the [OWASP input validation cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html#goals-of-input-validation).

### Don’t load JavaScript (and other assets) from untrusted domains

#### Security concern

Loading untrusted JavaScript from domains other than `<canister-id>.icp0.io` means you completely trust that domain. Also, assets loaded from these domains (incl. `<canister-id>.raw.icp0.io`) will not use asset certification.

If they deliver malicious JavaScript they can take over the web app/account by e.g. reading the private key managed by agent-js from LocalStorage.

Note that also loading other assets such as CSS from untrusted domains is a security risk, see e.g. [here](https://xsleaks.dev/docs/attacks/css-injection/).

#### Recommendation

-   Loading JavaScript and other assets from other origins should be avoided. Especially for security critical applications, we can’t assume other domains to be trustworthy.

-   Make sure all the content delivered to the browser is served and certified by the canister using asset certification. This holds in particular for any JavaScript, but also for fonts, CSS, etc.

-   Use a [content security policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to prevent scripts and other content from other origins to be loaded at all. See also [define security headers including a content security policy (CSP)](#define-security-headers-including-a-content-security-policy-csp).

### Define security headers including a content security policy (CSP)

#### Security concern

Security headers can be used to cover many security concerns, such as:
- Disallow [clickjacking](https://owasp.org/www-community/attacks/Clickjacking).
- [Harden TLS](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html).
- Make sure [JavaScript or other content from untrusted domains cannot be executed in the browser](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/script-src), etc. 

Not configuring these headers appropriately can lead to concrete security issues and missing defense-in-depth.

#### Recommendation

-   Check your site using <https://securityheaders.com/>.

-   Use [HSTS](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html), [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP), [X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options), [referrer-policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy), permissions-policy ([generator](https://www.permissionspolicy.com/)).

-   These headers (including CSP) have been successfully integrated e.g. in [Internet Identity](https://github.com/dfinity/internet-identity).

-   Check your CSP against [CSP evaluator](https://csp-evaluator.withgoogle.com/).

-   See also the [OWASP secure headers project](https://owasp.org/www-project-secure-headers/).

### Crypto: protect key material against XSS using Web Crypto API

#### Security concern

Storing key material in browser storage (such as [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) or [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)) is considered unsafe because these keys can be accessed by JavaScript code. This could happen through an XSS attack or when loading untrusted scripts from other domains (see also [don’t load JavaScript from untrusted domains](#dont-load-javascript-and-other-assets-from-untrusted-domains)).

#### Recommendation

Use [Web Crypto API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) to hide key material from JavaScript, by using `extractable=false` in `generateKey` , see [this](https://developer.mozilla.org/en-US/docs/Web/API/SubtleCrypto/generateKey). An example for this can be found in the encrypted notes example, see [here](https://github.com/dfinity/examples/blob/master/motoko/encrypted-notes-dapp/src/frontend/src/lib/crypto.ts#L149-L159). This makes it impossible to access the private key from JavaScript.

### Use a secure web framework

#### Security concern

Modern web frameworks make attacks such as XSS very difficult since they safely escape / sanitize any potentially user-provided data that is rendered on a web page. Not using such a framework is risky as it is hard to avoid attacks like XSS.

#### Recommendation

-   Use a web framework that has a secure templating mechanism such as [Svelte](https://svelte.dev/) to avoid XSS. This is used e.g. in the [NNS dapp](https://github.com/dfinity/nns-dapp) project.

-   Don’t use insecure features of the framework, such as e.g. [@html in Svelte](https://svelte.dev/docs#template-syntax-html).

### Make sure the logout is effective

#### Security concern

If a logout action by a user is not effective, this may lead to account takeover e.g. if a shared or public device is used.

#### Recommendation

-   Clear all session data (especially [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) and [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)), clear [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API), etc. on logout.

-   Make sure also other browser tabs showing the same origin are logged out if the logout is triggered in one tab. This does not happen automatically when agent-js is used, since agent-js keeps the private key in memory once initialized.

### Use prompts to warn the user on any security critical actions, let the user explicitly confirm

#### Security concern

If this is not the case, a user may by accident execute some sensitive actions.

#### Recommendation

-   Show the user a prompt with a security warning describing the exact consequences of the action and let them confirm explicitly.

-   For applications with high security requirements, consider the use of transaction approval, i.e. using e.g. a WebAuthn device to let the user confirm certain critical actions or transactions. This is recommended e.g. when token or cycle transfers is involved. For example, using a hardware wallet in the [NNS dapp](https://github.com/dfinity/nns-dapp) achieves this.

-->
