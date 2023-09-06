<img src="https://user-images.githubusercontent.com/15371828/158857061-8fa8d079-d33f-4ed2-88aa-56d452d238d8.svg" align="right" alt="DFINITY logo" width="270">

# Internet Computer 開発者ポータル

IC開発者ポータルには、開発者が
Internet Computer を構築するために必要なすべてのリソースが集まっています。

https://internetcomputer.org

[![Netlify Status](https://api.netlify.com/api/v1/badges/0ef9e793-aa30-446a-ae7a-a18ac304db58/deploy-status)](https://app.netlify.com/sites/icportal/deploys) [![CD](https://github.com/dfinity/portal/actions/workflows/cd.yml/badge.svg)](https://github.com/dfinity/portal/actions/workflows/cd.yml)

## 貢献

IC 開発者ポータルは[Docusaurus](https://docusaurus.io/docs) を使用しています。

## 書式とスタイルのガイドライン

開発者ドキュメントを投稿するには、提供されているスタイルガイドに従わなければなりません：

[開発者ドキュメントのスタイルガイド](style-guide.md)

### ローカル開発

#### 必要条件

- [Node v16](https://nodejs.org/en/blog/release/v16.16.0) **(v18は推奨しません)**。

#### ローカルでのインストールと実行

リポジトリ内のドキュメントを変更する際、以下の
コマンドを実行することで、ローカルで変更をプレビューすることができます。

``` bash
cd portal/
git submodule update --init
npm install
npm start
```

開発者ポータルのフロントエンドがブラウザの http://localhost:3000 に表示されるはずです。

国際化貢献のコンテキストで特定のロケールをプレビューするには、次の
コマンドでドキュメントを起動してください：

``` bash
npm start -- --locale <locale>
```

### ドキュメントの追加

ドキュソーラスは非常に使いやすく、ドキュメントの作成が容易です。
開発者ポータルでは、[ドキュソーラスが提供する機能拡張により](https://docusaurus.io/docs/markdown-features)、デフォルトフォーマットとしてMarkdown
を使用しています。

ドキュメントを作成するには、`/docs` フォルダに移動し、任意のディレクトリに Markdown ファイルを作成します。ドキュメントの内容は開発者ドキュメントのスタイルガイドに従ってください：

開発[者ドキュメントのスタイルガイド](style-guide.md)

以下はdocsサブディレクトリの例です：

    developer-docs/
        developer-docs/
            home.mdx
            quickstart/
                ...
        references/
            ...

### ロードマップの変更

ロードマップの項目は
[roadmap](https://github.com/dfinity/portal/tree/master/roadmap)ディレクトリの中にマークダウンファイルとして保存されます。

項目はドメインとステータスによってグループ化されます。

    roadmap
    ├── 1_core-protocol
    │   ├── deployed
    │   │   ├── network_performance.md
    │   │   └── nodes_can_be_reassigned.md
    │   ├── in-progress
    │   │   ├── 1_btc-integration.md
    │   │   ├── 2_tecdsa.md
    │   │   ├── 3_https-outcalls.md
    │   │   ├── 4_high-replication-subnets.md
    │   │   └── 5_deterministic-time-slicing.md
    │   ├── index.md
    │   └── pending
    │       └── eth-integration.md
    ├── 2_boundary_nodes
    │   ├── deployed
    │   │   └── icos-nodes.md
    │   ├── in-progress
    │   │   ├── 1_seo_and_streaming.md
    │   │   ├── 2_asset_canister_caching.md
    ...

- アイテムのタイトル、リンク、ETAを変更するには、それぞれのマークダウンドキュメントの一番上にあるメタデータセクションを編集してください。
- アイテムのステータスを変更するには、例えば、`in-progress` から`deployed` に、ファイルを`deployed` ディレクトリに移動してください。
- アイテムはファイル名のアルファベット順に表示されます。アイテム間の順序を強制するには、
  ファイル名の前に数字を付けてください。
- アイテムをコミュニティリクエストとしてマークするには、アイテムのメタデータで`is_community: true` を設定します。

各ドメインフォルダには、タイトル、説明、カバー画像などのメタデータを追加する`index.md` ファイルが含まれています。

### 仕組み」コンテンツの変更

how it worksページカードとサブページは、
[how-it-works](https://github.com/dfinity/portal/tree/master/how-it-works)ディレクトリ内にマークダウンファイルとして保存されます。

    how-it-works
    ├── 1_about
    │   ├── 01-overview-of-the-internet-computer.card.md
    │   ├── 01-overview-of-the-internet-computer.subpage.md
    │   ├── canister-lifecycle.card.md
    │   ├── canister-lifecycle.subpage.md
    │   └── index.md
    ├── 2_featured
    │   ├── direct-integration-with-bitcoin.card.md
    │   ├── direct-integration-with-bitcoin.subpage.md
    │   ├── index.md
    │   ├── sns.card.md
    │   ├── threshold-ecdsa-signing.card.md
    │   ├── threshold-ecdsa-signing.subpage.md

このディレクトリには`.card.md` と`.subpage.md` で終わるマークダウン・ファイルがあります。

`.card.md` ファイルは、`/how-it-works page` の一部として、それらがグループ化されているセクションの下に表示されます（例:
Featured）。

`.subpage.md` ファイルは、ファイルのメタデータとコンテンツに基づいて、それぞれ`/how-it-works/` の下にサブページを生成します：

- `slug` パラメータは最終的な URL を決定します。例えば、スラッグ`canister-lifecycle` は
  ページ`/how-it-works/canister-lifecycle` を生成します。
- `title` 、`abstract` (オプション)、`coverImage` は、ソーシャルメディアで共有されたときにページがどのように見えるかを決定します。

**YouTubeの動画を埋め込む方法：**

YouTubeの動画を埋め込むには、動画のカバー画像を追加し、その画像を動画自体へのリンクにします（
例）：

    [![Watch youtube video](https://i.ytimg.com/vi/YWHTNr8RZHg/maxresdefault.jpg)](https://www.youtube.com/watch?v=YWHTNr8RZHg)

例：YouTubeのカバー画像にYouTubeのアイコンを追加することで、YouTubeの動画を埋め込むことができます。

以下の URL に動画 ID を代入すると、動画のカバー画像を取得できます：

    https://i.ytimg.com/vi/INSERT_VIDEO_ID_HERE/0.jpg

出来上がった画像が低画質な場合、`https://i.ytimg.com/vi/INSERT_VIDEO_ID_HERE/maxresdefault.jpg` 、試してみることができますが、
必ず存在するとは限りません。

### ⚠️ 投稿のマスト

- ファイルが [`.github/CODEOWNERS`](https://github.com/dfinity/portal/blob/master/.github/CODEOWNERS)ファイルが
  あなたが追加した新しいドキュメントで満たされていることを確認してください。こうすることで、将来のPull Requestが適切な
  人によってレビューされることを保証できます。
- ドキュメントを作成するときは、
  に登録する必要があります。 [`/sidebars.js`](https://github.com/dfinity/portal/blob/master/sidebars.js)に登録する必要があります。そうしないと、
  サイドナビゲーションバーに表示されません。
- すべての文書は、提供されている[Developer docs スタイルガイドに従って](style-guide.md)ください。

> ドキュメントの作成に関する詳細は、[ドキュザウルスのドキュメントを](https://docusaurus.io/docs/create-doc)参照してください。

### デプロイププレビュー

リポジトリにプルリクエストが作成されると、CIジョブが表示され、Netlifyにプレビューがデプロイされます。これにより、
のレビュアーがエンドユーザーと同じように変更を簡単に確認することができます。

プレビューにアクセスするには、プルリクエストの一番下にあるデプロイのリストにアクセスしてください。
ジョブが終了すると、"Preview Netlify" デプロイがアクティブになっているはずです。

<img width="800" alt="Screenshot 2022-03-17 at 11 45 25" src="https://user-images.githubusercontent.com/15371828/158793201-bb41f003-3d8d-4f95-9f91-8798613bc695.png">

そして、"View deployment "ボタンを押すだけで、新しいNetlifyのプレビューが表示されます。

### コントリビューションワークフロー

開発者ポータルへのコントリビューションの流れを説明します：

1.  貢献者はフォーク/ブランチを作成し、そこで変更を行います。
2.  このブランチから`master` にプルリクエスト(PR)が作成されます。プレビューが生成され、レビュアーはプレビューウェブサイト
    を直接チェックすることができます。
3.  プルリクエストが master にマージされた後、CI/CD がその内容を IC にデプロイします。変更内容は
    ウェブページの "Current" というドロップダウンのバージョンの下に表示されます。
4.  オプションです：リポジトリメンテナが master 上のコミットに $TAG というタグを付けると、CI/CD はその内容を IC にデプロイします。
    ウェブページのデフォルトは新しい "$TAG" バージョンになり、バージョンのドロップダウンに表示され、ユーザーはそのタグにピン留めされた
    の内容を見ることができます。

### ベストプラクティス

ポータルリポジトリでは、git サブモジュールを使用して複数のリポジトリにコンテンツのオーサリングを分散させています。これは、他のリポジトリの古いコンテンツを参照しているドキュメントを更新したい場合に、
困難であることがわかります。`portal` に貢献する際に役立つベストプラクティス
をいくつか紹介します。

1.  `submodules` に変更をコミットしない
    a. ステージアップした変更をチェックし、`submodules` ディレクトリに入れ子になっているファイルがないか確認してください。
    それらの変更は対応するサブモジュールリポジトリに移動する必要があります。
2.  
    a.`submodules` のコンテンツに変更を加える場合は、まず
    のリポジトリに変更をマージする必要があります。
    b. 変更がマージされたら、`portal` の参照先をそのリポジトリに更新する必要があります。そのためには
    を実行してください。`git submodule update --remote --merge`
3.  
    a. 変更をプッシュする前に`npm run build` をローカルで実行しておくと、頭痛の種から解放されます。
    ビルドがローカルで失敗すると、CI でもほぼ間違いなく失敗します。
    b. 私たちの CI では、PR をマージする前に一連のチェックが通過する必要があります。そのうちの一つは、コードが
     canister にデプロイできるかどうかをチェックするものです。canister をローカルにデプロイする必要はありませんが、少なくとも`npm run build`
     はパスする必要があります。

## コミュニティが作成したエージェントと CDK

[エージェントと](https://internetcomputer.org/docs/current/developer-docs/build/agents) [CDK の](https://internetcomputer.org/docs/current/developer-docs/build/cdks)セクションは、DFINITY- 作成されたエージェントと CDK のドキュメント
を含むだけではいけません。
そのため、私たちは他のプロジェクトに次のことを推奨します：

- それぞれのインデックスページに、自分たちのエージェントや CDK をリンクしてください (編集するファイルは`docs/developer-docs/build/agents/index.md` または`docs/developer-docs/backend/choosing-language.md` の
  です)。
- `Agents` または`CDKs` のフォルダに、独自のドキュメントを追加してください。

## コミュニティが作成した開発者ツール

`src/components/Common/toolingItems.ts` の`communityToolingItems`
配列にエントリを追加することで、開発[者](https://internetcomputer.org/tooling)ツールを
[開発者ツール](https://internetcomputer.org/tooling)ページに追加できます。

他の開発者がツールを発見しやすいように、適切なタグを追加してください。
可能であれば、新しいタグの導入は避けてください。

## コミュニティが作成したサンプルプロジェクト

[サンプル](https://internetcomputer.org/samples)ページに表示するサンプルプロジェクトを投稿することができます。

投稿を[コミュニティプロジェクトファイルに](/community/communityProjects.ts)追加し、プルリクエストを開いてください。
TypeScript をサポートしたエディタを使って、
[スキーマに従って](/src/components/Common/sampleItems.ts)いることを確認してください。

## ショーケースの投稿ガイドライン

*免責事項: ショーケースとして紹介する製品は、canister 。私たちのチームがPRを確認し、
。ご質問がある場合は、devcomms@dfinity.org までご連絡ください。*

プロジェクトを [`showcase.json`](/showcase.json).
の必須フィールドについては、以下のオブジェクトスキーマを参照してください。

ユニークなプロジェクトIDを作成してください。例えば、あなたのプロジェクトが`Awesome ICP Project!` と呼ばれる場合、プロジェクトIDは
のように`awesome-icp-project` となります。

ロゴ／ビデオ／スクリーンショットのファイルには、プロジェクトIDを先頭に付け、`/static/img/showcase`
フォルダに置いてください。例えば、プロジェクトIDが`awesome-icp-project` の場合、ロゴファイルは
に`awesome-icp-project_logo.webp` という名前を付けて、`/static/img/showcase` フォルダに置きます。

[エコシステム・ヘルパーは](https://mvw4g-yiaaa-aaaam-abnva-cai.icp0.io/)、internetcomputer.orgへのプロジェクト提出を支援するオンチェーンツールです。画像変換、サイズ変更、ウェブサイト全体で使用されるプロジェクトカードのプレビューを支援し、使用可能な有効なJSONドキュメントを作成します。フォームに記入し、アセットバンドルをzipファイルでダウンロードしてください。

### アセットガイドライン

| アセット |  | 必要条件 | フォーマット | 注意事項 |
| --- | --- | --- | --- | --- |
| ロゴ | 必須 | 112x112px | webp/svg/png | 現在表示されているサイズ 56x56px |
| スクリーンショット | 任意 | 1024x576px | webp/jpg | スキーマは複数のファイルをサポートしますが、最初のファイルのみが表示されます。 |
| ビデオ | 任意 | 最大10MB | webm/mp4 | ビデオファイルが指定された場合、スクリーンショットの代わりに表示されます。 |

### タグ

タグのリストは最終的なものではなく、プロジェクトの発展とともに更新されます。現在のところ、以下のタグが利用可能です：

- `Wallet`
- `Bitcoin`
- `NFT`
- `SocialFi`
- `DeFi`
- `Games`
- `DAO`
- `Metaverse`
- `Tools / Infrastructure`

### オブジェクトスキーマ

```
  {
    id: string,
    name: string,
    oneLiner: string, // short description of the project
    website: string, // URL starting with `https://`

    tags: ('Wallet' | 'Bitcoin' | 'NFT' | 'SocialFi' | 'DeFi' | 'Games' | 'DAO' | 'Metaverse' | 'Tools / Infrastructure')[],
    description: string, // description of the project
    stats: string, // eg. "10,000 users"
    logo: string, // url to logo file, eg. /img/showcase/awesome-icp-project_logo.webp
    
    usesInternetIdentity: boolean,
    authOrigins?: string[]; // optional additional (URL) origins that can be utilized for signing in to your dapp

    github?: string, // full URL to github repo, if available
    youtube?: string, // full URL to a YouTube video or channel, if available
    twitter?: string, // full URL to a twitter account, if available

    screenshots?: string[], // optional array of urls to screenshot files

    video?: string, // optional url to video file, eg. /img/showcase/awesome-icp-project_video.webm
    videoContentType?: 'video/webm' | 'video/mp4', // to feed into the type attribute of the video/source element

    submittableId?: string, // optional id of the submittable form
  },
```

<!---
<img src="https://user-images.githubusercontent.com/15371828/158857061-8fa8d079-d33f-4ed2-88aa-56d452d238d8.svg" align="right" alt="DFINITY logo" width="270">

# Internet Computer developer portal

The IC developer portal brings together all the resources needed for developers to build on the
Internet Computer.

https://internetcomputer.org

[![Netlify Status](https://api.netlify.com/api/v1/badges/0ef9e793-aa30-446a-ae7a-a18ac304db58/deploy-status)](https://app.netlify.com/sites/icportal/deploys) [![CD](https://github.com/dfinity/portal/actions/workflows/cd.yml/badge.svg)](https://github.com/dfinity/portal/actions/workflows/cd.yml)

## Contributing

The IC developer portal uses [Docusaurus](https://docusaurus.io/docs).

## Format and style guidelines

The developer documentation must follow the provided style guide in order to be submitted:

[Developer docs style guide](style-guide.md).

### Local development

#### Requirements

- [Node v16](https://nodejs.org/en/blog/release/v16.16.0) **(v18 not recommended)**.

#### Install and run locally

While modifying documentation in the repository, you can preview the changes locally by executing the following
commands.

```bash
cd portal/
git submodule update --init
npm install
npm start
```

The developer portal frontend should appear in your browser under http://localhost:3000.

To preview a specific locale in the context of an internationalization contribution, start the docs with the following
command:

```bash
npm start -- --locale <locale>
```

### Adding a document

Docusaurus is quite easy to use and facilitates the creation of documents.
The developer portal uses Markdown as its default format
with [enhancements provided by Docusuaurus](https://docusaurus.io/docs/markdown-features).

To create a document, head to the `/docs` folder and create a Markdown file in the directory of your choice. Please assure that the document's contents follow the developer docs style guide:

[Developer docs style guide](style-guide.md).

Here is an example of a docs sub-directory:

```
developer-docs/
    developer-docs/
        home.mdx
        quickstart/
            ...
    references/
        ...
```

### Changing the roadmap

The roadmap items are stored as markdown files inside
the [roadmap](https://github.com/dfinity/portal/tree/master/roadmap) directory.

The items are grouped by domain and status.

```
roadmap
├── 1_core-protocol
│   ├── deployed
│   │   ├── network_performance.md
│   │   └── nodes_can_be_reassigned.md
│   ├── in-progress
│   │   ├── 1_btc-integration.md
│   │   ├── 2_tecdsa.md
│   │   ├── 3_https-outcalls.md
│   │   ├── 4_high-replication-subnets.md
│   │   └── 5_deterministic-time-slicing.md
│   ├── index.md
│   └── pending
│       └── eth-integration.md
├── 2_boundary_nodes
│   ├── deployed
│   │   └── icos-nodes.md
│   ├── in-progress
│   │   ├── 1_seo_and_streaming.md
│   │   ├── 2_asset_canister_caching.md
...
```

- To change the title, links, or ETA of an item, edit the metadata section at the top of each markdown document.
- To change the status of an item, eg. from `in-progress` to `deployed`, move the file to the `deployed` directory.
- Items are listed in alphabetical order of their filename, to enforce a certain ordering between items you can prefix
  the filenames with numbers.
- To mark an item as a community request, set `is_community: true` in the item's metadata.

Each domain folder contains an `index.md` file which adds metadata, like a title, a description, and cover images.

### Changing the 'how it works' content

The how it works page cards and subpages are stored as markdown files inside
the [how-it-works](https://github.com/dfinity/portal/tree/master/how-it-works) directory.

```
how-it-works
├── 1_about
│   ├── 01-overview-of-the-internet-computer.card.md
│   ├── 01-overview-of-the-internet-computer.subpage.md
│   ├── canister-lifecycle.card.md
│   ├── canister-lifecycle.subpage.md
│   └── index.md
├── 2_featured
│   ├── direct-integration-with-bitcoin.card.md
│   ├── direct-integration-with-bitcoin.subpage.md
│   ├── index.md
│   ├── sns.card.md
│   ├── threshold-ecdsa-signing.card.md
│   ├── threshold-ecdsa-signing.subpage.md
```

The directory contains markdown files ending in `.card.md` and `.subpage.md`.

The `.card.md` files will show up as part of the `/how-it-works page`, under the section they are grouped in (eg.
Featured).

The `.subpage.md` files will each generate a subpage under `/how-it-works/`, based on the files' metadata and content:

- The `slug` parameter will determine the final URL, eg. the slug `canister-lifecycle` will generate the
  page `/how-it-works/canister-lifecycle`.
- The `title`, `abstract` (optional), and `coverImage` will determine how the page looks when shared on social media

**How to embed YouTube videos:**

You can embed YouTube videos by adding the video's cover image, then turning the image into a link to the video itself,
eg:

```
[![Watch youtube video](https://i.ytimg.com/vi/YWHTNr8RZHg/maxresdefault.jpg)](https://www.youtube.com/watch?v=YWHTNr8RZHg)
```

The page will automatically put the YouTube icon on the cover image, imitating an embedded video.

You can get the cover image of a video by substituting the video ID into the following URL:

```
https://i.ytimg.com/vi/INSERT_VIDEO_ID_HERE/0.jpg
```

If the resulting image is low quality, you can try `https://i.ytimg.com/vi/INSERT_VIDEO_ID_HERE/maxresdefault.jpg`, but
it does not always exist.

### ⚠️ Contributions' musts:

- Making sure that the [`.github/CODEOWNERS`](https://github.com/dfinity/portal/blob/master/.github/CODEOWNERS) file is
  filled with new documents that you added. This way we can ensure that future Pull Requests are reviewed by the right
  people.
- When creating a document, it must be registered
  in [`/sidebars.js`](https://github.com/dfinity/portal/blob/master/sidebars.js), otherwise, it will not appear in the
  side navigation bar.
- All documents must follow the provided [Developer docs style guide](style-guide.md).

> More information about document creation on [Docusaurus's docs](https://docusaurus.io/docs/create-doc).

### Deployed Previews

Whenever a Pull Request is created on the repository, a CI job will appear and deploy a Preview on Netlify so that
reviewers can easily check the changes made the way the end users will.

To access the preview, head to the very bottom of your pull request where you will see the list of deployments.
Once the job is finished, you should see as active the "Preview Netlify" deployment.

<img width="800" alt="Screenshot 2022-03-17 at 11 45 25" src="https://user-images.githubusercontent.com/15371828/158793201-bb41f003-3d8d-4f95-9f91-8798613bc695.png">

Then simply press the "View deployment" button to in your fresh Netlify preview.

### Contribution workflow

Here is a description of how a contribution should be made to the developer portal:

1. The contributor creates a fork/branch where the changes are made.
2. A pull request (PR) is created from this branch to `master`. the preview is generated and the reviewers can directly check
   the preview website.
3. After the pull request is merged into master, CI/CD will deploy the contents to the IC. The changes made will appear
   on the webpage under the version in the dropdown named "Current".
4. Optional: When a repo maintainer tags a commit on master as $TAG, then CI/CD deploys the contents to the IC. The
   webpage will default to the new "$TAG" version, visible in the versions dropdown, and allow users to view the
   contents pinned at that tag.

### Best practices

The portal repo uses git submodules to distribute content authoring across several repositories. This can prove
challenging if you want to make updates to docs that refer to stale content in other repos. Here are some best practices
that may prove helpful when contributing to `portal`.

1. Don't commit changes to `submodules`.
   a. Check your staged changes and see if any files are nested within the `submodules` directory. If so - revert them.
   Those changes must go into the corresponding submodule repository.
2. Be wary of the order of operations when it comes to submodules.
   a. If you do intend to make changes to content under `submodules`, the changes need to be merged into the respective
   repo first.
   b. Once the change has been merged, you will also need to update `portal`'s ref to that repo. You can do that by
   running `git submodule update --remote --merge`
3. Build locally.
   a. You can save yourself some headache if you run `npm run build` locally before you push your changes. If the build
   fails locally, it will almost certainly fail in CI.
   b. Our CI requires that a set of checks pass before a PR can be merged. One of those checks whether the code can be
   deployed to a canister. You do not need to deploy a canister locally, but at the very least `npm run build` should
   pass.

## Community-created agents and CDKs

The [agents](https://internetcomputer.org/docs/current/developer-docs/build/agents)
and [CDKs](https://internetcomputer.org/docs/current/developer-docs/build/cdks) sections should not only contain docs
for DFINITY-created agents and CDKs.
We therefore invite other projects to:

- Link to their own agents or CDKs on the respective index pages (the files to be edited are
  in `docs/developer-docs/build/agents/index.md` or `docs/developer-docs/backend/choosing-language.md`).
- Add their own documentation as a folder under `Agents` or `CDKs`.

## Community-created developer tools

We invite developers to add their IC-focused developer tools to
the [developer tools page](https://internetcomputer.org/tooling) by appending an entry to the `communityToolingItems`
array in `src/components/Common/toolingItems.ts`.

Please make sure to add appropriate tags to make the tool easy to discover for other devs. Avoid introducing new tags if
possible.

## Community-created sample projects

You can submit your sample project to be displayed on the [samples page](https://internetcomputer.org/samples).

Add your submission to [the community projects file](/community/communityProjects.ts) and open a pull request. You can
use an editor with TypeScript support to make sure your submission
follows [the schema](/src/components/Common/sampleItems.ts).

## Showcase submission guidelines

_Disclaimer: You should have a working canister for your product to be showcased. Our team will review the PR and get
back to you for any further questions. In the meantime, please contact devcomms@dfinity.org if you have any questions._

Add your project to the end of [`showcase.json`](/showcase.json). Refer to the object schema below for the required
fields.

Make up a unique project id. For example, if your project is called `Awesome ICP Project!`, your project id could
be `awesome-icp-project`.

Your logo/video/screenshots files should be prefixed with your project id, and placed in the `/static/img/showcase`
folder. For example, if your project id is `awesome-icp-project`, your logo file should be
named `awesome-icp-project_logo.webp` and placed in the `/static/img/showcase` folder.

The [Ecosystem Helper](https://mvw4g-yiaaa-aaaam-abnva-cai.icp0.io/) is an on-chain tool that helps you submit your project to internetcomputer.org. It helps with image conversion, resizing, previewing the project cards used throughout the website, and it produces a valid JSON document you can use. Fill out the form and download the asset bundle in a zip file.

### Asset guidelines

| Asset       |          | Requirements | Format       | Notes                                                                             |
|-------------|----------|--------------|--------------|-----------------------------------------------------------------------------------|
| logo        | required | 112x112px    | webp/svg/png | Currently displayed 56x56px                                                       |
| screenshots | optional | 1024x576px   | webp/jpg     | The schema supports multiple files, but only the first one will be displayed      |
| video       | optional | max 10MB     | webm/mp4     | If there is a video file specified, it will be displayed instead of a screenshot. |

### Tags

The list of tags is not final, and will be updated as the project evolves. For now, the following tags are available:

- `Wallet`
- `Bitcoin`
- `NFT`
- `SocialFi`
- `DeFi`
- `Games`
- `DAO`
- `Metaverse`
- `Tools / Infrastructure`

### Object schema

```
  {
    id: string,
    name: string,
    oneLiner: string, // short description of the project
    website: string, // URL starting with `https://`

    tags: ('Wallet' | 'Bitcoin' | 'NFT' | 'SocialFi' | 'DeFi' | 'Games' | 'DAO' | 'Metaverse' | 'Tools / Infrastructure')[],
    description: string, // description of the project
    stats: string, // eg. "10,000 users"
    logo: string, // url to logo file, eg. /img/showcase/awesome-icp-project_logo.webp
    
    usesInternetIdentity: boolean,
    authOrigins?: string[]; // optional additional (URL) origins that can be utilized for signing in to your dapp

    github?: string, // full URL to github repo, if available
    youtube?: string, // full URL to a YouTube video or channel, if available
    twitter?: string, // full URL to a twitter account, if available

    screenshots?: string[], // optional array of urls to screenshot files

    video?: string, // optional url to video file, eg. /img/showcase/awesome-icp-project_video.webm
    videoContentType?: 'video/webm' | 'video/mp4', // to feed into the type attribute of the video/source element

    submittableId?: string, // optional id of the submittable form
  },
```

-->
