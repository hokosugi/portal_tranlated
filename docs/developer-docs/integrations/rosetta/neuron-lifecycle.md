# Neuron ライフcycle

## 概要

neuron は、Internet Computer ガバナンスcanister によって管理される資産の一種です。Neuronは、提案の提出や投票によって、その管理者がネットワークのガバナンスに参加することを可能にします。

## 。neuron

neuron の作成には2つのステップがあります：

- 任意の台帳アカウントからneuronのアドレスに**ICPを**送金します。Neuron ガバナンスに属する台帳上のアドレスcanister 。呼び出し側は、2つの部分からneuron アドレスを計算します：
  
  - ガバナンスのプリンシパルcanister 。
  
  - **サブアカウントは**、それ自体が、コントローラのプリンシパルと整数 のnonceとのハッシュです。1つのプリンシパルが複数のneuronを制御するには、サブアカウントの計算に異なる整数ノ ンスを使用します。

- ガバナンスの`claim_or_refresh_neuron_from_account` メソッドを呼び出すcanister 。呼び出しが成功すると、ペイロードには**neuron ID** が含まれます。これは、新しく作成されたneuron の数値識別子です。ガバナンスcanister はneuron ID とサブアカウント間のマッピングを保持します。ほとんどの管理操作は、neuron IDまたはサブアカウントアドレスのいずれかを使用して実行できます。

## Neuron 属性

neuron には、その寿命cycle と報酬分配を制御する以下の属性があります：

### 溶解遅延

は、neuron が溶解を開始してから「液体」になるまでの時間です。neuron がすでに溶解している場合、ディレイはneuron が**DISSOLVED**ステートに遷移するまでの残り時間を示します。新しく作成されたneuron の溶解遅延は 0 です。少なくとも 15778800 秒 (約 6 か月) の溶解遅延を持つneurons だけが、提案の投票に使用できます (2021-07-27 時点で、正確な遅延要件は変更される可能性があります)。一度設定した溶解遅延時間は、管理コマンドで短縮することはできません。

### 解散ステート

#### NOT\_DISSOLVING

Neuron は固定のディゾルブ遅延を持ち、**エイジが**発生します。これは新しく作成された のデフォルトステートです。neuron

#### DISSOLVING

Neuronのディゾルブ遅延は時間の経過とともに減少します。溶解遅延が 0 になると、neuron は**DISSOLVED**ステートに遷移し、張り付けられた ICP を払い出すことができます。

#### DISSOLVED

Neuronの溶解遅延は0です。neuron ホルダーは、ステークされた ICP を払い出すか、溶解遅延を増加させるかのいずれかを自由に行うことができ、**NOT\_DISSOLVING**ステートに遷移します。

### 年齢

Ageは、neuron が最後に**NOT\_DISSOLVING**ステートに遷移してからの経過時間。neuron が**DISSOLVING** の場合、Age は 0 です。

### 成熟度

成熟度は、このneuron 。Neuronのコントローラは、2つの方法で成熟度を消費できます：

- 溶解遅延が7日で、満期金と同額のステークを持つ新しいneuron を**生成**します。

- 満期をステークに**マージ**し、投票力を増加させます。

### 投票力

投票権はステークされたICPの**量に**比例します。neuron が**NOT\_DISSOLVING**ステートの場合、年齢と溶解遅延の乗算ボーナスが投票力を増加させます。

## 報酬

提案が受理されると、その提案に投票したすべてのneuronは満期の増加という形で報酬を受け取ります。ガバナンスcanister は毎日報酬を分配します。報酬は投票力に比例します。

neuron のステート遷移と報酬の詳細については、[ Internet ComputerのNetwork Nervous System](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8) 、[ neuron](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8) の[理解、および ICP ユーティリティ・トークンを](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8)参照してください。

## ステート遷移

    stateDiagram-v2
        state "Non-dissolving Neuron" as non_dissolving
        state "Dissolving Reward Neuron" as dissolving
        state "Dissolved Reward Neuron" as dissolved
        [*] --> non_dissolving : transfer + claim_or_refresh_neuron_from_account
        non_dissolving --> non_dissolving : increase_dissolve_delay
        non_dissolving --> dissolving : start_dissolving
        dissolving --> non_dissolving : stop_dissolving
        dissolving --> dissolved : passage of time
        dissolved --> non_dissolving: increase_dissolve_delay
        dissolved --> [*] : disburse

<!---
# Neuron lifecycle

## Overview

A neuron is a type of asset managed by the Internet Computer governance canister. Neurons allow their controllers to participate in the governance of the network by submitting and voting on proposals.

## Creating a neuron

Creating a neuron involves 2 steps:

-   Transfering some **amount** of ICPs from any ledger account to the neuron’s address. Neuron address on the ledger that belongs to the Governance canister. The caller computes the neuron address from two parts:

    -   The principal of the governance canister.

    -   The **subaccount** that is itself a hash of the controller’s principal with an integer nonce. One principal can control multiple neurons by picking different integer nonces for the subaccount computation.

-   Calling the `claim_or_refresh_neuron_from_account` method of the governance canister. If the call is successful, the payload contains the **neuron ID**, a numeric identifier of the newly created neuron. The governance canister maintains a mapping between neuron IDs and subaccounts. Most management operations can be performed using either the neuron ID or the subaccount address.

## Neuron attributes

A neuron has the following attributes that control its lifecycle and rewards distribution:

### Dissolve Delay  
is how long it will take a neuron to become "liquid" once it starts dissolving. If the neuron is already dissolving, the delay indicates the amount of time left before the neuron transitions to the **DISSOLVED** state. The dissolve delay of a newly created neuron is 0. Only neurons with a dissolve delay of at least 15778800 seconds (approx 6 months) can be used to vote on proposals (as of 2021-07-27, the exact delay requirements are subject to change). Once set, the dissolve delay cannot be reduced via management commands.

### Dissolve State  
#### NOT_DISSOLVING  
Neuron has a fixed dissolve delay and accrues **age**. This is the default state of a newly created neuron.

#### DISSOLVING  
Neuron’s dissolve delay is decreasing with the passage of time. Once the dissolve delay becomes 0, the neuron transitions to the **DISSOLVED** state and the staked ICPs can be disbursed.

#### DISSOLVED  
Neuron’s dissolve delay is 0. The neuron holder is free to either disburse the staked ICPs or increase the dissolve delay, which will cause a transition to the **NOT_DISSOLVING** state.

### Age  
Age is the amount of time that passed since the last time the neuron transitioned to the **NOT_DISSOLVING** state. If the neuron is **DISSOLVING**, its age is 0.

### Maturity  
Maturity is the total amount of unspent rewards accrued by this neuron. Neuron’s controller can spend maturity in two ways:

- **Spawn** a new neuron with a dissolve delay of seven days and stake equal to the maturity.

- **Merge** maturity into the stake, increasing voting power.

### Voting power  
Voting power is proportional to the **amount** of staked ICPs. Age and dissolve delay multiplicative bonuses increase the voting power if the neuron is in the **NOT_DISSOLVING** state.

## Rewards

Once a proposal is accepted, all the neurons that voted on that proposal receive rewards in form of a maturity increase. The governance canister distributes rewards daily. The rewards are proportional to the voting power.

For more information on neuron state transitions and rewards, see [understanding the Internet Computer’s Network Nervous System, neurons, and ICP utility tokens](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8).

## State transitions

    stateDiagram-v2
        state "Non-dissolving Neuron" as non_dissolving
        state "Dissolving Reward Neuron" as dissolving
        state "Dissolved Reward Neuron" as dissolved
        [*] -!-> non_dissolving : transfer + claim_or_refresh_neuron_from_account
        non_dissolving -!-> non_dissolving : increase_dissolve_delay
        non_dissolving -!-> dissolving : start_dissolving
        dissolving -!-> non_dissolving : stop_dissolving
        dissolving -!-> dissolved : passage of time
        dissolved -!-> non_dissolving: increase_dissolve_delay
        dissolved -!-> [*] : disburse

-->
