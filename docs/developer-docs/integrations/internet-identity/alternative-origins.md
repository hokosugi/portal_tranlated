# 代替フロントエンドの起源

## 概要

アプリケーションがドメイン名を変更する段階に達し、Internet Identity (II)で認証している場合、ユーザがすでに使用しているプリンシパルをシームレスに維持できるようにしたいと思うでしょう。この機能をサポートするために、このガイドを使用して、代替のフロントエンドオリジン用にアプリケーションを設定できます。

![End Result](../_attachments/alternative-origins.png)

以下のいずれかを行う場合、このガイドが必要になるでしょう：

- `<canister-id>.icp0.io` からカスタムドメインへの移行。
- `/` ではなく`/login` でログインするようユーザに要求する場合。
- `raw.icp0.io` を使用しているユーザーをサポートする場合。
- 組織内の複数のアプリが同じプリンシパルを使用するように構成する場合。

## 制約

現在、最大**10 の**代替オリジンを記載できます。

これらの代替オリジンを構成するオリジンが、**認証済みアセットを**使用してcanister でホストされている場合にのみ、II はこの仕様に従います。

詳細については、[Internet Identity 仕様を](https://github.com/dfinity/internet-identity/blob/main/docs/ii-spec.md#alternative-frontend-origins)参照してください。

## 代替オリジンの設定

この例では、Aと**Bの**2つのドメインを使用します。**Aは**正規オリジン、**Bは**代替ドメインです。このモデルを説明するために、https://internetcomputer.org と https://hwvjt-wqaaa-aaaam-qadra-cai.icp0.io の両方でホストされているWebサイトを考えてみます。

この例では、**Aは** canister ID、つまりhttps://hwvjt-wqaaa-aaaam-qadra-cai.icp0.io。

**Bは**代替オリジン、つまりhttps://internetcomputer.org。

### オリジンのリスト

オリジン**Aに対して**、**Bが**有効なオリジンであることをInternet Identityに伝えるファイルを提供する必要があります。設定ファイルは、フロントエンドcanister の`src/assets` ディレクトリに配置します。フロントエンドcanister が`dist` フォルダからアセットをデプロイするように設定されている場合、canister の`sources` を更新し、両方を含めるようにしてください：

``` json
"source": [
    "dist",
    "src/assets"
]
```

`src/assets` の中に`.well-known` フォルダを作成し、次の名前のファイルを追加します。`ii-alternative-origins.`

:::info
ファイル名は`ii-alternative-origins` で、拡張子はありません。中のコンテンツはJSONとしてフォーマットされますが、ファイルは`.json`.
:: で終わってはいけません：

ファイルの中に、**Bの**代替オリジンをリストアップします：

``` json
{
  "alternativeOrigins": ["https://internetcomputer.org"]
}
```

:::info
オリジンから末尾のスラッシュやクエリパラメータを削除してください
::：

これで、プロジェクトは次のようになります：

    ├── dfx.json
    ├── src
    │   ├── assets
    │   │   ├── .well-known
    │   │   │   └── ii-alternative-origins

### フロントエンドの設定canister

通常、`.well-known` のドット構文はファイルシステムによって "hidden" として扱われるため、canister フロントエンドを設定してドキュメントをアップロードする必要があります。フロントエンドcanister を設定するには、`.ic-assets.json` という新しいファイルを作成します。`.ic-assets.json` は、`sources` にリストされているcanister 用のディレクトリの中に置く必要があるので、`src/assets` を再び使用できるようにします。新しいファイルのリストはこのようになるはずです：

    ├── dfx.json
    ├── package.json
    ├── src
    │   ├── project_frontend
    │   │   ├── src
    │   │   │   ├── .ic-assets.json
    │   │   │   ├── .well-known
    │   │   │   │   └── ii-alternative-origins

次に、`.well-known` ディレクトリをインクルードするように設定します：

``` json
[
  {
    "match": ".well-known",
    "ignore": false
  },
  {
    "match": ".well-known/ii-alternative-origins",
    "headers": {
      "Access-Control-Allow-Origin": "*",
      "Content-Type": "application/json"
    },
    "ignore": false
  }
]
```

これには、`.well-known` ディレクトリを無視しない一般的なルールと、`ii-alternative-origins` をアクセス制御と content-type ヘッダ付きで配信するルールが含まれます。

あとは、canister をデプロイするだけです。以降、オリジン**B**から認証を行おうとすると、**A を**使用しているときと同じプリンシパルが返されます。

<!---
# Alternative frontend origins

## Overview
If your application has reached the stage where you want to change domain names, and you have been authenticating with Internet Identity (II), you will want to make sure that your users can seamlessly keep the same principals they have already been using. To support this functionality, you can configure your application for alternative frontend origins using this guide.

![End Result](../_attachments/alternative-origins.png)

You may need this guide if you are doing any of the following:

- Moving from `<canister-id>.icp0.io` to a custom domain.
- Asking users to login at `/login` instead of `/`.
- Supporting users using `raw.icp0.io`.
- Configuring multiple apps in your organization to use the same principals.

## Constraints

Currently, a maximum of **10** alternative origins can be listed.

II will only follow this specification when the origin configuring these alternatives is hosted on a canister using **certified assets**.

For more information, see the [Internet Identity specification](https://github.com/dfinity/internet-identity/blob/main/docs/ii-spec.md#alternative-frontend-origins).

## Configuring alternative origins

For this example, we will have two domains, **A** and **B**. **A** will be the canonical origin, and **B** will be the alternative domain. To help illustrate this model, consider this website, which is hosted both at https://internetcomputer.org and https://hwvjt-wqaaa-aaaam-qadra-cai.icp0.io.

In this example, **A** would be the canister ID, or https://hwvjt-wqaaa-aaaam-qadra-cai.icp0.io.

**B** would be the alternative origin, or https://internetcomputer.org.

### Listing origins

For origin **A**, you will need to provide a file that tells Internet Identity that **B** is a valid origin. We'll be placing the config files in `src/assets` directory of your frontend canister. If your frontend canister is currently configured to deploy assets from a `dist` folder, make sure to update the `sources` for your canister to include both:

```json
"source": [
    "dist",
    "src/assets"
]
```

Inside of `src/assets`, create a `.well-known` folder, and add a file named `ii-alternative-origins.`

:::info
The file needs to exactly be named `ii-alternative-origins`, with no file extension. The content inside will be formatted as JSON, but the file should not end with `.json`.
:::

Inside of the file, list your alternative origin for **B**. It will look something like this:

```json
{
  "alternativeOrigins": ["https://internetcomputer.org"]
}
```

:::info
Make sure that you remove any trailing slash or query parameters from the origin
:::

Now, your project should look something like this:

```
├── dfx.json
├── src
│   ├── assets
│   │   ├── .well-known
│   │   │   └── ii-alternative-origins
```

### Configuring your frontend canister

Because the dot syntax in `.well-known` ordinarily will be treated as "hidden" by the file system, the frontend canister will need to be configured to upload your document. To configure the frontend canister, create a new file, `.ic-assets.json`. `.ic-assets.json` needs to be placed inside a directory listed in `sources` for your canister, so we can use `src/assets` again. Your new list of files should look like this:

```
├── dfx.json
├── package.json
├── src
│   ├── project_frontend
│   │   ├── src
│   │   │   ├── .ic-assets.json
│   │   │   ├── .well-known
│   │   │   │   └── ii-alternative-origins
```

Then, configure the `.well-known` directory to be included, with:

```json
[
  {
    "match": ".well-known",
    "ignore": false
  },
  {
    "match": ".well-known/ii-alternative-origins",
    "headers": {
      "Access-Control-Allow-Origin": "*",
      "Content-Type": "application/json"
    },
    "ignore": false
  }
]
```

This includes a general rule to not ignore the `.well-known` directory, and rules to deliver the `ii-alternative-origins` with access control and content-type headers.

Then, all you need to do is deploy your canister. When you attempt to authenticate from origin **B** from then on, you will get back the same principal you get while using **A**.

-->
