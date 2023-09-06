---

title: Consensus
---
![](/img/how-it-works/consensus.webp)

# コンセンサス

コンセンサスはICのサブネットを動かすコアICプロトコルのコンポーネントです。
各サブネットは他のサブネットから独立して、コンセンサスを含むICコアプロトコルを実行するブロックチェーンです。
コンセンサス・プロトコルの目的は、サブネットの各ノードが決定論的にメッセージを実行する際に同じステート遷移を行えるように、あるラウンドで同じ順序のメッセージ・ブロックを出力することです。

ICのコンセンサスプロトコルは、次の要件を満たすように設計されています。
低レイテンシ（ほぼ瞬時の終局性）、
高スループット、
堅牢性（ノードやネットワークに障害が発生した場合に、レイテンシとスループットを優雅に劣化させる）。ICコンセンサスプロトコルはまた、*暗号的に保証された最終性を*提供します。これは、ビットコインのようなプロトコルが*確率的な最終性しか*提供しないのとは対照的であり、ブロックチェーンにおいて十分な数のブロックがその上に構築された時点でブロックが最終的なものとみなされます。

[より深く](/how-it-works/consensus/)

<!---


![](/img/how-it-works/consensus.webp)

# Consensus

Every blockchain needs a consensus mechanism that allows the nodes to agree on the messages to be processed, as well as their ordering.
Consensus is the component of the core IC protocol that drives the subnets of the IC.
Each subnet is a blockchain that runs the IC core protocol, including consensus, independently of the other subnets.
The purpose of the consensus protocol is to output the same block of ordered messages on each node of a subnet in a given round so that each node can make the same state transition when deterministically executing those messages.

The IC’s consensus protocol is designed to meet the following requirements:
low latency (almost instant finality);
high throughput;
robustness (graceful degradation of latency and throughput in the presence of node or network failures). The IC consensus protocol also provides *cryptographically guaranteed finality*. This is in contrast to Bitcoin-like protocols which only provides *probabilistic finality*, where a block is considered final once a sufficient number of blocks have built on top of it in the blockchain.

[Go deeper](/how-it-works/consensus/)

-->
