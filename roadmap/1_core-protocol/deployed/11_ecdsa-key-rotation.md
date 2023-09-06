---

title: ECDSA key rotation and resharing
links:
  Forum Link: https://forum.dfinity.org/t/threshold-ecdsa-signatures/6152/245
eta: 2022
is_community: true
in_beta: false
---
閾値ECDSAの機能を可能な限り安全にするため、すべてのECDSA秘密共有は秘密鍵を再共有することで定期的に更新されます。この分散鍵生成プロトコルで使用される暗号化鍵もノードによって定期的に更新されます。これにより、攻撃者が十分な数のECDSA秘密鍵を盗むことが難しくなります。

<!---


To make the threshold ECDSA feature as secure as possible, all ECDSA secret shares are periodically refreshed by resharing the secret key. The encryption keys that are used in this distributed key generation protocol are also regularly updated by the nodes. This makes it harder for an attacker to steal sufficiently many ECDSA key shares, as the attack now has to be performed in a small time window. 
-->
