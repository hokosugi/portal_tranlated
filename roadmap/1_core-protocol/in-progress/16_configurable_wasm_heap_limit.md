---

title: Configurable Wasm Heap Limit
links:
  Forum Link: https://forum.dfinity.org/t/proposal-configurable-wasm-heap-limit/17794
  Proposal: https://dashboard.internetcomputer.org/proposal/105322
eta: 2023
is_community: true
---
canisters のWasmヒープは4GiBに制限されています。この制限は基本的なものであり、
、32ビットのメモリアドレスのため増やすことはできません。canister が利用可能なヒープ領域
をすべて使用した場合、メモリ不足エラーが発生し、
が動作しなくなり、データの損失やcanister のブリックにつながる可能性があります。
開発者がこの問題に気づくのは、問題を修正するのが手遅れになったときです。

この機能は、
 canister 設定で構成できる、明示的なWasmヒープ制限を導入することを目的としています。この制限のデフォルト値は、
3GiBなどの保守的な量になります。canister が
の制限を超えるメモリを使用しようとすると、メモリ不足エラーが発生します。これは
開発者に潜在的なメモリ問題を警告し、
canister をより少ないメモリを使用するバージョンに安全にアップグレードできるようにします。

<!---


The Wasm heap of canisters is limited to 4GiB. The limit is fundamental and
cannot be increased because of 32-bit memory addresses. If a canister uses all
of the available heap space, it will start producing out-of-memory errors and
may stop working, which could lead to data loss and bricking of the canister.
The developer may not realize this until it is too late to fix the issue.

This feature aims to introduce an explicit Wasm heap limit that can be
configured in the canister settings. The default value for this limit will be a
conservative amount, such as 3GiB. If a canister tries to use more memory than
the limit, it will receive an out-of-memory error. This will alert the
developer to the potential memory issue and allow them to safely upgrade the
canister to a version that uses less memory.

-->
