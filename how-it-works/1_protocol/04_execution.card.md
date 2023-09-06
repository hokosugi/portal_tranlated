---

title: Execution
---
![](/img/how-it-works/execution.webp)

# 実行

コアICプロトコルスタックの最上位層である実行層は、canister スマートコントラクトコードの実行を担当します。
コードの実行は、各ノードにデプロイされた[*WebAssembly （Wasm）*](https://webassembly.org/)仮想マシンによって行われます。
WebAssembly バイトコードは、ブロックチェーンシステムにとって重要な決定論的実行が可能で、ネイティブに近い速度で実行されます。
Canister メッセージ、すなわちユーザーによるイングレスメッセージや他の によるメッセージは、メッセージルーティングによってサブネット上の のキューに誘導されます、canisters canisters
メッセージ・ルーティングは次に実行レイヤーに制御を引き継ぎ、実行レイヤーは ' キュー内のすべてのメッセージが消費されるか、 ラウンドの上限に達するまで、決定論的にメッセージを実行します。canisters cycles 

実行レイヤーには多くのユニークな機能があり、ICを他のブロックチェーンとは一線を画しています：

1.  *決定論的タイムスライス（DTS）*- 数十億のWasm命令を実行する必要がある非常に大きなメッセージの実行を、複数のICラウンドに分割することができます。複数のラウンドにわたってメッセージを実行するこの機能は、Internet Computer ブロックチェーン独自のものです。
2.  *同時実行*-canister Wasmバイトコードの実行は複数のCPUコアで*同時に*行われます。これは、canister がそれぞれ独立したステートを持っているために可能です。
3.  *疑似乱数生成器*- 実行レイヤーは予測不可能で偏りのない*疑似乱数生成*器にアクセスできます。Canisters 、ランダム性を必要とするアルゴリズムを実行できるようになりました。

[より深く](/how-it-works/execution-layer/)

<!---


![](/img/how-it-works/execution.webp)

# Execution

The execution layer, the topmost layer of the core IC protocol stack, is responsible for executing canister smart contract code.
Code execution is done by a [*WebAssembly (Wasm)*](https://webassembly.org/) virtual machine deployed on every node.
WebAssembly bytecode can be executed deterministically, which is important for a blockchain system, and with near-native speed.
Canister messages, i.e., ingress messages by users or messages by other canisters, have been inducted into the queues of the canisters on the subnet by message routing.
Message routing then hands over control to the execution layer, which deterministically executes messages, either until all messages in the canisters' queues are consumed or the cycles limit for the round has been reached, to ensure bounded round times.

The execution layer has many unique features, which sets apart the IC from other blockchains: 
1. *Deterministic time slicing (DTS)* - The execution of very large messages requiring billions of Wasm instructions to be executed can be split across multiple IC rounds. This capability of executing messages over multiple rounds is unique to the Internet Computer blockchain.
2. *Concurrency* - Execution of canister Wasm bytecode is done *concurrently* on multiple CPU cores, which is possible due to each canister having its own isolated state. 
3. *Pseudorandom number generator* - Execution layer has access to unpredictable and unbiasable *pseudorandom number generator*. Canisters can now execute algorithms that require randomness. 

[Go deeper](/how-it-works/execution-layer/)

-->
