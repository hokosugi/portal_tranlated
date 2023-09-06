# デプロイとアップグレードcanisters

## 概要

デプロイ時には、主に2つの懸念事項があります:canisters とコードです。canisters の懸念は、コードが最終的に実行される場所と、それを適切な場所に持っていく方法のすべてです。もう1つの懸念は、データの損失がなく、ダウンタイムが可能な限り少なく、設定ミスがないことを保証するために、コードがどのように管理されるかです。デプロイ中には考慮すべきことがたくさんあり、うまくいかないこともたくさんあるため、開発者の生活を楽にするツールが存在します。そのようなツール（このページでは図解として使用します）の1つがICソフトウェア開発キット（SDK）です。

## Canister ライフcycle

Internet Computer は、いわゆるcanisters で任意のWebAssembly モジュールを実行します。最初のデプロイメントでは、実行されるはずの個々のWebAssembly モジュールごとに新しいcanisters を作成する必要があります。canisters を作成すると、canister ID が割り当てられます。canisters の中央レジストリは存在しないため、開発者（またはおそらく開発者のツール）はこれらの ID を追跡する必要があります。コードがcanisters にデプロイされると、canister ID を使って他のcanisters の関数を呼び出すことができます。

Canisters 実行中は を消費します。計算の実行には のコストがかかりますが、データの保存にもコストがかかります。 が実行されている限り、オペレーションを維持するのに十分な が必要です。ここでも、開発者（またはそのツール）がIDおよび/または の残高を追跡する必要があります。 の の残高が非常に少なくなった場合、フリーズされ (アップデートコールに応答しなくなることを意味します)、完全になくなった場合は削除されます。cycles cycles canister cycles cycles canister cycles

誰もがcanister を変更できるわけではありません。Canisters はすべて、設定を更新したり、実行中のコードをインストール/アップグレードしたり、canister を削除したりできるコントローラのリストを持っています。開発者、自動化ツール、他のcanisters 、またはまったく何もコントローラになることはできません。canister の目的によって、コントローラの組み合わせは異なります。以下の[信頼性のデモの](#demonstrating-trust)セクションで詳しく説明します。

canister の寿命が来たら、cycle を削除することができます。cycles そうする前に、(所有者の1人が)canister を削除する前に、残りのcycles を削除することを推奨します。cycles を削除するのは手作業でかなり面倒なプロセスなので、そのプロセスを容易にするツールが存在します。canister を削除する以外の方法としては、自動的に削除されるまで実行したままにすることもできます。

## コードのデプロイ

canisters は任意のWebAssembly (WASM) モジュールを実行することができますが、Internet Computer にはその機能を最大限に活用しやすくするための規約がいくつかあります。例えば、`canister_init` という関数は、コードが初めてインストールされた後に最初に呼び出される関数です。このような規約を守りやすくするために、Canister Development Kits (CDK) がさまざま[な言語](../backend/choosing-language.md)向けに用意されています。

canister にコードをインストールするには、Internet Computer の`install_code` 関数を 3 つのモードのいずれかで使用します：

- `install` モードは、すべてのcanister が最初に使用するモードです。このモードは、コードがインストールされていないcanisters に対してのみ呼び出すことができ、canister に提供されている WASM モジュールを入力します。インストールが完了すると、前述の関数`canister_init` (通常 CDK では`init` として公開されています) が存在すればそれが呼び出されます。これにより、呼び出しが行われる前に必要なセットアップを行うことができます。
- `reinstall` モードの動作は`install` とほぼ同じですが、canister にすでに実行中のコードが含まれている場合、そのステート は破棄され、実行中のコードは削除されます。その後は、モード`install` の手順に従います。
- `upgrade` モードは、最も頻繁に使用されるモードです。このモードでは、canister のステー トをすべて失うことなくコードを変更できます。このモードでは、canister はまず、`canister_pre_upgrade` （CDKでは`pre_upgrade` として公開されています）関数で、ステートを安定したメモリに保存します。その後、新しいコードがインストールされ、init関数を呼び出す代わりに`canister_post_upgrade` （CDKでは`post_upgrade` ）関数が実行され、安定したメモリからデータがロードされます。

`install_code` の3つのモードはすべてアトミックです。説明したステップのいずれかで**何か**問題が発生すると、canister のステートは、`install_code` 関数が呼び出される前の状態に戻ります。また、これらの説明だけではよくわからないことがある場合には、[ `install_code` の IC 仕様が](/references/ic-interface-spec.md#ic-install_code)公式の真実の情報源です。

:::caution
 Internet Computer 上のすべての関数呼び出しには、実行可能な命令数の制限があります。`pre_upgrade` 関数がこの制限を超えると、関数はキャンセルされ、アップグレードは失敗します。このため、canister をアップグレードできなくなる可能性があります。cycles の制限を回避する方法については、Wiki に[いくつかアイデアが](https://wiki.internetcomputer.org/wiki/Dealing_with_cycles_limit_exceeded_errors)あります。
::：

既存のcanisters をアップグレードする場合、さらにいくつか注意すべきことがあり ます：

- **未処理のコールバック**:canister が別のcanister からの応答を`await`している場合、応答を送受信する間にアップグレードできます。コードが`upgrade` モードでインストー ルされている場合、コールバックはアップグレードが行われなかったかの ように実行されます。このようなことが起こらないようにするには、canister をまず停止するか、コードをアンインストールしてから再度インストールする必要があります。
- **インターフェイスの互換性**:canisters やスクリプトが、アップグレードされたcanister が特定のインターフェイスを持つことを期待している場合、アップグレードによって既存のワークフローが壊れる可能性があります。`dfx` は、アップグレードによって特定のシグネチャが壊れることを（可能であれば）ユーザーに警告しますが、常に見逃される可能性のあるコーナーケースがあります。

## 考慮すべきこと

- [資金調達](#funding)。
- [信頼の実証](#demonstrating-trust)。

### 資金調達

Internet Computer canisters は、cycles の残高を使って資金を調達し、時間の経過とともに徐々にバーンダウンしていきます。割り当てたメモリ量と、クエリや更新にかかるCPU時間に対して支払うことになります。

以下は、検討する必要がある事柄のチェックリストです：

- **\[canister の資金源は何ですか**？
  - 開発者による前払い。
  - コミュニティからの寄付
  - ICPまたはcycles の継続的な収入によって賄われます。
- \[ \]** canister の残高はどのように監視**しますか?
- \[ \] 残高が**少なくなった場合の計画は**?
- \[ \] 残高が**なくなり、canister が最終的に消去された場合は**どうなりますか？

あなたのユースケースに応じて、あなたのアプリケーションにとって意味のある答えは異なるでしょう。追加されたすべてのコンテンツが永遠に利用可能であると仮定する他のブロックチェーンとは異なり、Internet Computer では、使用するコンピューティングリソースのコストと持続可能性について考える必要があります。

NFTプロジェクトやDeFicanister は、取引手数料を利用して独自のcycles を購入することで、自己資金を確保できるかもしれません。その他のユースケースの場合、企業やDAOに残高の監督を任せることもできます。いずれにせよ、canister の長寿命化をどのように図るかを考えることは価値があります。

### 信頼の実証

ブロックチェーン分野の大きなトピックは、ユーザーがやり取りするスマートコントラクトをどのように信頼できるかということです。展開したいプロジェクトの種類によって、適切な信頼のレベルは異なります。

以下は、検討する必要がある事柄のチェックリストです：

- **\[このプロジェクトにはどれくらいの信頼が必要ですか？**
- ** canisters \[このプロジェクトはどれだけの信頼を必要としますか** **？**
  - [ canisters](/concepts/trust-in-canisters.md)の[信頼と](/concepts/trust-in-canisters.md) [再現可能なビルドの](../backend/reproducible-builds.md)セクションに、このトピックに関連する情報があります。
- \[ \]**ユーザーは、コードが突然変更されないことをどのように信頼**できますか?

<!---
# Deploying and upgrading canisters

## Overview

During deployment, there are two main concerns: canisters and code. The concern of canisters is all about where the code will end up running, and how to get it to the right place. The other concern is how code is managed to ensure there is no data loss, as little downtime as possible, and no misconfigurations. Since there are a lot of things to consider and many things that can go wrong during deployment, tools exist to make developers' lives easier. One such tool (which will be used as illustration on this page) is the IC Software Development Kit (SDK).

## Canister lifecycle

The Internet Computer runs arbitrary WebAssembly modules in so-called canisters. For the first deployment, new canisters have to be created for every individual WebAssembly module that is supposed to run. On creation of those canisters, they will be assigned a canister ID. Since there is no central registry of canisters, the developers (or more likely their tools) have to keep track of those IDs. Once code is deployed to canisters, the canister IDs can be used to call functions on other canisters.

Canisters consume cycles while they're running. Performing computation costs cycles, but so does storing data. As long as a canister is supposed to be running it has to contain enough cycles to maintain operations. This again is a place where developers (or their tools) need to keep track of IDs and/or cycles balances. If a canister runs very low on cycles, it will be frozen (meaning it won't respond to update calls anymore), and if it runs out entirely, it will be deleted.

Not everyone is allowed to change a canister. Canisters all have a list of controllers that are allowed to update its settings, install/upgrade the running code or even delete the canister. Developers, automated tools, other canisters or nothing at all can be controllers. Depending on the goals for the canister, different combinations of controllers can make sense. The below section [demonstrating trust](#demonstrating-trust) explains more about that.

Once a canister is at the end of its lifecycle, it can be deleted. Before doing so, it is recommended that (one of) its owner(s) withdraws remaining cycles before they delete the canister. Otherwise those cycles are lost. Since withdrawing those cycles is a rather tedious process manually, tools exist to facilitate the process. The other option besides deleting the canister is to just leave it running until it gets deleted automatically.

## Deploying code

While canisters can run arbitrary WebAssembly (WASM) modules, the Internet Computer has a few conventions that make it easier to get the most out of its capabilities. For example, the function `canister_init` is the first function that gets called after the code is installed for the first time. To facilitate adhering to those conventions, Canister Development Kits (CDKs) exist for [many different languages](../backend/choosing-language.md).

To install code in a canister, the `install_code` function of the Internet Computer is used in one of three modes:
- The `install` mode is the one every canister starts with: it is only callable for canisters without any installed code and populates the canister with the supplied WASM module. Once installation is complete, the aforementioned function `canister_init` (usually exposed as `init` in CDKs) is called if it exists. This allows the code to perform any required setup before any calls arrive.
- The `reinstall` mode works almost the same as `install`, but if the canister already contains running code, its state is discarded and the running code is deleted. After that, the procedure from mode `install` is followed.
- The `upgrade` mode is the one used most often. This mode allows canister code to be changed without losing all of its state. In this mode, the canister first has the chance to save any state to stable memory in the `canister_pre_upgrade` (exposed as `pre_upgrade` in CDKs) function. After that, the new code is installed and instead of calling the init function, the `canister_post_upgrade` function (exposed as `post_upgrade` in CDKs) is run so that data can be loaded from stable memory.

All three `install_code` modes are atomic. If **anything** goes wrong in one of the described steps, the canister state is reverted to its state before the `install_code` function was called. And in case anything is not clear enough from these explanations, the [IC specification for `install_code`](/references/ic-interface-spec.md#ic-install_code) is the official source of truth.

:::caution
Every function call on the Internet Computer has a limit to how many instructions can be executed. If your `pre_upgrade` function exceeds this limit, it will be canceled and the upgrade fails. This can make a canister un-upgradeable. The Wiki [contains some ideas](https://wiki.internetcomputer.org/wiki/Dealing_with_cycles_limit_exceeded_errors) how one can work around the cycles limit.
:::

When upgrading existing canisters, there are a few more things that one should keep in mind:
- **Outstanding callbacks**: if a canister is `await`ing a response from another canister, it can be upgraded in-between sending and receiving a response. If the code is installed in `upgrade` mode, the callback will be executed as if no upgrade has been made. If this should not happen, the canister should either first be stopped, or the code has to be uninstalled and then installed again.
- **Interface compatibility**: if canisters or scripts expect the upgraded canister to have a certain interface, upgrades can break existing workflows. `dfx` will warn the user (if possible) that the upgrade will break certain signatures, but there are always corner cases that may be missed.

## Things to consider

  * [Funding](#funding).
  * [Demonstrating trust](#demonstrating-trust).

### Funding

Internet Computer canisters are funded using a balance of cycles that will gradually burn down over time. You will be paying for the amount of memory you have allocated, as well as the CPU time that queries and updates take up.

Here is a checklist of the things you will need to consider:

- [ ] **What will the canister's source of funds be?**
  * Paid for up-front by the developer.
  * Funded by donations from the community.
  * Funded by ongoing revenue in ICP or cycles.
- [ ] **How will I monitor the canister's balance?**
- [ ] **What is your plan in case the balance runs low?**
- [ ] **What will happen if the balance runs out and the canister is eventually erased?**

Depending on your use case, different answers will make sense for your application. Unlike other blockchains, where you assume that all content added is available forever, the Internet Computer requires you to think about the costs and sustainability of the computing resources that you are using.

An NFT project or DeFi canister may be able to self-fund by using transaction fees to purchase their own cycles. For other use cases, you may want a company or DAO to be responsible for supervising the balance. In any case, it is worthwhile to think about how you plan to ensure your canister's longevity.

### Demonstrating trust

A big topic in the blockchain space is how users can trust the smart contracts they interact with. Depending on the kind of project you want to deploy, different levels of trust can be appropriate.

Here is a checklist of the things you will need to consider:

- [ ] **How much trust does this project require?**
- [ ] **How can I demonstrate that the canisters do what they are supposed to do?**
  * The sections [trust in canisters](/concepts/trust-in-canisters.md) and [reproducible builds](../backend/reproducible-builds.md) contain information related to this topic.
- [ ] **How can users trust that the code will not suddenly change?**

-->
