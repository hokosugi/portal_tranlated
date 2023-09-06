---

title: Wasm-native stable memory
links:
  Forum Link: https://forum.dfinity.org/t/proposal-wasm-native-stable-memory/15966
  Proposal: https://dashboard.internetcomputer.org/proposal/88812
eta: 2023
is_community: false
---
Wasm ネイティブ安定メモリを導入する目的は、Wasm ロードとストアが Wasm ヒープにアクセスするのと同じ方法で安定メモリに直接アクセスすることで、安定メモリの読み取りと書き込みのパフォーマンスを向上させることです。

これにより、安定メモリの直接使用がより実用的になり、canister 開発者が安定メモリの使用方法を変更する必要はありません。

<!---


The goal of introducing Wasm-native stable memory is to improve the performance of stable reads and writes by letting these operations directly access stable memory in the same way Wasm loads and stores access the Wasm heap.

This will make direct use of stable memory more practical and it will not require canister developers to make any changes to how they use stable memory.

-->
