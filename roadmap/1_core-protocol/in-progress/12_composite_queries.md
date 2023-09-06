---

title: Composite queries
links:
  Forum Link: https://forum.dfinity.org/t/proposal-composite-queries/15979
  Proposal: https://dashboard.internetcomputer.org/proposal/87599
eta: 2023
is_community: true
in_beta: false
---
Canisters には更新とクエリーの2種類のメソッドがあります。更新とは対照的に、クエリはコンポーザブルではありません。つまり、クエリーは他のクエリーを呼び出すことはできません。

コンポジットクエリーは、他のクエリーを呼び出すことができる新しいタイプのクエリーです。この機能により、開発者は複数のcanisters にまたがるデータをシャードするスケーラブルなdapps を簡単に構築できるようになります。

<!---


Canisters have two types of methods: updates and queries. In contrast to updates, queries are not composable. In other words, a query cannot call other queries.

A composite query is a new type of query that can call other queries. This feature will make it easier for developers to build scalable dapps that shard data across multiple canisters.

-->
