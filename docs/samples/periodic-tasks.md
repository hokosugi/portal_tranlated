# 定期的なタスクとタイマー

## 概要

他のブロックチェーンとは異なり、Internet Computer は指定された遅延の後、または定期的にcanister スマートコントラクトを自動的に実行することができます。

IC上でcanister の自動実行をスケジュールするには2つの方法があります：

1.  **タイマー**：最小タイムアウトまたは間隔を指定したワンショットまたは定期的なcanister 呼び出し。
2.  **ハート**ビート：ブロックチェーンの最終化レート（1秒）に近い間隔で、レガシーな定期的canister 呼び出し。ハートビートは、後方互換性と非常に特殊なユースケースのためにICでサポートされています。新しく開発されたcanisters は、ハートビートよりもタイマーを使用することをお勧めします。

この例では、Internet Computer で周期的タスクをスケジューリングするさまざまな方法（タイマーとハートビート）を示します。この例では両者の違いを示し、どの方法が最も適しているかを判断するのに役立ちます。

この例は、`heartbeat` と`timer` という2つのcanisters で構成されており、どちらも同じ機能を実装しています。

## チュートリアル

## 前提条件

この例では、以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールしてください。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください: https://github.com/dfinity/examples

### 例 1：

- #### ステップ 1: ターミナル・ウィンドウを開き、プロジェクトのディレクトリに移動します。

<!-- end list -->

``` sh
cd examples/rust/periodic_tasks
```

- #### ステップ2: きれいなローカルのInternet Computer レプリカとウェブサーバーを起動します：

<!-- end list -->

``` sh
dfx stop
dfx start --clean
```

このターミナルは、`Ctrl+C` が押されるか、`dfx stop` コマンドが実行されるまで、ログメッセージを出力しながらブロックされたままになります。

出力例：

``` sh
% dfx stop && dfx start --clean
[...]
Dashboard: http://localhost:63387/_/dashboard
```

- #### ステップ 3: 同じディレクトリで別のターミナル・ウィンドウを開きます：

<!-- end list -->

``` sh
cd examples/rust/periodic_tasks
```

- #### ステップ4:`heartbeat` と`timer` canisters をコンパイルしてデプロイし、定期的なタスクの間隔を10秒に設定します：

<!-- end list -->

``` sh
dfx deploy --argument 10
```

どちらのcanisters でも、カウンターは～10秒ごとに増加し始めます。

出力の例です：

``` sh
% dfx deploy --argument 10
[...]
Deployed canisters.
URLs:
   Backend canister via Candid interface:
      heartbeat: http://127.0.0.1/...
      timer: http://127.0.0.1/...
```

- #### ステップ5: 10秒後、canisters の両方で、同様の非ゼロ・カウンターを観察します：

<!-- end list -->

``` sh
dfx canister call heartbeat counter
dfx canister call timer counter
```

canisters が1つずつ展開されるため、カウンターにわずかな不一致があるかもしれないことに注意してください。

出力例：

``` sh
% dfx canister call heartbeat counter               
(8 : nat32)
% dfx canister call timer counter    
(7 : nat32)
```

- #### ステップ6: 10秒間隔で定期タスクをスケジュールするために使用されるcycles の量を比較します：

<!-- end list -->

``` sh
dfx canister call heartbeat cycles_used
dfx canister call timer cycles_used
```

出力例：

``` sh
% dfx canister call heartbeat cycles_used
(10_183_957 : nat64)
% dfx canister call timer cycles_used
(2_112_067 : nat64)
```

10秒間隔の定期タスクでは、`heartbeat` canister が`timer` canister よりもcycles を*多く*使用しています。

タイマーの方がcycles の使用量が少ないだけでなく、よりコンポーザブルです。グローバル・ステートやエクスポートするメソッドがないため、タイマーを使った異なるライブラリを同じプロジェクトで簡単に使うことができます。

また、タイマはスケジューリング・ロジックと周期タスクの分離を提供します。定期的なタスクが失敗した場合、このタスクが行った変更は全て元に戻りますが、タイマー・ライブラリのステートは更新されます。

このような実行コンテキストとスケジューリング・コンテキストの分離のために、内部的に timers ライブラリは selfcanister 呼び出しを使用します：

``` rust
# This is a pseudo-code of a self call:
ic_cdk::call(ic_cdk::id(), "periodic_task", ());
```

このような自己canister 呼び出しには[コストが](https://internetcomputer.org/docs/current/developer-docs/production/computation-and-storage-costs)かかりますが、それでもtimersライブラリのcycles 使用量はハートビートより少なくなります。

### 例2：1秒間隔のタスクのCycles 。

- #### ステップ1：例のルート・ディレクトリで新しいターミナル・ウィンドウを開きます：

<!-- end list -->

``` sh
cd examples/rust/periodic_tasks
```

- #### ステップ2: きれいなローカルInternet Computer レプリカとウェブサーバーを開始します：

<!-- end list -->

``` sh
dfx stop
dfx start --clean
```

このターミナルは、`Ctrl+C` が押されるか、`dfx stop` コマンドが実行されるまで、ログメッセージを表示しながらブロックされたままになります。

- #### ステップ3：同じディレクトリに別のターミナルウィンドウを開きます：

<!-- end list -->

``` sh
cd examples/rust/periodic_tasks
```

出力例：

``` sh
% dfx stop && dfx start --clean
[...]
Dashboard: http://localhost:63387/_/dashboard
```

- #### ステップ4:.`heartbeat` と`timer` canisters をコンパイルしてデプロイし、定期タスクの間隔を1秒に設定します：

<!-- end list -->

``` sh
dfx deploy --argument 1
```

どちらのcanisters でも、カウンターは1秒ごとに増加し始めます。

出力例：

``` sh
% dfx deploy --argument 1
[...]
Deployed canisters.
URLs:
   Backend canister via Candid interface:
      heartbeat: http://127.0.0.1/...
      timer: http://127.0.0.1/...
```

- #### ステップ5: 数秒後、canisters の両方で、同様のゼロでないカウンターを観察します：

<!-- end list -->

``` sh
dfx canister call heartbeat counter
dfx canister call timer counter
```

canisters が1つずつ展開されるため、カウンターにわずかな不一致があるかもしれないことに注意してください。

出力例：

``` sh
% dfx canister call heartbeat counter               
(8 : nat32)
% dfx canister call timer counter    
(9 : nat32)
```

- #### ステップ6: 1秒間隔で定期タスクをスケジュールするために使用されるcycles の量を比較します：

<!-- end list -->

``` sh
dfx canister call heartbeat cycles_used
dfx canister call timer cycles_used
```

出力例：

``` sh
% dfx canister call heartbeat cycles_used
(2_456_567 : nat64)
% dfx canister call timer cycles_used
(4_545_326 : nat64)
```

1秒間隔の定期タスクでは、`heartbeat` canister 、`timer` canister 、cycles の使用*量が少なくなって*います。

この場合、`heartbeat` の方がcycles よりも使用量が少ないにもかかわらず、この解決策は大きなプロジェクト内で構成するのは困難です。ハートビートを内部的に使用しているライブラリが2つある場合、ハートビートに必要なグローバルな`canister_heartbeat` メソッドをエクスポートしようとするため、一緒にコンパイルすることすらできません。

また、スケジューリング・ロジックと周期的タスクは分離されていません。定期タスクが失敗すると、タスクと`canister_heartbeat` メソッドで行われた変更はすべて元に戻ります。つまり、失敗したタスクはハートビートごとに何度も実行されることになります。

このような実行コンテキストとスケジューリング・コンテキストの分離のために、timers ライブラリは`Demo 1` で説明されているように、内部的に selfcanister 呼び出しを使用します。このような selfcanister 呼び出しに関連する[コストが](https://internetcomputer.org/docs/current/developer-docs/production/computation-and-storage-costs)かかるため、`timer` canister は、非常に頻繁に実行される周期的タスクのために、より多くのcycles を使用します。

## さらなる学習

1.  ローカルで実行されているダッシュボードを見てください。URLは`dfx start` コマンドの最後にあります：`Dashboard: http://localhost/...`
2.  `heartbeat` と`timer` canisters キャンディッド・ユーザー・インターフェイスをチェックしてください。URLは`dfx deploy` コマンドの最後にあります：`heartbeat: http://127.0.0.1/...`
3.  `timer` と`heartbeat` canisters で、定期タスクの実行コストを均等にするインターバルを見つけてください：`dfx deploy --argument 5`

### Canister インターフェース

`heartbeat` および`timer` canisters は以下のインターフェイスを提供します：

- `counter` - の値、すなわち定期タスクが何回実行されたかを返します（クエリ、両方 ）。`COUNTER` canisters
- `start_with_interval_secs` - 指定された間隔（秒単位）で新しいタイマーを開始します（タイマー ）。canister
- `set_interval_secs` - 定期タスクを呼び出す新しい間隔を秒単位で設定します（heartbeat ）。canister
- `stop` - 定期タスクの実行を停止します (両方 )canisters
- `cycles_used` - 定期タスクで観測された の数を返します (両方 )cycles canisters

使用例：

``` sh
dfx canister call timer start_with_interval_secs 5
```

## まとめ

コードの構成性、実行コンテキストの分離、コスト効率の観点から、canister の開発者はハートビートよりもタイマーを使用することをお勧めします。

`Example 2` にあるように、ハートビートにはまだ特殊な使用例があるかもしれません。そのようなケースは、コンポーザビリティと分離の問題を念頭に置いて、ケースバイケースで検討されるべきです。

## セキュリティの考慮事項とセキュリティのベストプラクティス

この例に基づいてアプリケーションを開発する場合、Internet Computer で開発するための[セキュリ ティ・ベスト・プラクティスを](https://internetcomputer.org/docs/current/references/security/)よく理解し、遵守することを推奨します。 この例では、すべてのベスト・プラクティスを実装していないかもしれません。

<!---
# Periodic tasks and timers

## Overview

Unlike other blockchains, the Internet Computer can automatically execute canister smart contracts after a specified delay or periodically.

There are two ways to schedule an automatic canister execution on the IC:

1. **Timers**: one-shot or periodic canister calls with specified minimum timeout or interval.
2. **Heartbeats**: legacy periodic canister invocations with intervals close to the blockchain finalization rate (1s). Heartbeats are supported by the IC for backward compatibility and some very special use cases. Newly developed canisters should prefer using timers over the heartbeats.

This example demonstrates different ways of scheduling periodic tasks on the Internet Computer: timers and heartbeats. The example shows the difference between the two, and helps to decide which method suits you the best.

The example consist of two canisters named `heartbeat` and `timer`, both implementing the same functionality: schedule a periodic task to increase a counter.

## Tutorial

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples

### Example 1:

- #### Step 1: Begin by opening a terminal window and navigating into the project's directory.

```sh
cd examples/rust/periodic_tasks
```

- #### Step 2: Start a clean local Internet Computer replica and a web server:

```sh
dfx stop
dfx start --clean
```

This terminal will stay blocked, printing log messages, until the `Ctrl+C` is pressed or `dfx stop` command is run.

Example output:

```sh
% dfx stop && dfx start --clean
[...]
Dashboard: http://localhost:63387/_/dashboard
```

- #### Step 3: Open another terminal window in the same directory:

```sh
cd examples/rust/periodic_tasks
```

- #### Step 4: Compile and deploy `heartbeat` and `timer` canisters, setting the interval for periodic tasks to 10s:

```sh
dfx deploy --argument 10
```

The counter will start increasing every ~10s in both canisters.

Example output:

```sh
% dfx deploy --argument 10
[...]
Deployed canisters.
URLs:
   Backend canister via Candid interface:
      heartbeat: http://127.0.0.1/...
      timer: http://127.0.0.1/...
```

- #### Step 5: After 10s, observe similar non-zero counters in both canisters:

```sh
dfx canister call heartbeat counter
dfx canister call timer counter
```

Note, as the canisters deployed one by one, there might be a minor discrepancy in the counters.

Example output:

```sh
% dfx canister call heartbeat counter               
(8 : nat32)
% dfx canister call timer counter    
(7 : nat32)
```

- #### Step 6: Compare the amount of cycles used to schedule the periodic task with 10s interval:

```sh
dfx canister call heartbeat cycles_used
dfx canister call timer cycles_used
```

Example output:

```sh
% dfx canister call heartbeat cycles_used
(10_183_957 : nat64)
% dfx canister call timer cycles_used
(2_112_067 : nat64)
```

For periodic tasks with 10 sec interval the `heartbeat` canister uses *more* cycles than the `timer` canister.

Not only timers use less cycles, but they also more composable. As there is no global state or methods to export, different libraries with timers could be easily used in the same project.

Also, timers provide isolation between the scheduling logic and the periodic task. If the periodic task fails, all the changes made by this task will be reverted, but the timers library state will be updated, i.e. the failed task will be removed from the list of timers to execute.

For such an isolation of execution and scheduling contexts, internally timers library uses self canister calls:

   ```rust
   # This is a pseudo-code of a self call:
   ic_cdk::call(ic_cdk::id(), "periodic_task", ());
   ```

Despite the [costs](https://internetcomputer.org/docs/current/developer-docs/production/computation-and-storage-costs) associated with such self canister calls, timers library still use less cycles than the heartbeats.

### Example 2: Cycles usage for tasks with 1s interval


- #### Step 1: Open a new terminal window in the example root directory:

```sh
cd examples/rust/periodic_tasks
```

- #### Step 2: Start a clean local Internet Computer replica and a web server:

```sh
dfx stop
dfx start --clean
```

This terminal will stay blocked, printing log messages, until the `Ctrl+C` is pressed or `dfx stop` command is run.

- #### Step 3: Open another terminal window in the same directory:

```sh
cd examples/rust/periodic_tasks
```

Example output:

```sh
% dfx stop && dfx start --clean
[...]
Dashboard: http://localhost:63387/_/dashboard
```

- #### Step 4:. Compile and deploy `heartbeat` and `timer` canisters, setting the interval for periodic tasks to 1s:

```sh
dfx deploy --argument 1
```

The counter will start increasing every second in both canisters.

Example output:

```sh
% dfx deploy --argument 1
[...]
Deployed canisters.
URLs:
   Backend canister via Candid interface:
      heartbeat: http://127.0.0.1/...
      timer: http://127.0.0.1/...
```

- #### Step 5: After a few seconds, observe similar non-zero counters in both canisters:

```sh
dfx canister call heartbeat counter
dfx canister call timer counter
```

Note, as the canisters deployed one by one, there might be a minor discrepancy in the counters.

Example output:

```sh
% dfx canister call heartbeat counter               
(8 : nat32)
% dfx canister call timer counter    
(9 : nat32)
```

- #### Step 6: Compare the amount of cycles used to schedule the periodic task with 1s interval:

```sh
dfx canister call heartbeat cycles_used
dfx canister call timer cycles_used
```

Example output:

```sh
% dfx canister call heartbeat cycles_used
(2_456_567 : nat64)
% dfx canister call timer cycles_used
(4_545_326 : nat64)
```

For periodic tasks with 1 sec interval the `heartbeat` canister uses *less* cycles than the `timer` canister.

Despite the `heartbeat` uses less cycles in this case, this solution is hard to compose within a big project. If there are two libraries using heartbeats internally, they won't even compile together, as they both would be trying to export the global `canister_heartbeat`  method required for the heartbeats.

Also, there is no isolation between the scheduling logic and the periodic task. If the periodic task fails, all the changes made by the task and by the `canister_heartbeat` method will be reverted. So the failed task will be executing over and over again every heartbeat.

For such an isolation of execution and scheduling contexts, timers library uses internally self canister calls as described in the `Demo 1`. Due to the [costs](https://internetcomputer.org/docs/current/developer-docs/production/computation-and-storage-costs) associated with such self canister calls, `timer` canister uses more cycles for very frequent periodic tasks.

## Further learning
1. Have a look at the locally running dashboard. The URL is at the end of the `dfx start` command: `Dashboard: http://localhost/...`
2. Check out `heartbeat` and `timer` canisters Candid user interface. The URLs are et the end of the `dfx deploy` command: `heartbeat: http://127.0.0.1/...`
3. Find which interval makes even the costs of running periodic tasks in the `timer` and `heartbeat` canisters: `dfx deploy --argument 5`

### Canister Interface

The `heartbeat` and `timer` canisters provide the following interface:

* `counter` &mdash; returns the value of the `COUNTER`, i.e. how many times the periodic task was executed (query, both canisters)
* `start_with_interval_secs` &mdash; starts a new timer with the specified interval in seconds (timer canister)
* `set_interval_secs` &mdash; sets a new interval to call the periodic task in seconds (heartbeat canister)
* `stop` &mdash; stops executing periodic task (both canisters)
* `cycles_used` &mdash; returns the number of cycles observed in the periodic task (both canisters)

Example usage:

```sh
dfx canister call timer start_with_interval_secs 5
```

## Conclusion

For the code composability, execution context isolation and cost efficiency, canister developers should prefer to use timers over the heartbeats.

As shown in `Example 2`, there might be still very specific use cases for the heartbeats. Those should be considered case by case, with composability and isolation issues in mind.


## Security considerations and security best practices

If you base your application on this example, we recommend you familiarize yourself with and adhere to the [security best practices](https://internetcomputer.org/docs/current/references/security/) for developing on the Internet Computer. This example may not implement all the best practices.

-->
