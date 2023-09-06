---

title: Improved I/O Handling in State Manager
links:
  Forum Link: https://forum.dfinity.org/t/long-term-r-d-scalability-proposal/9387
  Proposal:
eta: 
is_community: false
---
この機能は、ステートマネージャのディスクとのインタラクション方法の改善をまとめたものです。相互作用の数を減らしたり、ステートマシンのループから取り出したりすることで、ステート・マネージャーが実行をブロックしたり、他の方法で妨害したりするインスタンスを減らすことができます。この機能での作業は、ICがスケーラビリティ要件に対応できるようにするために重要です。

<!---


This feature is a collection of improvements to how we interact with the disk in the state manager. By reducing the number of interactions, or taking them out of the state machine loop, we can reduce the instances where the state manager blocks, or otherwise interferes with, execution. The work in this feature is important to make sure that the IC can keep up with the scalability requirements.

-->
