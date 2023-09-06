---

title: Chain-key cryptography underpins the IC's security architecture
card: /img/what-is-the-ic/chainkey.webp
cardImageFit: cover
---
サブネットの正しい運用（およびサブネット間通信）は、チェーン[*キー暗号と*](/how-it-works/chain-key-technology/)総称される一連の新しい暗号プロトコルに依存しています。チェーン・キー暗号は、サブネットがユーザーの要求に対する応答を認証することを可能にします。

- を認証することを可能にします、
- サブネットのステート、およびサブネット間メッセー
- サブネット間メッセージ
  を完全に分散化された方法で認証することを可能にします。

NNSは、分散型認証局のようにサブネットの公開鍵を承認します。ユーザーはNNSの48バイトのBLS公開鍵だけで、どのcanister とのやり取りも検証できます。従来のブロックチェーンでは、新しく参加した当事者はチェーン上で実行されたすべてのトランザクションをやり直す必要があり、ICのような高スループット・システムでは実現不可能です。

ICは連鎖鍵暗号方式を採用しています：

- サブネットのメンバー変更 - レプリカは、最新の有効なチェックポイントからサブネットに参加することも、任意の時点でサブネットから離脱することもできます。
- プロアクティブなセキュリティ - サブネットの閾値鍵は、サブネットの現在のノード間で定期的に再共有されます。
- 永続的な公開鍵 - メンバーシップの変更や鍵の再共有は、どのサブネットの公開鍵にも影響しません。
- ガベージコレクション - ストレージが無限に増えるのを防ぐため、定期的に各サブネットのブロックチェーンから以前のブロックが削除されます。

<!---


The correct operation of subnets (and inter-subnet communication) relies on a suite of novel cryptographic protocols, collectively referred to as [*chain-key cryptography*](/how-it-works/chain-key-technology/). Chain-key cryptography makes it possible for subnets to authenticate
* responses to user requests,
* the subnet state, and
* inter-subnet messages
in a completely decentralized way.

The NNS endorses the public keys of subnets, much like a decentralized certification authority. Users only need the 48-byte BLS public key of the NNS to validate the interaction with any canister. Traditional blockchains typically require newly joined parties to redo all transactions ever performed on the chain, which is not feasible in a high-throughput system like the IC. 

The IC uses chain-key cryptography to provide:
* Subnet membership changes – A replica can join a subnet, by starting from the most recent valid checkpoint, or leave at any point in time.
* Proactive security – Threshold keys of the subnet are periodically reshared between the current nodes of the subnet.
* Permanent public keys – Membership changes and key resharing do not affect the public key of any subnet.
* Garbage collection – Periodically, previous blocks are pruned from each subnet blockchain to prevent storage from growing infinitely.


-->
