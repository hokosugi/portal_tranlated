---

sidebar_position: 3
---
# cycles ウォレットの使用

## 概要

[トークンとcycles](/concepts/tokens-cycles.md) で説明したように、ICP トークンは、 の運用に必要な電力に変換できます。 **cycles**canister Cycles は、 が消費する通信、計算、ストレージの運用コストを反映しています。dapps 

ICP トークンと異なり、cycles はcanisters にのみ関連付けられ、ユーザーや開発者のプリンシパルには関連付けられません。操作を実行し、使用するリソースの代金を支払うためにcycles を必要とするのはcanisters だけであるため、ユーザーと開発者は**cycles ウォレットと**呼ばれる特別なタイプのcanister を通じてcycles の配布と所有権を管理します。cycles ウォレットは、新しいcanisters を作成するなどの操作を実行するために必要なcycles を保持します。これらの操作は、ユーザプリンシパルの代わりにcycles ウォレットのcanister プリンシパルを使って実行されます。

ローカルのcanister 実行環境を使用する目的で、SDK はすべてのプロジェクトで自動的にデフォルトのcycles ウォレットを作成し、cycles ウォレットを使用して実行される操作のほとんどは裏で行われます。例えば、cycles ウォレットは、canister プリンシパルを登録し、canisters をローカルのcanister 実行環境にデプロイするために、あなたに代わって動作します。

しかし本番環境では、cycles を新しいcanisters に明示的に登録および転送し、カストディアンとして機能できるプリンシパルを指定し、所有権を持つプリンシパルを管理する必要があります。これらのタスクの一部は、ウェブブラウザで動作するデフォルトのcycles ウォレットdapp を使用して実行できます。特定のアクションによっては、ターミナルで`dfx wallet` コマンドを実行したり、デフォルトのcycles ウォレットcanister のメソッドを直接呼び出したりして、これらのcycle およびcanister 管理タスクを実行することもできます。`dfx wallet` の使い方については[こちらで](https://internetcomputer.org/docs/current/references/cli-reference/dfx-wallet)詳しく説明しています。

ただし、cycles ウォレットcanister への呼び出しは、現在選択されているユーザー ID に関連付けられたcycles ウォレットプリンシパルを使用して実行されることに留意する必要があります。現在選択されている ID と、その ID に関連付けられたプリンシパルがウォレットのコントローラとして追加されているかカストディアンとして追加されているかによって、異なる結果が表示されたり、特定のメソッドへのアクセスが拒否されたりする可能性があります。

現在使用している ID を確認するには、以下のコマンドを実行してください：

    dfx identity whoami

## コントローラとカストディアンの役割

ユーザープリンシパルまたはcanister プリンシパルは、**コントローラ**または**カストディアンの**ロールに割り当てることができます。

:::info
このドキュメントで説明するコントローラの役割は、通常Internet Computer について説明するときに意味するコントローラの役割ではありません。 一般的に、コントローラはcanister を制御するプリンシパルを指します。 ここでは、コントローラは、たまたま同じ名前を持つ**ウォレット内部の**役割です。より詳細な区別については、[このフォーラムの投稿を](https://forum.dfinity.org/t/why-is-my-cycles-wallet-canister-slowly-losing-cycles/13190/11)参照してください。
::：

### コントローラ

**コントローラは**最も特権的なロールであり、コントローラロールに割り当てられたプリンシパルは、以下を含む特権的なタスクを実行できます：

- 他のプリンシパルをコントローラとして追加および削除します。

- 他のプリンシパルをカストディアンとして承認および承認解除。

- cycles ウォレットのアドレス帳へのエントリの追加。

- cycles ウォレットの残高およびその他すべてのウォレット関連情報へのアクセス。

- 他のcanisters にcycles を送信します。

- 他のcanisters からのcycles の受領。

- cycles ウォレットの名前を変更します。

- canisters と追加のcycles ウォレットを作成します。

:::caution
 canister は最大10個のコントローラを持つことができます。詳しくは[こちら](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-create_canister)
::：

### カストディアン

**カストディアンロールに**割り当てられたプリンシパルは、以下を含むcycles ウォレット管理タスクのサブセットのみを実行できます：

- cycles ウォレットの残高およびその他のすべてのウォレット関連情報へのアクセス。

- cycles を他のcanisters に送信します。

- 他のcanisters からのcycles の受領。

- canisters の作成。

プリンシパルにカストディアンとしての権限を付与しても、自動的にcycles ウォレットへのアクセス権が付与されるわけではありません。カストディアン役割に割り当てられた ID にも、cycles ウォレットのプリンシパルを割り当てる必要があります。たとえば、`alice_custodian` という ID をローカルプロジェクトのcycles ウォレット (`rwlgt-iiaaa-aaaaa-aaaaa-cai`) のカストディアンとして承認する場合、そのユーザも`dfx identity set-wallet rwlgt-iiaaa-aaaaa-aaaaa-cai` コマンドでそのウォレットを使用するように割り当てられる必要があります。

## cycles ウォレットの作成

ローカルで開発している場合、`dfx canister create` を使って新しいcanister プリンシパルを登録するとき、あるいは`dfx deploy` を使ってcanister を登録、ビルド、デプロイするときにcycles ウォレットが作成されます。

Internet Computer メインネットにデプロイする場合は、通常、ICP トークンをcycles に変換し、cycles を新しいcanister プリンシパルに転送し、canister をデフォルトのcycles ウォレットWebAssembly モジュール（WASM）で更新することでcycles ウォレットを作成します。[Convert ICP tocycles](/developer-docs/setup/deploy-mainnet.md#creating-a-cycles-wallet)にその方法が示されています。

ICPをcycles に変換し、新しいcycles ウォレットを作成するのに役立つdapps 、例えば[NNSdapp](../../../tokenomics/token-holders/nns-app-quickstart#_deploy_a_canister_with_cycles) 。

## cycle 残高の確認

ローカルのcanister 実行環境またはInternet Computer 上のcycles ウォレットで、：

    dfx wallet balance

または

    wallet_balance

コマンドを使って現在のcycle 残高を確認できます。

### ローカルで開発する場合のcycles 残高の確認

ローカルで開発を行っている場合、`dfx wallet balance` コマンドを使用して、プロジェクトごとに現在のcycles 残高を確認できます。

ローカルプロジェクトのcycles 残高を確認するには：

- #### ステップ 1: ターミナルを開き、プロジェクトのルート・ディレクトリに移動します。

- #### ステップ 2: 以下のコマンドを実行して、ローカルのcanister 実行環境を起動します：
  
  ```
    dfx start --background
  ```

- #### ステップ 3: 次のコマンドを実行して、現在選択されている ID に関連付けられたcycles ウォレットからcycles の残高を表示します：
  
  ```
    dfx wallet balance
  ```
  
  コマンドは次のような出力を表示します：
  
  ```
    78.000 TC (trillion cycles).
  ```

### メインネットにデプロイする際のcycles 残高の確認

Internet Computer にcycles ウォレットをデプロイしている場合は、`dfx wallet balance` コマンドを使用してネットワーク上の現在のcycles 残高を確認できます。

Internet Computer でcycles の残高を確認するには：

- #### ステップ 1: ターミナルを開き、`dfx.json` 設定ファイルを含むディレクトリに移動します。

- #### ステップ2：次のコマンドを実行して、Internet Computer への接続を確認します：
  
  ```
    dfx ping ic
  ```

- #### ステップ 3: 次のコマンドを実行して、現在選択されている ID に関連付けられたcycles ウォレットからcycle の残高を表示します：
  
  ```
    dfx wallet --network ic balance
  ```
  
  コマンドは次のような出力を表示します：
  
  ```
    67.992 TC (trillion cycles).
  ```

#### cycles `wallet_balance` メソッドを呼び出します。

cycles ウォレットの`wallet_balance` メソッドcanister を直接呼び出して、cycles の残高を確認することもできます。たとえば、プリンシパルが`h5aet-waaaa-aaaab-qaamq-cai` cycles ウォレットのコントローラである場合、次のコマンドを実行することで、現在のcycle 残高を確認できます：

    dfx canister --network ic call h5aet-waaaa-aaaab-qaamq-cai wallet_balance

このコマンドは、Candid フォーマットを使用して、次のように金額フィールド（ハッシュ 3\_573\_748\_184 で表される）と残高 6,895,656,625,450cycles を持つレコードとして残高を返します：

    (record { 3_573_748_184 = 6_895_656_625_450 })

コマンドを使ってcycles ウォレットcanister を作成します。`dfx wallet` コマンドを使ってcycles ウォレットの設定を変更したり、cycles を他のcycles ウォレットに送信したり、コントローラとカストディアンを追加したり削除したりできます。

## ウォレットの一覧表示

ウォレットの一覧を表示するには

    dfx wallet addresses

コマンドを使うと、ウォレットのプリンシパルと役割 (コンタクト、カストディアン、コントローラ) が表示され、アドレスに関連付けられた名前と種類 (不明、ユーザ、canister) が表示されます。

## ウォレットの使用

`dfx identity deploy-wallet` コマンドを使用してcycles ウォレットcanister を作成した後、`dfx wallet` コマンドを使用してcycles ウォレットの設定を変更したり、cycles を他のcycles ウォレットに送信したり、コントローラとカストディアンを追加または削除したりできます。

サポートされている各手法の詳細については、[dfx wallet リファレンスを](../../../references/cli-reference/dfx-wallet)参照してください。

### `dfx` ウォレット関数

`dfx wallet` コマンドとサブコマンドおよびフラグを使用して、自分の ID のcycles ウォレットを管理し、他のアカウントのcycles ウォレットcanisters のウォレットにcycles を送信します。

dfx ウォレットコマンドを実行するための基本構文は次のとおりです：

    dfx wallet [option] <subcommand> [flag]

指定する`dfx wallet` サブコマンドによっては、追加の引数、オプション、フラグが適用されたり、必要になったりします。特定の`dfx wallet` サブコマンドの使用情報を表示するには、サブコマンドと`--help` フラグを指定します。たとえば、`dfx wallet` send の使用情報を表示するには、次のコマンドを実行します：

    dfx wallet send --help

dfx walletコマンドを使用する際の参考情報と例については、適切なコマンドを選択し てください。

- `add-controller`選択した ID のプリンシパルを使用してコントローラを追加します。
- `addresses` cycles ウォレットのアドレス帳を表示します。
- `authorize`選択した ID のcycles ウォレットのプリンシパルでカストディアンを承認します。
- `balance`選択した ID のcycles ウォレットの残高を表示します。
- `controllers`選択した ID のcycles ウォレットのコントローラのリストを表示します。
- `custodians`選択した ID のcycles ウォレットのカストディアンのリストを表示します。
- `deauthorize`カストディアンのプリンシパルを使用してcycles ウォレットのカストディアンを認証解除します。
- `help`指定されたサブコマンドの使用法メッセージとヘルプを表示します。
- `name`dfx wallet set-name コマンドを使用した場合、cycles ウォレットの名前を返します。
- `redeem-faucet-coupon` cycles の蛇口でコードを引き換えます。
- `remove-controller`選択した ID のcycles ウォレットから指定したコントローラーを削除します。
- `send`選択した ID のcycles ウォレットから、宛先ウォレットcanister ID を使用して別のcycles ウォレットに指定した量のcycles を送信します。
- `set-name` cycles ウォレットの名前を指定します。
- `upgrade` cycles ウォレットの Wasm モジュールを DFX にバンドルされている現在の Wasm にアップグレードします。

### コントローラの追加

コントローラの役割を割り当てられた ID は、最も高い権限を持ち、選択した ID のcycles ウォレットで次のアクションを実行できます。コントローラとして追加されたアイデンティティは、カストディアンとしてもリストされます。

コントローラを追加するには、以下のコマンドを使用します：

    dfx wallet add-controller

追加するコントローラが別の環境にある場合は、`--network` オプションを使用して指定します。例

    dfx wallet add-controller b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe

### コントローラの表示

選択した ID のcycles ウォレットのコントローラである ID のプリンシパルを一覧表示するには、次のコマンドを使用します：

    dfx wallet controllers

### コントローラの削除

ID のcycles ウォレットからコントローラを削除するには、次のコマンドを使用します：

    dfx wallet remove-controller

たとえば、alice\_auth をコントローラとして削除するには、次のコマンドで彼女のプリンシパルを指定します：

    dfx wallet remove-controller dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae

### プリンシパルをカストディアンとして認可する場合

cycles ウォレットのカストディアンを承認するには、以下のコマンドを使用します：

    dfx wallet authorize <custodian> [flag]

たとえば、alice\_auth をカストディアンとして追加するには、次のコマンドで彼女のプリンシパルを指定します：

    dfx wallet authorize dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae

### カストディアンの一覧

現在選択されている ID のcycles ウォレットのカストディアンを一覧表示するには、次のコマンドを使用します：

    dfx wallet custodians

### プリンシパルのカストディアンとしての認可解除

cycles ウォレットからカストディアンの権限を解除するには、以下のコマンドを使用します：

    dfx wallet deauthorize <custodian> [flag]

例えば、"alice\_auth" をカストディアンから外すには、彼女のプリンシパルを以下のコマンドで指定します：

    dfx wallet deauthorize dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae

## `dfx wallet` コマンドとして公開されていない追加メソッド

デフォルトのcycles 財布canister には、`dfx wallet` コマンドとして公開されていないメソッドが追加されています。追加メソッドは、新しいcanisters の作成やイベントの管理など、より高度なcycles 管理タスクをサポートします。

### 新しいcycles ウォレットの作成

`wallet_create_wallet` メソッドを使用して、新しいcycles ウォレットcanister をcycle の初期残高で作成します。制御するプリンシパルを指定しない場合、新しいウォレットを作成するために使用したcycles ウォレットが新しいウォレットのコントローラになります。

たとえば、次のようなコマンドを実行して新しいウォレットを作成し、プリンシパルをコントローラとして割り当てることができます：

    dfx canister --network ic call f3yw6-7qaaa-aaaab-qaabq-cai wallet_create_wallet '(record { cycles = 5000000000000 : nat64; controller = principal "vpqee-nujda-46rtu-4noo7-qnxmb-zqs7g-5gvqf-4gy7t-vuprx-u2urx-gqe"})'

このコマンドは、新しいウォレットのプリンシパルを返します：

    (record { 1_313_628_723 = principal "dcxxq-jqaaa-aaaab-qaavq-cai" })

### canister プリンシパルの新規登録

新しいcanister プリンシパルをInternet Computer に登録するには、`wallet_create_canister` メソッドを使用します。 このメソッドは、cycle の初期残高を持つ新しい「空の」canister プレースホルダを作成し、オプションでそのコントローラとして特定のプリンシパルを指定します。canister プリンシパルを登録したら、別の手順としてcanister 用のコードをインストールできます。

例えば、以下のようなコマンドを実行して新しいウォレットを作成し、プリンシパルをコントローラとして割り当てることができます：

    dfx canister --network ic call f3yw6-7qaaa-aaaab-qaabq-cai wallet_create_canister '(record { cycles = 5000000000000 : nat64; controller = principal "vpqee-nujda-46rtu-4noo7-qnxmb-zqs7g-5gvqf-4gy7t-vuprx-u2urx-gqe"})'

このコマンドは、作成した新しいcanister のプリンシパルを返します：

    (record { 1_313_628_723 = principal "dxqg5-iyaaa-aaaab-qaawa-cai" })

### からcycles を受け取ります。canister

cycles を受信するエンドポイントとして`wallet_receive` メソッドを使用します。

### ウォレットからのコールの転送

`wallet_call` メソッドを使用して、cycles ウォレットプリンシパルを発呼側として使用してコールを転送します。

### アドレスの管理

以下のメソッドを使用して、アドレス帳エントリを管理します：

- `add_address`(address: AddressEntry) → ()；

- `remove_address`(address: プリンシパル) → ()；

### イベントの管理

以下のメソッドを使用して、イベントとチャート情報を取得します。

- `get_events`: (opt record { from: opt nat32; to: opt nat32; }) → (vec Event) query；

- `get_chart`: (opt record { count: opt nat32; precision: opt nat64; } ) → (vec record { nat64; nat64; }) query；

例えば、`get_events` メソッドを使って`canister_create` やその他のイベントを返すには、以下のようなコマンドを実行します：

    dfx canister call <cycles-wallet-principal> get_events '(record {from = null; to = null})'

cycles ウォレット (`gastn-uqaaa-aaaae-aaafq-cai`) がInternet Computer メインネットワーク上にデプロイされている場合、以下のようなコマンドを実行してイベントを返すことができます：

    dfx canister --network ic call gastn-uqaaa-aaaae-aaafq-cai get_events '(record {from = null; to = null})'

コマンドからの出力は以下のような Candid Format です：

    (
      vec { record { 23_515 = 0; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe"; 1_224_700_491 = null; 1_269_754_742 = variant { 4_218_395_836 };} }; 2_781_795_542 = 1_621_456_688_636_513_683;}; record { 23_515 = 1; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 2_494_206_670 };} }; 2_781_795_542 = 1_621_461_468_638_569_551;}; record { 23_515 = 2; 1_191_829_844 = variant { 1_205_528_161 = record { 2_190_693_645 = 11_000_000_000_000; 2_631_180_839 = principal "gvvca-vyaaa-aaaae-aaaga-cai";} }; 2_781_795_542 = 1_621_462_573_993_647_258;}; record { 23_515 = 3; 1_191_829_844 = variant { 1_205_528_161 = record { 2_190_693_645 = 11_000_000_000_000; 2_631_180_839 = principal "gsueu-yaaaa-aaaae-aaagq-cai";} }; 2_781_795_542 = 1_621_462_579_193_578_440;}; record { 23_515 = 4; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "install_code"; 2_631_180_839 = principal "aaaaa-aa";} }; 2_781_795_542 = 1_621_462_593_047_590_026;}; record { 23_515 = 5; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "install_code"; 2_631_180_839 = principal "aaaaa-aa";} }; 2_781_795_542 = 1_621_462_605_779_157_885;}; record { 23_515 = 6; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "authorize"; 2_631_180_839 = principal "gsueu-yaaaa-aaaae-aaagq-cai";} }; 2_781_795_542 = 1_621_462_609_036_146_536;}; record { 23_515 = 7; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "greet"; 2_631_180_839 = principal "gvvca-vyaaa-aaaae-aaaga-cai";} }; 2_781_795_542 = 1_621_463_144_066_333_270;}; record { 23_515 = 8; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 2_494_206_670 };} }; 2_781_795_542 = 1_621_463_212_828_477_570;}; record { 23_515 = 9; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "wallet_balance"; 2_631_180_839 = principal "gastn-uqaaa-aaaae-aaafq-cai";} }; 2_781_795_542 = 1_621_878_637_071_884_946;}; record { 23_515 = 10; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 4_218_395_836 };} }; 2_781_795_542 = 1_621_879_473_916_547_313;}; record { 23_515 = 11; 1_191_829_844 = variant { 313_999_214 = record { 1_136_829_802 = principal "gastn-uqaaa-aaaae-aaafq-cai"; 3_573_748_184 = 10_000_000_000;} }; 2_781_795_542 = 1_621_977_470_023_492_664;}; record { 23_515 = 12; 1_191_829_844 = variant { 2_171_739_429 = record { 25_979 = principal "gastn-uqaaa-aaaae-aaafq-cai"; 3_573_748_184 = 10_000_000_000; 4_293_698_680 = 0;} }; 2_781_795_542 = 1_621_977_470_858_839_320;};},
    )

この例では、12のイベントレコードがあります。この例では、12件のイベントレコードがあります。`Role` フィールド(ハッシュ`1_269_754_742`)は、プリンシパルがコントローラ(ハッシュ`4_218_395_836`)かカストディアン(ハッシュ`2_494_206_670`)かを指定します。この例のイベントはまた、10,000,000,000cycles の転送を持つ金額フィールド(ハッシュ`3_573_748_184` で表される)を示しています。

<!---

# Using a cycles wallet
 
## Overview

As discussed in [tokens and cycles](/concepts/tokens-cycles.md), ICP tokens can be converted into **cycles** to power canister operations. Cycles reflect the operational cost of communication, computation, and storage that dapps consume.

Unlike ICP tokens, cycles are only associated with canisters and not with user or developer principals. Because only canisters require cycles to perform operations and pay for the resources they use, users and developers manage the distribution and ownership of cycles through a special type of canister called a **cycles wallet**. The cycles wallet holds the cycles required to perform operations such as creating new canisters. These operations are executed using the canister principal of the cycles wallet instead of your user principal.

For the purposes of using the local canister execution environment, the SDK automatically creates a default cycles wallet for you in every project and most of the operations performed using the cycles wallet happen behind the scenes. For example, the cycles wallet acts on your behalf to register canister principals and deploy canisters in the local canister execution environment.

In a production environment, however, you need to explicitly register and transfer cycles to new canisters, specify the principals that can act as custodians, and manage the principals with ownership rights. You can perform some of these tasks using the default cycles wallet dapp running in a web browser. Depending on the specific action you want to take, you can also perform these cycle and canister management tasks by running `dfx wallet` commands in a terminal or by calling methods in the default cycles wallet canister directly. You can learn more about how to use `dfx wallet` [here](https://internetcomputer.org/docs/current/references/cli-reference/dfx-wallet).

You should keep in mind, however, that calls to the cycles wallet canister are executed using the cycles wallet principal associated with the currently selected user identity. Depending on your currently selected identity and whether the principal associated with that identity has been added as a controller or a custodian for a wallet, you might see different results or be denied access to a specific method.

To check the identity you are currently using, run the following command:

    dfx identity whoami

## Controller and custodian roles

A user principal or canister principal can be assigned to a **controller** or **custodian** role.

:::info
The controller role this document talks about is NOT the same controller role usually meant when talking about the Internet Computer. Typically, the controller refers to a principal that controls a canister. Here, a controller is a **wallet-internal** role that happens to have the same name. For a more detailed differentiation, see [this forum post](https://forum.dfinity.org/t/why-is-my-cycles-wallet-canister-slowly-losing-cycles/13190/11).
:::

### Controllers
A **controller** is the most privileged role and a principal assigned to the controller role can perform privileged tasks including the following:

-   Add and remove other principals as controllers.

-   Authorize and de-authorize other principals as custodians.

-   Add entries to the cycles wallet address book.

-   Access the cycles wallet balance and all other wallet-related information.

-   Send cycles to other canisters.

-   Accept receipt of cycles from other canisters.

-   Rename the cycles wallet.

-   Create canisters and additional cycles wallets.

:::caution
A canister can have a maximum of 10 controllers. Learn more [here](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-create_canister).
:::

### Custodians

A principal assigned to the **custodian** role can only perform a subset of cycles wallet management tasks, including the following:

-   Access the cycles wallet balance and all other wallet-related information.

-   Send cycles to other canisters.

-   Accept receipt of cycles from other canisters.

-   Create canisters.

Authorizing a principal as a custodian does not automatically grant the principal access to a cycles wallet. The identity assigned to the custodian role must also be assigned a cycles wallet principal. For example, if you authorize the identity `alice_custodian` as a custodian of a cycles wallet (`rwlgt-iiaaa-aaaaa-aaaaa-cai`) in a local project, that user would also need to be assigned to use that wallet with the `dfx identity set-wallet rwlgt-iiaaa-aaaaa-aaaaa-cai` command.

## Create a cycles wallet

If you are doing local development, your cycles wallet is created when you register a new canister principal using `dfx canister create` or when you register, build, and deploy a canister with `dfx deploy`.

If you are deploying on the Internet Computer mainnet, you typically create your cycles wallet by converting ICP tokens to cycles, transferring the cycles to a new canister principal, and updating the canister with the default cycles wallet WebAssembly module (WASM). [Convert ICP to cycles](/developer-docs/setup/deploy-mainnet.md#creating-a-cycles-wallet) shows how to do this.

There are dapps that can help you convert ICP to cycles and create a new cycles wallet, e.g., [NNS dapp](../../../tokenomics/token-holders/nns-app-quickstart#_deploy_a_canister_with_cycles).

## Check the cycle balance

In the local canister execution environment or with a cycles wallet on the Internet Computer, you can use the:


```
dfx wallet balance
```

or 

```
wallet_balance
``` 

commands to check the current cycle balance.

### Check your cycles balance when developing locally

If you are doing local development, you can use the `dfx wallet balance` command to check the current cycles balance on a project-by-project basis.

To check the cycles balance in a local project:

- #### Step 1:  Open a terminal and navigate to the root directory of the project.

- #### Step 2:  Start the local canister execution environment by running the following command:

        dfx start --background

- #### Step 3:  Display the cycles balance from the cycles wallet associated with the currently-selected identity by running the following command:

        dfx wallet balance

    The command displays output similar to the following:

        78.000 TC (trillion cycles).

### Check the cycles balance when deploying on the mainnet

If you have deployed a cycles wallet on the Internet Computer, you can use the `dfx wallet balance` command to check the current cycles balance on the network.

To check the cycles balance on the Internet Computer:

- #### Step 1:  Open a terminal and navigate to a directory that contains a `dfx.json` configuration file.

- #### Step 2:  Check your connection to the Internet Computer by running the following command:

        dfx ping ic

- #### Step 3:  Display the cycle balance from the cycles wallet associated with the currently-selected identity by running the following command:

        dfx wallet --network ic balance

    The command displays output similar to the following:

        67.992 TC (trillion cycles).

#### Call the cycles `wallet_balance` method

You can also check the cycles balance by calling the `wallet_balance` method in the cycles wallet canister directly. For example, if your principal is a controller for the `h5aet-waaaa-aaaab-qaamq-cai` cycles wallet, you can check the current cycle balance by running the following command:

    dfx canister --network ic call h5aet-waaaa-aaaab-qaamq-cai wallet_balance

The command returns the balance using Candid format as a record with an amount field (represented by the hash 3\_573\_748\_184) and a balance of 6,895,656,625,450 cycles like this:

    (record { 3_573_748_184 = 6_895_656_625_450 })

command to create a cycles wallet canister tied to an identity. You can use `dfx wallet` commands to modify your cycles wallet settings, send cycles to other cycles wallets, and add or remove controllers and custodians.

## Listing wallets

You can use the:

```
dfx wallet addresses
``` 

command to display the wallet's principal and role (contact, custodian, or controller), and might contain a name, and kind (unknown, user, or canister) associated with the address.

## Using your wallet

After you have used the `dfx identity deploy-wallet` command to create a cycles wallet canister tied to an identity, you can use `dfx wallet` commands to modify your cycles wallet settings, send cycles to other cycles wallets, and add or remove controllers and custodians.

For more details on each supported method, see the [dfx wallet reference](../../../references/cli-reference/dfx-wallet).

### `dfx` wallet functions

Use the `dfx wallet` command with subcommands and flags to manage the cycles wallets of your identities and to send cycles to the wallets of other account cycles wallet canisters.

The basic syntax for running the dfx wallet commands is:

```
dfx wallet [option] <subcommand> [flag]
```

Depending on the `dfx wallet` subcommand you specify, additional arguments, options, and flags might apply or be required. To view usage information for a specific `dfx wallet` subcommand, specify the subcommand and the `--help` flag. For example, to see usage information for `dfx wallet` send, you can run the following command:

```
dfx wallet send --help
```

For reference information and examples that illustrate using dfx wallet commands, select an appropriate command.

- `add-controller`: add a controller using the selected identity's principal.
- `addresses`: displays the address book of the cycles wallet.
- `authorize`: authorize a custodian by principal for the selected identity's cycles wallet
- `balance`: displays the cycles wallet balance of the selected identity.
- `controllers`: displays a list of the selected identity's cycles wallet controllers.
- `custodians`:	displays a list of the selected identity's cycles wallet custodians.
- `deauthorize`: deauthorize a cycles wallet custodian using the custodian's principal.
- `help`: displays a usage message and the help of the given subcommand(s).
- `name`: returns the name of the cycles wallet if you've used the dfx wallet set-name command.
- `redeem-faucet-coupon`: redeem a code at the cycles faucet.
- `remove-controller`: removes a specified controller from the selected identity's cycles wallet.
- `send`: sends a specified amount of cycles from the selected identity's cycles wallet to another cycles wallet using the destination wallet canister ID.
- `set-name`: specify a name for your cycles wallet.
- `upgrade`: upgrade the cycles wallet's Wasm module to the current Wasm bundled with DFX.

### Adding a controller
An identity assigned the role of controller has the most privileges and can perform the following actions on the selected identity's cycles wallet. Identities that are added as controllers are also listed as custodians. 

To add a controller, the following command can be used:

```
dfx wallet add-controller
```

If the controller you want to add is on a different environment, specify it using the `--network` option. For example:

```
dfx wallet add-controller b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe
```

### Viewing controllers
To list the principals of the identities that are controllers of the selected identity's cycles wallet, the following command can be used:

```
dfx wallet controllers
```

### Removing a controller
To remove a controller from an identity's cycles wallet, the following command can be used:

```
dfx wallet remove-controller
```

For example, to remove alice_auth as a controller, specify her principal in the following command:

```
dfx wallet remove-controller dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae
```

### Authorizing a principal to be a custodian

To authorize a custodian for a cycles wallet, the following command can be used:

```
dfx wallet authorize <custodian> [flag]
```

For example, to add alice_auth as a custodian, specify her principal in the following command:

```
dfx wallet authorize dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae
```

### Listing custodians

To list custodians of the currently selected identity's cycles wallet, the following command can be used:

```
dfx wallet custodians
```

### De-authorizing a principal from being a custodian

To de-authorize a custodian from a cycles wallet, the following command can be used:

```
dfx wallet deauthorize <custodian> [flag]
```

For example, to remove "alice_auth" as a custodian, specify her principal in the following command:

```
dfx wallet deauthorize dheus-mqf6t-xafkj-d3tuo-gh4ng-7t2kn-7ikxy-vvwad-dfpgu-em25m-2ae
```

## Additional methods that are not exposed as `dfx wallet` commands

The default cycles wallet canister includes additional methods that are not exposed as `dfx wallet` commands. The additional methods support more advanced cycles management tasks such as creating new canisters and managing events.

### Create a new cycles wallet

Use the `wallet_create_wallet` method to create a new cycles wallet canister with an initial cycle balance and, optionally, with a specific principal as its controller. If you don’t specify a controlling principal, the cycles wallet you use to create the new wallet will be the new wallet’s controller.

For example, you can run a command similar to the following to create a new wallet and assign a principal as a controller:

    dfx canister --network ic call f3yw6-7qaaa-aaaab-qaabq-cai wallet_create_wallet '(record { cycles = 5000000000000 : nat64; controller = principal "vpqee-nujda-46rtu-4noo7-qnxmb-zqs7g-5gvqf-4gy7t-vuprx-u2urx-gqe"})'

The command returns the principal for the new wallet:

    (record { 1_313_628_723 = principal "dcxxq-jqaaa-aaaab-qaavq-cai" })

### Register a new canister principal

Use the `wallet_create_canister` method to register a new canister principal on the Internet Computer. This method creates a new "empty" canister placeholder with an initial cycle balance and, optionally, with a specific principal as its controller. After you have registered the canister principal, you can install code for your canister as a separate step.

For example, you can run a command similar to the following to create a new wallet and assign a principal as a controller:

    dfx canister --network ic call f3yw6-7qaaa-aaaab-qaabq-cai wallet_create_canister '(record { cycles = 5000000000000 : nat64; controller = principal "vpqee-nujda-46rtu-4noo7-qnxmb-zqs7g-5gvqf-4gy7t-vuprx-u2urx-gqe"})'

The command returns the principal for the new canister you created:

    (record { 1_313_628_723 = principal "dxqg5-iyaaa-aaaab-qaawa-cai" })

### Receive cycles from a canister

Use the `wallet_receive` method as an endpoint to receive cycles.

### Forward calls from a wallet

Use the `wallet_call` method to forward calls using the cycles wallet principal as caller.

### Manage addresses

Use the following methods to manage address book entries:

-   `add_address`: (address: AddressEntry) → ();

-   `remove_address`: (address: principal) → ();

### Manage events

Use the following methods to retrieve event and chart information.

-   `get_events`: (opt record { from: opt nat32; to: opt nat32; }) → (vec Event) query;

-   `get_chart`: (opt record { count: opt nat32; precision: opt nat64; } ) → (vec record { nat64; nat64; }) query;

For example, you can use the `get_events` method to return `canister_create` and other events by running a command similar to the following:

    dfx canister call <cycles-wallet-principal> get_events '(record {from = null; to = null})'

If the cycles wallet (`gastn-uqaaa-aaaae-aaafq-cai`) is deployed on the Internet Computer main network, you could run a command that looks like this to return events:

    dfx canister --network ic call gastn-uqaaa-aaaae-aaafq-cai get_events '(record {from = null; to = null})'

The output from the command is in Candid format similar to the following:

    (
      vec { record { 23_515 = 0; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe"; 1_224_700_491 = null; 1_269_754_742 = variant { 4_218_395_836 };} }; 2_781_795_542 = 1_621_456_688_636_513_683;}; record { 23_515 = 1; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 2_494_206_670 };} }; 2_781_795_542 = 1_621_461_468_638_569_551;}; record { 23_515 = 2; 1_191_829_844 = variant { 1_205_528_161 = record { 2_190_693_645 = 11_000_000_000_000; 2_631_180_839 = principal "gvvca-vyaaa-aaaae-aaaga-cai";} }; 2_781_795_542 = 1_621_462_573_993_647_258;}; record { 23_515 = 3; 1_191_829_844 = variant { 1_205_528_161 = record { 2_190_693_645 = 11_000_000_000_000; 2_631_180_839 = principal "gsueu-yaaaa-aaaae-aaagq-cai";} }; 2_781_795_542 = 1_621_462_579_193_578_440;}; record { 23_515 = 4; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "install_code"; 2_631_180_839 = principal "aaaaa-aa";} }; 2_781_795_542 = 1_621_462_593_047_590_026;}; record { 23_515 = 5; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "install_code"; 2_631_180_839 = principal "aaaaa-aa";} }; 2_781_795_542 = 1_621_462_605_779_157_885;}; record { 23_515 = 6; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "authorize"; 2_631_180_839 = principal "gsueu-yaaaa-aaaae-aaagq-cai";} }; 2_781_795_542 = 1_621_462_609_036_146_536;}; record { 23_515 = 7; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "greet"; 2_631_180_839 = principal "gvvca-vyaaa-aaaae-aaaga-cai";} }; 2_781_795_542 = 1_621_463_144_066_333_270;}; record { 23_515 = 8; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "ejta3-neil3-qek6c-i7rdw-sxreh-lypfe-v6hjg-6so7x-5ugze-3iohr-2qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 2_494_206_670 };} }; 2_781_795_542 = 1_621_463_212_828_477_570;}; record { 23_515 = 9; 1_191_829_844 = variant { 1_955_698_212 = record { 2_190_693_645 = 0; 2_374_371_241 = "wallet_balance"; 2_631_180_839 = principal "gastn-uqaaa-aaaae-aaafq-cai";} }; 2_781_795_542 = 1_621_878_637_071_884_946;}; record { 23_515 = 10; 1_191_829_844 = variant { 4_271_600_268 = record { 23_515 = principal "b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe"; 1_224_700_491 = null; 1_269_754_742 = variant { 4_218_395_836 };} }; 2_781_795_542 = 1_621_879_473_916_547_313;}; record { 23_515 = 11; 1_191_829_844 = variant { 313_999_214 = record { 1_136_829_802 = principal "gastn-uqaaa-aaaae-aaafq-cai"; 3_573_748_184 = 10_000_000_000;} }; 2_781_795_542 = 1_621_977_470_023_492_664;}; record { 23_515 = 12; 1_191_829_844 = variant { 2_171_739_429 = record { 25_979 = principal "gastn-uqaaa-aaaae-aaafq-cai"; 3_573_748_184 = 10_000_000_000; 4_293_698_680 = 0;} }; 2_781_795_542 = 1_621_977_470_858_839_320;};},
    )

In this example, there are twelve event records. The `Role` field (represented by the hash `1_269_754_742`) specifies whether a principal is a controller (represented by the hash `4_218_395_836`) or a custodian (represented by the hash `2_494_206_670`). The events in this example also illustrate an amount field (represented by the hash `3_573_748_184`) with a transfer of 10,000,000,000 cycles.
-->
