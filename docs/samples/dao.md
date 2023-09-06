# 基本的な分散型自律組織(DAO)

このサンプル・プロジェクトは、基本的な[分散型](https://en.wikipedia.org/wiki/Decentralized_autonomous_organization)自律組織(DAO)のデモです。 [Internet Computer](https://github.com/dfinity/ic).基本的なDAOのサンプルコードは [Motoko](https://github.com/dfinity/examples/tree/master/motoko/basic_dao)および[Rustで](https://github.com/dfinity/examples/tree/master/rust/basic_dao)利用可能です。[YouTubeで](https://youtu.be/3IcYlieA-EE)簡単な紹介を見ることができます。

## 概要

`basic_dao` は、プリンシパル ID からトークンの量へのマッピングであるアカウントのセットで初期化できます。アカウントの所有者は、`account_balance` をコールすることでアカウントの残高をクエリーコールでき、`transfer` をコールすることで他のアカウントにトークンを転送できます。誰でも`list_accounts` 。

アカウント所有者は、`submit_proposal` を呼び出してプロポーザルを提出できます。プロポーザルは、canister 、メソッド、およびこのメソッドの引数を指定します。アカウント所有者は、`vote` を呼び出すことで、提案に対して投票 (`Yes` または`No` のいずれか) を行うことができます。投票数は、アカウント所有者が持っているトークンの数と同じです。十分な数の`Yes` 票が投じられた場合、`basic_dao` は指定されたメソッドを指定された引数で呼び出し、指定されたcanister に対して提案を実行します。十分な数の`No` 票が投じられた場合、提案は実行されず、代わりに`Rejected` としてマークされます。

提案の可決に必要な`Yes` 票の数など、特定のシステムパラメータは`get_system_params` を呼び出すことでクエリーできます。これらのシステムパラメータはプロポーザルプロセスで変更できます。つまり、プロポーザルはアップデートされた値で`update_system_params` をコールするようにできます。以下のデモはまさにその例です。

詳細は[canister サービス定義を](https://github.com/dfinity/examples/blob/master/rust/basic_dao/src/basic_dao/src/basic_dao.did)ご覧ください。

## Motoko バリアント

この例では、以下のサンプルレポを使用しています：

- [Basic\_daoMotoko サンプル](https://github.com/dfinity/examples/tree/master/motoko/basic_dao)

### 前提条件

この例では以下のインストールが必要です：

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールしてください。
- \[x\] テストスクリプトを実行するには、[ic-repl](https://github.com/chenyan2002/ic-repl/releases) をダウンロードする必要があります。
- \[x\] GitHub から以下のプロジェクトファイルをダウンロードしてください: https://github.com/dfinity/examples

ターミナル・ウィンドウを開きます。

- #### ステップ 1: プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカルインスタンスをコマンドで起動します：

`cd basic_dao`
`dfx start --background`

- #### ステップ2: コマンドでテストIDを作成します：

<!-- end list -->

    dfx identity new Alice --disable-encryption; dfx identity use Alice; export ALICE=$(dfx identity get-principal);
    dfx identity new Bob --disable-encryption; dfx identity use Bob; export BOB=$(dfx identity get-principal);

- #### ステップ 3: 初期テストアカウントで`basic_dao` をデプロイします。

<!-- end list -->

    dfx deploy --argument "(record {
     accounts = vec { record { owner = principal \"$ALICE\"; tokens = record { amount_e8s = 100_000_000 }; };
                      record { owner = principal \"$BOB\"; tokens = record { amount_e8s = 100_000_000 };}; };
     proposals = vec {};
     system_params = record {
         transfer_fee = record { amount_e8s = 10_000 };
         proposal_vote_threshold = record { amount_e8s = 10_000_000 };
         proposal_submission_deposit = record { amount_e8s = 10_000 };
     };
    })"

- #### ステップ 4: ic-repl テストスクリプトを実行します：

<!-- end list -->

    ic-repl tests/account.test.sh
    ic-repl tests/proposal.test.sh

## Rust のバリエーション

この例では、以下のサンプルレポを使用します：

- [Basic\_dao Rust サンプル](https://github.com/dfinity/examples/tree/master/rust/basic_dao)

### 前提条件

この例では、以下のインストールが必要です：

- \[x\] Rust ツールチェーン (cargo など)。
- \[x\][didc](https://github.com/dfinity/candid/tree/master/tools/didc)。
- \[x\][IC SDKを](../developer-docs/setup/install/index.mdx)インストールします。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください: https://github.com/dfinity/examples/tree/master/rust/basic\_dao

ターミナル・ウィンドウを開いて開始します。

- #### ステップ 1:`basic_dao` canister をビルドします：

<!-- end list -->

    make clean; make

- #### ステップ 2: プロジェクトのファイルを含むフォルダに移動し、Internet Computer のローカル・インスタンスをコマンドで起動します：

<!-- end list -->

    cd basic_dao
    dfx start --background

- #### ステップ 3: コマンドを使用してテスト用 ID を作成します：

<!-- end list -->

    dfx identity new --disable-encryption Alice; dfx identity use Alice; export ALICE=$(dfx identity get-principal); 
    dfx identity new --disable-encryption Bob; dfx identity use Bob; export BOB=$(dfx identity get-principal);

- #### ステップ4: basic\_daoを初期テストアカウントでデプロイします：

<!-- end list -->

    dfx deploy --argument "(record {
     accounts = vec { record { owner = principal \"$ALICE\"; tokens = record { amount_e8s = 100_000_000:nat64 }; }; 
                      record { owner = principal \"$BOB\"; tokens = record { amount_e8s = 100_000_000:nat64 };}; };
     proposals = vec {};
     system_params = record {
         transfer_fee = record { amount_e8s = 10_000:nat64 };
         proposal_vote_threshold = record { amount_e8s = 10_000_000:nat64 };
         proposal_submission_deposit = record { amount_e8s = 10_000:nat64 };
     };
    })"

- #### ステップ5: アカウントを一覧表示し、2つのテストアカウントがあることを確認します：

<!-- end list -->

    dfx canister call basic_dao list_accounts '()'

出力します：

    (
      vec {
        record {
          owner = principal "5l3ql-7jlet-6yy5p-fk2ud-e7qul-6vqqx-cnqvu-zq75f-r76jx-tf6gb-2ae";
          tokens = record { amount_e8s = 100_000_000 : nat };
        };
        record {
          owner = principal "gbr7o-qdqaz-fm5ds-xrg4l-k7bwl-6m3vk-tvjas-ner6w-wt2hq-hiav7-3ae";
          tokens = record { amount_e8s = 100_000_000 : nat };
        };
      },
    )

- #### ステップ6: Bobとして`account_balance` ：

<!-- end list -->

    dfx canister call basic_dao account_balance '()'

出力が表示されるはずです：

    (record { amount_e8s = 100_000_000 : nat64 })

- #### ステップ 7: Aliceにトークンを転送します：

<!-- end list -->

    dfx canister call basic_dao transfer "(record { to = principal \"$ALICE\"; amount = record { amount_e8s = 90_000_000:nat;};})";

出力：

    (variant { Ok })

- #### ステップ8: アカウントを一覧表示して、転送が行われたことを確認します：

<!-- end list -->

    dfx canister call basic_dao list_accounts '()'

出力：

```
 (
   vec {
     record {
       owner = principal "$ALICE";
       tokens = record { amount_e8s = 190_000_000 : nat64 };
     };
     record {
       owner = principal "$BOB";
       tokens = record { amount_e8s = 9_990_000 : nat64 };
     };
   },
 )
```

:::info
ボブの口座から送金手数料が差し引かれたことに注意してください。
::：

- #### ステップ9: 振込手数料を変更する提案をしてみましょう。`get_system_params` を呼び出して、現在の振込手数料を知ることができます：

<!-- end list -->

    dfx canister call basic_dao get_system_params '()';

出力：

    (
      record {
        transfer_fee = record { amount_e8s = 10_000 : nat64 };
        proposal_vote_threshold = record { amount_e8s = 10_000_000 : nat64 };
        proposal_submission_deposit = record { amount_e8s = 10_000 : nat64 };
      },
    )

`transfer_fee` を変更するには、`ProposalPayload` を引数として受け取る`submit_proposal` を呼び出して、提案書を提出する必要があります：

    type ProposalPayload = record {
      canister_id: principal;
      method: text;
      message: blob;
    };

`transfer_fee` を変更するには、`basic_dao` の`update_system_params` メソッドを呼び出します。このメソッドは、`UpdateSystemParamsPayload` を引数として取ります。 は、`ProposalPayload` で使用するために blob にエンコードする必要があります。`UpdateSystemParamsPayload` をエンコードするには didc を使います：

    didc encode '(record { transfer_fee = opt record { amount_e8s = 20_000:nat64; }; })' -f blob

出力：

    blob "DIDL\03l\01\f2\c7\94\ae\03\01n\02l\01\b9\ef\93\80\08x\01\00\01 N\00\00\00\00\00\00"

- #### ステップ10：これでプロポーザルを提出できます：

<!-- end list -->

    dfx canister call basic_dao submit_proposal '(record { canister_id = principal "rrkah-fqaaa-aaaaa-aaaaq-cai";
    method = "update_system_params":text;
    message = blob "DIDL\03l\01\f2\c7\94\ae\03\01n\02l\01\b9\ef\93\80\08x\01\00\01 N\00\00\00\00\00\00"; })'

出力された課題IDに注意してください：

    (variant { Ok = 0 : nat64 })

- #### ステップ 11: プロポーザルが作成されたことを確認します：

<!-- end list -->

    dfx canister call basic_dao get_proposal '(0:nat64)'

`state = variant { Open };` が出力されます。

- #### ステップ 12: 提案に投票します：

<!-- end list -->

    dfx canister call basic_dao vote '(record { proposal_id = 0:nat64; vote = variant { Yes };})'

次の出力が表示されるはずです：

    (variant { Ok = variant { Open } })

私たちはBobとして投票し、Bobには提案を通すだけの投票権がないため、提案はOpenのままです。提案を通すには、アリスで投票します：

    dfx identity use Alice; dfx canister call basic_dao vote '(record { proposal_id = 0:nat64; vote = variant { Yes };})';

次の出力が表示されるはずです：

    (variant { Ok = variant { Accepted } })

もう一度提案に問い合わせます：

    dfx canister call basic_dao get_proposal '(0:nat64)'

そしてステート`Succeeded` ：

    state = variant { Succeeded };

システムパラメータに再度問い合わせ、transfer\_feeが更新されていることを確認します：

    dfx canister call basic_dao get_system_params '()'

出力されます：

    (
      record {
        transfer_fee = record { amount_e8s = 20_000 : nat64 };
        proposal_vote_threshold = record { amount_e8s = 10_000_000 : nat64 };
        proposal_submission_deposit = record { amount_e8s = 10_000 : nat64 };
      },
    )

### リソース

- [ic-cdk](https://docs.rs/ic-cdk/latest/ic_cdk/)
- [ic-cdk-macros](https://docs.rs/ic-cdk-macros)
- [JavaScript APIリファレンス](https://erxue-5aaaa-aaaab-qaagq-cai.ic0.app/)

<!---
# Basic decentralized autonomous organization (DAO)

This sample project demonstrates a basic [decentralized autonomous organization](https://en.wikipedia.org/wiki/Decentralized_autonomous_organization) (DAO) that can be deployed to the [Internet Computer](https://github.com/dfinity/ic). The basic DAO sample code is available in [Motoko](https://github.com/dfinity/examples/tree/master/motoko/basic_dao) and [Rust](https://github.com/dfinity/examples/tree/master/rust/basic_dao). You can see a quick introduction on [YouTube](https://youtu.be/3IcYlieA-EE).

## Overview

A `basic_dao` can be initialized with a set of accounts: mappings from principal IDs to an amount of tokens. Account owners can query their account balance by calling `account_balance` and transfer tokens to other accounts by calling `transfer`. Anyone can call `list_accounts` to view all accounts.

Account owners can submit proposals by calling `submit_proposal`. A proposal specifies a canister, method and arguments for this method. Account owners can cast votes (either `Yes` or `No`) on a proposal by calling `vote`. The amount of votes cast is equal to amount of tokens the account owner has. If enough `Yes` votes are cast, `basic_dao` will execute the proposal by calling the proposal’s given method with the given args against the given canister. If enough `No` votes are cast, the proposal is not executed, and is instead marked as `Rejected`.

Certain system parameters, like the number of `Yes` votes needed to pass a proposal, can be queried by calling `get_system_params`. These system params can be modified via the proposal process, i.e. a proposal can be made to call `update_system_params` with updated values. The below demo does exactly that.

View the [canister service definition](https://github.com/dfinity/examples/blob/master/rust/basic_dao/src/basic_dao/src/basic_dao.did) for more details.
 

## Motoko variant 
This example uses the following sample repo:

- [Basic_dao Motoko example](https://github.com/dfinity/examples/tree/master/motoko/basic_dao)

### Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] To run the test scripts, you need to download [ic-repl](https://github.com/chenyan2002/ic-repl/releases).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples

Begin by opening a terminal window.

- #### Step 1: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

`cd basic_dao`
`dfx start --background`

- #### Step 2: Create test identities with the commands:

```
dfx identity new Alice --disable-encryption; dfx identity use Alice; export ALICE=$(dfx identity get-principal);
dfx identity new Bob --disable-encryption; dfx identity use Bob; export BOB=$(dfx identity get-principal);
```

- #### Step 3: Deploy `basic_dao` with initial test accounts.

```
dfx deploy --argument "(record {
 accounts = vec { record { owner = principal \"$ALICE\"; tokens = record { amount_e8s = 100_000_000 }; };
                  record { owner = principal \"$BOB\"; tokens = record { amount_e8s = 100_000_000 };}; };
 proposals = vec {};
 system_params = record {
     transfer_fee = record { amount_e8s = 10_000 };
     proposal_vote_threshold = record { amount_e8s = 10_000_000 };
     proposal_submission_deposit = record { amount_e8s = 10_000 };
 };
})"
```

- #### Step 4: Run the ic-repl test scripts:

```
ic-repl tests/account.test.sh
ic-repl tests/proposal.test.sh
```

## Rust variation

This example uses the following sample repo:

- [Basic_dao Rust example](https://github.com/dfinity/examples/tree/master/rust/basic_dao)

### Prerequisites
This example requires an installation of:

- [x] The Rust toolchain (e.g. cargo).
- [x] [didc.](https://github.com/dfinity/candid/tree/master/tools/didc)
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/tree/master/rust/basic_dao

Begin by opening a terminal window.

* #### Step 1: Build the `basic_dao` canister:

```
make clean; make
```

- #### Step 2: Navigate into the folder containing the project's files and start a local instance of the Internet Computer with the command:

```
cd basic_dao
dfx start --background
```

- #### Step 3: Create test identities with the commands:

```
dfx identity new --disable-encryption Alice; dfx identity use Alice; export ALICE=$(dfx identity get-principal); 
dfx identity new --disable-encryption Bob; dfx identity use Bob; export BOB=$(dfx identity get-principal);
```

- #### Step 4: Deploy basic_dao with the initial test accounts:

```
dfx deploy --argument "(record {
 accounts = vec { record { owner = principal \"$ALICE\"; tokens = record { amount_e8s = 100_000_000:nat64 }; }; 
                  record { owner = principal \"$BOB\"; tokens = record { amount_e8s = 100_000_000:nat64 };}; };
 proposals = vec {};
 system_params = record {
     transfer_fee = record { amount_e8s = 10_000:nat64 };
     proposal_vote_threshold = record { amount_e8s = 10_000_000:nat64 };
     proposal_submission_deposit = record { amount_e8s = 10_000:nat64 };
 };
})"
```

- #### Step 5: List accounts and confirm you see the two test accounts:

```
dfx canister call basic_dao list_accounts '()'
```

Output:

```
(
  vec {
    record {
      owner = principal "5l3ql-7jlet-6yy5p-fk2ud-e7qul-6vqqx-cnqvu-zq75f-r76jx-tf6gb-2ae";
      tokens = record { amount_e8s = 100_000_000 : nat };
    };
    record {
      owner = principal "gbr7o-qdqaz-fm5ds-xrg4l-k7bwl-6m3vk-tvjas-ner6w-wt2hq-hiav7-3ae";
      tokens = record { amount_e8s = 100_000_000 : nat };
    };
  },
)
```

- #### Step 6: Call `account_balance` as Bob:

```
dfx canister call basic_dao account_balance '()'
```

You should see the output:

```
(record { amount_e8s = 100_000_000 : nat64 })
```

- #### Step 7: Transfer tokens to Alice:

```
dfx canister call basic_dao transfer "(record { to = principal \"$ALICE\"; amount = record { amount_e8s = 90_000_000:nat;};})";
```

Output:

```
(variant { Ok })
```

- #### Step 8: List accounts and see that the transfer was made:

```
dfx canister call basic_dao list_accounts '()'
```

Output:

```
 (
   vec {
     record {
       owner = principal "$ALICE";
       tokens = record { amount_e8s = 190_000_000 : nat64 };
     };
     record {
       owner = principal "$BOB";
       tokens = record { amount_e8s = 9_990_000 : nat64 };
     };
   },
 )
 ```

:::info
Note that the transfer fee was deducted from Bob's account.
:::

- #### Step 9: Let's make a proposal to change the transfer fee. We can call `get_system_params` to learn the current transfer fee:

```
dfx canister call basic_dao get_system_params '()';
```

Output:

```
(
  record {
    transfer_fee = record { amount_e8s = 10_000 : nat64 };
    proposal_vote_threshold = record { amount_e8s = 10_000_000 : nat64 };
    proposal_submission_deposit = record { amount_e8s = 10_000 : nat64 };
  },
)
```

To change `transfer_fee`, we need to submit a proposal by calling `submit_proposal`, which takes a `ProposalPayload` as an arg:

```
type ProposalPayload = record {
  canister_id: principal;
  method: text;
  message: blob;
};
```

We can change `transfer_fee` by calling `basic_dao`'s `update_system_params` method. This method takes a `UpdateSystemParamsPayload` as an arg, which we need to encode into a blob to use in `ProposalPayload`. Use didc to encode a `UpdateSystemParamsPayload`:

```
didc encode '(record { transfer_fee = opt record { amount_e8s = 20_000:nat64; }; })' -f blob
```

Output:

```
blob "DIDL\03l\01\f2\c7\94\ae\03\01n\02l\01\b9\ef\93\80\08x\01\00\01 N\00\00\00\00\00\00"
```

- #### Step 10: We can then submit the proposal:

```
dfx canister call basic_dao submit_proposal '(record { canister_id = principal "rrkah-fqaaa-aaaaa-aaaaq-cai";
method = "update_system_params":text;
message = blob "DIDL\03l\01\f2\c7\94\ae\03\01n\02l\01\b9\ef\93\80\08x\01\00\01 N\00\00\00\00\00\00"; })'
```

Note the output proposal ID:

```
(variant { Ok = 0 : nat64 })
```

- #### Step 11: Confirm the proposal was created:

```
dfx canister call basic_dao get_proposal '(0:nat64)'
```

You should see `state = variant { Open };` in the output.

- #### Step 12: Vote on the proposal:

```
dfx canister call basic_dao vote '(record { proposal_id = 0:nat64; vote = variant { Yes };})'
```

You should see the following output:

```
(variant { Ok = variant { Open } })
```

Because we voted as Bob, and Bob does not have enough voting power to pass proposals, the proposal remains Open. To get the proposal accepted, we can vote with Alice:

```
dfx identity use Alice; dfx canister call basic_dao vote '(record { proposal_id = 0:nat64; vote = variant { Yes };})';
```

You should see the following output:

```
(variant { Ok = variant { Accepted } })
```

Query the proposal again:

```
dfx canister call basic_dao get_proposal '(0:nat64)'
```

And see that the state is `Succeeded`:

```
state = variant { Succeeded };
```

Query the system params again and see that transfer_fee has been updated:

```
dfx canister call basic_dao get_system_params '()'
```

Output:

```
(
  record {
    transfer_fee = record { amount_e8s = 20_000 : nat64 };
    proposal_vote_threshold = record { amount_e8s = 10_000_000 : nat64 };
    proposal_submission_deposit = record { amount_e8s = 10_000 : nat64 };
  },
)
```

### Resources
- [ic-cdk](https://docs.rs/ic-cdk/latest/ic_cdk/)
- [ic-cdk-macros](https://docs.rs/ic-cdk-macros)
- [JavaScript API reference](https://erxue-5aaaa-aaaab-qaagq-cai.ic0.app/)
-->
