# cycles 管理サービスの利用

## 概要

Internet Computer にデプロイされると、開発者はcanisters で使用するコンピュートとストレージの代金をcycles で支払う必要があります。cycles をバーンしてcanister に転送するプロセスは、canister の「トップアップ」と呼ばれます。

Cycles 管理サービスは、 の監視と自動化された トップアップを提供し、 アプリケーションが稼働し続け、 が不足しないようにします。canister cycles canisters cycles

`dfx` 経由でcanister を手動でトッピングしたり、カスタムソリューションをスクリプト化する代わりに、cycles 管理サービスはテスト済みのトッピング自動化とcanister メトリックインサイトを提供します。

## 人気のcycles 管理サービス

cycles 人気の管理サービスには以下が含まれます：

- [CycleOps](https://cycleops.dev)- オンチェーン、プロアクティブ、ノーコードcanister モニタリング（履歴トレンドグラフ、トップアップ電子メール通知、ダウンロード可能なトランザクション履歴付き）。
- [TipJar](https://k25co-pqaaa-aaaab-aaakq-cai.ic0.app/)-Internet Computer 上のお気に入りのcanisters にcycles を寄付し、彼らを生き生きと健康に保ちます！

## Cycles 管理ライブラリ

### Motoko

- [cycles-manager](https://github.com/CycleOperators/cycles-manager)- マルチcanister アプリケーション用の、簡素化されたパーミッション付きのcycles 管理フレームワークを提供します (CycleOps がスポンサー)。

<!---
# Using a cycles management service

## Overview

Once deployed to the Internet Computer, developers need to pay in cycles for the compute and storage utilized by their canisters. The process of burning cycles and transferring them to a canister is referred to as "topping-up" a canister.

Cycles management services provide canister monitoring and automated cycles top-ups to ensure canisters applications remain up and running and do not run out of cycles.

Instead of manually topping up a canister via `dfx` or scripting a custom solution, cycles management services provide tested top-up automation and and canister metric insights.

## Popular cycles management services
Popular cycles management services include:

* [CycleOps](https://cycleops.dev) - On-chain, proactive, no-code canister monitoring with historical trend graphs, topup email notifications & downloadable transaction history.
* [TipJar](https://k25co-pqaaa-aaaab-aaakq-cai.ic0.app/) - Donate cycles to your favorite canisters on the Internet Computer and keep them live and healthy!

## Cycles management libraries

### Motoko

* [cycles-manager](https://github.com/CycleOperators/cycles-manager) - Provides a simplified, permissioned cycles management framework for multi-canister applications (sponsored by CycleOps).

-->
