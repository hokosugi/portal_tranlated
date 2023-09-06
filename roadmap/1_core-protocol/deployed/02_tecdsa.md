---

title: Threshold ECDSA Signatures
links:
  Forum Link: https://forum.dfinity.org/t/threshold-ecdsa-signatures/6152
  Proposal: https://dashboard.internetcomputer.org/proposal/21340
  Documentation: https://internetcomputer.org/docs/current/developer-docs/integrations/t-ecdsa/
eta: 2022
is_community: false
in_beta: false
---
この機能により、canister スマート・コントラクトはECDSA公開鍵を持ち、それに基づいて署名することができます。対応する秘密鍵は、大規模なサブネットのノード間で閾値共有されます。閾値ECDSA署名は、Internet Computer 、ビットコイン、イーサリアム、そしておそらく将来的にはさらなるECDSAベースのブロックチェーンとの直接統合のための前提条件です。

<!---


This feature enables canister smart contracts to have an ECDSA public key and to sign with regard to it. The corresponding secret key is threshold-shared among the nodes of a large subnet. Threshold ECDSA signatures are a prerequisite for the direct integration between the Internet Computer and Bitcoin, Ethereum, and possibly further ECDSA-based blockchains in the future.

-->
