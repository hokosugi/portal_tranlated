---

title: Overview
---
![](/img/how-it-works/ic-overview.jpg)

# 概要

Internet Computer は、ユーティリティ・トークンである ICP トークンの名前の由来であるInternet Computer Protocol (ICP) によって運営されています。
IC プロトコルの中核部分である*コア IC プロトコルは*、各サブネットのノード上で実行される 4 層プロトコルです。
コアICプロトコルを実行することで、サブネットのノードはブロックチェーンベースの*複製ステートマシンを*実現し、他のサブネットとは独立して進捗します（ただし、サブネットとは非同期で通信します）。
多数のサブネットが同時に動作するこのアーキテクチャにより、ICは実質的に無制限に拡張することができます。
サブネットは、ユーザーによって投稿された*メッセージや*他のサブネットから送られてきたメッセージを処理します。

コアとなるICプロトコルは、下から順に以下の4つのレイヤーで構成されています：

1.  ピアツーピア
2.  コンセンサス
3.  メッセージ・ルーティング
4.  実行

下位の2つのレイヤ、P2Pとコンセンサスは、一緒に受信メッセージの*選択と順序付けを*実装し、*ブロックの*形で上位の2つのレイヤにメッセージを提供します。
上位の2つのレイヤ、メッセージルーティングと実行は、スタックの下位部分から順序付けられたメッセージを含むブロックを受信し、サブネットのすべてのノード上で完全に決定論的な方法でそれらを実行します。
これは複製されたステートマシンを実現し、サブネットのすべてのノードがラウンドごとに同じ開始ステートから同じ終了ステートへ遷移します（すべてのノードが同じメッセージを同じ順序で、つまり完全に決定論的に実行することが保証されなければなりません）、完全に決定論的に)。

[より深く](/how-it-works/core-ic-protocol-overview/)

<!---


![](/img/how-it-works/ic-overview.jpg)

# Overview

The Internet Computer is powered by the Internet Computer Protocol (ICP), from which its utility token, the ICP token, derives its name.
The core part of the IC protocol, the *core IC protocol*, is a 4-layer protocol that is running on the nodes of each subnet.
By running the core IC protocol, the nodes of a subnet realize a blockchain-based *replicated state machine* that makes progress independently of the other subnets (but communicates asynchronously with them).
This architecture of many concurrently-operating subnets enables the IC to scale practically without limits.
Subnets process *messages*, which are submitted by users or come from other subnets.

The core IC protocol comprises the following four layers, from bottom to top:
1. Peer-to-peer
2. Consensus
3. Message routing
4. Execution

The lower two layers, P2P and consensus, together implement a *selection and ordering* of incoming messages and provide messages to the upper two layers in the form of *blocks*.
The upper two layers, message routing and execution, receive blocks containing ordered messages from the lower part of the stack and execute them in a completely deterministic manner on every node of the subnet.
This realizes a replicated state machine, where every node in the subnet transitions from the same starting state to the same ending state in every round (it must be ensured that every node executes the same messages in the same order, i.e., fully deterministically).

[Go deeper](/how-it-works/core-ic-protocol-overview/)

-->
