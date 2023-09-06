---

title: Deterministic Time Slicing
links:
  Forum Link: https://forum.dfinity.org/t/deterministic-time-slicing/10635
  Proposal:
eta: 2023
is_community: false
in_beta: true
---
決定論的タイムスライシングは、あるラウンドの終了時に実行を中断し、次のラウンドで再開することで、長時間実行される複数ラウンドの計算を可能にします。

この機能は現在、すべてのアプリケーションと検証済みアプリケーションのサブネットで有効になっています。
クエリを除くすべてのメッセージは自動的にスライスされ、複数ラウンドで実行されます。
このようなメッセージの命令上限が50億命令から200億命令に増加しました。
「設定可能なワズム・ヒープ上限」機能が出荷された後、さらに増加する予定です。

<!---


Deterministic time slicing allows for long(er) running, multi-round computations by suspending the execution at the end of one round and resuming it in the next.

The feature is currently enabled on all application and verified application subnets.
All messages except for queries are automatically sliced and executed in multiple rounds.
The instruction limit for such messages has been increased from 5 billion instructions to 20 billion instructions.
Further increases will follow after the "Configurable Wasm Heap Limit" feature ships.

-->
