---

title: Direct Bitcoin Integration
links:
  Forum Link: https://forum.dfinity.org/t/direct-integration-with-bitcoin/6147
  Proposal: https://dashboard.internetcomputer.org/proposal/20586
  IC Wiki: https://wiki.internetcomputer.org/wiki/Bitcoin_integration
  Documentation: https://internetcomputer.org/bitcoin-integration
eta: 2022
is_community: false
in_beta: false
---
ビットコイン・ブロックチェーンとInternet Computer の直接統合により、canister スマート・コントラクトがビットコインを受け取り、保有し、送金することが可能になります。この機能では、追加の信頼前提も、ブリッジのような追加の当事者も必要ありません。ビットコインの統合は、サブネットが秘密共有鍵でcanister に代わって署名することを可能にする閾値ECDSA署名に依存しています。

<!---


Direct integration of the Internet Computer with the Bitcoin blockchain enables canister smart contracts to receive, hold and transfer bitcoin. With this feature, neither additional trust assumptions, nor additional parties, such as bridges, are required. Bitcoin integration relies on threshold ECDSA signatures that make it possible for a subnet to sign on behalf of a canister with a secret-shared key.

-->
