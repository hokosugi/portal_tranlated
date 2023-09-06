# HTTPSアウトコール

## 概要

これまでブロックチェーンは孤立した存在であり、スマートコントラクトは外部のサーバーや他のブロックチェーンと直接通信することができませんでした。その理由は、ブロックチェーンは複製されたステートマシンであり、各レプリカは各ラウンドで同じ遷移を行うために同じステートに対して同じ計算を実行する必要があるからです。外部サービスからの結果を入力として計算を行うことは、素朴な方法で行われた場合、レプリカのステート発散につながりやすく、実行可能であるためにはいくつかの技術的な考慮が必要です。

**canister ** Internet Computer 、**HTTP(S)リクエスト**、または**HTTP(S)アウトコールの**機能により、ブロックチェーンの歴史上初めて、スマートコントラクトがブロックチェーンの外部のHTTP(S)サーバーに直接コールを行い、その応答をスマートコントラクトのさらなる処理で使用することが可能になり、レプリケートされたステートをそれらの入力を使用して安全にアップデートすることができます。これまでのところ、スマートコントラクトが外部サーバーと通信する唯一の手段は、いわゆる**オラクルを**介したものでした。

:::info
このドキュメントの残りの部分では、基礎となるプロトコルを参照して、**HTTPと** **HTTPSの**両方に**HTTP**代表を使用する場合があることに注意してください。
 ：

Canister HTTPリクエストは多くのユースケースを可能にし、現在使われているオラクルモデルよりも多くの利点があります。

- **より強力な信頼モデル：** canister HTTPアウトコールは、スマートコントラクトが外部サーバーと通信するために外部の仲介者（オラクル）が必要ないため、より強力な信頼モデルに基づいています。
- **手数料の**低減：サービスに対して追加手数料を請求する仲介者が存在しません。
- **標準的なプログラミングパラダイムに**近い：スマートコントラクトが直接外部サーバーにHTTPリクエストを行うというパラダイムは、オラクルを使用する場合と比較すると、エンジニアが慣れ親しんでいる「通常の」プログラミングパラダイムにはるかに近いです。したがって、ブロックチェーンのためにプログラミングするという事実をさらに抽象化することができます。

#### なぜブロックチェーンにとって外部とのインターフェースが重要なのでしょうか？

- 現実世界のdApp のユースケースのほとんどは、チェーン外のエンティティとの何らかの形でのデータ交換を必要とします。
- 現在、世界のデータのほとんどは伝統的な（Web 2.0）サービスに保持されており、dApps の多くはこのデータに基づいて構築されているため、このデータにアクセスする必要があります。
- **ブロックチェーンのシンギュラリティに**到達するためには、スマートコントラクトがWeb 2.0サービスと相互作用できる必要があります。ブロックチェーン・シンギュラリティへの旅において、増え続けるデータがWeb 3.0のブロックチェーンの世界に引き込まれ、Web 2.0サーバーを介さずに異なるスマートコントラクト間で相互作用が行われるようになるでしょう。

## 使用例

canister HTTPSアウトコールのユースケースは数多くあります。

- 最も重要なユースケースの1つは、外部のHTTP APIからデータを読み取ることです。例えば、DEXで使用される価格データや、分散型保険dApps で使用される天候データなどです。
- IoTdApps は、センサーが相互作用する従来のサーバーからセンサーデータを取得する必要があります。将来的には、センサーとICブロックチェーンが直接やり取りすることも想定されます。
- ユーザーにメッセージ着信のプッシュ通知を送信するチャットサービス。

HTTPコールの大半は、Web 2.0データを読み取るための`GET` コールであると予想されますが、`POST` 、Web 2.0サーバーにデータを書き込めるようにするために、外部システムとのインタラクションでも重要な役割を果たすことは明らかです。

## リソース

- HTTPS outcalls 機能がどのように機能するのか、また、canister をコーディングする際にどのように使用するのかを深く知りたい場合は、「[how it works](https-outcalls-how-it-works.md)」のセクションを参照してください。

- [examples リポジトリには](https://github.com/dfinity/examples)、独自のdapp を構築するための出発点として使用できるサンプルコードがあります：
  
  - [Rust で HTTP GET リクエスト](https://github.com/dfinity/examples/tree/master/rust/send_http_get)を行うサンプルコード
  - で[HTTP GET](https://github.com/dfinity/examples/tree/master/motoko/send_http_get)リクエストを行うサンプルコード[ Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_get)
  - Rust で HTTP[POST](https://github.com/dfinity/examples/tree/master/rust/send_http_post)リクエストを行うサンプルコード
  - で HTTP[POST](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)リクエストを行うサンプルコード[ Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)

<!---
# HTTPS outcalls

## Overview
Up until now, blockchains have been isolated entities and smart contracts have not been able to directly communicate with external servers or other blockchains. The reason for this is that a blockchain is a replicated state machine where each replica needs to perform the same computations on the same state to make the same transitions in each round. Doing computations based on results from external services as input may easily lead to state divergence on the replicas if done in a naïve manner and thus requires some technical considerations to be workable.

The feature of **canister HTTP(S) requests**, or **HTTP(S) outcalls**, on the Internet Computer enables — for the first time in blockchain history — smart contracts to directly make calls to HTTP(S) servers external to the blockchain and use the response in the further processing of the smart contract such that the replicated state can safely be updated using those inputs. So far, the only means of communication of smart contracts with external servers has been through so-called **oracles**. 

:::info
Note that in the remainder of this documentation we may use **HTTP** representative for both **HTTP** and **HTTPS**, referring to the underlying protocol. Practically all HTTP traffic on public networks runs over secured HTTPS these days.
:::

Canister HTTP requests allow for a plethora of use cases and have numerous advantages over the currently used oracle model.
* **Stronger trust model:** canister HTTP outcalls are based on a stronger trust model because no external intermediaries (oracles) are required for smart contracts to communicate with external servers.
* **Lower fees:** no intermediaries are in place to charge additional fees for their services.
* **Closer to the standard programming paradigm**: the paradigm of a smart contract directly making HTTP requests to external servers is much closer to the "normal" programming paradigm engineers are used to when compared to using oracles. Thus, the fact that one programs for a blockchain can be further abstracted away.

#### Why is interfacing with the external world so important for a blockchain?
* Most real-world dApp use cases need some form of data exchange with off-chain entities.
* Most of the world's data is currently held in traditional (Web 2.0) services and many dApps build on this data and therefore need access to it.
* In order to be able to reach **blockchain singularity**, smart contracts need to be able to interact with Web 2.0 services. In our journey towards blockchain singularity, an ever increasing amount of data will be pulled into the blockchain world of Web 3.0 and interactions will increasingly take place between different smart contracts without involving Web 2.0 servers.


## Use cases
There are many use cases for canister HTTPS outcalls, see the following list for some prominent examples.
* One of the most important use cases is reading data from external HTTP APIs, e.g., pricing data used in DEXs or weather data used in decentralized insurance dApps.
* IoT dApps need to obtain sensor data from traditional servers with which the sensors interact. In the future, we may even envision direct interactions of sensors with the IC blockchain.
* Chat services sending push notifications about incoming messages to users.

We expect the majority of HTTP calls to be `GET` calls for reading Web 2.0 data, but `POST` clearly also plays an important role for the interaction with external systems in order to be able to write data to Web 2.0 servers.

## Resources

- If you want to take a deep dive into how the HTTPS outcalls feature works and how to use it when coding a canister, see the [how it works](https-outcalls-how-it-works.md) section.

- In the [examples repository](https://github.com/dfinity/examples) you can find sample code which you can use as starting point for building your own dapp.:
    * Sample code for making [HTTP GET requests in Rust](https://github.com/dfinity/examples/tree/master/rust/send_http_get) 
    * Sample code for making [HTTP GET requests in Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_get) 
    * Sample code for making [HTTP POST requests in Rust](https://github.com/dfinity/examples/tree/master/rust/send_http_post) 
    * Sample code for making [HTTP POST requests in Motoko](https://github.com/dfinity/examples/tree/master/motoko/send_http_post)
-->
