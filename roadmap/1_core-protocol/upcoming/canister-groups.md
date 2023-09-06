---

title: Canister groups
links:
  Forum Link: https://forum.dfinity.org/t/canister-groups/16015
  Proposal: 
eta:
is_community: false
---
同じサブネット上のcanisters 間の通信は、サブネット間よりもかなり高速です。  canister 間の通信レイテンシはあまり気にする必要がないため、canisters のコロケーションが保証されている限り、マルチcanister アーキテクチャによってdapps を拡張することができます。

今後のロードバランシングメカニズムはサブネット間でcanisters を移動させる必要があるので、同じdapp の一部であるcanisters を分割しないようにする必要があります。Canister グループがあれば、dapp の開発者は、どのcanisters が一緒に「属して」いて、どの が常に同じサブネットにあるべきかを明示的に示すことができます。

<!---


Communication between canisters on the same subnet is significantly faster than between subnets.  Since inter-canister communication latency is less of a concern, one can scale dapps through multi-canister architectures, as long as the canisters are guaranteed to be collocated. 

Since upcoming load-balancing mechanisms need to move canisters between subnets, we need to avoid splitting up canisters that are part of the same dapp.   Canister groups would allow a dapp developer to explicitly indicate which canisters "belong" together and which should always be located on the same subnet.


-->
