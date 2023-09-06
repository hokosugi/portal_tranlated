---

title: Certified Queries
links:
  Forum Link:
  Proposal:
eta:
is_community: false
---
クエリーコールは非常に優れたパフォーマンスを持っていますが、静的なアセットを提供する場合にのみ、認証を見送ったり、セキュリティをあきらめたりする必要がありません。認証されたクエリでは、バウンダリノードが一度に複数のレプリカにクエリを発行し、それらの署名された応答を集約してクライアントに送り返すことで、すべてのクエリコール（動的な応答であっても）を安全にすることができます。

<!---


While query calls have very good performance, they only allow to serve static assets without having to forgo certification and giving up security. Certified queries allow to secure all query calls (even for dynamic responses) by having the boundary nodes issuing the query to multiple replicas at once, aggregating their signed responses and sending that back to the client.

-->
