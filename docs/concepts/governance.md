# Neuronガバナンス

## 概要

分散型ブロックチェーンとして、Internet Computer の構成と動作に対するすべての変更は、Network Nervous System (NNS) と呼ばれるガバナンス機関によって制御されます。NNSは以下を含むInternet Computer ブロックチェーンの多くの側面を制御します：

- どのデータセンター・プロバイダーがネットワークに参加するか。

- データセンター・プロバイダーから受け入れられるノードの数、場所、所有権。

- サブネットのブロックチェーンへのノードの割り当て。

- canisters 、または新しいプロトコルバージョンへのアップグレードの可否。

さらに、Internet Computer レプリカのアップグレードやInternet Computer protocol の修正要求を採択または却下する投票を行えるのは、ガバナンス機関のメンバーのみです。

## 利害関係者の投票権

Internet Computer 上のトークンは一般的に流動的であるため、ガバナンスの目的に使用するためには、保有者側の十分な安定したコミットメントを表すものではありません。責任あるガバナンスに必要な安定性を提供するため、トークンは**neurons に変換できます。** neuron は、最低期間（ロックアップ期間）交換できない ICP トークンの数を表します。

個人または組織がある程度の数のICPトークンをneuron にロックアップしている場合、neuron の保有者は議案に対する投票権を持ち、ロックアップされたICPの数、ロックアップ期間の長さ、相対的な投票数に比例して投票に対する報酬が支払われます。

Internet Computer ブロックチェーンは、neuronにロックアップされた ICP トークンの数を追跡し、ICP トークンのアカウント残高の管理と連動して投票に必要なロジックを提供します。

<!---
# Neurons and governance

## Overview 
As a decentralized blockchain, all changes to the configuration and behavior of the Internet Computer are controlled by a governance body called the Network Nervous System (NNS). The NNS controls many aspects of the Internet Computer blockchain including the following:

-   Which data center providers participate in the network.

-   The number, location, and ownership of the nodes accepted from a data center provider.

-   Assignment of nodes to subnet blockchains.

-   Whether upgrades to canisters or to a new protocol version are allowed or not.

In addition, only members of the governance body can vote to adopt or reject requests to upgrade Internet Computer replicas or modify the Internet Computer protocol.

## Voting rights for stakeholders

Because tokens on the Internet Computer are generally liquid, they do not represent a stable enough commitment on the part of their holders for them to be used for governance purposes. To provide the stability required for responsible governance, tokens can be converted to **neurons**. A neuron represents a number of ICP tokens that cannot be exchanged for a minimum period of time (the lock-up period).

When a person or organization has some number of ICP tokens locked up in a neuron, the neuron holder has the right to vote on proposals, and to be paid for voting in proportion to the number of ICP locked up, the length of the lock-up period and the relative number of votes cast.

The Internet Computer blockchain tracks the number of ICP tokens that are locked up in neurons and provides the logic necessary for voting in conjunction with managing ICP token account balances.

-->
