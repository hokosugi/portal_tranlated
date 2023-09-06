# Motoko ドキュメントの生成

`mo-doc` は、 ソースコードのドキュメントを生成するためのコマンドラインツールです。ソースファイルを処理し、さまざまな形式のドキュメントを生成します。Motoko 

## クイックスタート

Motoko の[GitHub リリース](https://github.com/dfinity/motoko/releases)ページから`mo-doc` をダウンロードするか、[dfx の](https://internetcomputer.org/docs/current/developer-docs/setup/install)インストールに含まれているバイナリを使用してください：

    $(dfx cache show)/mo-doc [options]

## オプション

- `--source <path>`:Motoko ソースファイルを検索するディレクトリを指定します。デフォルトは`src` です。

- `--output <path>`:ドキュメントが生成されるディレクトリを指定します。デフォルトは`docs` です。

- `--format <format>`:生成されるフォーマットを指定します。以下のいずれかを指定します：
  
  - `html`:HTML 形式のドキュメントを生成します。
  - `adoc`:AsciiDoc 形式のドキュメントを生成します。
  - `plain`:Markdown ドキュメントを生成します。
  
  デフォルトは`html` です。

- `--help`:使用情報を表示します。

## 例

1.  デフォルトのソースディレクトリ (`src`) から HTML ドキュメントを生成し、デフォルトの出力ディレクトリ (`docs`) に配置します：
    
    ``` bash
    mo-doc
    ```

2.  特定のソース・ディレクトリから AsciiDoc ドキュメントを生成します：
    
    ``` bash
    mo-doc --format plain --source ./motoko-code
    ```

3.  カスタム出力ディレクトリにMarkdownドキュメントを生成します：
    
    ``` bash
    mo-doc --format adoc --output ./public
    ```

## docコメントの記述

`mo-doc` は特別なブロックコメント ( ) と行コメント ( ) を使って コードを文書化することをサポートしています。`/** */``///` Motoko 

docコメントは関数、クラス、型、モジュール、変数などの説明を提供するために使用することができます。複数の行にまたがることができ、豊富なMarkdownフォーマットを含むことができます：

```` motoko
/// Calculate the factorial of a given positive integer.
/// 
/// Example:
/// ```motoko
/// factorial(0); // => null
/// factorial(3); // => ?6
/// ```
func factorial(n : Nat) : ?Nat {
    // ...
}
````

## リソース

その他の例やベスト・プラクティスについては、Motoko's[base library souce codeを](https://github.com/dfinity/motoko-base/tree/master/src)チェックしてください。

`mo-doc` のソースコードは[dfinity/](https://github.com/dfinity/motoko/tree/master/src/docs) [motoko GitHubリポジトリで入手できます。貢献を歓迎します！](https://github.com/dfinity/motoko/tree/master/src/docs)

<!---
# Generating Motoko documentation

`mo-doc` is a command-line tool for generating documentation for Motoko source code. It processes source files and generates documentation in various formats. 

## Quick start

Download `mo-doc` from Motoko's [GitHub releases page](https://github.com/dfinity/motoko/releases) or simply use the binary included in your [dfx](https://internetcomputer.org/docs/current/developer-docs/setup/install) installation:

```
$(dfx cache show)/mo-doc [options]
```

## Options

- `--source <path>`: Specifies the directory to search for Motoko source files. Defaults to `src`.

- `--output <path>`: Specifies the directory where the documentation will be generated. Defaults to `docs`.

- `--format <format>`: Specifies the generated format. Should be one of the following:
  - `html`: Generates HTML format documentation.
  - `adoc`: Generates AsciiDoc format documentation.
  - `plain`: Generates Markdown documentation.
  
  Defaults to `html`.

- `--help`: Shows usage information.

## Examples

1. Generate HTML documentation from the default source directory (`src`) and place it in the default output directory (`docs`):

   ```bash
   mo-doc
   ```

2. Generate AsciiDoc documentation from a specific source directory:

   ```bash
   mo-doc --format plain --source ./motoko-code
   ```

3. Generate Markdown documentation in a custom output directory:

   ```bash
   mo-doc --format adoc --output ./public
   ```

## Writing doc comments

`mo-doc` supports documenting your Motoko code using special block comments (`/** */`) and line comments (`///`).

Doc comments can be used to provide explanations for functions, classes, types, modules, variables, and more. They can span multiple lines and may contain rich Markdown formatting:

```motoko
/// Calculate the factorial of a given positive integer.
/// 
/// Example:
/// ```motoko
/// factorial(0); // => null
/// factorial(3); // => ?6
/// ```
func factorial(n : Nat) : ?Nat {
    // ...
}
```

## Resources
Check out Motoko's [base library souce code](https://github.com/dfinity/motoko-base/tree/master/src) for additional examples and best practices. 

The source code for `mo-doc` is available in the [dfinity/motoko](https://github.com/dfinity/motoko/tree/master/src/docs) GitHub repository. Contributions are welcome!

-->
