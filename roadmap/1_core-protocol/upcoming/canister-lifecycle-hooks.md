---

title: Canister Lifecycle Hooks
links:
  Forum Link: https://forum.dfinity.org/t/canister-lifecycle-hooks/17089
  Proposal: https://dashboard.internetcomputer.org/proposal/106146
eta:
is_community: true
---
現在、開発者は、cycle の残高とメモリ使用量を定期的にポーリング
することによって、canisters を積極的に監視しなければなりませんcanisters 。定期的な
ポーリングは、リソース使用量の点で非効率的であり、多数のcanisters を持つ
dapps を維持するのは困難です。

この機能は、canisters の監視と観測性を改善することを目的としています。
はプッシュ・モデルを導入しており、
のcycles とメモリが不足すると、canister に自動的に通知されます。

<!---


Currently, developers have to actively monitor their canisters by periodically
polling the cycle balance and the memory usage of the canisters. Periodic
polling is inefficient in terms of resource usage and difficult to maintain for
dapps with many canisters.

This feature aims to improve the monitoring and observability of canisters by
introducing a push model, where the canister is automatically notified when it
is low on cycles and memory.

-->
