---

id: index
title: Running in production
description: When you are ready to deploy your project, this guide will help you have the most seamless possible launch or upgrade.
---
# 本番稼動中

## 概要

プロジェクトをデプロイする準備ができたら、このガイドを参考にして、可能な限りシームレスな立ち上げやアップグレードを行ってください。

このページはトピックの概要です。具体的な手順やトラブルシューティングについては、次のページを参照してください：

- [デプロイとアップグレードcanisters](./deploying-and-upgrading.md).
- [大規模なウェブアセンブリモジュール](./larger-wasm.md)
- [カスタムドメイン](custom-domain/custom-domain.md)
- [命令の制限](./instruction-limits.md)
- [計算とストレージのコスト](../gas-cost.md)
- [ソーシャルメディアでのリンクの共有](./social-sharing.md)。

## 信頼の実証

Internet Computer では、canister のソースコードを常に検査すること はできず、コントローラはいつでも、canister 上で実行されているコードを置き換えることができます。 アップグレード可能であることは便利であり、継続的な開発を可能にしますが、契約 が安定していて信頼できることを依存する必要があるユーザーにとってはリスクも生じます。このトピックの詳細については、[ canisters の信頼](/concepts/trust-in-canisters.md)性を参照してください。

## 安全にアップグレードする方法

これについては、[Motoko アップグレードの互換性](/motoko/main/compatibility.md) で詳しく説明しています。

<!---


# Running in production

## Overview

When you are ready to deploy your project, this guide will help you have the most seamless possible launch or upgrade.

This page is an overview of the topic. For specific steps and troubleshooting information, please refer to the following pages:

* [Deploying and upgrading canisters](./deploying-and-upgrading.md).
* [Large web assembly modules](./larger-wasm.md).
* [Custom domains](custom-domain/custom-domain.md).
* [Instruction limits](./instruction-limits.md).
* [Computation and storage cost](../gas-cost.md).
* [Sharing links on Social Media](./social-sharing.md).

## Demonstrating trust

In the Internet Computer, a canister's source code cannot always be inspected, and at any point, a controller can replace the code running on the canister. Upgradeability is convenient and allows for continuous development, but it also creates risk for users who need to depend on the contract to be stable and trustworthy. Read [trust in canisters](/concepts/trust-in-canisters.md) for more information on this topic.

## How to upgrade safely

This is explored in depth in [Motoko upgrade compatibility](/motoko/main/compatibility.md).

-->
