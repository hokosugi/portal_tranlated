---

title: Future Boundary-Node Architecture
links:
  Forum Link: https://forum.dfinity.org/t/boundary-node-roadmap/15562
  Proposal: https://dashboard.internetcomputer.org/proposal/35671
eta:
is_community: false
---
この機能はICのエッジに新しいアーキテクチャを導入します。現在のバウンダリノードを、NNS制御でAPIコールのルーティングを担当する*APIノードと*、自己管理でTLSの終了、サービスワーカーの提供、HTTPリクエストのAPIコールへの変換を担当する*HTTPゲート*ウェイに分割します。

<!---


This feature introduces a new architecture for the IC's edge. It splits today's boundary nodes into _API nodes_, which are NNS-controlled and responsible for the routing of API calls, and _HTTP gateways_, which are self-managed and responsible for terminating TLS, serving the service worker, and translating HTTP requests to API calls.

-->
