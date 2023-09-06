---

title: Off chain observability stack for Node Providers
links:
  Forum Post:
  Proposal:
eta:
is_community: false
---
ノード・プロバイダは、a) 自分のノードからのログやメトリクスにアクセスすることができません。そのため、ノードプロバイダは自分のノードの健康状態を監視することができません。目的は、ノードプロバイダが独自にノードの健全性をトリアージし、ノードを再デプロイする必要があるかどうかを決定し、ノードが不健全である根本的な原因を独自に理解できるように、観測可能なソリューションを利用できるようにすることです。

<!---


The node providers a) do not have access to the logs and metrics from their node, and b) do not have access to the alerts we have in place. Therefore, the node providers can not monitor the health status of their nodes. The objective is to make a observability solution available for node providers that allows them to independently triage the node health and decide whether a nodes needs to be redeployed, and to independently understand an underlying cause of a node being unhealthy.

-->
