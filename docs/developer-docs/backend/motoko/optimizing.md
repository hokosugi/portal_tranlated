# 8: 最適化canisters

## 概要

canisters の最適化は重要な開発ステップです。canister のcycles 使用量とバイナリサイズを最適化することで、canister が最も効率的なワークフローを使用していることを保証できます。  このガイドでは、Motoko canisters を最適化する方法について説明します。

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## を使用します。`wasm-opt`

`Wasm-opt` は、dfx のバージョン 0.14.0 以降で使用できるようになった汎用 Wasm オプティマイザです。

`Wasm-opt` を使用すると、プロジェクトの ファイルにある設定オプションを使って の最適化を有効にすることができます：`dfx.json` canister 

    {
      "canisters": {
        "my_canister": {
          "optimize": "cycles"
        }
      }
    }

## cycle 使用の最適化レベル：

`"optimize": "cycles"` オプションを使用すると、Motoko canisters のcycles 使用率が約 10% 低下するという概算が期待できます。

`"optimize": "cycles"` オプションは、`wasm-opt` パッケージの最適化レベル 3 に対応するため、推奨されるデフォルトです。

cycles の最適化レベルは以下のとおりです：

    O4
    O3 (equivalent to “cycles”)
    O2
    O1
    O0 (performs no optimizations)

## バイナリ・サイズの最適化レベル：

代わりにバイナリ・サイズを最適化するには、`"optimize": "size"` オプションを使用します。サイズ・オプションを使用すると、バイナリ・サイズを約16%削減できます。

バイナリ・サイズの最適化レベルは以下のとおりです：

    Oz (equivalent to “size”)
    Os

各最適化では、各canister のInternet Computer 固有のメタデータ・セクションが保持されます。

:::info
最適化によってWasmモジュールの特定の関数の複雑さが増し、レプリカによって拒否される場合があることに注意してください。この問題が発生した場合は、複雑さの制限を超えないように、あまり積極的でない最適化レベルを使用することをお勧めします。
::：

canister 最適化に関する詳細情報および`wasm-opt` ベンチマークテストに関する情報は、[こちらのフォーラム投稿を](https://forum.dfinity.org/t/canister-optimizer-available-in-dfx-0-14-0/21157)参照してください。

## 次のステップ

次に、[ライブラリモジュールのインポートを見て](phonebook.md)みましょう。

<!---
# 8: Optimizing canisters

## Overview

Optimizing canisters is an important development step that can assure your canister is using the most efficient workflow by optimizing the canister's cycles usage and binary size.  This guide covers how to optimize Motoko canisters. 

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Using `wasm-opt`

`Wasm-opt` is a general purpose Wasm optimizer that is now available in dfx, versions 0.14.0 and newer. 

`Wasm-opt` can be used to enable canister optimizations through a configuration option in the project's `dfx.json` file, such as:

```
{
  "canisters": {
    "my_canister": {
      "optimize": "cycles"
    }
  }
}
```

## Optimization levels for cycle usage:

Using the `"optimize": "cycles"` option, you can expect a rough estimate of decreased cycles usage for Motoko canisters by around 10%. 

The `"optimize": "cycles"` option is the recommended default, as it maps to optimization level 3 in the `wasm-opt` package. 

The optimization levels for cycles usage are as follows:

```
O4
O3 (equivalent to “cycles”)
O2
O1
O0 (performs no optimizations)
```

## Optimization levels for binary size:

To optimize the binary size instead, you can use the `"optimize": "size"` option. By using the size option, binary sizes can be reduced by roughly 16%. 

The optimization levels for binary size are as follows:

```
Oz (equivalent to “size”)
Os
```

Each optimization preserves the Internet Computer specific metadata sections of each canister. 

:::info
Note that in certain cases the optimizations can increase the complexity of certain functions in your Wasm module such that they are rejected by the replica. If you run into this issue, we recommend using a less aggressive optimization level such that you do not exceed the complexity limit.
:::

More information on canister optimization and information on `wasm-opt` benchmark testing can be found [on this forum post](https://forum.dfinity.org/t/canister-optimizer-available-in-dfx-0-14-0/21157).

## Next steps

Next, let's take a look at [importing library modules](phonebook.md).
-->
