# Internet Computer 開発者ドキュメント：スタイル、フォーマット、言語ガイド

## 概要

このガイドでは、Internet Computer の開発者向けドキュメントにページを投稿する際に使用すべき書式、言語、スタイルについて概説します。これは、複数の個人やチームによって寄稿されたドキュメントのすべてのページにおいて、 まとまりのある、統一されたルックアンドフィールを作成するために、 ドキュメントの規格化を支援するためのものです。

このガイドでは、以下のことを説明します：

- ページの構成
- ページ見出しの使用
- 大文字と小文字の使い分け
- 文書の言語、スペル、文法、単語の選択
- 句読点の使用
- 箇条書きリスト。
- 太字テキスト。
- イタリック体。
- ヒント。
- リンクとハイパーリンク
- コードスニペットとコードブロック
- コマンドライン構文
- FAQセクション
- チュートリアルとガイドの違い。
- チュートリアルの書き方。
- ガイドの書き方

## ページ構成

開発者向けドキュメントには、チュートリアル、ガイド、 情報ページ、リファレンスページなど、さまざまな種類のドキュメントが含まれています。このため、ページ構造はそのページがどのような種類の文書であるかによって異なります。

情報ページとガイドについては、以下のページ構成に従ってください：

    # Page title
    
    ## Overview
    
    Text
    
    ## Prerequisites (Optional; only necessary for guides that require prerequisite conditions or parameters be met.)
    
    - [x] Prerequisite 1.
    - [x] Prerequisite 2.
    - [x] Prerequisite 3.
    
    ## Topic 1
    
    Text
    
    ### Subtopic 1
    
    Text
    
    ### Subtopic 2
    
    - Bullet 1.
    - Bullet 2.
    - Bullet 3.
    
    ## Topic 2
    
    Text
    
    ### Subtopic 3
    
    Text
    
    - #### Step 1: Description of step for reader to take.
    - #### Step 2: Description of step for reader to take.
    - #### Step 3: Description of step for reader to take.
    
    ## Conclusion
    
    Text
    
    ## Resources
    - [Link](link.com)

チュートリアルでは、以下のページ構成に従ってください：

    # Page title
    
    ## Overview
    
    Text
    
    ## Prerequisites 
    
    - [x] Prerequisite 1.
    - [x] Prerequisite 2.
    - [x] Prerequisite 3.
    
    ## Step 1: Description of step
    
    Text
    
    ## Step 2: Description of step
    
    Text
    
    ## Step 3: Description of step
    
    Text
    
    ## Conclusion
    
    Text
    
    ## Resources
    - [Link](link.example)

## ページの見出し

IC developer docsでは4種類の見出しが使われています：

- 見出しのサイズ 1：`#`
- 見出しのサイズ 2：`##`
- 見出しのサイズ 3`###`
- 見出しのサイズ 4：`####`

### 見出しの使い方

| 見出しサイズ | 使い方 |
| --- | --- |
| 見出し1 | ページタイトルのみ、ページコンテンツ内では使用されません。 |
| 見出し2 | 概要」、「前提条件」、「結論」、「参考文献」などの一貫したページ見出しに使用されます。 |
| 見出し3 | ページ内の主要トピックの下にあるサブトピック、またはチュートリアル内のユーザーステップに使用されます。ステップに複数の異なるコンセプトやサブトピックが含まれている場合など、チュートリアルのステップに見出し 2 を使用した方が適切な場合があります。詳しくは下記のチュートリアルの書き方を参照してください。 |
| 見出し 4 | このシナリオでは、この見出しは箇条書きと組み合わせて使用します。見出し4は、`glossary` ドキュメントページ内の用語のリストや、すでにある見出し3のサブトピック見出しの下のサブトピックにも使われます。 |

### 見出しの使用例

以下は、4 種類の見出しをどのように使用すべきかを示すガイド文書のサンプルです：

    # File management
    
    ## Overview
    
    This document describes basic file management workflows.
    
    ### Workflows covered
    
    The following workflows are covered:
    - Creating a file.
    - Saving a file. 
    
    ## Prerequisites
    
    - [x] Download and install a text editor program. 
    
    ## Creating a file
    
    To create a new file:
    
    - #### Step 1: Open the text editor program.
    - #### Step 2: From the navigation bar, select **File** then **New File**.
    - #### Step 3: Enter text into the new, blank file. 
    
    ## Saving a file
    
    To save a file:
    
    - #### Step 1: From the navigation bar, select **File** then **Save**.
    - #### Step 2: Enter a file name to save your file under.
    - #### Step 3: Confirm the file name and select **Save**.
    
    ## Conclusion
    This document described basic file management workflows.
    
    ## Resources
    - [Link](link.example)

## 前提条件セクション

ガイドまたはチュートリアルを始める前に、読者が完了しなければならないタスクを含む前提条件セクションを含むガイドまたはチュートリアルでは、次の形式を使用します：

## 前提条件

- \[x\] プログラムをダウンロードしてインストールしてください。
- \[x\] パッケージをダウンロードしてインストールしてください。
- \[x\] フレームワークをダウンロードしてインストールしてください。

## 資本表記

開発者向けドキュメント内の大文字小文字の表記は、以下の規格に従ってください：

- タイトルに固有名詞が含まれていない限り、タイトルの最初の単語だけを大文字にします。
  - 例の使い方Internet Computer
  - 例を使ったインターネット・アイデンティティの使い方dapp
  - 例：Motoko リファレンスガイド
- タイトルに固有名詞が含まれている場合を除き、ページ見出しの最初の単語のみを大文字にします。
  - 例概要
  - 例の登録、ビルド、デプロイdapp
  - 例Motoko の使用canisters
- 他のドキュメントページやブログ記事などの外部記事へのリンクのタイトルは、タイトル内の固有名詞を除いて大文字にしないでください。
  - 例[ Internet Computer の](link) [使い方に関する](link)ブログ記事をご覧ください。
  - 例[ dapp の](link) [インターネット ID の使用に関する](link)ガイドを参照してください。
- 参照されるインターフェイス内で大文字で表記される特定のGUIボタンやその他の視覚的補助への言及は、GUIに表示される形式に合わせて大文字で表記する必要があります。
  - 例アカウントを開き、「**My Products**」に移動します。
  - 例**Save**\] ボタンをクリックします。
  - 例**Add site**\]を選択して、設定を保存します。
- 箇条書きリストが文から始まる場合、文の最初の単語は大文字にする必要があります：
  - 例ステップ 1：まず、境界ノードの IP アドレスを調べる必要があります。
- 箇条書きリストが用語の定義に使用されている場合、用語の後の最初の単語は大文字にしないでください：
  - 例**用語**：用語の定義。
- メソッド、変数、整数、データ型の名前など、コード固有の値を参照する場合は、コード内または言語のリファレンス・ドキュメントで使用されている大文字表記に従います。
  - 例ConstructionPayloadRequest
  - 例： ingress\_start

### 固有名詞

以下の固有名詞は、常に大文字にします：

- ブロックチェーン特異点
- Candid
- DFINITY 財団
- Internet Computer
- Internet Computer Protocol
- インターネット・アイデンティティ
- JavaScript
- Motoko
- Network Nervous System
- ノードJS
- README
- さび
- サービス神経系
- タイプスクリプト
- WebAssembly (Wasm)
- ウィキ
- ワールドコンピュータ
- YouTube

ツール名、会社名、プロジェクト名などの固有名詞は大文字で表記します。Unity、Godot、Namecheap、GoDaddy、GitHubなどがその例です。

### 略語と頭字語

以下は、開発者向けドキュメント内で大文字で表記される一般的な略語のリストです：

- BTC
- CDK
- ckBTC
- DAO
- DeFi
- ECDSA
- HTTP/HTTPS
- IC
- ICP
- ICRC-1
- II
- IOS
- NFT
- NNS
- SDK
- SNS
- XDR

## 使用言語

IC開発者向けドキュメントでは、以下の言語および用語の大文字小文字を使用します：

- ビッグテック
- ビットコイン統合
- ビットコインネットワーク：「ビットコインを送る」の代わりに使用すべき。
- スマートコントラクトInternet Computer
- canister
- canister スマートコントラクト
- dappIC アプリ、分散型アプリケーション、または 'dApp' への言及の代わりに使用します。
- DeFi
- イーサリアムとの統合
- イーサリアム：ETHまたはETHトークンの代わりに使用する必要があります。
- HTTPアウトコール
- IC SDK: ICのSDKへのあらゆる参照の代わりに使用されるべきです。
- メインネット
  - 文脈上：`to the mainnet` または`on the mainnet` のデプロイ。`the` という単語の使用に注意。
  - その他の文脈上の用法：
    - `The Internet Computer mainnet network.`
    - `The dapp has been deployed to the IC mainnet.`
    - `Before deploying on to the Internet Computer mainnet...`
      さらに、Bitcoin mainnet についての言及は、同じ構造（単語の前に`the` を付ける）を使用する必要があります。
- 成熟度
- Motoko 遊び場
- neuron
- ノードプロバイダ
- オープンインターネットサービス
- リバースガスモデル
- ウェブ3
- ワールド・ワイド・ウェブ

### スペル、文法、単語の選択

以下のスペル、文法、単語選択の規則に従ってください：

- スペルや文法に関しては、すべての記事のタイトルがイギリスの大文字小文字のルールに従うという例外を除いて、アメリカのスペルや文法を使用すべきです（詳細は大文字小文字のセクションをご覧ください）。

- 英語を母国語としない人たちにも読みやすく、"Dig in "のような紛らわしい言い回しや表現は避けましょう。

- 開発者向けドキュメントでは、インフォーマルで個人的な考えや不必要な表現は避けてください。このような例はいくつかあります：
  
  - デフォルト設定を「退屈なデフォルト設定」と呼ぶこと。
  
  - ドキュメント内でジョークを言い、そのジョークを認めるために「(Get it? heh)」のような括弧を使うこと。
  
  - インタラクティブなオンボーディングを目的としたチュートリアルでない限り、読者に直接話しかけることは避けてください。
  
  例えば、次のような表現は避けてください：
  
  - 「わかった？
  - "深く潜ってください。"
  - "ウサギの穴に落ちろ"
  - 「掘り下げてください。
  
  ただし、文脈によっては以下のような表現も許容されます：
  
  - "Ready to get started?さあ、始めましょう！"
  - "以下は、検討する必要のある事柄のチェックリストです。"

## 数字

数字は他の文字ではなく、`'` 。例えば

- 44'760'000
- 54'000
- 1'000

## APIメソッド

`GET` 、`POST` 、`PUT` 、`DELETE` 、`HEAD` などのAPIメソッドリクエストの場合は、そのように書式設定します：

- 見出しでは、追加の書式設定なしで大文字にします。
  - 例「HTTP GETコールを使用"
  - 例例："HTTP PUT呼び出しの使用"
- 文書本文では、インラインコードとして次のように書式設定します：
  - 例例: "`GET` HTTPSリクエストを行うための最小限の例"
  - 例"つまり、canister はクォーラムサイズを1と定義し、`POST` リクエストを実行するレプリカを1つだけ持つことができます。"

## 句読点

以下の句読点の規格に従うこと：

- すべての文章はピリオドで終わります。
- 感嘆符は適切であれば使ってもかまいませんが、多用すべきではありません。
- クエスチョンマークは必要に応じて使用してください。
- セミコロン(;)は、文中の休止を示すために使用します。
- 括弧は、適切な場合、追加の文脈を含めるために使用します。
- すべての箇条書きリストは、序文とコロンで始めます。
- 箇条書きリスト内の項目は、その項目が全文であるかどうかにかかわらず、すべてピリオドで終わるようにしてください。
- すべての番号付きリストは、各リスト項目の番号付けに`1.` の書式を使用してください。
- ガイドやチュートリアルのすべてのユーザーステップでは、各ステップの内容の前に`Step 1:` という書式を使用してください。

## 箇条書きリスト

以下の箇条書きの書式と規格に従ってください：

- 箇条書きリストは、`-` 文字または`*` 文字のいずれかを使用して記述できます。
- 用語や技術を定義する箇条書きリストは、以下の形式に従ってください：
  - **用語**：用語の定義。
- ガイドのユーザーステップを示す箇条書きリストでは、次の形式を使用してください：
  - #### ステップ1：ステップの説明
- サブトピックまたはサブポイントを含む箇条書きリストでは、次の形式を使用します：
  - 箇条書き 1.
    - サブトピックの箇条書き
  - 箇条書き 2.

## 番号付きリスト

以下の番号付きリストの書式と規格に従うべきです：

- 番号付きリストは、ツールやサービスのバックエンドやフロントエンドで起こることを説明するアーキテクチャステップの概要を示すために使用されるべきです。
  - 例えば、サービスがクライアントアプリケーションとどのように相互作用するかを詳述するステップは、番号付きリストを使って説明されるべきです。一方、ユーザーがクライアントアプリケーションとどのように相互作用できるかを詳述するステップは、正しい見出しのサイズとフォーマットを使用するガイドまたはチュートリアルステップとしてリストされるべきです。
- 番号付きリストは、読者に質問リストを提示し、読者の考えを喚起する場合にも使用します。例えば
  1.  どのWebAssembly (Wasm) コードがcanister に対して実行されていますか？
  2.  canisters は通常、Motoko や Rust などの高水準言語で書かれており、Wasm で直接書かれているわけではありません。2つ目の疑問は、実行されているWasmは本当にソースコードをコンパイルした結果なのか、ということです。

## 太字テキスト

太字テキストは以下の規格に従ってください：

- 用語のリストと定義に使われる箇条書きリストは、定義される用語を太字にしてください。
  - **用語**：用語の定義。
- IC用語集にある用語など、読者が理解することが重要な用語は、太字にして強調する必要があります。
- ガイドまたはチュートリアルでユーザーが従うべき手順を記述する場合、ユーザーがボタンまたはGUIインターフェースと対話することが予想される場合は、ボタンの名前を太字にし、GUIに見られる大文字の書式に従います。
  - 例**保存**ボタンをクリックしてください。

## 斜体テキスト

IC開発者向けドキュメントでは、イタリック体は使用しません。太字テキストは、重要な用語やフレーズを強調するために使用してください。

## ヒント

IC開発者向けドキュメントでは、ドキュソーラスフレームワークが提供する2種類のヒントを使用します：

- **情報**：情報ヒントは、読者に読むように促す重要な情報を提供するために使用されます。このヒントは、追加の文脈やリソースを提供したり、読み手が先に進む前に知っておくべき重要な注意事項を追加したりするために使用できます。
- **注意**: 注意ヒントは、ガイド、ドキュメント、またはチュートリアルで示されている特定のステップまたはアクションの結果について、読者に警告を提供するために使用されます。

ヒントは以下のマークダウン形式で使用できます：

:::info
情報ヒント
::：

:::caution
注意のヒント
::：

## リンク

開発者ドキュメント内で参照されるリンクは、以下のフォーマットと構造を使用してください：

- リンクのタイトルの大文字小文字は、前の大文字小文字のセクションで説明したのと同じ形式とルールに従ってください。例えば
  - [効果的なラストに関するRomanのブログ記事を読むcanisters](link.example).
  - [信頼を示す](link.example)
- インラインリンクでは、リンク先のページやセクションのタイトルを大文字にすべきではありません。例えば
  - 以下のセクションは[信頼の](#demonstrating-trust)証です。
  - [Wikiには](https://wiki.internetcomputer.org/wiki/Dealing_with_cycles_limit_exceeded_errors)、cycles の制限を回避する方法が[いくつか掲載](https://wiki.internetcomputer.org/wiki/Dealing_with_cycles_limit_exceeded_errors)されています。

## コード・スニペットとコード・ブロック

コード・スニペットとコード・ブロックには、以下のフォーマットを使用してください：

- コマンド、言語固有のメソッド、その他のコード関連用語へのインライン・コード参照は、次のようなインライン・コード表現を使用して強調する必要があります：
  - canister にコードをインストールするには、Internet Computer の`install_code` 関数を使用します。
  - 例えば、`canister_init` 関数は、コードが初めてインストールされた後に呼び出される最初の関数です。
- メソッド名、変数名、整数名、データ型名など、コード固有の値を参照する場合は、コード内または言語 のリファレンス・ドキュメント内で使用されている大文字表記に従います。
  - 例`ConstructionPayloadRequest`
  - 例`ingress_start`
- ユーザーが実行するコマンドラインコマンドは、コード書式を使用して、次のように独自の行に記載します：
  - #### ステップ1: 次のコマンドを実行して、Reactモジュールをインストールします：
  <!-- end list -->
  ```
            npm install --save react react-dom
  ```
- コードブロックは、該当する場合、コードブロック内で使用される言語を先頭に記述します。例えば

<!-- end list -->

``` bash
cd motoko/threshold_ecdsa
dfx start --background
npm install
dfx deploy
```

## コマンドラインの構文

`dfx` のすべての CLI コマンドの例では、フラグは常にコマンドの最後に記述します：

    dfx wallet balance --network ic

追加例：

    dfx deploy --background

    dfx identity new Alice --disable-encryption

    dfx deploy internet_identity --argument '(null)'

## FAQセクション

よくある質問(FAQ)セクションは、以下の構成と書式を使うべきです：

    ## Frequently asked questions
    
    - #### Question 1?
    Answer 
    
    - #### Question 2?
    Answer

## チュートリアルとガイドの違い

チュートリアルは、ユーザーがAからZまでたどることができる独立したプロジェクトであるべきで、サンプルコードから構築することができ、さまざまな異なる概念、ツール、または資産を使用することができ、最終的には動作するサンプルプロジェクトになります。

対照的に、ガイドは、個々のコンセプトやワークフローに重点を置き、それらを基に構築されるべきです。例えば、Motoko を使ったスマートコントラクトのバックエンドを構築するためのガイドでは、計算機関数での整数の使用や、actor を使ったクエリなど、Motoko 内の個々の概念に焦点を当て、それをベースにしています。

### チュートリアルの作成

チュートリアルを投稿する際には、以下の一般的な形式と構造を使用する必要があります。チュートリアルの内容は様々であり、より詳細なワークフローや内容を含めるために、この形式を拡張し、詳しく説明することができることに注意することが重要です。

    # Page title
    
    ## Overview
    
    Text
    
    ## Prerequisites 
    
    - [x] Prerequisite 1.
    - [x] Prerequisite 2.
    - [x] Prerequisite 3.
    
    ## Step 1: Description of step
    
    Text
    
    ### Subtopic 1
    
    Text
    
    ## Step 2: Description of step
    
    Text
    
    ### Subtopic 2
    
    Text
    
    ## Step 3: Description of step
    
    Text
    
    ## Conclusion
    
    Text
    
    ## Resources
    - [Link](link.example)

### ガイドの作成

ガイドを投稿する場合、以下の一般的なフォーマットと構造を使用する必要があります。ガイドの内容はさまざまであり、より詳細なワークフローやコンテンツを含めるために、このフォーマットを拡張し、詳しく説明することができることに注意することが重要です。

    # Page title
    
    ## Overview
    
    Text
    
    ## Prerequisites (Optional; only necessary for guides that require prerequisite conditions or parameters be met.)
    
    - [x] Prerequisite 1.
    - [x] Prerequisite 2.
    - [x] Prerequisite 3.
    
    ## Topic 1
    
    Text
    
    ### Subtopic 1
    
    Text
    
    - #### Step 1: Description of step for reader to take.
    - #### Step 2: Description of step for reader to take.
    - #### Step 3: Description of step for reader to take.
    
    ### Subtopic 2
    
    Text
    
    - #### Step 1: Description of step for reader to take.
    - #### Step 2: Description of step for reader to take.
    - #### Step 3: Description of step for reader to take.
    
    ## Topic 2
    
    Text
    
    ### Subtopic 3
    
    Text
    
    - #### Step 1: Description of step for reader to take.
    - #### Step 2: Description of step for reader to take.
    - #### Step 3: Description of step for reader to take.
    
    ## Conclusion
    Text
    
    ## Resources
    - [Link](link.example)

<!---
# Internet Computer developer documentation: style, format, and language guide

## Overview 

This guide outlines the format, language, and style that should be used when contributing pages to the Internet Computer developer documentation. This is designed to assist with standardizing the documentation to create a cohesive, uniform look and feel across all pages of documentation that have been contributed by multiple individuals and teams. 

This guide will outline the following:
- Page structure.
- Use of page headings.
- Use of capitalization.
- Documentation language, spelling, grammar, and word choice.
- Use of punctuation.
- Bulleted lists.
- Bold text.
- Italic text.
- Hints.
- Links and hyperlinks.
- Code snippets and code blocks.
- Command line syntax.
- FAQ sections.
- The difference between a tutorial and a guide.
- How to write a tutorial.
- How to write a guide.

## Page structure

The developers docs contain a wide variety of different document types, such as tutorials, guides, informational pages, and reference pages. For this reason, the page structure will vary based on what type of document the page is. 

The following page structure should be followed for informational pages and guides:

```
# Page title

## Overview

Text

## Prerequisites (Optional; only necessary for guides that require prerequisite conditions or parameters be met.)

- [x] Prerequisite 1.
- [x] Prerequisite 2.
- [x] Prerequisite 3.

## Topic 1

Text

### Subtopic 1

Text

### Subtopic 2

- Bullet 1.
- Bullet 2.
- Bullet 3.

## Topic 2

Text

### Subtopic 3

Text

- #### Step 1: Description of step for reader to take.
- #### Step 2: Description of step for reader to take.
- #### Step 3: Description of step for reader to take.

## Conclusion

Text

## Resources
- [Link](link.com)
```

The following page structure should be followed for tutorials:

```
# Page title

## Overview

Text

## Prerequisites 

- [x] Prerequisite 1.
- [x] Prerequisite 2.
- [x] Prerequisite 3.

## Step 1: Description of step

Text

## Step 2: Description of step

Text

## Step 3: Description of step

Text

## Conclusion

Text

## Resources
- [Link](link.example)
```

## Page headings

There are 4 types of headings used throughout the IC developer docs:

- Heading size 1: `#`
- Heading size 2: `##`
- Heading size 3: `###`
- Heading size 4: `####`

### Heading usage

| Heading size | Usage |
|--------------|-------|
| Heading 1    | Page title only; not used within the page's content.|
| Heading 2    | Used for primary topics amongst the page; used for consistent page headings such as 'Overview', 'Prerequisites', 'Conclusion' and 'References'.|
| Heading 3    | Used for subtopics under primary topics amongst the page or user steps within a tutorial. Note; there are some instances where using Heading 2 for a tutorial step is more fitting, such as when a step contains several different concepts or subtopics. Please refer to the writing a tutorial section below for further information.|
| Heading 4    | Used for listing steps a user should take within a guide document, where the guide document includes several different, separate walkthroughs for the user to follow; in this scenario, this heading should be used in conjunction with a bullet point. Heading 4 is also used for listing terms within the `glossary` document page and for subtopics under an already existing Heading 3 subtopic heading. |

### Heading usage example

The following is a sample guide document that showcases how the 4 types of headings should be used:

```
# File management

## Overview

This document describes basic file management workflows.

### Workflows covered

The following workflows are covered:
- Creating a file.
- Saving a file. 

## Prerequisites

- [x] Download and install a text editor program. 

## Creating a file

To create a new file:

- #### Step 1: Open the text editor program.
- #### Step 2: From the navigation bar, select **File** then **New File**.
- #### Step 3: Enter text into the new, blank file. 

## Saving a file

To save a file:

- #### Step 1: From the navigation bar, select **File** then **Save**.
- #### Step 2: Enter a file name to save your file under.
- #### Step 3: Confirm the file name and select **Save**.

## Conclusion
This document described basic file management workflows.

## Resources
- [Link](link.example)
```

## Prerequisites sections
For guides or tutorials that include a prerequisites section that contains tasks a reader must complete before starting to follow the guide or tutorial, the following format should be used:

## Prerequisites

- [x] Download and install a program. 
- [x] Download and install a package.
- [x] Download and install a framework.

## Capitalization

Capitalization within the developer documentation should follow these outlined standards:

- Only the first word of a title should be capitalized unless the title includes a proper noun.
    - Example: How to use the Internet Computer
    - Example: Using Internet Identity with a dapp
    - Example: Motoko reference guide
- Only the first word of a page heading should be capitalized unless the title includes a proper noun.
    - Example: Overview
    - Example: Register, build, and deploy the dapp
    - Example: Using Motoko canisters
- Titles of links to other documentation pages or external articles, such as blog posts, should not be capitalized except for the proper nouns within the title. 
    - Example: Check out the blog post on [how to use the Internet Computer](link).
    - Example: Reference the guide on [using the Internet Identity in a dapp](link).
- Any reference to specific GUI buttons or other visual aids that are capitalized within the referenced interface should be capitalized to match the format shown on the GUI.
    - Example: Open your account and navigate to **My Products**.
    - Example: Click on the **Save** button.
    - Example: Select **Add site** to save the configuration. 
- When a bullet point list begins with a sentence, the first word in the sentence should be capitalized:
    - Example: Step 1: First, you need to look up the IP addresses of the boundary nodes.
- When a bullet point list is used to define terms, the first word after the term should not be capitalized:
    - Example: **Term**: definition of the term.
- When a code-specific value, such as the name of a method, variable, integer, or data type, is being referenced, the capitalization used within the code or the language's reference documentation should be followed.
    - Example: ConstructionPayloadRequest
    - Example: ingress_start

### Proper nouns

The following proper nouns should always be capitalized:
- Blockchain Singularity
- Candid
- DFINITY Foundation
- Internet Computer
- Internet Computer Protocol
- Internet Identity
- JavaScript
- Motoko
- Network Nervous System
- NodeJS
- README
- Rust
- Service Nervous System
- TypeScript
- WebAssembly (Wasm)
- Wiki
- World Computer
- YouTube

This is not an exhaustive list, and other proper nouns such as tool names, company names, or project names should be capitalized. Examples of these are Unity, Godot, Namecheap, GoDaddy, and GitHub. 

### Abbreviations and acronyms  
The following is a list of common abbreviations that are capitalized within the developer docs:
- BTC
- CDK
- ckBTC
- DAO
- DeFi
- ECDSA
- HTTP/HTTPS
- IC
- ICP
- ICRC-1
- II
- IOS
- NFT
- NNS
- SDK
- SNS
- XDR

## Language
The following language and capitalization of certain terms and phrases should be used across the IC developer documentation:
- Big Tech
- Bitcoin integration
- Bitcoin network: should be used in place of "sending bitcoin". 
- Built on the Internet Computer
- canister
- canister smart contract
- dapp: should be used in place of any reference to an IC app, decentralized application, or 'dApp'. 
- DeFi
- Ethereum integration
- Ethereum: should be used in place of ETH or ETH token. 
- HTTP outcalls
- IC SDK: should be used in place of any reference to the IC's SDK.
- mainnet
    - In context: Deploying `to the mainnet` or `on the mainnet`. Note the use of the word `the`.
    - Other contextual usage:
        - `The Internet Computer mainnet network.`
        - `The dapp has been deployed to the IC mainnet.`
        - `Before deploying on to the Internet Computer mainnet...`
    Additionally, mentions of the Bitcoin mainnet should use the same structure (prefaced with the word `the`.)
- maturity
- Motoko playground
- neuron
- node provider
- open internet service
- reverse gas model
- Web3
- World Wide Web

### Spelling, grammar, and word choice
The following spelling, grammar, and word choice rules should be followed:
- Regarding spelling and grammar, American spelling and grammar should be used with the exception that all article titles follow British capitalization rules (see details in the Capitalization section.)
- Assure that the language in the document reads well for non-native English speakers, and avoid phrases or sayings that may be confusing, such as "Dig in."
- Avoid informal, personal thoughts or otherwise unnecessary language within the developer docs. A few examples of this might be:
    - Referring to the default configuration as the 'boring default configuration'.
    - Making a joke within the document, then using parentheses to acknowledge the joke such as "(Get it? heh)".
  
    - Avoid speaking directly to the reader, unless within a tutorial that is designed to be an interactive, onboarding experience. 
    
    For example, avoid the following:
    - "Get it? heh."
    - "Take a deep dive."
    - "Go down the rabbit hole."
    - "Dig in."
    
    Where as, the following are acceptable in certain contexts:
    - "Ready to get started? Let's go!"
    - "Here is a checklist of the things you will need to consider:"

## Numbers
Numbers should be formatted using the `'` character, and not any other character. For example:
- 44'760'000
- 54'000
- 1'000

## API methods

For API methods such as `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, or any other API method requests, they should be formatted as such:

- In headings, they should be capitalized without additional formatting. 
    - Example: "Using HTTP GET calls."
    - Example: "Using HTTP PUT calls."
- In the body of documents, they should be formatted as in-line code, such as:
    - Example: "A minimal example to make a `GET` HTTPS request."
    - Example: "That is, the canister could define the quorum size to be 1 and have only 1 replica execute the `POST` request."


## Punctuation

The following punctuation standards should be followed:
- All sentences end in a period.
- Exclamation points can be used where appropriate, but should not be used excessively. 
- Question marks can be used where appropriate. 
- Semicolons (;) should be used to indicate a pause within a sentence. 
- Parentheses should be used to include additional context where appropriate. 
- All bullet point lists should be prefaced with introduction text followed by a colon.
- All items within a bullet point list should end in a period, regardless if the entry is a full sentence or not.
- All numbered lists should use the format `1.` for numbering each list entry. 
- All user steps for guides and tutorials should use the format `Step 1:` to preface each step's contents. 

## Bulleted lists

The following bullet point format and standards should be followed:

- Bullet point lists can be written using either the `-` character or the `*` character; they both display the same on the webpage. 
- Bullet point lists that define a term or technology should follow the format:
    - **Term**: definition of the term.
- Bullet point lists that are used to indicate user steps for a guide should use the format:
    - #### Step 1: Description of step
- Bullet point lists that contain subtopics or sub-points should use the format:
    - Bullet point 1.
        - Subtopic bullet point.
    - Bullet point 2.  

## Numbered lists
The following numbered list format and standards should be followed:
- Numbered lists should be used to outline architecture steps that describe what happens within the backend or frontend of a tool or service, where it does not describe steps that a user interacts with directly. 
    - For example, steps that detail how a service interacts with a client application should be explained using a numbered list, where as steps detailing how a user can interact with the client application should be listed as guide or tutorial steps that use the correct heading sizes and format. 
- Numbered lists should also be used when presenting a list of questions to the reader to provoke their thoughts. For example:
    1. Which WebAssembly (Wasm) code is being executed for a canister?
    2. The canisters are normally written in a higher-level language, such as Motoko or Rust, and not directly in Wasm. The second question is then: is the Wasm that’s running really the result of compiling the purported source code?

## Bold text

The following standards for bold text should be followed:

- Bullet point lists that are used to list and define terms should bold the term(s) being defined. 
    - **Term**: definition of the term.
- Terms that are important for the reader to understand, such as a term found in the IC glossary, should be bolded for emphasis.
- When describing steps for a user to follow in a guide or tutorial, if the user is expected to interact with a button or GUI interface, the button's name should be bolded and follow the capitalization format found on the GUI. 
    - Example: Click on the **Save** button.

## Italic text
Italic text is not used within the IC developer documentation. Bold text should be used to emphasis terms or phrases that are important. 

## Hints
The IC developer documentation uses two forms of hints provided by the Docusaurus framework:
- **Info**: the info hint is used to provide important information that the reader should be prompted to read. This hint can be used to provide additional context, resources, or add important notes that the reader should be aware of before continuing.
- **Caution**: the caution hint is used to provide a warning to readers about the consequences of certain steps or actions as indicated in the guide, doc, or tutorial. 

Hints can be used with the following markdown format:

:::info
Info hint
:::

:::caution
Caution hint
:::

## Links
Links that are referenced within the developer docs should use the following format and structure:
- Capitalization of link titles should follow the same format and rules as described in the previous capitalization section; only the first word and proper nouns are capitalized. For example:
    - [Read Roman’s blog post on effective Rust canisters](link.example).
    - [Demonstrating trust](link.example). 
- In-line links should not capitalize the title of the page or section that is being linked. For example:
    - The below section [demonstrating trust](#demonstrating-trust).
    - The Wiki [contains some ideas](https://wiki.internetcomputer.org/wiki/Dealing_with_cycles_limit_exceeded_errors) how one can work around the cycles limit.

## Code snippets and code blocks
The following format should be used for code snippets and code blocks:
- In-line code references to commands, language specific methods, or other code-related terms should be emphasized using in-line code expressions such as:
    - To install code in a canister, the `install_code` function of the Internet Computer is used.
    - For example, the function `canister_init` is the first function that gets called after the code is installed for the first time.
- When a code-specific value, such as the name of a method, variable, integer, or data type, is being referenced, the capitalization used within the code or the language's reference documentation should be followed.
    - Example: `ConstructionPayloadRequest`
    - Example: `ingress_start`
- Command-line commands that should be run by the user should be listed in their own line using code formatting, such as:
    - #### Step 1:  Install the React module by running the following command:
  ```
            npm install --save react react-dom
  ```
- Code blocks should be prefaced with the language used within the code block where applicable. For example:

```bash
cd motoko/threshold_ecdsa
dfx start --background
npm install
dfx deploy
```

## Command line syntax

For all CLI command examples for `dfx`, flags should always be at the end of the command, such as:

```
dfx wallet balance --network ic
```

Additional examples:

```
dfx deploy --background
```

```
dfx identity new Alice --disable-encryption
````

```
dfx deploy internet_identity --argument '(null)'
```

## FAQ sections

Frequently asked question (FAQ) sections should use the following structure and format:

```
## Frequently asked questions

- #### Question 1?
Answer 

- #### Question 2?
Answer
```

## Difference between tutorials and guides

Tutorials should be stand-alone projects that a user can follow A-Z that may build off of an example code, may use a variety of different concepts, tools, or assets, and ultimately results in a working example project. 

In contrast, guides should be more focused on individual concepts or workflows and then building off of those. For example, the guides for building a smart contract backend with Motoko each focus and build upon individual concepts within Motoko, such as using integers in calculator functions or querying using an actor.

### Writing a tutorial
When contributing a tutorial, the following general format and structure should be used. It is important to note that the content of tutorials will vary, and this format can be expanded and elaborated on to include more detailed workflows or content. 

```
# Page title

## Overview

Text

## Prerequisites 

- [x] Prerequisite 1.
- [x] Prerequisite 2.
- [x] Prerequisite 3.

## Step 1: Description of step

Text

### Subtopic 1

Text

## Step 2: Description of step

Text

### Subtopic 2

Text

## Step 3: Description of step

Text

## Conclusion

Text

## Resources
- [Link](link.example)
```

### Writing a guide
When contributing a guide, the following general format and structure should be used. It is important to note that the content of guides will vary, and this format can be expanded and elaborated on to include more detailed workflows or content. 

```
# Page title

## Overview

Text

## Prerequisites (Optional; only necessary for guides that require prerequisite conditions or parameters be met.)

- [x] Prerequisite 1.
- [x] Prerequisite 2.
- [x] Prerequisite 3.

## Topic 1

Text

### Subtopic 1

Text

- #### Step 1: Description of step for reader to take.
- #### Step 2: Description of step for reader to take.
- #### Step 3: Description of step for reader to take.

### Subtopic 2

Text

- #### Step 1: Description of step for reader to take.
- #### Step 2: Description of step for reader to take.
- #### Step 3: Description of step for reader to take.

## Topic 2

Text

### Subtopic 3

Text

- #### Step 1: Description of step for reader to take.
- #### Step 2: Description of step for reader to take.
- #### Step 3: Description of step for reader to take.

## Conclusion
Text

## Resources
- [Link](link.example)
```

-->
