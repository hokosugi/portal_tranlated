# 8: ラストの最適化canisters

## 概要

RustをWasmにコンパイルすると、ファイルサイズが大幅に増加することがよくあります。dfxのバージョン0.14.0以降には、`wasm-opt` 最適化パッケージが含まれており、これを使用すると、cycle の消費量とバイナリサイズを最適化できます。

## 前提条件

始める前に、[開発者環境ガイドの](./3-dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

このガイドでは、最適化したい既存のcanister があることを前提としています。既存のcanister を入手するには、このセクションの前のドキュメント・ページを参照してください。

## cycle 消費量の削減

最適化されたシステムへの第一歩はプロファイリングです。エンドポイントが消費する命令数を測定することから始めましょう。

`instruction_counter` API を使えば、最後のエントリー・ポイント以降にコードが消費した命令数を知ることができます。命令はICランタイムの内部通貨です。ICの1命令は、メモリアドレスから32ビット整数をロードするなど、システムが実行できる仕事の量です。システムは各Wasm命令とシステムコールに相当する命令コストを割り当てます。また、すべての制限を命令で定義します。ICの現在の命令とWasmの制限の詳細な内訳については、[このページを](../../backend/resource-limits.md)参照してください。

以下は、命令数を測定するために使用できる方法の例です：

``` rust
#[update]
async fn transfer(from: Account, to: Account, amount: Nat) -> Result<TxId, Error> {
  let start = ic_cdk::api::instruction_counter();
  let tx = apply_transfer(from, to, amount)?;
  let tx_id = archive_transaction(tx).await?;
  // NOTE: the await point above resets the instruction counter.
  let end = ic_cdk::api::instruction_counter();
  record_measurement(end - start);
Ok(tx_id)
}
```

## を使用します。`wasm-opt`

`Wasm-opt` はbinaryenの汎用Wasmオプティマイザーパッケージで、現在dfxのバージョン0.14.0以降で使用できます。

`Wasm-opt` を使用すると、プロジェクトの ファイルの設定オプションで の最適化を有効にすることができます：`dfx.json` canister 

    {
      "canisters": {
        "my_canister": {
          "optimize": "cycles"
        }
      }
    }

## cycle 使用の最適化レベル：

`"optimize": "cycles"` オプションを使用すると、Rustcanisters のcycles 使用量がおおまかに見積もって約 7% 減少します。

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

各最適化では、各canister のInternet Computer 固有のメタデータ・セクションが保持されます。さらに、`--keep-name-section` フラグを付けて`ic-wasm` を直接呼び出すことで、Wasm モジュールの名前セクションを保持できます。

:::info
最適化により、Wasm モジュールの特定の関数の複雑さが増し、レプリカによって拒否される場合があることに注意してください。この問題が発生した場合は、複雑さの制限を超えないように、あまり積極的でない最適化レベルを使用することをお勧めします。
::：

canister 最適化に関する詳細情報および`wasm-opt` ベンチマークテストに関する情報は、[こちらのフォーラム投稿を](https://forum.dfinity.org/t/canister-optimizer-available-in-dfx-0-14-0/21157)参照してください。

## 次のステップ

次は[カウンターのインクリメントを見て](9-counter.md)みましょう。

<!---
# 8: Optimizing Rust canisters

## Overview
Compiling Rust to Wasm often increases the file size significantly. dfx versions 0.14.0 and newer include a the `wasm-opt` optimization package that can be used to optimize cycle consumption and binary size. 

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./3-dev-env.md).

This guide assumes you have an existing canister that you'd like to optimize. To get an existing canister, review the previous documentation pages in this section. 

## Reducing cycle consumption
The first step towards an optimized system is profiling. Start by measuring the number of instructions your endpoints consume.

The `instruction_counter` API will tell you the number of instructions your code has consumed since the last entry point. Instructions are the internal currency of the IC runtime; one IC instruction is the quantum of work that the system can do, such as loading a 32-bit integer from a memory address. The system assigns an instruction cost equivalent to each Wasm instruction and system call. It also defines all its limits in terms of instructions. For a detailed breakdown of the current instruction and Wasm limitations on the IC, please review [this page](../../backend/resource-limits.md).


The following is an example method that can be used to measures the number of instructions:

```rust
#[update]
async fn transfer(from: Account, to: Account, amount: Nat) -> Result<TxId, Error> {
  let start = ic_cdk::api::instruction_counter();
  let tx = apply_transfer(from, to, amount)?;
  let tx_id = archive_transaction(tx).await?;
  // NOTE: the await point above resets the instruction counter.
  let end = ic_cdk::api::instruction_counter();
  record_measurement(end - start);
Ok(tx_id)
}
```

## Using `wasm-opt`

`Wasm-opt` is a general purpose Wasm optimizer package from binaryen that is now available in dfx, versions 0.14.0 and newer. 

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

Using the `"optimize": "cycles"` option, you can expect a rough estimate of decreased cycles usage for Rust canisters by around 7%. 

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

Each optimization preserves the Internet Computer specific metadata sections of each canister. Additionally, the name sections in your Wasm module can be preserved by directly invoking `ic-wasm` with the `--keep-name-section` flag.

:::info
Note that in certain cases the optimizations can increase the complexity of certain functions in your Wasm module such that they are rejected by the replica. If you run into this issue, we recommend using a less aggressive optimization level such that you do not exceed the complexity limit.
:::

More information on canister optimization and information on `wasm-opt` benchmark testing can be found [on this forum post](https://forum.dfinity.org/t/canister-optimizer-available-in-dfx-0-14-0/21157).

## Next steps

Next, let's take a look at [incrementing a counter.](9-counter.md)

-->
