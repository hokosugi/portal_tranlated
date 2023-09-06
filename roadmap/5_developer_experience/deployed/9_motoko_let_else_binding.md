---

title: Motoko let-else Binding
links:
  Forum Link: https://forum.dfinity.org/t/solution-in-moc-0-8-3-let-else-match-and-take-in-motoko-do-for-variants-was-when/13427/6
  Proposal:
eta: Q1 23
is_community: true
---
このMotoko 言語機能により、バインディングに失敗した場合に失敗ブロックを実行することができます。この
機能の主な動機は、可読性の低いコードにつながる深い入れ子の switch 文を避けることです。

<!---

This Motoko language feature allows a failure block to be run in case of a binding failure. The main motivation for this
feature is to avoid deeply nested switch statements that lead to less readable code.

-->
