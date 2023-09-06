---

sidebar_position: 4
---
# DAOの取得と維持の代替方法

## 概要

SNS はシステムが提供する DAO ですInternet Computer 。
もちろん、DAO を取得する他の選択肢もあります。

これらの可能性によって、コミュニティは、メンテナンスが可能な限り自動化された、ICによってサービスとして提供されるDAO（
）を使うか、どのように進化させることができるかについて完全な柔軟性を持つDAO（
）を使うかを選択することができます。

## SNSコードの自己展開

開発者またはコミュニティは、公開されている
[オープンソースコードを](https://github.com/dfinity/ic/tree/master/rs/sns)再利用することで、DAOを自己デプロイすることを選択できます。
彼らはこのコードを通常のアプリケーションサブネット上にデプロイし、手動でアップグレードすることができます。
彼らはその後、
システムが提供するSNSが従うのと同じSNSバージョンに従うか、またはこのパスから逸脱することを選択できます。
このオプションでは、DAOコミュニティは、
コードバージョンの実装、テスト、および承認においてより積極的でなければなりません。
このオプションに対するシステム提供SNSの利点の1つは、SNSサブネット上のすべてのSNSが、
NNSコミュニティによって検証された同じcansiterバージョンを実行するため、コードの検証が
より簡単になることです。

## 独自のDAOを構築/他のDAOフレームワークを使用

これは上記のオプションと概念的に似ていますが、私たちは
SNS コードが実装しているもの以外にも、もちろん DAO を実現する
デザインがあることを強調したいと思います。
これらのほとんどについて、コミュニティへの影響は同様です。 DAO
コミュニティは DAO のバージョンを保守するか、
第三者を信頼する必要があります。

<!---

# Alternatives how to get and maintain a DAO

## Overview

SNSs are system-provided DAOs on the Internet Computer.
There are of course also other alternatives to getting a DAO.

These possibilities allow communities to choose between using DAOs that are provided
as a service by the IC, where maintenance is as automated as possible, and DAOs
that have full flexibility of how they can evolve.


## Self-deploy the SNS code
  A developer or a community can choose to self-deploy a DAO by reusing the publicly
  available [open source code](https://github.com/dfinity/ic/tree/master/rs/sns).
They can deploy this code on a normal application subnet and manually upgrade it.
  They can then choose to follow the same SNS versions than
  system-provided SNSs follow or they can choose to deviate from this path.
  In this option, the DAO community has to be more active in
  implementing, testing and approving code versions. In exchange, they have more flexbility.
One advantage of the system-provided SNSs over this option is that the verification of the code is
easier as all SNSs on the SNS subnet run the same cansiter versions that are verified by the
  NNS community.

## Build your own DAO / use other DAO frameworks
  While this is conceptually similar to the above option, we would like to emphasize
  that there are of course other designs than what the SNS code implements
  that also realize a DAO.
  For most of these, the implications for the communities will be similar: The DAO
  communities will have to maintain the DAO versions or trust
  a third party to do so.

-->
