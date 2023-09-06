---

sidebar_position: 3
---
# SNSインデックスcanister

## 概要

canister インデックスは、[元帳canister](ledger-integration.md)からトランザクションを取得し、**口座**別にインデックスを付けます。
これにより、元帳チェーンから降順で口座のトランザクションを照会したり、**プリンシパルに**属する口座のリストを照会したりできます。
インデックスcanister は、SNS プロジェクトの一部として常にデプロイされます。

このcanister は、特定の口座のトランザクションを表示したいアプリケーションに便利です。

定期的に(ハートビートごとに)、インデックスcanister は
の元帳canister からトランザクションを照会し、アカウントごとに既知のトランザクションのインデックスを構築します。

## 初期化

canister
NNS が の新しい SNS を作成すると、自動的に SNS インデックス がデプロイされます。dapp canister

インデックスcanister の初期化には、インデックスを作成する ICRC-1 台帳canister のプリンシパルが必要です：

    type InitArgs = record {
    ledger_id : principal;
    };

`dfx` を使用した例：

``` shell
dfx deploy icrc1-index --argument "(record {
      ledger_id = principal \"rrkah-fqaaa-aaaaa-aaaaq-cai\"
    }
)"
```

## 使用方法

提供されるメソッドは以下のとおりです：

    get_account_transactions : (GetAccountTransactionsArgs) -> (GetTransactionsResult);

- このメソッドは、指定されたアカウントのトランザクションを返します。取引は id の降順で返されます。
  オプションとして、ユーザーは開始取引 ID を指定することができ、この ID より前の取引のみを照会できます。開始が指定されない場合は、最後のトランザクションが使用されます。

<!-- end list -->

    ledger_id : () -> (principal) query;

- このメソッドは、インデックス付けされている元帳canister のプリンシパルを返します。

<!-- end list -->

    list_subaccounts : (ListSubaccountsArgs) -> (vec SubAccount) query;

- このメソッドは、プリンシパルに対してインデックス付けされたすべてのサブアカウントをリストします。

## キャンディッド参照ファイル

インターフェースの詳細については、インデックスcanister の[Candid ファイルを](https://gitlab.com/dfinity-lab/public/ic/-/blob/master/rs/rosetta-api/icrc1/index/index.did)確認してください。

<!---

# SNS index canister
## Overview
The index canister fetches transactions from the [ledger canister](ledger-integration.md) and indexes them by **account**. 
It allows to query the transactions of an account in descending order from the ledger chain, and the list of accounts that belongs to a **principal**. 
An index canister is always deployed as part of an SNS project.

This canister is useful for applications that want to show the transactions of a specific account.

Regularly (at each heartbeat), the index canister will query the transactions from
the ledger canister and then build the index of known transaction per account.

## Initialization

This sections explains how to deploy an index canister in isolation.
When the NNS creates a new SNS for a dapp, it will automatically be deployed with an SNS index canister.

The index canister initialisation requires the principal of the ICRC-1 ledger canister that should be indexed:

```
type InitArgs = record {
ledger_id : principal;
};
```

Example with `dfx`:

```shell
dfx deploy icrc1-index --argument "(record {
      ledger_id = principal \"rrkah-fqaaa-aaaaa-aaaaq-cai\"
    }
)"
```

## Usage

The provided methods are:

```
get_account_transactions : (GetAccountTransactionsArgs) -> (GetTransactionsResult);
```
- This method returns the transactions for a given account. Transactions are returned in descending id order.
Optionally, the user can specify a starting transaction id allowing to only query for transactions before this id. If no start is specified, the last transaction is used.

```
ledger_id : () -> (principal) query;
```
- This method returns the principal of the ledger canister being indexed.

```
list_subaccounts : (ListSubaccountsArgs) -> (vec SubAccount) query;
```
- This method lists all indexed subaccounts for a principal.

## Candid reference file

Please check the [Candid file](https://gitlab.com/dfinity-lab/public/ic/-/blob/master/rs/rosetta-api/icrc1/index/index.did) of the index canister for the interface details.

-->
