# ICP移送サンプルコード

## 概要

ICP transferはcanister 、ICPをその口座から他の口座に転送することができます。これは、元帳canister を使用するcanister の一例です。サンプルコードは [Motoko](https://github.com/dfinity/examples/tree/master/motoko/ledger-transfer)と[Rustに](https://github.com/dfinity/examples/tree/master/rust/tokens_transfer)あります。

## アーキテクチャ

サンプルコードは、転送するICPの量、ICPを転送する口座（オプションでサブ口座も）、ICP転送canister 、転送に必要なICPがない場合などに成功かエラーを返します。成功の場合、トランザクションの一意な識別子が返されます。この識別子は元帳の取引メモに保存されます。

このサンプルでは、Motoko バリアントを使用します。

このサンプルでは、[samples の Github リポジトリに](https://github.com/dfinity/examples/tree/master/motoko/ledger-transfer)ある ledger transfer プロジェクトファイルを使用します。

## 前提条件

このサンプルには、以下のインストールが必要です：

- \[x\][IC SDK](../developer-docs/setup/install/index.mdx) をインストールします。
- \[x\][git を](https://git-scm.com/downloads)ダウンロードしてインストールしてください。

### ステップ 1:`dfx` プロジェクトを新規作成し、プロジェクトのディレクトリに移動します。

    dfx new ledger_transfer
    cd ledger_transfer

### ステップ2: 事前にビルドされた ledgercanister モジュールと Candid インターフェース・ファイルをダウンロードします：

    export IC_VERSION=dd3a710b03bd3ae10368a91b255571d012d1ec2f
    curl -o ledger.wasm.gz "https://download.dfinity.systems/ic/$IC_VERSION/canisters/ledger-canister_notify-method.wasm.gz"
    gunzip ledger.wasm.gz
    curl -o ledger.private.did "https://raw.githubusercontent.com/dfinity/ic/$IC_VERSION/rs/rosetta-api/ledger.did"
    dfx canister --network ic call ryjl3-tyaaa-aaaaa-aaaba-cai __get_candid_interface_tmp_hack '()' --query | sed 's/\\n/\n/g'

3つのファイルをダウンロードします：

- [ledger.wasm](./_attachments/ledger.wasm)
- [ledger.public.did](./_attachments/ledger.public.did)
- [ledger.private.did](./_attachments/ledger.private.did)

### ステップ3：`dfx.json` ファイルで、既存の内容を以下のように置き換えます：

    {
      "canisters": {
        "ledger": {
          "type": "custom",
          "wasm": "ledger.wasm",
          "candid": "ledger.public.did",
          "remote": {
        "candid": "ledger.public.did",
        "id": {
          "ic": "ryjl3-tyaaa-aaaaa-aaaba-cai"
        }
      }
        },
        "ledger_transfer_backend": {
          "main": "src/ledger_transfer_backend/main.mo",
          "type": "motoko"
        },
        "ledger_transfer_frontend": {
          "dependencies": [
            "ledger_transfer_backend"
          ],
          "frontend": {
            "entrypoint": "src/ledger_transfer_frontend/src/index.html"
          },
          "source": [
            "src/ledger_transfer_frontend/assets",
            "dist/ledger_transfer_frontend/"
          ],
          "type": "assets"
        }
      },
      "defaults": {
         "replica": {
          "subnet_type":"system"
        },
        "build": {
          "args": "",
          "packtool": ""
        }
      },
      "output_env_file": ".env",
      "version": 1
    }

### ステップ5： ローカルレプリカを開始します：

    dfx start --background

### ステップ6：発行アカウントとして機能する新しいIDを作成します：

    dfx identity new minter
    dfx identity use minter
    export MINT_ACC=$(dfx ledger account-id)

発行アカウントからの送金はMintトランザクションを作成します。発行アカウントからの送金はバーントランザクションを作成します。

### ステップ 7: デフォルトの ID に切り替え、その元帳アカウント識別子を記録します：

    dfx identity use default
    export LEDGER_ACC=$(dfx ledger account-id)

### ステップ 8: 台帳canister をネットワークにデプロイします：

    dfx deploy ledger --argument '(record {minting_account = "'${MINT_ACC}'"; initial_values = vec { record { "'${LEDGER_ACC}'"; record { e8s=100_000_000_000 } }; }; send_whitelist = vec {}})'

本番デプロイと同じ方法で台帳をセットアップする場合は、アーカイブを有効にしてデプロイします。このセットアップでは、台帳canister は動的に新しいcanisters を作成し、古いブロックを保存します。ブロックをフェッチするためにインターフェイスを使用する場合は、このセットアップを使用することをお勧めします。

### ステップ 9: 開発に使用する ID のプリンシパルを取得します。

このプリンシパルがアーカイブcanisters のコントローラになります。

    dfx identity use default
    export ARCHIVE_CONTROLLER=$(dfx identity get-principal)

### ステップ 10：元帳canister をアーカイブオプション付きでデプロイします：

    dfx deploy ledger --argument '(record {minting_account = "'${MINT_ACC}'"; initial_values = vec { record { "'${LEDGER_ACC}'"; record { e8s=100_000_000_000 } }; }; send_whitelist = vec {}; archive_options = opt record { trigger_threshold = 2000; num_blocks_to_archive = 1000; controller_id = principal "'${ARCHIVE_CONTROLLER}'" }})'

成功すると、次のように出力されます：

    Deployed canisters.
    URLs:
      Frontend canister via browser
        ledger_transfer_frontend: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
      Backend canister via Candid interface:
        ledger: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
        ledger_transfer_backend: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=be2us-64aaa-aaaaa-qaabq-cai

### ステップ 11: 台帳canister が正常で、期待どおりに動作していることを、コマンドを使用して確認します：

    dfx canister call ledger account_balance '(record { account = '$(python3 -c 'print("vec{" + ";".join([str(b) for b in bytes.fromhex("'$LEDGER_ACC'")]) + "}")')' })'

出力は次のようになります：

    (record { e8s = 100_000_000_000 : nat64 })

### ステップ10：別の作業ディレクトリで、`ledger_transfer` プロジェクトのファイルを含む Github リポジトリをクローンします：

    git clone https://github.com/dfinity/examples.git

### ステップ11:`ledger-transfer` canister のファイルを`ledger_transfer` ワークスペースにコピーします：

    cp -r ./examples/motoko/ledger-transfer/src/* ./ledger_transfer/src

### ステップ12:`dfx.json` ファイルを編集して、'canisters' セクション内に以下の情報を含めます：

    ...
      "canisters": {
        "ledger_transfer": {
          "dependencies": [
            "ledger"
          ],
          "main": "src/ledger_transfer/main.mo",
          "type": "motoko"
        },
        "ledger": {
          "type": "custom",
          "candid": "ledger.public.did",
          "wasm": "ledger.wasm"
        }
      },
    ...

### ステップ 13: この新しいcanister をデプロイします：

    dfx deploy

出力は以下のようになるはずです：

    Deployed canisters.
    URLs:
      Frontend canister via browser
        ledger_transfer_frontend: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
      Backend canister via Candid interface:
        ledger: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
        ledger_transfer: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bw4dl-smaaa-aaaaa-qaacq-cai
        ledger_transfer_backend: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=be2us-64aaa-aaaaa-qaabq-cai

### ステップ14:canister のアドレスを決定します：

    dfx canister call ledger_transfer canisterAccount '()'

出力は以下のようになるはずです：

    (
      blob "\94\b9\bc]\ab(\ad\b93\8dE\19#\914\b6\a0\0e\dfam5\e4\e5\80\b5\01\9a~\e1_{",
    )

### ステップ15:canister に送金します：

    dfx canister call ledger transfer '(record { to = blob "\08.\cf.?dz\c6\00\f4?8\a6\83B\fb\a5\b8\e6\8b\08_\02Y+w\f3\98\08\a8\d2\b5"; memo = 1; amount = record { e8s = 2_00_000_000 }; fee = record { e8s = 10_000 }; })'

成功すれば、次のように出力されます：

    (variant { Ok = 1 : nat64 })

### ステップ16: 新規ユーザーとしてメッセージを投稿します：

    dfx identity new --disable-encryption ALICE
    dfx identity use ALICE
    dfx canister call ledger_transfer post "(\"Test message\")"

### ステップ17: ユーザーに報酬を配布します：

    dfx identity use default
    dfx canister call ledger_transfer distributeRewards '()'

<!---
# ICP transfer sample code

## Overview
ICP transfer is a canister that can transfer ICP from its account to other accounts. It is an example of a canister that uses the ledger canister. Sample code is available in [Motoko](https://github.com/dfinity/examples/tree/master/motoko/ledger-transfer) and [Rust](https://github.com/dfinity/examples/tree/master/rust/tokens_transfer).

## Architecture
The sample code revolves around one core transfer function which takes as input the amount of ICP to transfer, the account (and optionally the subaccount) to which to transfer ICP and returns either success or an error in case e.g. the ICP transfer canister doesn’t have enough ICP to do the transfer. In case of success, a unique identifier of the transaction is returned. This identifier will be stored in the memo of the transaction in the ledger.

This sample will use the Motoko variant. 

This example uses the ledger transfer project files located in the [samples Github repo](https://github.com/dfinity/examples/tree/master/motoko/ledger-transfer).

## Prerequisites

This example requires an installation of:
- [x] Install the [IC SDK](../developer-docs/setup/install/index.mdx).
- [x] Download and install [git.](https://git-scm.com/downloads)

### Step 1: Create a new `dfx` project and navigate into the project's directory.

```
dfx new ledger_transfer
cd ledger_transfer
```

### Step 2: Download a pre-built ledger canister module and Candid interface files:

```
export IC_VERSION=dd3a710b03bd3ae10368a91b255571d012d1ec2f
curl -o ledger.wasm.gz "https://download.dfinity.systems/ic/$IC_VERSION/canisters/ledger-canister_notify-method.wasm.gz"
gunzip ledger.wasm.gz
curl -o ledger.private.did "https://raw.githubusercontent.com/dfinity/ic/$IC_VERSION/rs/rosetta-api/ledger.did"
dfx canister --network ic call ryjl3-tyaaa-aaaaa-aaaba-cai __get_candid_interface_tmp_hack '()' --query | sed 's/\\n/\n/g'
```

This download should result in 3 files:
- [ledger.wasm](./_attachments/ledger.wasm)
- [ledger.public.did](./_attachments/ledger.public.did)
- [ledger.private.did](./_attachments/ledger.private.did)

### Step 3: In the `dfx.json` file, replace the existing content with the following:

```
{
  "canisters": {
    "ledger": {
      "type": "custom",
      "wasm": "ledger.wasm",
      "candid": "ledger.public.did",
      "remote": {
    "candid": "ledger.public.did",
    "id": {
      "ic": "ryjl3-tyaaa-aaaaa-aaaba-cai"
    }
  }
    },
    "ledger_transfer_backend": {
      "main": "src/ledger_transfer_backend/main.mo",
      "type": "motoko"
    },
    "ledger_transfer_frontend": {
      "dependencies": [
        "ledger_transfer_backend"
      ],
      "frontend": {
        "entrypoint": "src/ledger_transfer_frontend/src/index.html"
      },
      "source": [
        "src/ledger_transfer_frontend/assets",
        "dist/ledger_transfer_frontend/"
      ],
      "type": "assets"
    }
  },
  "defaults": {
     "replica": {
      "subnet_type":"system"
    },
    "build": {
      "args": "",
      "packtool": ""
    }
  },
  "output_env_file": ".env",
  "version": 1
}
```

### Step 5: Start a local replica:

```
dfx start --background
```

### Step 6: Create a new identity that will work as a minting account:

```
dfx identity new minter
dfx identity use minter
export MINT_ACC=$(dfx ledger account-id)
```

Transfers from the minting account will create Mint transactions. Transfers to the minting account will create Burn transactions.

### Step 7: Switch back to your default identity and record its ledger account identifier:

```
dfx identity use default
export LEDGER_ACC=$(dfx ledger account-id)
```


### Step 8: Deploy the ledger canister to your network:

```
dfx deploy ledger --argument '(record {minting_account = "'${MINT_ACC}'"; initial_values = vec { record { "'${LEDGER_ACC}'"; record { e8s=100_000_000_000 } }; }; send_whitelist = vec {}})'
```

If you want to setup the ledger in a way that matches the production deployment, you should deploy it with archiving enabled. In this setup, the ledger canister dynamically creates new canisters to store old blocks. We recommend using this setup if you are planning to exercise the interface for fetching blocks.

### Step 9: Obtain the principal of the identity you use for development. 
This principal will be the controller of archive canisters.

```
dfx identity use default
export ARCHIVE_CONTROLLER=$(dfx identity get-principal)
```

### Step 10: Deploy the ledger canister with archiving options:

```
dfx deploy ledger --argument '(record {minting_account = "'${MINT_ACC}'"; initial_values = vec { record { "'${LEDGER_ACC}'"; record { e8s=100_000_000_000 } }; }; send_whitelist = vec {}; archive_options = opt record { trigger_threshold = 2000; num_blocks_to_archive = 1000; controller_id = principal "'${ARCHIVE_CONTROLLER}'" }})'
```

If successful, the output should be:

```
Deployed canisters.
URLs:
  Frontend canister via browser
    ledger_transfer_frontend: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
  Backend canister via Candid interface:
    ledger: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
    ledger_transfer_backend: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=be2us-64aaa-aaaaa-qaabq-cai
```

### Step 11: Verify that the ledger canister is healthy and working as expected by using the command:

```
dfx canister call ledger account_balance '(record { account = '$(python3 -c 'print("vec{" + ";".join([str(b) for b in bytes.fromhex("'$LEDGER_ACC'")]) + "}")')' })'
```

The output should be:

```
(record { e8s = 100_000_000_000 : nat64 })
```

### Step 10: In a separate working directory, clone the Github repo containing the `ledger_transfer` project's files:

```
git clone https://github.com/dfinity/examples.git
```

### Step 11: Copy the files for the `ledger-transfer` canister into your `ledger_transfer` workspace:

```
cp -r ./examples/motoko/ledger-transfer/src/* ./ledger_transfer/src
```

### Step 12: Edit your `dfx.json` file to include the following information within the 'canisters' section:

```
...
  "canisters": {
    "ledger_transfer": {
      "dependencies": [
        "ledger"
      ],
      "main": "src/ledger_transfer/main.mo",
      "type": "motoko"
    },
    "ledger": {
      "type": "custom",
      "candid": "ledger.public.did",
      "wasm": "ledger.wasm"
    }
  },
...
```


### Step 13: Deploy this new canister:

```
dfx deploy
```

Your output should resemble the following:

```
Deployed canisters.
URLs:
  Frontend canister via browser
    ledger_transfer_frontend: http://127.0.0.1:4943/?canisterId=br5f7-7uaaa-aaaaa-qaaca-cai
  Backend canister via Candid interface:
    ledger: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bkyz2-fmaaa-aaaaa-qaaaq-cai
    ledger_transfer: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=bw4dl-smaaa-aaaaa-qaacq-cai
    ledger_transfer_backend: http://127.0.0.1:4943/?canisterId=bd3sg-teaaa-aaaaa-qaaba-cai&id=be2us-64aaa-aaaaa-qaabq-cai
```

### Step 14: Determine out the address of your canister:

```
dfx canister call ledger_transfer canisterAccount '()'
```

Your output should resemble the following:

```
(
  blob "\94\b9\bc]\ab(\ad\b93\8dE\19#\914\b6\a0\0e\dfam5\e4\e5\80\b5\01\9a~\e1_{",
)
```

### Step 15: Transfer funds to your canister:

```
dfx canister call ledger transfer '(record { to = blob "\08.\cf.?dz\c6\00\f4?8\a6\83B\fb\a5\b8\e6\8b\08_\02Y+w\f3\98\08\a8\d2\b5"; memo = 1; amount = record { e8s = 2_00_000_000 }; fee = record { e8s = 10_000 }; })'
```

If successful, the output should be:

```
(variant { Ok = 1 : nat64 })
```

### Step 16: Post a message as a new user:

```
dfx identity new --disable-encryption ALICE
dfx identity use ALICE
dfx canister call ledger_transfer post "(\"Test message\")"
```

### Step 17: Distribute rewards to users:

```
dfx identity use default
dfx canister call ledger_transfer distributeRewards '()'
```




-->
