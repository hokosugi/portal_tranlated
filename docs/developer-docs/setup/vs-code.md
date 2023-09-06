# Visual Studioコードの使用

## 概要

Visual Studio (VS) Codeは[、](https://survey.stackoverflow.co/2022/#section-worked-with-vs-want-to-work-with-integrated-development-environment) canister の開発をサポートするオープンソースのIDEです。 [Motoko](https://internetcomputer.org/docs/current/motoko/intro/)および[Rust](https://www.rust-lang.org/) での開発をサポートしています。

### Motoko

[Motoko VS Code 拡張](https://github.com/dfinity/vscode-motoko)機能は、Motoko canister 開発のための型チェック、書式設定、自動補完、Go-to-definition、コードスニペットなどを提供します。

[![Showcase](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/intro.webp)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

フルスタックの[Vite + React +Motoko](https://github.com/rvanasa/vite-react-motoko#readme)スタータープロジェクトを使って、ブラウザで拡張機能をお試しください：

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/rvanasa/vite-react-motoko)

## インストール

[![Visual Studio Marketplace](https://img.shields.io/visual-studio-marketplace/v/dfinity-foundation.vscode-motoko?color=brightgreen&logo=visual-studio-code)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

[VS Marketplace](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)、またはVS Codeプロジェクトの[**Extensions**パネルから](https://code.visualstudio.com/docs/editor/extension-marketplace)拡張機能をインストールします。

[VSCodium](https://vscodium.com/)ユーザーは、[Open VSX](https://open-vsx.org/extension/dfinity-foundation/vscode-motoko)または[GitHubリリース](https://github.com/dfinity/vscode-motoko/releases)ページから拡張機能をダウンロードできます。

## キーボードショートカット

以下は、拡張機能でサポートされる一般的に使用される機能のデフォルトのキーバインドです：

- **コードフォーマッタ**(`Shift` +`Alt` +`F`):[prettier-plugin-motoko](https://github.com/dfinity/prettier-plugin-motoko) を使ってMotoko ファイルをフォーマットします。
- イン**ポートの整理**(`Shift` +`Alt` +`O`): インポートをグループ化し、Motoko ファイルの先頭でソートします。
- **インポートコードアクション**(`Ctrl/Cmd` +`.` 未解決の変数にカーソルを合わせている間): インポートのクイックフィックスオプションを表示します。
- **定義へ移動**(`F12`): ローカルまたはインポートされた識別子の定義へジャンプします。
- **インテリセンス**(`Ctrl` +`Space`): 利用可能なすべてのオートコンプリートとコードスニペットを表示します。

[![Snippets](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/snippets.png)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

## その他の機能

- [Vessel](https://github.com/dfinity/vessel)と[MOPS](https://mops.one/)(Motoko で最もよく使われている二つのパッケージマネージャ) がこの拡張機能ですぐにサポートされます。
- `array-2-buffer` や`principal-2-text` のようなコードスニペットを使って、Motoko タイプ間を素早く変換できます。
- `dfx` をインストールせずにMotoko を学習したい場合、Motoko VS Code 拡張機能は、すべての主要なオペレーティングシステム (Windows を含む) 上でスタンドアロンで動作します。
- この拡張機能は、`dfx.json` 設定ファイルのスキーマ検証やオートコンプリートも提供します。
- 関数名、インポート、その他の式にカーソルを合わせると、型情報とドキュメントを表示します。

[![Tooltips](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/tooltips.png)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

### 貢献

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?logo=github)](https://github.com/dfinity/prettier-plugin-motoko)

Motoko VS Code 拡張機能は完全にオープンソースであり、[GitHub で利用可能](https://github.com/dfinity/vscode-motoko)です。

## Rust

Rustcanisters を開発するためのワークスペースを設定するには、公式の[Rust in Visual Studio Code](https://code.visualstudio.com/docs/languages/rust)ドキュメントを参照してください。

<!---
# Using Visual Studio Code

## Overview

Visual Studio (VS) Code is a [widely used](https://survey.stackoverflow.co/2022/#section-worked-with-vs-want-to-work-with-integrated-development-environment) open-source IDE which supports canister development in [Motoko](https://internetcomputer.org/docs/current/motoko/intro/) and [Rust](https://www.rust-lang.org/).

### Motoko

The [Motoko VS Code extension](https://github.com/dfinity/vscode-motoko) provides type checking, formatting, autocompletion, go-to-definition, code snippets, and more for Motoko canister development.

[![Showcase](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/intro.webp)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

Try the extension in your browser with a full-stack [Vite + React + Motoko](https://github.com/rvanasa/vite-react-motoko#readme) starter project:

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/rvanasa/vite-react-motoko)

## Installation

[![Visual Studio Marketplace](https://img.shields.io/visual-studio-marketplace/v/dfinity-foundation.vscode-motoko?color=brightgreen&logo=visual-studio-code)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

Install the extension through the [VS Marketplace](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko), or alternatively the [**Extensions** panel](https://code.visualstudio.com/docs/editor/extension-marketplace) in your VS Code project. 

[VSCodium](https://vscodium.com/) users can download the extension from [Open VSX](https://open-vsx.org/extension/dfinity-foundation/vscode-motoko) or the [GitHub releases](https://github.com/dfinity/vscode-motoko/releases) page.

## Keyboard shortcuts

Below are the default key bindings for commonly used features supported in the extension:

- **Code formatter** (`Shift` + `Alt` + `F`): format a Motoko file using [prettier-plugin-motoko](https://github.com/dfinity/prettier-plugin-motoko).
- **Organize imports** (`Shift` + `Alt` + `O`): group and sort imports at the top of your Motoko file.
- **Import code action** (`Ctrl/Cmd` + `.` while hovering over an unresolved variable): show import quick-fix options. 
- **Go to definition** (`F12`): jump to the definition of a local or imported identifier.
- **IntelliSense** (`Ctrl` + `Space`): view all available auto-completions and code snippets. 

[![Snippets](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/snippets.png)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

## Other features

- [Vessel](https://github.com/dfinity/vessel) and [MOPS](https://mops.one/) (the two most popular Motoko package managers) are supported out-of-the-box in this extension. 
- Quickly convert between Motoko types using code snippets such as `array-2-buffer` or `principal-2-text`.
- In case you're hoping to learn Motoko without installing `dfx`, the Motoko VS Code extension works standalone on all major operating systems (including Windows). 
- This extension also provides schema validation and autocompletion for `dfx.json` config files.
- View type information and documentation by hovering over function names, imports, and other expressions.

[![Tooltips](https://github.com/dfinity/vscode-motoko/raw/master/guide/assets/tooltips.png)](https://marketplace.visualstudio.com/items?itemName=dfinity-foundation.vscode-motoko)

### Contributing

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?logo=github)](https://github.com/dfinity/prettier-plugin-motoko)

The Motoko VS Code extension is completely open-source and [available on GitHub](https://github.com/dfinity/vscode-motoko). 

## Rust

Please refer to the official [Rust in Visual Studio Code](https://code.visualstudio.com/docs/languages/rust) documentation to configure your workspace for developing Rust canisters.

-->
