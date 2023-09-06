# Internet Computer とは？

ブロックチェーン **Internet Computer**は、開発者、組織、起業家が、安全で、自律的で、改ざん防止された **canisters****スマートコントラクトの**進化形です。

dapp の開発者として、Internet Computer が以下の主要な機能を提供すると考えると便利でしょう：

- スマート・コントラクトをウェブスピードで実行するための、**グローバルにアクセス可能なパブリック・ブロックチェーン**。

- 安全な暗号プロトコル(**Internet Computer protocol**)は、世界中の独立したデータセンターで独立したノードプロバイダーによって運営されるノードマシンによって実行されます。これにより、スマートコントラクトの安全な実行が保証されます。

- [チェーンキー暗号を](https://internetcomputer.org/how-it-works/#Chain-key-cryptography)使用して接続された**ブロックチェーンのネットワークで**、必要に応じて容量を拡張することができます。

## オープンなブロックチェーン

Internet Computer は、地理的に分散したデータセンターに設置された、独立した当事者が運営するノードマシン上でホストされるブロックチェーンです。ノードは、ブロックチェーン上で実行されるスマートコントラクトが改ざんや停止されないことを保証する高度な暗号フォールトトレラントプロトコルであるInternet Computer protocol を実行します。Internet Computer は並行して実行される個々のサブネットのブロックチェーンで構成され、チェーンキー暗号を使用して接続されています。つまり、サブネット上で実行されているcanisters は、Internet Computer ブロックチェーンの他のサブネットでホストされているcanisters をシームレスに呼び出すことができます。

Internet Computer のもう1つの重要な特徴は、分散型の無許可ガバナンス・システム（NNSと呼ばれる）の制御下で実行されることです。 **Network Nervous System**(NNS)と呼ばれる、完全にオンチェーンで実行される分散型ガバナンスシステムの制御下で実行されることです。NNSは、新しいサブネット・ブロックチェーンを作成することによるInternet Computer のスケールアウト、ノード・マシンの更新、Internet Computer protocol で使用されるパラメータの設定など、いくつかのトピックについて決定を下すことができます。誰でもガバナンスに参加し、新しい提案をNNSに提出したり、オープンな提案に投票したりすることができます。そのためには、Internet Computer のユーティリティ・トークンであるICPをステークし、NNSに **neuron**を作成する必要があります。

## 次世代のソフトウェアとサービスの構築

Internet Computer protocol はプラットフォームベースのリスクを軽減し、ソフトウェアの構築、デプロイ、アクセス方法を再構築することでイノベーションへの道を開きます。

例えば、Internet Computer を使用することで、開発者は、以下のような環境関連の雑念を排除し、canisters を使用してコードを書くことに集中することができます：

- 物理または仮想ネットワーク構成要件

- ロード・バランシング・サービス

- ファイアウォール、ネットワーク・トポロジー、ポート管理。

- データベースの構成とメンテナンス。

- ストレージ・ボリュームとデバイス

開発者がアプリケーションの構築と価値の提供に集中できるようにすることで、Internet Computer 、開発プロセスを簡素化し、市場投入までの時間を短縮し、イノベーションを促進します。

エンドユーザーにとって、Internet Computer は、dapps にアクセスするためのリスクを抑えた安全な環境を提供します。ブロックチェーン固有のセキュリティにより、Internet Computer 上で実行されるプログラムは悪意のあるコードに乗っ取られることがないため、アプリケーションのエンドユーザーと組織の双方にとって総所有コストを削減することもできます。

さらに、dapps は「自律的」かつパブリックであることができるため、開発者は安心してプロジェクトを革新し改善する余地を残しながら、生産性と効率を高める方法で互いに通信し、機能を共有するサービスを書くことができます。

また、Internet Computer は、開発者が暗号的に安全な ID を使用してアクセス制御を実施することを可能にし、ユーザー名とパスワード、または外部の ID 管理プラグインに依存する必要性を低減します。

<!---
# What is the Internet Computer?

The **Internet Computer** is a blockchain that enables developers, organizations, and entrepreneurs to build and deploy secure, autonomous, and tamper-proof **canisters**, an evolution of **smart contracts**.

As a dapp developer, you might find it useful to think of the Internet Computer as providing the following key features:

-   A **globally-accessible, public blockchain** for running smart contracts at web speed, that can serve interactive web content to users.

-   A secure cryptographic protocol (**Internet Computer protocol**) run by nodes machines operated by independent node providers in independent data centers all over the world. This guarantees the secure execution of smart contracts.

-   A **network of blockchains** connected using [chain key cryptography](https://internetcomputer.org/how-it-works/#Chain-key-cryptography) that can scale out its capacity as required.

## An open blockchain

The Internet Computer is a blockchain hosted on node machines operated by independent parties and located in geographically distributed data centers. The nodes run the Internet Computer protocol, an advanced cryptographic fault-tolerant protocol which ensures that smart contracts running on the blockchain cannot be tampered with or stopped. The Internet Computer is composed of individual subnet blockchains running in parallel and connected using chain key cryptography. This means that canisters running on a subnet can seamlessly call canisters hosted in any other subnet of the Internet Computer blockchain.

Another important feature of the Internet Computer is that it runs under the control of a decentralized, permissionless governance system, called **Network Nervous System** (NNS), which runs completely on-chain. The NNS can make decisions on several topics, including scaling out the Internet Computer by creating new subnet blockchains, updating the node machines, and configuring parameters used in the Internet Computer protocol. Anyone can participate in the governance and submit new proposals to the NNS or vote on open proposals. To do so, users have to stake ICP, the Internet Computer utility token, and create a **neuron** with the NNS.

## Building the next generation of software and services

The Internet Computer protocol reduces platform-based risks and paves the way for innovation by re-imagining how software is built, deployed, and accessed.

For example, with the Internet Computer, developers can focus on writing code using canisters without environment-related distractions such as:

-   Physical or virtual network configuration requirements.

-   Load balancing services.

-   Firewalls, network topology, or port management.

-   Database configuration and maintenance.

-   Storage volumes and devices.

By enabling developers to focus on building applications and delivering value, the Internet Computer helps simplify the development process, reduce time to market, and foster innovation.

For end-users, the Internet Computer provides a secure environment for accessing dapps with fewer risks. Because of the inherent security of the blockchain, programs running on the Internet Computer cannot be hijacked by malicious code, which also reduces the total cost of ownership for both application end-users or organizations.

In addition, because dapps can be "autonomous" and public, developers can write services that communicate with each other and share functions in ways that increase productivity and efficiency while leaving room to innovate and improve projects with confidence.

The Internet Computer also enables developers to use cryptographically-secure identities to enforce access controls, reducing the need to rely on usernames and passwords or external identity management plug-ins.

-->
