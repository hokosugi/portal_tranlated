---

title: Canisters
---
![](/img/how-it-works/canisters.webp)

# Canisters

Internet Computer 、スマート・コントラクトは次のような形態で提供されます。 *canisters*コードとステートを束ねた計算ユニット。各canister は、他のcanisters や、ブラウザやモバイルアプリなど IC の外部の関係者が呼び出すことができる関数を定義します。
Canisters は非同期メッセージを介して互いに通信しますが、そのような各メッセージの実行は完全に分離されて行われるため、大規模なレベルの同時実行が可能です。
Canisters はコントローラによって管理されます。canisters の制御構造は、集中型（コントローラに集中型エンティティが含まれる場合など）、分散型（コントローラがDAOの場合）、あるいは存在しないこともあり、その場合、canister は不変のスマートコントラクトです。
コントローラは、canister をInternet Computer にデプロイし、その実行を開始/停止し、コードを更新できる唯一のエンティティです。  コントローラーはまた、canisters が十分な *cycles*.これらは、canister 実行用のリソース（メモリ、ネットワーク帯域幅、計算能力）を獲得するために IC 上で使用されるユニットです。このため、ICはcanisters のリソース使用量を監視し、各canister がローカルに保持するcycle の残高からそのコストを差し引きます。

[もっと深く](/how-it-works/canister-lifecycle/)

<!---


![](/img/how-it-works/canisters.webp)

# Canisters

Smart contracts on the Internet Computer come in the form of *canisters*: computational units that bundle together code and state. Each canister defines functions that can be called by other canisters and parties external to the IC, such as browsers or mobile apps.
Canisters communicate with one another via asynchronous messages but the execution of each such message is done in complete isolation, allowing for massive levels of concurrent execution. 
Canisters are managed by controllers. Control structure of canisters could be centralized (e.g. when the controllers include some centralized entity), decentralized (when the controller is a DAO) or even non-existent, in which case the canister is an immutable smart contract. 
Controllers are the only entities which can deploy the canister to the Internet Computer, start/stop their execution and update their code.  The controllers also need to ensure that canisters hold sufficient *cycles*. These are the unit used on the IC to acquire resources for canister execution (memory, network bandwidth and computational power). To this end the IC monitors the resource usage of canisters and deducts their cost from a cycle balance maintained locally by each canister.

[Go deeper](/how-it-works/canister-lifecycle/)

-->
