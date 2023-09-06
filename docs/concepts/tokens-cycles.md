# トークンとcycles

## 概要

Internet Computer エコシステム内では、Internet Computer Protocol トークン（ICP トークン）は、オープンマー ケットで価値が決定されるネイティブなユーティリティ・トークンです。ICP トークンは、Internet Computer のガバナンスと経済の両方において重要な役割を果たします。

## ICPトークンの入手方法

ICP トークンを取得する方法はいくつかあります。例えば

- 取引可能な ICP トークンを掲載している取引所を通じて ICP トークンを直接購入。

- ICPトークンのガバナンスへの参加に対する報酬としてトークンを受け取る方法。Internet Computer

- Internet Computer 協会（ICA）またはDFINITY 財団を通じてトークンの交付を受けること。

- ノードプロバイダーとしてコンピューティング能力を提供することに対する報酬としてトークンを受け取ります。

## ICPトークンの使用方法

ICP トークンを持っているが、その使い方がよくわからない場合、次の図に最も一般的な 3 つのシナリオを簡略化して示します。

![icp tokens how to use](_attachments/icp-tokens-how-to-use.svg)

この図が示すように、ICP トークンをどのように使用するかは、主にトークンを取得する目的によって異なります。例えば、あなたが開発者や創設者であれば、ICP トークンを **cycles**Cycles canisters を運営するための支払いに使用することができます。ガバナンスに参加し、 の方向性に影響を与えることに関心のあるコミュニティのメンバーであれば、 と呼ばれるステークに ICP トークンをロックアップし、提案の提出や投票ができるようにすることができます。Internet Computer neuron

:::info
取引手数料については、**取引送信**者があらゆる取引手数料を負担する責任があります。
::：

## cycles の仕組み

開発者にとって ICP トークンは重要です。ICP トークンはcycles に変換することができ、変換されたトークンはリソースの消費に対する支払いに使用されます。

例として、給湯器、キッチンストーブ、乾燥機、スペースヒーターにプロパンを使用している家があるとします。これらの電化製品を使用すると、手持ちのガスが枯渇するため、定期的にプロバイダーに連絡してガスを補充してもらい、電化製品を中断することなく使い続けられるようにします。これはcanisters と似ていますが、canister はcycles のアカウントを持ち、canisterのアプリケーションが消費する通信、計算、ストレージの料金を支払う必要があります。

cycles の詳細と使用方法については、[こちらを](/docs/developer-docs/setup/cycles/converting_icp_tokens_into_cycles.md)ご覧ください。

## 計算コスト

Cycles 物理的ハードウェア、ラックスペース、エネルギー、ストレージデバイス、帯域幅などのリソースを含む、 ブロックチェーンでホストされるアプリケーションの運用にかかる実際のコストを反映します。Internet Computer 

Canister cycles スマートコントラクトは、完全な実行（オール・オア・ナッシング）に対して支払うことができなければなりませんが、プラットフォームは、悪意のあるコードがリソースを消耗するのを防ぐために、 が保持および消費できる数に制限を設けています。canister 

運用コストが比較的安定しているため、例えば100万件のメッセージを処理するのに必要なcycles を予測しやすくなっています。

通信、計算、ストレージに関連するコストは、時間とともに増加するよりも減少する可能性の方が高いです。例えば、ディスクスペースが安くなり、ハードウェアが効率的になるため、Internet Computer protocol 。

Cycles 特に を トークンの形で価値に戻すことはできませんが、 間で転送することで が操作の代金を支払うことができます。cycles Internet Computer Protocol canisters canisters 

正確なコストについては、[計算コストと保管](/developer-docs/gas-cost.md)コストの表を参照してください。

## トークンの価値とボラティリティ

トークン（ICP）はInternet Computer ブロックチェーンの価値を反映し、変動する可能性があります。トークンの価値がcanister が処理できるメッセージ数に影響するのを防ぐため、トークンはリソースの支払いに直接使用されません。

トークンはトークン保有者間で交換したり、ガバナンス・システムの一部として議決権を確保するために**neuronに保管したりすることができます。**

トークンは、計算能力を提供するノード・プロバイダーや、投票や提案の提出によってInternet Computer のガバナンスに参加するneuron ホルダーへの報酬として使用されます。

## ノード・プロバイダへの支払い

このモデルにより、Internet Computer ブロックチェーンは、必要なときに必要な場所でリソースが利用できるように、計算能力の予測可能な経済モデルをノードプロバイダに提供します。ノードプロバイダは、Internet Computer ブロックチェーンが通常のトラフィックとワークロードの急増の両方に対応できる能力を持つように、アクティブノードとスペアノードの両方に対して報酬を受け取ります。

Internet Computer 経済モデルは、キャパシティを管理する権限と責任の多くをガバナンス・システム（Network Nervous System ）に負わせます。報酬とサービス・レベル要件に関する具体的な詳細は、本文書の範囲外です。

## リソース

トークンとcycles に関する詳細情報をお探しの場合は、以下の関連リソースをご覧ください：

- [トークン経済の概要（ビデオ）](https://www.youtube.com/watch?v=H2p5q0PR2pc)。

<!---
# Tokens and cycles

## Overview 
Within the Internet Computer ecosystem, Internet Computer Protocol tokens (ICP tokens) are a native utility token with a value determined on the open market. ICP tokens play a key role in both the governance and the economics of the Internet Computer.

## How to get ICP tokens

There are a few different ways you might acquire ICP tokens. For example, you might:

-   Purchase ICP tokens directly through an exchange that lists ICP tokens available for trade.

-   Receive tokens as rewards for participating in the governance of the Internet Computer

-   Receive a grant of tokens through the Internet Computer Association (ICA) or the DFINITY Foundation.

-   Receive tokens as remuneration for providing computing capacity as a node provider.

## How to use ICP tokens

If you have ICP tokens, but aren’t sure how to use them, the following diagram provides a simplified overview to illustrate the three most common scenarios.

![icp tokens how to use](_attachments/icp-tokens-how-to-use.svg)

As this diagram suggests, how you use ICP tokens depends primarily on your goals in acquiring them. For example, if you are a developer or founder, ICP tokens can be converted to **cycles**. Cycles can then be used to pay for running canisters that deliver products and services to the market. If you are a member of the community interested in participating in governance and influencing the direction of the Internet Computer, you can lock up ICP tokens in a stake, called a neuron, so that you can submit and vote on proposals.

:::info
Regarding transaction fees, the **transaction sender** is responsible for covering any/all transaction fees.
:::

## How cycles work

For developers, ICP tokens are important because they can be converted to cycles that, in turn, are used to pay for resource consumption.

As an example, imagine you have a house where propane is used for a water heater, kitchen stove, dryer, and space heater. As you use these appliances, you deplete the supply of gas you have on hand, so periodically you contact a provider to refill your supply so you can continue to use your appliances without interruption. This is similar to canisters in that each canister must have an account with cycles available to pay for the communication, computation, and storage that the canister’s application consumes.

To learn more about cycles and how to use them, see [here](/docs/developer-docs/setup/cycles/converting_icp_tokens_into_cycles.md).

## Cost of computation

Cycles reflect the real costs of operations for applications hosted in the Internet Computer blockchain including resources such physical hardware, rack space, energy, storage devices, and bandwidth.

Canister smart contracts must be able to pay for complete execution (all or nothing), but the platform sets limits on how many cycles a canister can hold and consume to prevent malicious code from draining resources.

The relative stability of operational costs makes it easier to predict the cycles required to process, for example, a million messages.

The costs associated with communication, computation, and storage are more likely to decrease than to increase over time. For example, because disk space becomes cheaper and hardware becomes more efficient, the Internet Computer protocol will also improve over time to make better use of the resources.

Cycles are not a currency; in particular cycles cannot be converted back to value in the form of Internet Computer Protocol tokens, but can be transferred between canisters to enable canisters to pay for operations.

For exact costs see the tables in [computation and storage costs](/developer-docs/gas-cost.md).

## Token value and volatility

Tokens (ICP) reflect the value of the Internet Computer blockchain and can fluctuate. To prevent the token value from affecting the number of messages a canister can process, tokens are not used to pay for resources directly.

Tokens can be exchanged between token holders or locked up in **neurons** to secure voting rights as part of the governance system.

Tokens are used to reward node providers for providing compute capacity and neuron holders for participating in the governance of the Internet Computer by voting and submitting proposals.

## Payment to node providers

With this model, the Internet Computer blockchain provides node providers with a predictable economic model for computing power capacity to ensure resources are available when and where they are needed. Node providers receive compensation for both active and spare nodes so that the Internet Computer blockchain has capacity to handle both normal traffic and workload spikes.

The Internet Computer economic model places much of the power and responsibility of managing capacity on the governance system—the Network Nervous System. Specific details about compensation and service level requirements are outside the scope of this document.

## Resources

If you are looking for more information about tokens and cycles, check out the following related resources:

-   [Overview of token economics (video)](https://www.youtube.com/watch?v=H2p5q0PR2pc).

-->
