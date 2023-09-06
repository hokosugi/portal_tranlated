---

sidebar_position: 8
---
# 定期的なタスクとタイマー

## 概要

他のブロックチェーンとは異なり、Internet Computer は指定された遅延の後、または定期的にcanister スマートコントラクトを自動的に実行することができます。

IC上でcanister の自動実行をスケジュールする方法は2つあります：

1.  **タイマー**：最小タイムアウトまたは間隔を指定した、単一期限または定期的なcanister 呼び出し。
2.  **ハート**ビート：ブロックチェーンの最終化レート（1秒）に近い間隔で、レガシーな定期的canister 呼び出し。ハートビートは、後方互換性と非常に特殊なユースケースのためにICでサポートされています。**新しく開発されたcanisters では、ハートビートよりもタイマーを使用することをお勧めします。**

## タイマー

タイマーは2つのレイヤーで実装されています：

- **プロトコル・レベルの実装：** Internet Computer protocol は、`ic0.global_timer_set()` システムAPIコールと`canister_global_timer` ハンドラを介して、canister ごとに最小限のオンショット・グローバル・タイマーをサポートします（[Internet Computer インタフェース仕様を](../../references/ic-interface-spec.md#timer)参照）。
- **CDK timers ライブラリレベル:**ライブラリは最小限のプロトコル実装をラップし、その上にマルチタイマーと周期タイマーを追加します。Canister 開発者は、[Rust](https://crates.io/crates/ic-cdk-timers)用の CDK timers ライブラリや [Motoko](../../motoko/main/timers.md).

内部的には、CDK timers ライブラリは以下のことを行います：

1.  ライブラリは、canister 内部の複数タスクと周期タスクのグローバルリストを保持します。
2.  `ic0.global_timer_set()` システム API を呼び出して、リストから次のタスクをスケジュールします。
3.  以下のロジックで`canister_global_timer` メソッドを実装します：
    - 期限切れの各タスクについて、ハンドラは selfcanister 呼び出しを開始し、タスクを互いに、またライブラリのコードから分離します。[通常のインターcanister コールのコストが](../gas-cost.md)適用されます。
    - 実行終了時に定期タスクを再スケジュールします。
    - 次のタスクをスケジュールするために`ic0.global_timer_set()` システム API を呼び出します。

:::info
ライブラリはcanister のアップグレードを処理しません。`canister_pre_upgrade` でタイマーをシリアライズし、必要に応じて`canister_post_upgrade` メソッドでタイマーを再アクティブ化するのは、canister 開発者次第です。
::：

:::caution
コードコンポーザビリティの理由から、つまり、1つのプロジェクトでタイマーを持つ異なるライブラリを使用できるように、canister 開発者はプロトコルレベルのAPIやハートビートよりもCDKタイマーライブラリを使用することが推奨されます。
::：

### タイマー・ライブラリの制限

ハートビートよりも優れているにもかかわらず、CDK タイマーライブラリにはいくつかの既知の欠点があります：

- **Canister アップグレード:**ライブラリは、 ヒープ内に複数の定期的なタスクのグローバルリストを保持します。 のアップグレード中に、新しい ステートが作成され、すべてのタイマーが非アクティブになり、タイマーのリストがクリアされます。 でタイマーをシリアライズし、必要に応じて メソッドで再アクティブ化するのは、 開発者次第です。canister canister WebAssembly `canister_pre_upgrade` `canister_post_upgrade` canister 
- **自己canister 呼び出し:**タスクを互いに、またスケジューリングロジックから分離するために、CDK timersライブラリは各タスクを実行するために自己canister 呼び出しを開始します。いくつかの意味があります：
  - 各タスクの実行には、通常の[インターcanister コールのコストが](../gas-cost.md)適用されます。各タスクの実行には通常の呼び出し間コスト（）が適用されます。なお、タイマーの方がハートビート[よりもコスト効率が](https://github.com/dfinity/examples/tree/master/rust/periodic_tasks)高いことに注意してください。
  - 実行するタスクは、canister 入力キューの最後に追加されます。canister とサブネットの負荷によっては、実際の実行が遅れることがあります。
  - canister の出力キューのサイズには制限があるため (現時点では 500 メッセージ)、1 ラウンドでスケジューリングできるタスクの数が暗黙のうちに制限されます。
- **高度なスケジューリング:**CDK timers ライブラリはタスクのスケジューリングに相対時間を使用します。絶対時間を使うには、canister 、現在からその時点までの時間を計算するか、サードパーティ製のライブラリを使う必要があります。

## ハートビート

Internet Computer canister が`canister_heartbeat` 関数をエクスポートすると、サブネットのハートビート間隔ごとに呼び出されます（[Internet Computer インタフェース仕様を](../../references/ic-interface-spec.md#heartbeat)参照）。

ハートビートを無効にする唯一の方法は、canister を`canister_heartbeat` メソッドをエクスポートしないバージョンにアップグレードすることです。また、ハートビート間隔は実装定義であり、調整する方法はありません。

このような制限があるため、多くの場合、定期的なスケジューリングには[Rust](https://crates.io/crates/ic-cdk-timers)用のCDKタイマーライブラリか [Motoko](../../motoko/main/timers.md)を使うほうがよいでしょう。

## よくある質問

- #### タイマーは決定論的タイム・スライシング（DTS）をサポートしていますか？

はい、CDK timers library は各タスクを実行するために selfcanister コールを開始するため、DTS を有効にすると通常の[アップデートメッセージ命令の制限が](../production/instruction-limits.md)適用されます。

- #### タイマーハンドラが呼び出し待ちの場合、どうなりますか？

通常の待機ポイントのルールが適用されます。待機ポイントでは、新しいメッセージ、別のタイマー・ハンドラ、ハートビートなど、任意の新しい実行を開始できます。その新しい実行が終了するか、待ち合わせポイントに達すると、現在のタイマーハンドラの実行が再開されます。

- #### 1秒周期タイマが2秒間実行されるとどうなりますか？

タイマーハンドラにawaitポイントがなければ、定期タイマーは実行終了時に再スケジュールされます。awaitポイントがある場合、周期タイマーがいつ再スケジュールされるかは実装によって決まります。

## チュートリアルと例

### チュートリアルとサンプルMotoko

- **Motoko 開発者ガイド** [タイマー](../../motoko/main/timers.md)
- **Motoko 開発者ガイド: タイマー：** [ハートビート](../../motoko/main/heartbeats.md)

### Rust を使ったチュートリアルと例

- **バックエンドチュートリアル：** [タイマーの使用法](rust/10-timers.md)。
- **例** [定期的なタスクとタイマー](https://github.com/dfinity/examples/tree/master/rust/periodic_tasks)（タイマーとハートビートのコストの比較）。

<!---

# Periodic tasks and timers

## Overview
Unlike other blockchains, the Internet Computer can automatically execute canister smart contracts after a specified delay or periodically.

There are two ways to schedule an automatic canister execution on the IC:

1. **Timers**: single-expiration or periodic canister calls with specified minimum timeout or interval.
2. **Heartbeats**: legacy periodic canister invocations with intervals close to the blockchain finalization rate (1s). Heartbeats are supported by the IC for backward compatibility and some very special use cases. **Newly developed canisters should prefer using timers over the heartbeats.**

## Timers

Timers are implemented on two layers:

- **The protocol level implementation:** the Internet Computer protocol supports minimalistic on-shot global timer per canister via `ic0.global_timer_set()` system API call and `canister_global_timer` handler (see the [Internet Computer interface specification](../../references/ic-interface-spec.md#timer)).
- **The CDK timers library level:** the library wraps the minimalistic protocol implementation, adding multiple and periodic timers on top. Canister developers can enjoy the familiar timers functionality using the CDK timers library for [Rust](https://crates.io/crates/ic-cdk-timers) or [Motoko](../../motoko/main/timers.md).

Internally the CDK timers library does the following:

1. The library keeps a global list of multiple and periodic tasks inside the canister.
2. Calls the `ic0.global_timer_set()` system API to schedule the next task from the list.
3. Implements the `canister_global_timer` method with the following logic:
   * For each expired task, the handler initiates a self canister call to isolate the tasks from each other and from the library code. Note, the [normal inter-canister call costs](../gas-cost.md) costs apply.
   * Reschedules periodic tasks at the end of their execution.
   * Calls the `ic0.global_timer_set()` system API to schedule the next task.

:::info
The library does not handle the canister upgrades. It is up to the canister developer to serialize the timers in the `canister_pre_upgrade` and reactivate the timers in the `canister_post_upgrade` method if needed.
:::

:::caution
For the code composability reasons, i.e. to be able to use different libraries with timers in a single project, canister developers are encouraged to use the CDK timers library over the protocol level API or the heartbeats.
:::

### Timers library limitations

Despite its superiority over the heartbeats, the CDK timers library has a few known shortcomings:

- **Canister upgrades:** the library keeps a global list of multiple and periodic tasks inside the canister heap. During the canister upgrade, a fresh WebAssembly state is created, all the timers are deactivated and the list of timers is cleared. It is up to the canister developer to serialize the timers in the `canister_pre_upgrade` and reactivate them in the `canister_post_upgrade` method if needed.
- **Self canister calls:** to isolate the tasks from each other and from the scheduling logic, CDK timers library initiates a self canister call to execute each task. There are a few implications:
   * Normal [inter-canister call costs](../gas-cost.md) apply to execute each task. Note, timers are [still more cost effective](https://github.com/dfinity/examples/tree/master/rust/periodic_tasks) than the heartbeats.
   * The tasks to execute are added at the end of the canister input queue. Depending on the canister and subnet load, the actual execution might be delayed.
   * As the canister output queue is limited in size (500 messages at the moment), this implicitly limits the number of tasks which might be scheduled in one round.
- **Advanced scheduling:** the CDK timers library uses relative time to schedule tasks. To use an absolute time, canister developers should calculate the duration between now and the point in time, or use a third party library.

## Heartbeats

Once an Internet Computer canister exports the `canister_heartbeat` function, it will be called every subnet heartbeat interval (see the [Internet Computer interface specification](../../references/ic-interface-spec.md#heartbeat)).

The only way to disable the heartbeats is to upgrade the canister to a version which does not export the `canister_heartbeat` method. Also, the heartbeat interval is implementation-defined, and there is no way to adjust it.

Because of those limitations, in most cases CDK timers library for [Rust](https://crates.io/crates/ic-cdk-timers) or [Motoko](../../motoko/main/timers.md) is a better option to schedule periodic tasks.

## Frequently asked questions

- #### Do timers support deterministic time slicing (DTS)?  
Yes, as the CDK timers library initiates a self canister call to execute each task, normal [update message instruction limits](../production/instruction-limits.md) apply with DTS enabled.

- #### What happens if a timer handler awaits for a call?  
Normal await point rules apply: any new execution can start at the await point: a new message, another timer handler or a heartbeat. Once that new execution is finished or reached its await point, the execution of the current timer handler might be resumed.

- #### What happens if a 1s periodic timer is executing for 2s?  
If there are no await points in the timer handler, the periodic timer will be rescheduled at the end of the execution. If there are await points, it's implementation-defined when the periodic timer is rescheduled.

## Tutorials and examples

### Tutorials and examples using Motoko

- **Motoko developer guide:** [Timers](../../motoko/main/timers.md).
- **Motoko developer guide:** [Heartbeats](../../motoko/main/heartbeats.md).

### Tutorials and examples using Rust

- **Backend tutorial:** [Using timers](rust/10-timers.md).
- **Example:** [Periodic tasks and timers](https://github.com/dfinity/examples/tree/master/rust/periodic_tasks) (compares the costs of timers and heartbeats).

-->
