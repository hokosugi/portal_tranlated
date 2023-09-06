---

title: Boundary nodes
abstract:
shareImage: /img/how-it-works/boundary-nodes.jpg
slug: boundary-nodes
---
# 境界ノード

境界ノードは、Internet Computer (IC)
のグローバルな分散エッジを形成し、canister スマートコントラクトへのすべてのアクセスがこれを経由します。バウンダリ・ノード
はICのパブリック・エンドポイントを提供し、すべての受信リクエストを
の適切なサブネットにルーティングし、レプリカ・ノード間でリクエストをロードバランスし、パフォーマンスを向上させるためにレスポンス
をキャッシュします。

<figure>
<img src="/img/how-it-works/boundary-nodes.webp" alt="Architecture of boundary nodes" title="Architecture of boundary nodes" align="center">
<figcaption align="left">
Boundary nodes are the gateway to the Internet Computer, which allow users to seamlessly access the canister smart contracts.
</figcaption>
</figure>

バウンダリ・ノードは、Internet Computer 上でホストされている
スマート・コントラクトcanister にアクセスする2つの方法を提供します。1つ目は、HTTPゲートウェイ
を介してストック・ブラウザを使用してアクセスする方法、2つ目は、APIバウンダリ・ノードを介してAPIcanister コール
を使用してアクセスする方法です。

HTTPゲートウェイにより、ユーザはWeb 2.0サービスにアクセスするのと同じように、ブラウザ（
）を使ってIC上でホストされているdapps 。この目的のために、
HTTPゲートウェイは、すべての着信HTTPリクエストをAPIcanister コールに変換し、
その後、適切なサブネットにルーティングされます。

APIバウンダリー・ノードにより、ICネイティブ・アプリケーションは
canister スマート・コントラクトを直接呼び出すことができます。この場合、境界ノードはAPI
canister 呼び出しを正しいサブネットにルーティングするだけです。したがって、
ユーザーとバウンダリ・ノード間の信頼は必要ありません。

HTTPゲートウェイとAPIバウンダリ・ノードの両方は現在、
バウンダリ・ノードに統合されています。この2つを2つの独立したサービスに分離する作業が進行中です。
完成すれば、APIバウンダリノードは完全にNNSの管理下に置かれます。
一方、HTTPゲートウェイはコミュニティの誰でも実行することができ、地域の管轄権へのコンプライアンス（
）を保証します。
[詳細については、フォーラムの更新情報をご覧](https://forum.dfinity.org/t/boundary-node-roadmap/15562)ください。

バウンダリ・ノードは、ICにアクセスするために提供する2つのエンドポイントに加えて、
、IC上でホストされるdapps
 のパフォーマンスを向上させるためのキャッシュも提供します。

## さらに深く

[IC Wikiのバウンダリ・ノード](https://wiki.internetcomputer.org/wiki/Boundary_Nodes)

<!---


# Boundary nodes

The boundary nodes form the globally distributed edge of the Internet Computer (IC)
through which all the accesses to the canister smart contracts go. The boundary
nodes provide a public endpoint for the IC and route all incoming requests to
the right subnet, loadbalance requests across replica nodes, and cache responses
for improved performance.

<figure>
<img src="/img/how-it-works/boundary-nodes.webp" alt="Architecture of boundary nodes" title="Architecture of boundary nodes" align="center">
<figcaption align="left">
Boundary nodes are the gateway to the Internet Computer, which allow users to seamlessly access the canister smart contracts.
</figcaption>
</figure>

The boundary nodes provide two ways of accessing canister smart contracts hosted
on the Internet Computer: first, one can access them using stock browsers through
the HTTP gateway, and second, one can access them using API canister calls
through the API boundary node.

The HTTP gateway allows users to access the dapps hosted on the IC through their
browsers the same way they are used to accessing any Web 2.0 service. To this end,
the HTTP gateway translates all incoming HTTP requests into API canister calls,
which are then routed to the right subnet.

The API boundary node allows IC native applications to directly call the
canister smart contracts. In this case, the boundary node simply routes the API
canister calls to the right subnet. Hence, no trust is required between the
user and the boundary node.

Both the HTTP gateway and the API boundary node are currently combined into the
boundary node. Work to separate the two into two independent services is ongoing.
Once complete, the API boundary nodes will be fully under the control of the NNS,
while the HTTP gateways can be run by anyone in the community ensuring compliance
with local jurisdictions.
[For more information check our updates in the forum.](https://forum.dfinity.org/t/boundary-node-roadmap/15562)

In addition to the two endpoints, the boundary nodes provide to access the IC,
the boundary nodes also provide caching to improve the performance of the dapps
hosted on the IC.

## Go Even Deeper

[Boundary Nodes on the IC Wiki](https://wiki.internetcomputer.org/wiki/Boundary_Nodes)

-->
