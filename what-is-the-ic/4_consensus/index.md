---

title: Low-latency high-throughput consensus
card: /img/what-is-the-ic/consensus.webp
cardImageFit: cover
---
Internet Computer Protocol は、サブネットの複数のノードに障害が発生したり、誤動作した場合でも、どのサブネットでもcanister のステートが常に一貫していることを保証します。IC はプルーフ・オブ・ワークでもプルーフ・オブ・ステークでもなく、いわゆる DAO 制御ネットワークであり、NNS DAO がサブネットのノードメンバーシップを管理します。[コンセンサスプロトコルは](/how-it-works/consensus/)以下のような特性を持ちます：

- 低レイテンシ - 少数のラウンド交換で合意に達します。コンセンサスは通常1～2秒以内に達成されます。
- 高いスループット - すべてのコンセンサス実行は、メガバイトのオーダーのペイロードを扱うことができます。
- 暗号学的な最終性 - ICコンセンサスは、暗号学的に保証された最終性、つまり、確定されたステート変更は元に戻せません。
- ビザンチンフォールトトレランス（BFT） - ICは、任意に故障したノードの3分の1まで（ただしそれ以下）耐えることができます。

<!---


The Internet Computer Protocol ensures that the canister state on any subnet is always consistent—even if multiple nodes of a subnet are faulty or misbehave. The IC is neither a proof-of-work, nor a proof-of-stake network, but a so-called DAO-controlled network, where the NNS DAO manages subnet node membership. The [consensus protocol](/how-it-works/consensus/) has the following properties:

* Low latency – A small number of rounds of exchange suffice to reach agreement. Consensus is normally reached within 1 to 2 seconds.
* High throughput – Every consensus execution can handle payloads in the order of megabytes.
* Cryptographic finality – IC consensus reaches cryptographically-guaranteed finality, that is, finalized state changes cannot be undone.
* Byzantine fault tolerance (BFT) – The IC can tolerate up to (but less than) one third of arbitrarily faulty nodes.

-->
