# リソースの制約と制限Internet Computer

## 概要

このセクションでは、開発者が知っておくべきInternet Computer のリソース使用に関する現在の主な制約を定義します。

## リソースの制約と制限

| リソース | 制約 |
| --- | --- |
| Canister キューの制限 | 500メッセージ |
| 最大イングレスおよびクロスネット間canister 通話ペイロード | 2MB |
| 最大同一サブネット間canister コール・ペイロード（いずれ廃止される可能性あり） | 10MB |
| 最大応答サイズ | 2MB |
| アップデートコール/ハートビート/タイマーごとの命令数制限 | 1メソッド呼び出しあたり20B |
| クエリーコールあたりの命令数制限 | 5B |
| canister 、インストールおよびアップグレードの命令数制限 | 200B |
| サブネット容量 | 450GB |
| ワズムヒープサイズ | 4GB |
| Wasm安定メモリ | 64GB |
| Wasmカスタムセクション | サブネットあたり2GB;canister あたり1MB; 最大16セクション (canister あたり) |
| Wasmコードセクション | 10MB |
| クエリーコール実行スレッド | レプリカノードあたり2 |
| アップデートコール実行スレッド | サブネットあたり4 |

## その他の注意事項

IC は、以下のような理由でWebAssembly モジュールを拒否することがあります：

- 50,000 を超える関数を宣言している場合。
- 300 個以上のグローバルを宣言している場合。
- エクスポートされたカスタム・セクション (接頭辞 icp: が付いたカスタム・セクション名) の宣言数が 16 を超える場合。
- `canister_update <name>` または`canister_query <name>` と呼ばれるすべてのエクスポート関数の数が 1,000 を超えています。
- `canister_update <name>` または`canister_query <name>` と呼ばれるすべてのエクスポート済み関数の`<name>` の長さの合計が 20,000 を超えています。
- エクスポートされるカスタムセクションの合計サイズが 1MiB を超える場合。

これらの制限に関する詳細は、[Internet Computer インタフェース仕様に](https://internetcomputer.org/docs/current/references/ic-interface-spec/#system-api-module)記載されています。

<!---
# Resource constraints and limits on the Internet Computer

## Overview

This section defines the current main constraints regarding resource usage on the Internet Computer that developers should be aware of.

## Resource constraints and limits

| Resource          | Constraint            |
|-------------------|-----------------------|
| Canister queue limit | 500 messages        |
| Maximum ingress and cross-net inter-canister call payload | 2MB |
| Maximum same-subnet inter-canister call payload (may be deprecated at some point)| 10MB |
| Maximum response size | 2MB |
| Instruction limit per update call/heartbeat/timer | 20B per method invocation |
| Instruction limit query calls | 5B |
| Instruction limit for canister install and upgrade | 200B |
| Subnet capacity | 450GB |
| Wasm heap size | 4GB |
| Wasm stable memory | 64GB |
| Wasm custom sections| 2GB per subnet; 1MB per canister; 16 sections at most (per canister)|
| Wasm code section | 10MB |
| Query calls execution threads | 2 per replica node |
| Update calls execution threads| 4 per subnet |


## Additional notes

The IC may reject WebAssembly modules for reasons such that:

- They declare more than 50,000 functions.
- They declare more than 300 globals. 
- They declare more than 16 exported custom sections (the custom section names with prefix icp:).
- The number of all exported functions called `canister_update <name>` or `canister_query <name>` exceeds 1,000.
- The sum of `<name>` lengths in all exported functions called `canister_update <name>` or `canister_query <name>` exceeds 20,000.
- The total size of the exported custom sections exceeds 1MiB.

More information regarding these restrictions can be found in the [Internet Computer interface specification](https://internetcomputer.org/docs/current/references/ic-interface-spec/#system-api-module).

-->
