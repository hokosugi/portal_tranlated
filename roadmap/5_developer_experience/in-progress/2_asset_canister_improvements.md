---

title: Asset Caching
links:
  Forum Link:
  Proposal:
eta: Q4 22
is_community: false
---
バウンダリ・ノードは、非常に短い時間だけクエリをキャッシュします。アセット（HTMLページ、JSソース、画像など）はキャッシュされません。アセットcanister は、アセットがいつ期限切れになるかという TTL 情報を提供しません。この機能はアセットに有効期限情報を与え、サービスワーカーだけでなくバウンダリノードにも公開します。

<!---

Boundary nodes only cache queries for a very short amount of time. Assets (HTML pages, JS sources, images, etc) are not cached. The asset canister does not provide TTL information as to when the assets should expire. This feature gives the assets time-to-live information and expose it on the boundary nodes as well as the service worker.

-->
