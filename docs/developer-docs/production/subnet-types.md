# サブネットの種類

Internet Computer で利用可能なサブネット・タイプはさまざまです。これらのタイプは、
サブネットのプロパティに関連しており、さまざまなユースケースに適しています。

## 利用可能なタイプ

- `system` サブネット：これらのサブネットは、 の不可欠な部分である のために予約されています。通常、これらのサブネット上の は NNS によって制御され、 に支払うことはありません。ユーザーはこれらのサブネットに 。Internet Computer canisters canisters cycles canisters 
- `application` サブネット：これらのサブネットは、ユーザーが 。これらのサブネットのサイズは通常13ノードで、 、 を支払う必要があります。ユーザーが特別な要件を提供しない場合、ランダムなアプリケーションサブネットが作成先として選択され、 。canisters canisters cycles canister

これらの基本的なタイプに加えて、
 dapps にとって有用な特定の追加特性を提供する特殊なタイプのサブネットもあります。そのような特殊なタイプの最初のものは「Fiduciary」で、基本的に
アプリケーション・サブネットの大型バージョンです。より多くのノードを持つことで、「Fiduciary」サブネット上で実行されているdapps に対して、より優れたセキュリティ保証が提供されます。
その代償として、cycles コスト面でより高価になります（コストはノード数に対して線形に増加します。
詳細は[こちらを](../gas-cost.md)参照）。

<!---
# Subnet types

There are different subnet types available on the Internet Computer. These types are related to properties of the
subnets which make them suitable for different use cases.

## Available types

* `system` subnets: These subnets are reserved for canisters that are an integral part of the Internet Computer. Typically, canisters on these subnets are controlled by the NNS and they don't pay cycles. Users cannot deploy canisters on those subnets.
* `application` subnets: These are the default subnets that users can deploy canisters to. They typically have a size of 13 nodes and canisters on them have to pay cycles. If a user does not provide any specific requirements a random application subnet is chosen as the destination to create the canister.

On top of these basic types, there are also specialized types of subnets that offer certain additional properties that
might be useful to dapps. The first such specialized type is "Fiduciary" which is essentially a larger version of an
application subnet. Having more nodes provides better security guarantees to dapps running on a "Fiduciary" subnet at
the expense of being more expensive in terms of cycles cost (costs scale linearly to number of nodes, for more details
see [here](../gas-cost.md)).

-->
