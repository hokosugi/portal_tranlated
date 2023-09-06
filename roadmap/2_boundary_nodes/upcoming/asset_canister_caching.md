---

title: Asset Canister Caching
links:
  Forum Link: https://forum.dfinity.org/t/long-term-r-d-boundary-nodes-proposal/9401
  Proposal: https://dashboard.internetcomputer.org/proposal/35671
eta:
is_community: true
---
この機能は、アセット（HTMLページ、JSソース、画像など）にタイムトゥライブ情報（有効期限）を与え、バウンダリノードのNginxやサービスワーカーに公開することで、短時間のキャッシュクエリの問題を解決します。

<!---


This feature solves the problem of short-lived cache queries by giving assets (HTML pages, JS sources, images, etc.) time-to-live information (expiration times) and exposing them to Nginx on the boundary nodes as well as the service worker.

-->
