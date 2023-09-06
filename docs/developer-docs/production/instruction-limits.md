# 指導の制限

## 概要

Internet Computer スマートコントラクトのメッセージを実行するためのプラットフォームとしてWebAssembly を使用します。
 WebAssembly は[チューリング完全](https://en.wikipedia.org/wiki/Turing_completeness)であるため、非終了計算を含むさまざまな種類の計算を表現できます。
 Internet Computer は、メッセージの実行ごとにWebAssembly の命令数を制限することで、非終了計算から保護します。
命令数の制限は、次の表に示すようにメッセージの種類によって異なります。

| メッセージ・タイプ | 命令数制限 |
| --- | --- |
| 更新メッセージ | 200 億命令 |
| クエリ・メッセージ | 50億命令 |
| ハートビートとタイマー | 50億命令 |
| Canister インストールとアップグレード | 2,000億命令 |

:::info
アップデートメッセージ、ハートビート、タイマーの命令上限は近い将来300億命令まで引き上げられる[予定](https://forum.dfinity.org/t/deterministic-time-slicing/10635)です。
::：

<!---
# Instruction Limits

## Overview
The Internet Computer uses WebAssembly as the platform for executing messages of smart contracts.
Since WebAssembly is [Turing-complete](https://en.wikipedia.org/wiki/Turing_completeness), it can express different kinds of computations including non-terminating computations.
The Internet Computer protects against non-terminating computations by limiting the number of WebAssembly instructions per message execution.
The instruction limit depends on the message type as shown in the following table.

| Message type                 | Instruction limit        |
|------------------------------|--------------------------|
| Update messages              | 20 billion instructions  |
| Query messages               | 5 billion instructions   |
| Heartbeats and timers        | 5 billion instructions   |
| Canister install and upgrade | 200 billion instructions |

:::info
The instruction limit for update messages, heartbeats, and timers is [planned](https://forum.dfinity.org/t/deterministic-time-slicing/10635) to be raised to 30 billion instructions in near future.
:::

-->
