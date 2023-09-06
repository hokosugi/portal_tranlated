# 信頼canisters

## 概要

canisters におけるDeFiおよび関連アプリケーションの重要な側面は、ICPやビットコインなどの価値を移転する機能です。このような機能により、canisters に対する信頼が不可欠となります。ICP をcanister に預けることが安全であることを、どのように保証すればよいのでしょうか？

この質問に対する答えには、2つの側面があります：

1.  canister 、そのICPがすべきことをするという確信。

2.  canister の動作が予期せず変化することがないという確信。

## canister 。

canister の正しい動作は、2つのステップでチェックできます。まず、canister にデプロイされた Wasm コードを生成するために使用されたソースコードを検査し、 期待される/要求される機能を実装していること、およびその機能のみを実装 していることを確認します。

次に、canister で実行されるWasmモジュールが、主張されたソースコードから実際に生成されたものであることを確認します。ここで、ビルドの再現性が非常に重要です。開発者は、正確に同じWasmをゼロから再構築できるように、Wasmモジュールを構築している必要があります。ユーザーは、再構築されたWasmモジュールのハッシュと、ICが報告したモジュールハッシュを比較することができます。開発者とユーザーは、再現性を確保するためのガイダンスを、[再現可能なcanisters](/developer-docs/backend/reproducible-builds.md) 。

## canister の動作が予期せず変更されることはないという確信

Canister スマートコントラクトは、コントローラによってデプロイされ、管理されます。コントローラの分散化のレベルは、一人またはチームによって管理されるものから、NNSまたは別の種類のオンチェーンDAOによって管理されるものまで様々です。他の機能の中でも、コントローラーは自分が管理する のコードを変更することができるので、他のブロックチェーンのスマートコントラクトとは異なり、 のコードはcanisters canister **変更可能**です。コントローラーは、自分が管理する が保有するICPトークンやビットコインなどの資産を完全にコントロールできます。この機能により、 は一般的なソフトウェアに近づき、ソフトウェアロジックを必要に応じて変更できる幅広いアプリケーションに適しています。canister canisters 

canister canister DeFiで使用されているようなクリティカルなアプリケーションの場合、中央集権的な変更可能性は危険な場合があります。以下では、canister の変異の制御を検証可能な形で分散化する方法について、開発者が利用できるオプションの概要を説明します。

:::info
Canister コントローラは、自発的に分散化されていない場合、canister が保有するユーザー資産、たとえばユーザーの代わりにcanister が保有するICPトークンやビットコインを完全に制御できます。悪意があれば、コントローラはすべての資産を盗むことができます。言い換えると、ユーザーとして、自分の資産を扱うcanister とやり取りする場合、canister を検査し、その資産がどのように扱われるかを知ってください。canister がそのサブアカウントにアセットを保存していると判断した場合は、canister のコントローラが分散型であることを確認してください。
::：

## Network Nervous System （NNS）による単独コントロール

最も単純なオプションは、canister のコントローラを削除することです。コントローラがない場合、canister は、プラットフォームの完全性が維持されていることを前提に、NNSプロポーザルを介してNNSによってのみ変異させることができます。

ユーザーは、dfxを使用して、canister のコントローラのリストを確認できます。例えば

    dfx canister --network ic info ryjl3-tyaaa-aaaaa-aaaba-cai

これは、プリンシパル`ryjl3-tyaaa-aaaaa-aaaba-cai` (この例では、元帳canister)を持つcanister のコントローラのリストを返します。

ユーザは、[`read_state` リクエストを](/references/ic-interface-spec.md/#http-read-state)使用して、他のcanister のコントローラのリストを取得し、コントローラのリストを含む関連する[canister 情報を](/references/ic-interface-spec.md#state-tree-canister-information)取得することもできます。注意: 現在、canister はこの情報を取得できません。

同様の効果は、canister のコントローラを自分自身に設定することでも得られます。しかしこの場合、canister が再インストール要求を出すなどして、何らかの方法で自分自身をアップグレードする要求を出せないことを注意深く検証する必要があります。ここでは、コードの検査と再現可能なビルドが重要です。

最後に、canister の制御を、いわゆる[「ブラックホール](https://github.com/ninegua/ic-blackhole)」[ canister に渡すという、やや有用な解決策があります。この](https://github.com/ninegua/ic-blackhole) canister は、自分自身しかコントローラを持ちませんが、ブラックホール化されたcanister の利用可能なcycles バランスなど、ブラックホールが制御するcanisters に関する有用な情報を第三者が取得できます。ブラックホールcanister のインスタンスは[e3mmv-5qaaa-aaaa-aadma-caiで](https://icscan.io/canister/e3mmv-5qaaa-aaaah-aadma-cai)、[ここで](https://github.com/ninegua/ic-blackhole)徹底的に文書化されています。ここにリンクされているリポジトリでは、canister 不変性について言及していますが、これは冗長です。NNSは、「ブラックホール化」canister 、またはブラックホールcanister それ自身によってコントロールされているcanister に変更を加えることができます。

## NNSによる単独制御と他のガバナンス

より複雑で強力なアプローチは、canister の唯一のコントローラーを分散ガバナンス機構に設定することです。この場合、NNSは、コントローラリストに明示的に含まれていなくても、canister を最終的に制御することができます。メリットは、より具体的なガバナンスメカニズムをcanister に集中できることです。 このようなガバナンスメカニズムが実装する複雑さと制御のさまざまなレベルを想像することができます。たとえば、(今後予定されている)[SNS機能では](https://medium.com/dfinity/how-the-service-nervous-system-sns-will-bring-tokenized-governance-to-on-chain-dapps-b74fb8364a5c)、開発者が自分のcanister のコントローラを、統治するcanister に設定できます。

言うまでもなく、信頼の要件は、canister を制御する SNS に移され、そこではコード検査と再現性に関するすべての考慮事項が適用されます。

<!---
# Trust in canisters

## Overview

A key aspect of DeFi and related applications in canisters is the ability to transfer value, e.g. ICP or Bitcoin. Such functionality makes trust in canisters essential. How can one ensure that it is safe to entrust ICPs to a canister?

The answer to this question has two separate dimensions:

1.  Confidence that the canister does what it is supposed to do.

2.  Confidence that the canister behavior will not unexpectedly change.

## Confidence that the canister does what it is supposed to do

The correct behavior of a canister can be checked in two steps. First, inspect the source code used to generate the Wasm code deployed in a canister to ensure that it implements the expected/claimed functionality, and only this functionality. 

Second, ensure that the Wasm module the canister runs, has indeed been generated from the claimed source code. Here, reproducibility of the build is crucial: the developer should have constructed the Wasm module so that precisely the same Wasm can be rebuilt from scratch. The user can then compare the hash of the rebuilt Wasm module with the module hash reported by the IC. Developers and users can find guidance on ensuring reproducibility in [reproducible canisters](/developer-docs/backend/reproducible-builds.md).

## Confidence that the canister behavior will not unexpectedly change

Canister smart contracts are deployed and managed by controllers. A controller's level of decentralization can range from being managed by a single person, or team of people up to being managed by the NNS or another kind of on-chain DAO. Among other capabilities, the controllers can change the code for the canisters which they control so canister code is **mutable**, unlike smart contracts on other blockchains. The controllers have complete control over the assets like ICP tokens or Bitcoins held by the canister they manage. This feature brings canisters closer to typical software and makes them suitable for a broad range of applications where software logic can be changed on an as-needed basis.

For critical applications like those used in DeFi, centralized mutability can be dangerous; the controller could change a benign canister into a canister that steals assets. Below we outline some options available to developers on how to verifiably decentralize the control of a canister's mutations.

:::info
Canister controllers, if not voluntarily decentralized, have complete control over the user assets held by the canister, for example, any ICP Tokens or Bitcoin held by the canister on the user's behalf. The controller, if malicious, can steal all the assets. In other words, as a user, if you interact with a canister that deals with your assets, inspect the canister to know how it handles them. If you determine that the canister is storing the assets in its subaccounts, ensure that the canister controller is decentralized.
:::

## Sole control by the Network Nervous System (NNS)

The simplest option is to remove a canister's controller. Without a controller, the canister can only be mutated by the NNS via NNS proposal, assuming the integrity of the platform is maintained.

A user can verify the list of controllers for a canister using dfx. For example:

    dfx canister --network ic info ryjl3-tyaaa-aaaaa-aaaba-cai

This will return the list of controllers for the canister with principal `ryjl3-tyaaa-aaaaa-aaaba-cai` (in this example, the ledger canister).

A user can also obtain the list of controllers of another canister via a [`read_state` request](/references/ic-interface-spec.md/#http-read-state) to get the relevant [canister information](/references/ic-interface-spec.md#state-tree-canister-information) which includes the list of controllers. NB: currently a canister cannot obtain this information.

A similar effect can also be achieved by setting the controller of a canister to be itself. In this case, however, you need to carefully verify that the canister cannot somehow submit a request to upgrade itself, e.g. by issuing a reinstall request. Here, code inspection and reproducible builds are crucial.

Finally, a somewhat more useful solution is to pass control of the canister to a so-called [“black hole” canister](https://github.com/ninegua/ic-blackhole). This canister has only itself as a controller but allows third parties to obtain useful information about the canisters the black hole controls, such as the available cycles balance of a black-holed canister. An instance of a black hole canister is [e3mmv-5qaaa-aaaah-aadma-cai](https://icscan.io/canister/e3mmv-5qaaa-aaaah-aadma-cai) which is thoroughly documented [here](https://github.com/ninegua/ic-blackhole). Note that the repository linked here mentions canister immutability, but this is red herring. The NNS is still capable of making changes to a canister that is controlled by a 'black holed' canister, or the black hole canister itself.

## Sole control by NNS and other governance

A more complex, but powerful approach, is to set the sole controller of the canister to a distributed governance mechanism. In this case, the NNS still has ultimate control over the canister, even though it is not explicitly in the controller list. The advantage is that the more specific governance mechanism can be more focused on the canister. One can imagine different levels of complexity and control that such a governance mechanism may implement. An example is the (upcoming) [SNS feature](https://medium.com/dfinity/how-the-service-nervous-system-sns-will-bring-tokenized-governance-to-on-chain-dapps-b74fb8364a5c) which allows developers to set the controller of their canister to some governing canister.

Needless to say, the trust requirements are moved to the SNS controlling the canister where all of the considerations regarding code inspection and reproducibility apply.

-->
