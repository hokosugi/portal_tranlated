---

sidebar_position: 3
---
# NFT開発

## 概要

NFT（**ノン・ファンジブル・トークン**）とは、特定のデジタル資産または物理的資産に関連付けられたブロックチェーン上の記録です。ブロックチェーン上の一意のデジタル表現により、所有権の証明や取引が可能になります。

## ブロックチェーン上のNFTInternet Computer

Internet Computer （IC）はNFTに多くの可能性をもたらします。画像、サウンドクリップ、動画のようなデジタル資産の場合、資産全体がオンチェーンに存在し、オンチェーンゲームやメタバース体験に含めることができます。さらに、[HTTPSアウトコールを介して](/https-outcalls)IC内部や外部のデータに基づいて変化するダイナミックなNFTを想像することもできます。

多くのアプリケーションにとって、その決定的な特徴は永続性と不変性（または事前に定義されたルールに従った進化）です。リバースガスモデルやcanister スマートコントラクトのアップグレード可能性など、ICの設計上の決定事項の中には、NFT開発者が特に注意を払う必要があるものもあります。

IC上のNFT実装には通常、以下の3つの機能があります：

- 所有権を追跡し、譲渡を許可するレジストリ。
- 台帳または取引履歴
- 実際の資産（デジタル資産の場合）。

canister アーキテクチャによっては、これらすべての機能が1つのcanister に含まれる場合もあれば、複数のcanisters にまたがる場合もあります。これらの各機能は、canisters 、cycles 、恣意的なコード変更から保護されている必要があります。以下では、このような目標を達成するためにNFT開発者とそのユーザーを支援するメカニズム、ツール、アイデアのいくつかについて説明します。

## 基本

### すべてのcanisters を手厚くバックアップ

すべてのcanisters に、まず数年間維持できるだけのcycles があることを確認します。IC上のストレージと計算は、他のプラットフォームよりもはるかに安価であるため、通常、これは莫大な投資ではありません。他の人が簡単にcanisters を補充できるようにするには、[ブラックホールcanister](https://github.com/ninegua/ic-blackhole)または他の不変プロキシcanister をNFTcanisters のコントローラとして追加することを検討する必要があります。これにより、ユーザーは[チップジャーサービスを](https://k25co-pqaaa-aaaab-aaakq-cai.icp0.io/)使用してcanisters を補充することができます。

### 余裕のある凍結しきい値の設定

cycles Canisters ICには、canister の枯渇を防ぐ便利なメカニズムがあります。 [`freezing_threshold`](/references/ic-interface-spec.md#ic-create_canister).`freezing_threshold` はcanister のコントローラによって設定でき、秒単位で与えられます。ICはこれを閾値として動的に評価し、cycles 。この値は、canister が、`freezing_threshold` で指定された時間以上、アイドル状態のリソースを支払うことができるような値です。これを保証するために、cycle の残高がしきい値に達するとcanister はフリーズし、ハートビートとタイマーを含むすべてのアップデートコールは直ちに拒否され、canisterのcycle の残高には影響しません。デフォルト値は約30日ですが、NFTの場合、開発者は`freezing_threshold` を少なくとも90日、できれば180日に設定すべきです。これにより、NFTの開発者とそのユーザーは、最終的にcycles の残高がなくなる前に、canisters に対応し、上乗せするための十分な時間を確保することができます。

### canisters を監視できるようにします。

IC上では、canister のcycle 残高はコントローラにしか見えません。NFT（コレクション）は作成者よりも長生きする可能性があるため、サードパーティによる監視を計画する必要があります。これは、[DIP721](https://github.com/Psychedelic/DIP721/blob/064b04fbaf0429bf9fefdc0663d53fae033be0f9/src/main.rs#L450)および[EXT](https://github.com/Toniq-Labs/extendable-token/blob/86eabb7336ea259876be9be830fb69b03046ea14/examples/erc721.mo#L254)規格に含まれるような簡単なクエリメソッドを実装することで可能です。

この点でも、ブラックホールcanister をコントローラとして追加するのは良い方法です。 [`canister_status`](/references/ic-interface-spec.md#c-canister_status).

また、[Canister](https://canistergeek.app/) geek のような、より完全な監視ソリューションを使用することもできます。最近、Canistergeekのチームは、[人気のあるNFTコレクションのcycles バランスを](https://t5t44-naaaa-aaaah-qcutq-cai.raw.icp0.io/cycles)観察できる新機能を[NFTgeek](https://t5t44-naaaa-aaaah-qcutq-cai.raw.icp0.io/)製品に追加しました。

### 効率的な実装のためのベストプラクティス

canister を予想以上に高価にする可能性のあるフットガンがいくつかあります。以下は、NFTcanisters を実装する際に遭遇する可能性のあるいくつかの例です。

- ハートビートの使用：何もしない普通のハートビートでは、～0.055 Tcycles/日のコストがかかります。代わりに、[canister タイマー](/developer-docs/backend/periodic-tasks.md)（最小タイムアウトまたは間隔を指定したワンショットまたは定期的なcanister 呼び出し）を使用します。
- Motoko 開発者へのアドバイス：
  - `HashMap` の代わりに`TrieMap` を使用すると、HashMaps に関連する自動リサイズによるパフォーマンスの弊害を避けることができます。
  - 構造体のサイズを動的に変更する必要がある場合は、`Array` の代わりに`Buffer` を使用してください。
  - 大きなバイナリ資産を保存する場合は、`[Nat8]` の代わりに`Blob` を使用してください。
  - Candid`vec nat8/blob` の値を送受信する場合は、`[Nat8]` の代わりに`Blob` を使用してください。選択は自由ですが、`Blobs` の方が4倍コンパクトで、ガベージコレクション (GC) への負荷もずっと少なくなります。
  - 特に、その Blob の手動メモリ管理が単純な場合（追加のみで、削除はされないなど）、GC への負担をさらに軽減するために、大きな`Blob`を安定したメモリに格納することを検討してください。
  - 特に追加のみのシナリオでは、より大きなヒープへのアクセスを可能にし、大きな静止オブジェクトのコピーのコストを削減するために、`compacting-gc` の設定を使用することを検討してください。
- Rust開発者へのアドバイスです：
  - アップグレードのためにステートを(デ)シリアライズする必要がある場合、`Vec<u8>` 、したがって`String` 型の広範な使用には注意が必要です。
  - [効果的なRustに関するRomanのブログ](https://mmapped.blog/posts/01-effective-rust-canisters.html)記事をお読みください。[ canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)

もう1つ必読の記事は、[Joachim Breitnerによるcanister 開発のためのグッドプラクティスに関する](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)一般的な記事です。

cycle のバーンレートの高さや命令数の制限に驚かないようにするために、最近追加された[パフォーマンス・カウンタ API](https://forum.dfinity.org/t/introducing-performance-counter-on-the-internet-computer/14027)を使って、本番稼働前でもcanisters のプロファイリングを行うことができます。さらに、IC上のすべてのコストのリストは、[ここで](../gas-cost.md)見つけることができます。

### ステートのバックアップとリストアの実装

IC自体はまだcanister ステートのバックアップとリストアをサポートしていませんが、canister 自体に実装することができます。定期的なバックアップは、canister が割り当て解除されたり、canister のアップグレードに問題が発生したりする最悪のシナリオに対する保険です。[このフォーラム投稿では](https://forum.dfinity.org/t/backup-restore-function-for-a-canister/12849/3)、[Distrikt](https://distrikt.app)が使用しているアプローチについて説明しています。

### トランザクション履歴の保存に専用サービスを使用することを検討してください。

ICには、[CAPの](https://cap.ooo/)ようなトランザクションの監査ログを保持する専用サービスがあり、NFTコレクションがサービスとして使用できます。これにより、エクスプローラやウォレットに出所履歴を簡単に統合することができます。
さらに、メインのNFTcanister が紛失した場合でも、所有者のステートメントを再構築することができます。ただし、NFTの転送に必要なインターcanister コールのために追加コストが発生するなど、いくつかの欠点も考慮する必要があります。

## 高度なトピック

### ガバナンスについて

ほとんどのNFTの価値提案は、[ブラックホール](https://github.com/ninegua/ic-blackhole)（[ canister ）を唯一のコントローラに設定するなど、その永続性と不変性にあります。NFT](https://github.com/ninegua/ic-blackhole) canisters の開発者がコントローラーである限り、ユーザーは開発者の信頼性（および運用上のセキュリティ）に依存します。したがって、開発者はcanisters を不変にするか、DAO でcanisters を管理する必要があります。中間的な方法は、canister の変更を監査可能にする[Launchtrail の](https://devpost.com/software/launch-trail)ようなメカニズムです。

canister のブラックホール化にも問題があります。canister のコードにバグがあったり、非推奨になるかもしれない実験的なシステムAPIを使っていたりすると、後でcanister が機能しなくなるかもしれません。

このトピックに関するより詳しい情報は、[ canisters の](/concepts/trust-in-canisters.md) [信頼に](/concepts/trust-in-canisters.md)あります。

### 経済的な持続可能性について考えましょう

理想的には、canisters は、canisters が無期限にその存在費用を支払うために使用できる料金を生成するメカニズムを実装します。単純なアプローチとしては、移籍金の（一部を）canisters の燃料として利用することですが、より精巧なスキームとしては、ステーキングやその他の高度なメカニズムが考えられます。優れたベストプラクティスを私たちは知りませんが、巧妙なメカニズムを導入しているプロジェクトをご存知でしたら、ぜひ教えてください。

## リソース

以下のリソースはコミュニティ・プロジェクトです。ご自身で調査し、ご自身の責任においてご利用ください。

### NFTインターフェースの仕様と実装

- [DIP 721](https://github.com/Psychedelic/DIP721)：[ERC-721に](https://eips.ethereum.org/EIPS/eip-721)類似したインターフェース。
- [拡張可能トークン（EXT）](https://github.com/Toniq-Labs/extendable-token)：[ERC-1155に](https://eips.ethereum.org/EIPS/eip-1155)着想を得た拡張可能なインターフェース。

### NFTマーケットプレイスとローンチパッド

- [Entrepot](https://entrepot.app/)。
- [ゼリー](https://jelly.xyz/)。
- [NFT Anvil](https://nftanvil.com/)。
- [Yumi](https://tppkg-ziaaa-aaaal-qatrq-cai.raw.icp0.io/)。

<!---

# NFT development

## Overview

An NFT or **non-fungible token** is a record on a blockchain that is associated with a particular digital or physical asset. The unique digital representation on a blockchain allows the proving of ownership as well as their trading. 

## NFTs on the Internet Computer

The Internet Computer (IC) brings a lot of potential for NFTs. For digital assets like images, sound clips, or videos, the entire assets can live on-chain and can be included in on-chain games or metaverse experiences. Furthermore, we can imagine dynamic NFTs that change based on IC-internal and external data via [HTTPS outcalls](/https-outcalls).

For many applications, the defining characteristic is their permanence and immutability (or evolution according to predefined rules). Some of the design decisions of the IC, such as the reverse gas model and the upgradeability of canister smart contracts, require the NFT developer to be particularly aware.

An NFT implementation on the IC typically has the following three functions:

-  A registry that tracks ownership and allows transfers.
-  A ledger or transaction history.
-  The actual asset (in the case of digital assets).

Depending on the architecture, all of these functions may be in one canister or spread across multiple canisters right up to an asset canister per individual NFT. Each of these canisters must not run out of cycles, and should be protected against arbitrary code changes. In the following, we discuss some of the mechanisms, tools, and ideas that support NFT developers and their users to achieve these goals.


## The basics

### Top up all canisters very generously

Make sure that all canisters have enough cycles to sustain a few years to begin with. Storage and computation on the IC are magnitudes less expensive than on other platforms, so this is typically not a huge investment. To make it easy for others to top up the canisters you should consider adding the [black hole canister](https://github.com/ninegua/ic-blackhole) or some other immutable proxy canister as a controller to the NFT canisters. This allows users to use the [tip jar service](https://k25co-pqaaa-aaaab-aaakq-cai.icp0.io/) to top up the canisters.


### Set a generous freezing threshold

The IC has a useful mechanism to save your canister from running out of cycles. Canisters have a configurable [`freezing_threshold`](/references/ic-interface-spec.md#ic-create_canister). The `freezing_threshold` can be set by the controller of a canister and is given in seconds. The IC dynamically evaluates this as a threshold value in cycles. The value is such that the canister will be able to pay for its idle resources for at least the time given in `freezing_threshold`. To guarantee that, the canister is frozen when the cycle balance reaches the threshold, and all update calls, including the heartbeat and timer, are immediately rejected and won’t affect the canister’s cycle balance. The default value is approximately 30 days, but for NFTs, developers should set the `freezing_threshold` to at least 90 days, preferably 180 days. This makes sure that NFT developers and their users have enough time to react and top up the canisters before they finally run out of cycles.


### Make sure your canisters can be monitored

On the IC, the cycle balance of a canister is only visible to controllers. Since an NFT (collection) might outlive its creator, you should plan for monitoring by third parties. You can do this via implementing a simple query method as included in the [DIP721](https://github.com/Psychedelic/DIP721/blob/064b04fbaf0429bf9fefdc0663d53fae033be0f9/src/main.rs#L450) and [EXT](https://github.com/Toniq-Labs/extendable-token/blob/86eabb7336ea259876be9be830fb69b03046ea14/examples/erc721.mo#L254) standards.

Again, adding the black hole canister as a controller is a good practice in this regard, since it can act as a proxy to fetch the [`canister_status`](/references/ic-interface-spec.md#c-canister_status). 

You can also use a more complete monitoring solution like [Canistergeek](https://canistergeek.app/). Recently, the team behind Canistergeek added a new feature to their [NFTgeek](https://t5t44-naaaa-aaaah-qcutq-cai.raw.icp0.io/) product that allows observing the [cycles balance of popular NFT collections](https://t5t44-naaaa-aaaah-qcutq-cai.raw.icp0.io/cycles). 



### Follow best practices for efficient implementations

There are a few foot guns that could make your canister more expensive than you’d expect. Here are a few examples that you might encounter when implementing NFT canisters.

* Use of the heartbeat: A plain heartbeat without doing anything will cost ~0.055 T cycles/day. Instead, use [canister timers](/developer-docs/backend/periodic-tasks.md) &mdash; one-shot or periodic canister calls with specified minimum timeout or interval.
* Some advice for Motoko developers: 
    * Use `TrieMap` instead of `HashMap` to avoid the performance cliff of automatic resizing associated with HashMaps.
    * Use `Buffer` instead of `Array` if you need to dynamically resize the structure.
    * Use `Blob` instead of `[Nat8]` for storing large binary assets.
    * Consider using `Blob` instead of `[Nat8]` when sending or receiving Candid `vec nat8/blob` values. The choice is yours but `Blobs` are 4x more compact and much less taxing on garbage collection (GC).
    * Consider storing large `Blob`s in stable memory, to reduce pressure on the GC even further, especially when the manual memory management of that Blob is simple (e.g. they are only added, never deleted).
    * Consider using the `compacting-gc` setting, especially in append-only scenarios, to allow access to larger heaps and reduce the cost of copying large, stationary objects.
* Some advice for Rust developers:
    * Be careful with extensive use of `Vec<u8>` and hence the `String` type if you need to (de-)serialize state for upgrades.
    * Read [Roman’s blog post on effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)

Another must-read is the general article on [good practices for canister development by Joachim Breitner](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister). 

To make sure you won’t get surprised by a high cycle burn rate or hitting an instruction limit, you can use the recently added [performance counter API](https://forum.dfinity.org/t/introducing-performance-counter-on-the-internet-computer/14027) to profile your canisters even before going live. Furthermore, a list of all costs on the IC can be found [here](../gas-cost.md). 


### Implement mechanisms to backup and restore state

The IC itself does not yet support backup and restoration of the canister state, but it can be implemented in the canister itself. Regular backups are insurance against the worst-case scenario that a canister gets deallocated or there are issues with upgrading a canister. [This forum post](https://forum.dfinity.org/t/backup-restore-function-for-a-canister/12849/3) describes the approach [Distrikt](https://distrikt.app) is using.


### Consider using a dedicated service for storing the transaction history 

There are dedicated services on the IC to keep an audit log of transactions such as [CAP](https://cap.ooo/), which can be used by an NFT collection as a service. This allows simple integration of the provenance history in explorers and wallets. 
Furthermore, the state of ownership could be reconstructed in case the main NFT canister gets lost. However, some drawbacks have to be considered, e.g. NFT transfers incur additional costs due to the necessary inter-canister calls. 

## Advanced topics

### Think about governance

The value proposition of most NFTs is their permanence and immutability, e.g. by setting the [black hole canister](https://github.com/ninegua/ic-blackhole) as the only controller. As long as NFT canisters have their developers as controllers, users depend on the trustworthiness (and operational security) of the developers. Developers should therefore make the canisters immutable or manage the canisters with a DAO. A middle ground are mechanisms like [Launchtrail](https://devpost.com/software/launch-trail) that make changes to a canister auditable.

Blackholing a canister has its issues as well. If there are bugs in the canister code or you’re using experimental system APIs that might get deprecated, later on, the canister might stop functioning. 

More information on this topic can be found in the [trust in canisters](/concepts/trust-in-canisters.md) article.


### Think about economic sustainability

Ideally, your canisters implement mechanisms to generate fees that the canisters can use to pay for their existence indefinitely. A simple approach is to utilize (parts of) the transfer fee to fuel the canisters, but more elaborate schemes could involve staking or other advanced mechanisms. We’re unaware of any good best practices, but please share if you know of projects implementing clever mechanisms.


## Resources

The following resources are community projects. Please do your own research and use them at your own risk.

### NFT interface specifications and implementations

- [DIP 721](https://github.com/Psychedelic/DIP721): An interface similar to [ERC-721](https://eips.ethereum.org/EIPS/eip-721).
- [Extendable token (EXT)](https://github.com/Toniq-Labs/extendable-token): Extendable interface inspired by [ERC-1155](https://eips.ethereum.org/EIPS/eip-1155).

### NFT marketplaces and launchpads

- [Entrepot](https://entrepot.app/).
- [Jelly](https://jelly.xyz/).
- [NFT Anvil](https://nftanvil.com/).
- [Yumi](https://tppkg-ziaaa-aaaal-qatrq-cai.raw.icp0.io/).






-->
