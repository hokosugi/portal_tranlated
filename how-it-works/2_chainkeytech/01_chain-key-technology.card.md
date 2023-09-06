---

title: Chain-key cryptography
---
![](/img/how-it-works/chain-key-cryptography.webp)

# チェーンキー暗号

Internet Computer protocol は、チェーン*キー暗号と*総称される高度な暗号メカニズムのツールボックスを使用しており、これによりICは他のブロックチェーンでは不可能な機能とスケーラビリティを実現することができます。

連鎖鍵暗号の主要な構成要素は*閾値署名スキームで*、これは通常のデジタル署名スキームと同じですが、サブネット内のレプリカの1つ（あるいは大部分）を侵害しても鍵が盗まれないように、秘密の署名鍵がサブネット内のすべてのレプリカに分散されている点が異なります。この技術には次のような多くの利点があります：

1.  ブロックチェーン全体を同期させることなく、署名を検証するだけで、誰でもInternet Computer 。
2.  ICのトポロジーを自律的に進化させることが可能 -- 新しいノードやサブネットを追加したり、故障したノードを復旧させたり、プロトコルを自律的にアップグレードすることが可能。
3.  canisters のための予測不可能で偏りのない擬似乱数のソース。Canisters は、ランダム性を必要とするアルゴリズムを安全に実行できます。

[より深く](/how-it-works/chain-key-technology/)

<!---


![](/img/how-it-works/chain-key-cryptography.webp)

# Chain-key cryptography

The Internet Computer protocol uses a toolbox of advanced cryptographic mechanisms, collectively known as *chain-key cryptography*, which allows the IC to achieve functionalities and scalability that are impossible on other blockchains.

A key component of chain-key cryptography is a *threshold signature scheme*, which is like an ordinary digital signature scheme, except that the secret signing key is distributed among all the replicas in a subnet in such a way that the key cannot be stolen by compromising one (or even a large fraction) of the replicas in the subnet. The technology has many benefits including:
1. Anyone can verify the content received from the Internet Computer by simply validating a signature without syncing the entire blockchain. 
2. The topology of IC can evolve autonomously -- New nodes and subnets can be added, faulty nodes can be recovered and protocol can be upgraded autonomously. 
3. A source of unpredictable and unbiasable pseudo-random numbers for canisters. Canisters can securely run algorithms that need randomness. 

[Go deeper](/how-it-works/chain-key-technology/)

-->
