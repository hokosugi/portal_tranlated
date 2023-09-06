# Motoko サンプルコードとアプリケーション

## 概要

DFINITY によって開発された、またはコミュニティによって貢献されたサンプルコード、アプリケーション、およびマイクロサービスは、[DFINITY 公開リポジトリから](https://github.com/dfinity/examples)入手できます。

公開リポジトリにアクセスすると、サンプルプロジェクトを直接ダウンロード、クローン、フォーク、共有することができます。また、GitHub の標準ワークフローを使用して、公開されているプロジェクトの更新を提案したり、問題を報告したりして貢献することもできます。

サンプルプロジェクトは、あなたが他の開発者と実験したり協力したりするための方法を提供します。ただし、プロジェクトとサンプルコードは商用アプリケーションとして使用することを意図したものではなく、明示的または黙示的なサポートや保証を提供するものではありません。

このセクションでは、アプリケーションとマイクロサービスのサンプルコードを紹介します。

## Motoko

Motoko プログラミング言語を使用するプロジェクトについては、[Motoko サンプルプロジェクトを](https://github.com/dfinity/examples/tree/master/motoko)参照してください。

- [電卓: 単純な関数](https://github.com/dfinity/examples/tree/master/motoko/calc)

- [カウンター](https://github.com/dfinity/examples/tree/master/motoko/counter)

- [エコー](https://github.com/dfinity/examples/tree/master/motoko/echo)

- [Factor](https://github.com/dfinity/examples/tree/master/motoko/factorial)

- [こんにちは、cycles](https://github.com/dfinity/examples/tree/master/motoko/hello_cycles)

- [こんにちは、世界](https://github.com/dfinity/examples/tree/master/motoko/hello-world)

- [人生ゲーム：アップグレード](https://github.com/dfinity/examples/tree/master/motoko/life)

- [電話帳](https://github.com/dfinity/examples/tree/master/motoko/phone-book)

- [発行と購読](https://github.com/dfinity/examples/tree/master/motoko/pub-sub)

- [クイックソート](https://github.com/dfinity/examples/tree/master/motoko/quicksort)

- [暗号ランダムを使ったランダム迷路](https://github.com/dfinity/examples/tree/master/motoko/random_maze)

- [ToDoチェックリスト](https://github.com/dfinity/examples/tree/master/motoko/simple-to-do)

- [スーパーヒーローデータベース](https://github.com/dfinity/examples/tree/master/motoko/superheroes)

- [whoami](https://github.com/dfinity/examples/tree/master/motoko/whoami)

## その他のサンプルアプリケーション

このセクションには、現在公開されていないプロジェクトのサンプルコードが含まれています。

### 16進エンコードとデコード

`mo-hex` プロジェクトは、Motoko プログラミング言語用の 16 進エンコードとデコードルーチンを実装しています。

#### ソースコード

このプロジェクトには[hex.mo](./_attachments/hex.mo)ソースコードが含まれています。

### GF(256)の多項式除算

このプログラムはガロア体GF(256)の要素に対して多項式除算を行います。

#### ソースコード

標準ライブラリに加えて、このプロジェクトは2つの主要なMotoko ソースコードファイルを使います。

- [Galois.mo](./_attachments/Galois.mo)ファイルにはコア プログラミング ロジックが含まれています。

- [Nat.mo](./_attachments/Nat.mo)ファイルには`Galois.mo` ファイルで使用するためにインポートされる追加関数が含まれています。

<!---
# Motoko sample code and applications

## Overview
Sample code, applications, and microservices that have been developed by DFINITY or contributed by the community are available from the [DFINITY public repository](https://github.com/dfinity/examples).

By accessing the public repository, you can directly download, clone, fork, or share sample projects. You will also be able to contribute by suggesting updates or reporting issues for the published projects using the standard GitHub work flow.

Sample projects provide a way for you to experiment and collaborate with other developers. The projects and sample code are not, however, intended to be used as commercial applications and do not provide any explicit or implied support or warranty of any kind.

This section provides a preliminary look at some sample code for applications and microservices that you can copy and modify to jump-start your own projects.

## Motoko

For projects that use the Motoko programming language, see [Motoko sample projects](https://github.com/dfinity/examples/tree/master/motoko).

-   [Calculator: simple functions](https://github.com/dfinity/examples/tree/master/motoko/calc)

-   [Counter](https://github.com/dfinity/examples/tree/master/motoko/counter)

-   [Echo](https://github.com/dfinity/examples/tree/master/motoko/echo)

-   [Factorial](https://github.com/dfinity/examples/tree/master/motoko/factorial)

-   [Hello, cycles](https://github.com/dfinity/examples/tree/master/motoko/hello_cycles)

-   [Hello, world](https://github.com/dfinity/examples/tree/master/motoko/hello-world)

-   [Game of Life: upgrades](https://github.com/dfinity/examples/tree/master/motoko/life)

-   [Phone book](https://github.com/dfinity/examples/tree/master/motoko/phone-book)

-   [Publish and subscribe](https://github.com/dfinity/examples/tree/master/motoko/pub-sub)

-   [Quicksort](https://github.com/dfinity/examples/tree/master/motoko/quicksort)

-   [Random maze using cryptographic randomness](https://github.com/dfinity/examples/tree/master/motoko/random_maze)

-   [To-do checklist](https://github.com/dfinity/examples/tree/master/motoko/simple-to-do)

-   [Superheroes database](https://github.com/dfinity/examples/tree/master/motoko/superheroes)

-   [whoami](https://github.com/dfinity/examples/tree/master/motoko/whoami)

## Additional sample applications

This section includes sample code from projects that are not currently available in the public repository.

### Hex encoding and decoding

The `mo-hex` project implements hexadecimal encoding and decoding routines for the Motoko programming language.

#### Source code

The project includes the [hex.mo](./_attachments/hex.mo) source code.

### Polynomial long-division in GF(256)

This program performs polynomial long division for a Galois field GF(256) element.

#### Source code

In addition to standard libraries, this project uses two main Motoko source code files.

- The [Galois.mo](./_attachments/Galois.mo) file contains the core programming logic.

- The [Nat.mo](./_attachments/Nat.mo) file contains additional functions that are imported for use in the `Galois.mo` file.

-->
