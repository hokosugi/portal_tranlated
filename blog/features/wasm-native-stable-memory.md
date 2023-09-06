---

title: Wasm-native stable memory
description: "Introducing Wasm-native stable memory"
tags: [New features]
image: /img/blog/dev-blog-wasm-native.jpg
---
# Wasm-native 安定メモリ

Wasm-native stable memory は、Internet Computer's stable memory interface のパフォーマンスを向上させるもので、最近すべてのサブネットに展開されました。ほとんどの安定メモリ作業負荷に対して 1.5-2 倍の性能向上を提供し、 完全に透過的な方法でこれを行います。

すでに安定メモリを使用するcanisters を開発している場合、それらのcanisters は自動的にこの性能向上の恩恵を受けることになります。安定したメモリーを使用していないのであれば、今こそ試してみてはいかがでしょうか！

## 安定したメモリとは何ですか?

安定したメモリは、通常のWasmメモリとは別のデータストアです。任意のバイトを読み書きできる[安定メモリAPIを使って](https://internetcomputer.org/docs/current/references/ic-interface-spec#system-api-stable-memory)アクセスします。ライブラリ [`ic-stable-memory`](https://crates.io/crates/ic-stable-memory)や [`ic-stable-structures`](https://crates.io/crates/ic-stable-structures)などのライブラリも、安定メモリを人間工学的に使いやすくしています。

安定メモリを使う主な理由は以下の通りです：

1.  canister のアップグレードを越えて持続します。
2.  通常のWasmメモリの10倍以上の容量があります。

つまり、dapp の垂直スケーリング（つまり、dapp とともに成長する単一のcanister を持つこと）に関しては、stable メモリを使用するのが唯一の選択肢です。

しかし、安定したメモリの欠点は以下の通りです：

1.  ワズムメモリよりアクセスが遅い。
2.  データは安定したメモリに保存するときにシリアライズし、ロードするときにデシリアライズする必要があります。

Wasmネイティブの安定したメモリは、最初の欠点を改善する重要なステップです。

## なぜ安定メモリは遅いのですか？

安定したメモリからの読み取りや安定したメモリへの書き込みはそれぞれシステム API 呼び出しを経由しますが、この呼び出しには大きなオーバーヘッドがあります。まずWasmtimeのランタイムが呼び出され、レプリカのコードを呼び出して、安定したメモリから、または安定したメモリにデータをコピーします。実行中のcanister Wasm には安定メモリのバッキングストアに直接アクセスする方法がないため、このような間接処理が必要になります。

次の図は、Wasm のメインメモリと安定メモリのアクセスの違いを示しています：

![Original stable memory diagram](/img/blog/wasm-native-stable-memory-diagram-old.png)

## Wasmネイティブの安定メモリの仕組み

幸いなことに、Wasm の新機能である[複数メモリによって](https://github.com/WebAssembly/multi-memory/blob/master/proposals/multi-memory/Overview.md)、Wasm モジュールは Wasm 仕様で「メモリ」と呼ばれる複数のバイト配列を直接アドレス指定できるようになりました。この機能を使って安定メモリAPIの呼び出しを変更し、セカンダリWasmメモリから直接読み書きすることができます。

次の図は、Wasm ネイティブ安定メモリの下で、安定メモリアクセスがどのように動作するかを示しています：

![Wasm-native stable memory diagram](/img/blog/wasm-native-stable-memory-diagram-new.png)

## サンプルでのパフォーマンス向上dapp

ここでは、`ic-stable-structures` ライブラリを使用したdapp の例を取り上げ、Wasm-native の安定化変更後にパフォーマンスがどのように向上するかを示します。まずは`ic-stable-structures` リポジトリにある、キーバリューストアを実装した[基本的な例](https://github.com/dfinity/stable-structures/tree/main/examples/src/basic_example)から始めましょう。dapp はまず`StableBTreeMap` をセットアップしてデータを保存します：

``` rust
type Memory = VirtualMemory<DefaultMemoryImpl>;

thread_local! {
    // The memory manager is used for simulating multiple memories. Given a `MemoryId` it can
    // return a memory that can be used by stable structures.
    static MEMORY_MANAGER: RefCell<MemoryManager<DefaultMemoryImpl>> =
        RefCell::new(MemoryManager::init(DefaultMemoryImpl::default()));

    // Initialize a `StableBTreeMap` with `MemoryId(0)`.
    static MAP: RefCell<StableBTreeMap<u128, u128, Memory>> = RefCell::new(
        StableBTreeMap::init(
            MEMORY_MANAGER.with(|m| m.borrow().get(MemoryId::new(0))),
        )
    );
}
```

次に、多数のキーと値のペアを一括挿入するAPIを追加することで、基本的な例を拡張します：

``` rust
#[ic_cdk_macros::update]
fn insert_many(entries: Vec<(u128, u128)>) {
    MAP.with(|p| {
        let mut map = p.borrow_mut();
        for (k, v) in entries.into_iter() {
            map.insert(k, v);
        }
    })
}
```

このdapp をICにデプロイした後、多数のkey-valueペアを一括挿入する複数のメッセージを実行します（イングレス・メッセージ・サイズ制限の最大値に近い）。以下のグラフは、これらの各メッセージの実行時間を示しています。

まず、Wasm-native安定メモリなし：

![Metrics without Wasm-native stable memory](/img/blog/wasm-native-stable-memory-execution-metrics-no-wnsm.png)

次に、Wasm-native安定メモリを使用した場合です：

![Metrics with Wasm-native stable memory](/img/blog/wasm-native-stable-memory-execution-metrics-wnsm.png)

このdapp 、Wasm-native安定メモリの変更により、1.5倍のスピードアップを実現していることがわかります。

## プロダクションの例

安定メモリを使用した、より複雑なdapps のアーキテクチャに興味がある場合は、[Bitcoincanister](https://github.com/dfinity/bitcoin-canister)と[Internet Identitycanister](https://github.com/dfinity/internet-identity/tree/main/src/internet_identity)が良い例です。

## 結論

Dapps 安定したメモリを使用しているアプリケーションでは、開発者が変更を加えなくても、安定したメモリの読み書きのパフォーマンスが 1.5 ～ 2 倍向上するはずです。ですから、今すぐ安定メモリの使用を検討すべきです！

<!---


# Wasm-native stable memory
Wasm-native stable memory is a performance improvement to the Internet Computer’s stable memory interface, which was recently rolled out to all subnets. It provides a 1.5-2x performance improvement to most stable memory workloads and does so in a completely transparent way. 

If you’re already developing canisters that use stable memory, then those canisters will automatically benefit from this performance improvement. If you’re not using stable memory, maybe now’s the time to try it out!

## What is stable memory?

Stable memory is a data store separate from regular Wasm memory. It is accessed using the [stable memory API](https://internetcomputer.org/docs/current/references/ic-interface-spec#system-api-stable-memory) which allows reading/writing arbitrary bytes. Libraries like [`ic-stable-memory`](https://crates.io/crates/ic-stable-memory) and [`ic-stable-structures`](https://crates.io/crates/ic-stable-structures) also make stable memory ergonomic to use.

The main reasons to use stable memory are that:

1. It persists across canister upgrades.
1. It has a capacity over 10x larger than regular Wasm memory.

This means that using stable memory is really the only option when it comes to vertically scaling a dapp (i.e. having a single canister that grows with your dapp). 

But the downsides of stable memory are that:

1. It is slower to access than Wasm memory.
2. Data must be serialized when storing in stable memory and deserialized when loaded.

Wasm native stable memory is a significant step in improving the first downside.

## Why is stable memory slower?

Each read from or write to stable memory goes through a system API call, and this call has a significant overhead. It first calls into the Wasmtime runtime, which then invokes code in the replica to copy over data from or to the stable memory. This indirection is needed because the running canister Wasm has no way to directly access the backing store of the stable memory.

The following diagram shows the difference between accessing the main Wasm memory versus stable memory:

![Original stable memory diagram](/img/blog/wasm-native-stable-memory-diagram-old.png)
 
## How Wasm-native stable memory works

Fortunately, the new [multiple memories](https://github.com/WebAssembly/multi-memory/blob/master/proposals/multi-memory/Overview.md) Wasm feature allows a Wasm module to directly address multiple byte arrays, called “memories” in the Wasm spec. We can use this feature to modify calls to the stable memory APIs to directly read or write from a secondary Wasm memory.

The following diagram shows how stable memory accesses will work under Wasm-native stable memory:

![Wasm-native stable memory diagram](/img/blog/wasm-native-stable-memory-diagram-new.png)

## Performance gains on sample dapp

Here we take an example dapp using the `ic-stable-structures` library and show how its performance improves after the Wasm-native stable change. Let’s start with the [basic example](https://github.com/dfinity/stable-structures/tree/main/examples/src/basic_example) from `ic-stable-structures` repo which implements a key-value store. The dapp starts by setting up a `StableBTreeMap` to store data:

```rust
type Memory = VirtualMemory<DefaultMemoryImpl>;

thread_local! {
    // The memory manager is used for simulating multiple memories. Given a `MemoryId` it can
    // return a memory that can be used by stable structures.
    static MEMORY_MANAGER: RefCell<MemoryManager<DefaultMemoryImpl>> =
        RefCell::new(MemoryManager::init(DefaultMemoryImpl::default()));

    // Initialize a `StableBTreeMap` with `MemoryId(0)`.
    static MAP: RefCell<StableBTreeMap<u128, u128, Memory>> = RefCell::new(
        StableBTreeMap::init(
            MEMORY_MANAGER.with(|m| m.borrow().get(MemoryId::new(0))),
        )
    );
}
```

We then enhance the basic example by adding an API to bulk insert many key-value pairs:

```rust
#[ic_cdk_macros::update]
fn insert_many(entries: Vec<(u128, u128)>) {
    MAP.with(|p| {
        let mut map = p.borrow_mut();
        for (k, v) in entries.into_iter() {
            map.insert(k, v);
        }
    })
}
```

After deploying this dapp to the IC, we then execute multiple messages that bulk insert many key-value pairs (close to maxing out the ingress message size limit). The following graphs show the execution time for each of these messages.

First, without Wasm-native stable memory:

![Metrics without Wasm-native stable memory](/img/blog/wasm-native-stable-memory-execution-metrics-no-wnsm.png)

Then, with Wasm-native stable memory:

![Metrics with Wasm-native stable memory](/img/blog/wasm-native-stable-memory-execution-metrics-wnsm.png)

We can see that this dapp gets a 1.5x speedup from the Wasm-native stable memory change.

## Production examples

If you’re interested in seeing some architecture of more complicated dapps that are using stable memory, the [Bitcoin canister](https://github.com/dfinity/bitcoin-canister) and the [Internet Identity canister](https://github.com/dfinity/internet-identity/tree/main/src/internet_identity) are good examples.

## Conclusion

Dapps which use stable memory should see 1.5-2x performance improvements for stable memory reads and writes without developers needing to make any changes. So you should consider using stable memory today!

-->
