# 分散型データセンター

## 概要

Internet Computer ブロックチェーンは、物理的な場所に存在する物理的なハードウェアではありません。その代わりに、Internet Computer ブロックチェーンは、世界中で独立して運営されているデータセンターが提供するコンピューティングリソースを組み合わせています。

パブリッククラウドやプライベートクラウドとは異なり、Internet Computer ブロックチェーンは単一の民間企業によって所有・運営されているわけではありません。その代わりに、Internet Computer ブロックチェーンは、プロトコルで定義されたアルゴリズムによる分散型ガバナンスシステムによって更新と運用が管理される公共ユーティリティです。そのアーキテクチャは、複数のコンピュータが1つの非常に強力な仮想マシンのように動作することを可能にします。

Internet Computer を構成する世界中のデータセンターに配置されたノードは、サブネットのブロックチェーンに編成され、チェーンキー暗号を使用して相互に接続されます。分散アーキテクチャにより、ファイアウォールや攻撃を受けやすい技術を使わずに安全な通信が可能になります。独立したノード運営者は、データセンターにノードのホスティングを依頼し、Internet Computer ブロックチェーン上で実行されるdapps をサポートするためにコンピューティング能力とホスティングサービスを提供することで報酬を受け取ります。

## サブネットとデータセンター

潜在的なサービスの中断に耐えられる真の分散型ブロックチェーンを提供するため、任意のブロックチェーンのサブネットを構成する物理的なノードは、さまざまな場所にあるデータセンターに分散されています。ノード自体は、さまざまな当事者によって所有または提供され、提携している場合もあれば、運用されているデータセンターの場所とは無関係の場合もあります。

次の図は、4つのデータセンターにノードがあるサブネットを簡略化したものです。

![data center simplified](_attachments/data-center-simplified.svg)

この単純化したシナリオでは

- 地理的に独立した4つのデータセンターがあります。

- 各データセンターには、複数のノードプロバイダから供給されたハードウェアがあります。

- 1つのノード・プロバイダが複数のデータ・センターに装置を持つこともあります。

この例は複数のデータセンターにノードを持つ1つのサブネット・ブロックチェーンを表していますが、必要に応じてノードのいずれかをこのブロックチェーン・サブネットから移動して、新しいブロックチェーン・サブネットを形成することもできます。ネットワーク・トポロジーの変更は、Internet Computer ブロックチェーン・ガバナンス・システム **Network Nervous System**(NNS）を通じて管理されます。

## ノードプロバイダーとデータセンター事業者

ほとんどの場合、ノード・プロバイダまたは提携するデータセンター事業者は、Internet Computer ブロックチェーンが稼働する機器の計算能力を監視し、維持する責任を負います。例えば、ノードプロバイダーやデータセンター事業者は、ハードウェアに障害が発生した場合やシステムのパフォーマンスが低下した場合に、機器の修理や交換を行う必要があるかもしれません。しかし、ネットワークの運用とアップグレードは、分散型ガバナンスシステムであるNetwork Nervous System (NNS)を通じて監視・管理されます。

## リソース

データセンターの運用やノードプロバイダーに関する詳しい情報をお探しの方は、以下の関連リソースをご覧ください：

- [Internet Computer Wiki：ノードプロバイダのドキュメント](https://wiki.internetcomputer.org/wiki/Node_Provider_Documentation)
- [Internet Computer ](https://wiki.internetcomputer.org/wiki/Node_provider_hardware)Wiki：[ノードプロバイダのハードウェア](https://wiki.internetcomputer.org/wiki/Node_provider_hardware)
- [Internet Computer ](https://wiki.internetcomputer.org/wiki/Node_Provider_Onboarding)Wiki：ノード[プロバイダーのオンボーディング](https://wiki.internetcomputer.org/wiki/Node_Provider_Onboarding)

<!---
# Decentralized data centers

## Overview

The Internet Computer blockchain is not physical hardware that exists in any physical location. Instead, the Internet Computer blockchain combines computing resources provided by independently-operated data centers around the world.

Unlike a public or private cloud, the Internet Computer blockchain is not owned and operated by a single private company. Instead, the Internet Computer blockchain is a public utility with updates and operations that are managed through an algorithmic, decentralized governance system defined in the protocol. Its architecture enables multiple computers to operate like one, very powerful, virtual machine.

The nodes located in data centers around the globe that make up the Internet Computer are organized into subnet blockchains that in turn connect to each other using Chain Key cryptography. The distributed architecture enables secure communication without firewalls or technologies that are vulnerable to attack. Independent node operators pay data centers to host their nodes and receive remuneration for contributing computing capacity and hosting services to support dapps running on the Internet Computer blockchain.

## Subnets and data centers

To provide a truly decentralized blockchain that can withstand potential service disruptions, the physical nodes that make up any given blockchain subnet are distributed across data centers in diverse locations. The nodes themselves might be owned or provided by different parties in partnership or unaffiliated with the data center location where they operate.

The following diagram provides a simplified view of a subnet with nodes in four data centers.

![data center simplified](_attachments/data-center-simplified.svg)

In this simplified scenario:

-   There are four geographically-independent data centers.

-   Each data center has hardware supplied by multiple node providers.

-   Any single node provider might have equipment in multiple data centers.

Although this example represents one subnet blockchain with nodes in multiple data centers, any of the nodes could be moved out of this blockchain subnet to form a new blockchain subnet, if needed. Changes to the network topology are managed through the Internet Computer blockchain governance system called the **Network Nervous System** (NNS).

## Node providers and data center operators

In most cases, node providers—or the data center operators they partner with—are responsible for monitoring and maintaining the compute capacity of the equipment on which the Internet Computer blockchain runs. For example, node providers or data center operators might need to repair or replace equipment if there’s a hardware failure or if a system under-performs. Network operations and upgrades, however, are monitored and managed through the decentralized governance system, the Network Nervous System (NNS).

## Resources

If you are looking for more information about data center operations and node providers, check out the following related resources:

- [Internet Computer Wiki: node provider documentation](https://wiki.internetcomputer.org/wiki/Node_Provider_Documentation).
- [Internet Computer Wiki: node provider hardware](https://wiki.internetcomputer.org/wiki/Node_provider_hardware).
- [Internet Computer Wiki: node provider onboarding](https://wiki.internetcomputer.org/wiki/Node_Provider_Onboarding).

-->
