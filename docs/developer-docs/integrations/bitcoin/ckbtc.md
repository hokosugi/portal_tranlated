# チェーンキービットコイン（ckBTC）

## 概要

Chain-key Bitcoin (ckBTC)は[ICRC-1に準拠した](https://github.com/dfinity/ICRC-1/blob/aa82e52aaa74cc7c5f6a141e30b708bf42ede1e3/standards/ICRC-1/README.md)トークンで、ICメインネット上に100%保有されているビットコインによって1:1で裏付けされています。

ckBTCの機能は、2つのcanisters の相互作用によって提供されます：

- **ckBTCマイナー**。
- **ckBTC台帳**。

ckBTC台帳は、アカウントの残高を管理し、アカウント間でckBTCを送金する役割を担っています。以下の機能を提供します：

- ckBTCマイナーによるckBTCの鋳造と発行。
- ユーザー間でのckBTCの移動を可能にします。

ckBTCマイナーはckBTCトークンの発行とバーンを担当します。トークンは、ユーザーがビットコインをckBTCマイナー管理下の特定のビットコインアドレスに送金すると発行されます。ビットコインアドレスは送信されたビットコインの所有者を一意に識別するため、ckBTCマイナーにとって、鋳造されたckBTC資金を正しい所有者に関連付けることが可能になります。ビットコインには最終性がないため、ckBTCの総供給量に影響を与えるすべてのビットコイン取引について、ckBTC採掘者は多数の
確認を待ちます。ビットコインの取得要求を処理する場合、ckBTC ミンターは ckBTC をバーンしてから、対応する BTC 量（手数料を差し引いた額）を通常のビットコイン取引を使って送金します。

ckBTC minter の詳細な説明は[GitHub リポジトリに](https://github.com/dfinity/ic/tree/master/rs/bitcoin/ckbtc/minter)あります。

ckBTCを発行して送金するプロセスの簡略化された概要は、以下の図に示されています。

![ckBTC overview](../../../samples/_attachments/ckbtc-overview.png)

技術的な概要については、[こちらを](bitcoin-how-it-works.md)ご覧ください。

このページでは、ckBTCマイナーcanister とのやりとりに使用できるAPIエンドポイントの詳細を説明します。

## ckBTC台帳

ckBTC台帳は、ckBTCマイナーからの発行およびバーン要求を受け付け、正の残高を持つすべてのアカウントのckBTC残高を記録します。さらに、ckBTC台帳はckBTC取引を処理します。

ckBTC台帳はICRC-1トークン規格に準拠しています。技術的な詳細は、使用されている[ICRC-1台帳実装の](https://github.com/dfinity/ic/tree/master/rs/rosetta-api/icrc1)GitHubリポジトリにあります。

## ckBTCマイナー

ckBTC minterはcanister 、NNSによって制御され、[pzp6e](https://dashboard.internetcomputer.org/subnet/pzp6e-ekpqk-3c5x7-2h6so-njoeq-mt45d-h3h6c-q3mxf-vpeq5-fk5o7-yae)サブネット上で動作します。ckBTC mintercanister の設定では、以下の設定を行います：

- `retrieve_btc_min_amount`:これは、バーン可能な最小ckBTC量であり、それに対応して、引き出し可能な最小BTC量です。パラメータは0.001 BTC、または100,000 Satoshiに設定されています。
- `max_time_in_queue_nanos`:BTCの引き出しリクエストは、最大でもこの時間だけキューに保管されます。リクエストをすぐに処理するのではなく、キャッシュすることで、複数のリクエストを1回のトランザクションで処理することができ、ビットコインのマイナーの手数料を節約できるという利点があります。このパラメータは10分に設定されており、ビットコインブロック間の予想される時間に対応しています。
- `min_confirmations`:ckBTC マイナーがビットコイン取引を受け入れるために必要な確認の数。特に、ckBTC マイナーが管理するビットコインアドレスに BTC を送金するトランザクションがこのトランザクション数に達する前に、ckBTC マイナーは ckBTC を発行しません。このパラメータは当初 72 に設定されていましたが、その間に 12 に削減されました。
- `kyt_fee`:KYT チェックに支払わなければならない手数料。現在は2000サトシに設定されています。

他にもパラメータがありますが、[ckBTC minter の Candid ファイルに](https://github.com/dfinity/ic/blob/master/rs/bitcoin/ckbtc/minter/ckbtc_minter.did)記載されています。

## ckBTC minter API エンドポイント

ckBTC minter はcanister とやり取りするために使用できる以下の API エンドポイントを提供します：

- `get_btc_address`:呼び出し元がこのアドレスに BTC を送信して ckBTC を取得するために使用できる、特定のビットコインアドレスを返します。
- `update_balance`:ビットコインアドレスの残高をチェックし、所有者の口座にckBTCを鋳造するよう、ckBTCマイナーに指示します。
- `estimate_withdrawal_fee`:特定の BTC 量を取得する際に支払う手数料の現在の見積もりを返します。
- `get_deposit_fee`:ckBTCを発行する際に請求される手数料を返します。
- `get_withdrawal_account`:BTCを取得する前に所有者がckBTCを送金する必要がある特定のckBTCアカウントを返します。
- `retrieve_btc`:特定のckBTC量をバーンし、手数料を差し引いた対応するBTC量を指定されたビットコインアドレスに送信するようckBTCマイナーに指示します。
- `retrieve_btc_status`:以前の`retrieve_btc` 呼び出しのステータスを返します。
- `get_minter_info`:ckBTCマイナー自体の情報を返します。
- `get_events`:
  エンドポイントについては以下で詳しく説明します。

### `get_btc_address(owner: opt principal, subaccount: opt blob)`

提供されたプリンシパルIDとサブアカウントは、[ecdsa\_public\_key](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-ecdsa_public_key)関数の派生パスを形成するために連結されます。プリンシパルIDが提供されない場合、送信者のプリンシパルIDが使用されます。サブアカウントが提供されない場合、デフォルトのサブアカウント（すべてゼロ）が使用されます。

この公開鍵は、pay-to-witness-public-key-hash (P2WPKH) Bitcoin アドレスとしてエンコードされ、テキストとして返されます。

鍵の導出は、導出レベルごとに 31 ビットが使用される BIP-32 準拠ではないことに注意。その代わりに、プリンシパル ID とサブアカウントに基づいて単一の導出が実行されます。導出は決定論的であるため、canister は与えられたプリンシパル ID とサブアカウント自体のビットコインアドレスを導出できます。

### `update_balance(owner: opt principal, subaccount: opt blob)`

`update_balance` 関数は、特定のビットコインアドレスに新しい UTXO があるかどうかをチェックするよう ckBTC マイナーに指示するために呼び出されます。

新しい UTXO がない場合はエラーが返されます。

### `estimate_withdrawal_fee(amount: opt nat64)`

エンドポイントは、指定された BTC 量を取得するときに支払わなければならない手数料の見積もりを返します。内部的には、（有効な署名なしで）トランザクションが構築され、ビットコイン採掘者手数料、ckBTC 採掘者手数料、および KYT 手数料で構成される手数料が決定されます。金額が提供されない場合、トランザクションを構築するために 3 つの入力が必要であると見なされます。

ビットコインを取得するリクエストを発行する前に、ckBTC マイナーとビットコインcanister の内部ステートに変更がない場合、手数料は正確に返された見積もりとなります。

その間に新しいビットコイン・ブロックが採掘され、ビットコインcanister がビットコイン・マイナーの手数料を更新した場合や、手数料を見積もる際に使用された出力の一部を使用する別の取得要求が最初に処理された場合に、手数料が変更される可能性があります。

### `get_deposit_fee`

エンドポイントは、新しい UTXO を受け取ったときに、ckBTC マイナーが ckBTC を発行するために請求する手数料を返します。現在、この手数料は単にKYT手数料です。

### `get_withdrawal_account`

この関数は、呼び出し元の引き出し口座を返します。これは、ckBTC マイナーのプリンシパル ID から派生した口座と、呼び出し元のプリンシパル ID から派生したサブ口座です。

ユーザーは、最初にこの特定の口座にckBTCを送金することによってのみBTCを引き出すことができます。

### `retrieve_btc(address: text, amount: nat64)`

この関数はckBTCマイナーに指定された金額をバーンするよう指示します。リクエストがキューからピックアップされると、リクエスト額から手数料を差し引いた額を指定されたビットコインアドレスに送金する出力を含むビットコイントランザクションが作成されます。

ビットコインの取得は非同期に処理されるため、この関数はckBTCトークンをバーンするトランザクションのブロックインデックスを返します。

### `retrieve_btc_status(block_index: nat64)`

BTC取得リクエストのステータスは、この関数を使用して確認できます。可能なステータスは次のとおりです：

- `Unknown`:指定されたブロックインデックスに関連する検索要求がないか、検索要求が古く、対応する情報がすでに削除されているため、ckBTCマイナーで利用可能な情報がありません。
- `Pending`:BTC 検索リクエストがキューに入っています。
- `Signing`:BTC 検索リクエストは、BTC 検索リクエストに対応するためのすべての署名を取得中です。
- `Sending`:Bitcoin トランザクションが送信中です。
- `Submitted`:ビットコイントランザクションが送信されました。トランザクション ID が返されます。
- `AmountTooLow`:ビットコインマイナーの手数料が法外に高いため、BTC 検索リクエストを処理できませんでした。
- `Confirmed`:これは、トランザクションが上記の`min_confirmations` パラメータで指定された最低必要確認数を持っている場合に発生します。

### `get_minter_info`

この関数は、ckBTCマイナー内部の以下のパラメータを返します：

- `kyt_fee`
- `min_confirmations`
- `retrieve_btc_min_amount`

### `get_events(start: nat64, length: nat64)`

ckBTC minter は、その内部ステートを変更するイベントを追跡します。開始インデックスと長さパラメータが与えられると、ckBTC minterは、与えられた`start` インデックスのイベントからインデックス`start+length-1` のイベントまで、すべてのイベントを順次返します。

このエンドポイントはデバッグ目的で使用され、このエンドポイントがこの形式で存在し続ける保証はないことに注意してください。

## リソース

Bitcoin を使用した独自のアプリの構築を開始するには、以下のチュートリアルを参照してください：

- [ckBTC wiki ページ](https://wiki.internetcomputer.org/wiki/Chain-key_Bitcoin)。
- [最初のビットコインのデプロイdapp](../../../samples/deploying-your-first-bitcoin-dapp.md) 。
- [GitHub リポジトリ](https://github.com/dfinity/ic/tree/master/rs/bitcoin/ckbtc/minter)。
- [ローカル開発](./local-development.md)。

<!---
# Chain-key Bitcoin (ckBTC)

## Overview

Chain-key Bitcoin (ckBTC) is an [ICRC-1](https://github.com/dfinity/ICRC-1/blob/aa82e52aaa74cc7c5f6a141e30b708bf42ede1e3/standards/ICRC-1/README.md)-compliant token that is backed 1:1 by bitcoins held 100% on the IC mainnet.

The ckBTC functionality is provided through an interplay of two canisters:
- The **ckBTC minter**.
- The **ckBTC ledger**.

The ckBTC ledger is responsible for keeping account balances and for transferring ckBTC between accounts. It provides the following functionality:
- It enables the ckBTC minter to mint and burn ckBTC.
- It enables the transfer of ckBTC among users.

The ckBTC minter is responsible for the minting and burning of ckBTC tokens. Tokens are minted when a user transfers bitcoins to a specific Bitcoin address under the ckBTC minter's control. The Bitcoin address uniquely identifies the owner of the sent bitcoins, making it possible for the ckBTC minter to associate the minted ckBTC funds with the correct owner. The ckBTC minter waits for a large number of
confirmations for all Bitcoin transactions that affect the total supply of ckBTC because of the lack of finality in Bitcoin. When handling Bitcoin retrieval requests, the ckBTC minter burns ckBTC before transferring the corresponding BTC amount (minus fees) using a regular Bitcoin transaction.

A detailed description of the ckBTC minter can be found in its [GitHub repository](https://github.com/dfinity/ic/tree/master/rs/bitcoin/ckbtc/minter).

A simplified overview of the process to mint and transfer ckBTC is depicted in the following figure.

![ckBTC overview](../../../samples/_attachments/ckbtc-overview.png)

For a deeper technical overview, please see [here](bitcoin-how-it-works.md).

This page details the API endpoints that can be used to interact with the ckBTC minter canister.

## ckBTC ledger
The ckBTC ledger accepts minting and burning requests from the ckBTC minter and records the ckBTC balances of every account with a positive balance. Additionally, the ckBTC ledger handles the ckBTC transactions.

The ckBTC ledger adheres to the ICRC-1 token standard. Technical details can be found on the GitHub repository of the used [ICRC-1 ledger implementation](https://github.com/dfinity/ic/tree/master/rs/rosetta-api/icrc1).

## ckBTC minter
The ckBTC minter is a canister that is controlled by the NNS and running on the [pzp6e](https://dashboard.internetcomputer.org/subnet/pzp6e-ekpqk-3c5x7-2h6so-njoeq-mt45d-h3h6c-q3mxf-vpeq5-fk5o7-yae) subnet. In the configuration of the ckBTC minter canister, the following configurations are set:

- `retrieve_btc_min_amount`: This is the minimum ckBTC amount that can be burned and, correspondingly, the minimum BTC amount that can be withdrawn. The parameter is set to 0.001 BTC, or 100,000 Satoshi.
- `max_time_in_queue_nanos`: Any BTC retrieval request should be kept in a queue for at most this time. Caching requests rather than handling them right away has the advantage that multiple requests can be served in a single transaction, saving Bitcoin miner fees. The parameter is set to 10 minutes, which corresponds to the expected time between Bitcoin blocks.
- `min_confirmations`: The number of confirmations required for the ckBTC minter to accept a Bitcoin transaction. In particular, the ckBTC minter does not mint ckBTC before a transaction transferring BTC to a Bitcoin address managed by the ckBTC minter reaches this number of transactions. The parameter was initially set to 72 but has been reduced to 12 in the meantime.
- `kyt_fee`: The fee that must be paid for KYT checks. It is currently set to 2000 satoshi.

There are other parameters that are self-explanatory and can be found in the [ckBTC minter Candid file](https://github.com/dfinity/ic/blob/master/rs/bitcoin/ckbtc/minter/ckbtc_minter.did).

## ckBTC minter API endpoints
The ckBTC minter provides the following API endpoints that can be used to interact with the canister:

- `get_btc_address`: Returns a specific Bitcoin address that the caller can use to obtain ckBTC by sending BTC to this address.
- `update_balance`: Instructs the ckBTC minter to check the balance of a Bitcoin address and mint ckBTC into the account of the owner.
- `estimate_withdrawal_fee`: Returns a current estimate for the fee to be paid when retrieving a certain BTC amount.
- `get_deposit_fee`: Returns the fee charged when minting ckBTC.
- `get_withdrawal_account`: Returns a specific ckBTC account where the owner must transfer ckBTC before being able to retrieve BTC.
- `retrieve_btc`: Instructs the ckBTC minter to burn a certain ckBTC amount and send the corresponding BTC amount, minus fees, to a provided Bitcoin address.
- `retrieve_btc_status`: Returns the status of a previous `retrieve_btc` call.
- `get_minter_info`: Returns information about the ckBTC minter itself.
- `get_events`: Returns a set of events at the ckBTC minter.
The endpoints are discussed in more detail in the following.

### `get_btc_address(owner: opt principal, subaccount: opt blob)`
The provided principal ID and subaccount are concatenated to form the derivation path for the [ecdsa_public_key](https://internetcomputer.org/docs/current/references/ic-interface-spec#ic-ecdsa_public_key) function, which returns the derived public key. If no principal ID is provided, then the sender’s principal ID is used. If no subaccount is provided, then the default subaccount (all zeros) is used.

This public key is encoded as a pay-to-witness-public-key-hash (P2WPKH) Bitcoin address and returned as a text.

Note that the key derivation is not BIP-32 compliant where 31 bits are used for each derivation level. Instead, a single derivation is performed based on the full principal ID and subaccount. Since the derivation is deterministic, a canister can derive the Bitcoin address for a given principal ID and subaccount itself.

### `update_balance(owner: opt principal, subaccount: opt blob)`
The `update_balance` function is invoked to instruct the ckBTC minter to check if there are new UTXOs for a particular Bitcoin address.

For each newly discovered UTXO, the corresponding ckBTC amount is minted minus the deposit fee, which corresponds to the KYT fee.
If there are no new UTXOs, an error is returned.

### `estimate_withdrawal_fee(amount: opt nat64)`
The endpoint returns an estimate for the fee that must be paid when retrieving the given BTC amount. Internally, a transaction is built (without valid signatures) to determine the fee, which consists of the Bitcoin miner fee, the ckBTC minter fee, and the KYT fee. If no amount is provided, it is assumed that three inputs are required to build the transaction.

If there is no change to the internal state of the ckBTC minter and the Bitcoin canister before issuing the request to retrieve Bitcoins, the fee will be exactly the returned estimate.

The fee can change when a new Bitcoin block is mined in the meantime, which causes the Bitcoin canister to update the Bitcoin miner fees or when another retrieval request that spends some of the outputs that were used when estimating the fee is handled first.

### `get_deposit_fee`
The endpoint returns the fee that the ckBTC minter charges for minting ckBTC when receiving new UTXOs. Currently, this fee is simply the KYT fee.

### `get_withdrawal_account`
The function returns the caller’s withdrawal account, which is the account derived from the ckBTC minter’s principal ID and the subaccount derived from the caller’s principal ID.

A user can only retrieve BTC by first transferring ckBTC to this particular account.

### `retrieve_btc(address: text, amount: nat64)`
The function instructs the ckBTC minter to burn the specified amount. Once the request is picked up from the queue, a Bitcoin transaction is created containing an output that transfers the requested amount minus fees to the specified Bitcoin address.

Since Bitcoin retrieval is handled asynchronously, the function returns the block index of the transaction burning the ckBTC tokens.

### `retrieve_btc_status(block_index: nat64)`
The status of a BTC retrieval request can be checked using this function. The different possible statuses are:

- `Unknown`: There is no information available on the ckBTC minter because there is no retrieval request associated with the given block index or the retrieval request is old and the corresponding information has already been removed.
- `Pending`: The BTC retrieval request is queued.
- `Signing`: The BTC retrieval request is acquiring all signatures to serve the BTC retrieval request.
- `Sending`: The Bitcoin transaction serving the BTC retrieval request is being sent.
- `Submitted`: The Bitcoin transaction has been transmitted. The transaction ID is returned.
- `AmountTooLow`: The BTC retrieval request could not be served because the Bitcoin miner fees are prohibitively high.
- `Confirmed`: The Bitcoin transaction serving the BTC retrieval request is confirmed inside the ckBTC minter, which happens when the transaction has at least the minimum required number of confirmations specified in the `min_confirmations` parameter above.

### `get_minter_info`
The function returns the following parameters internal to the ckBTC minter:

- `kyt_fee`
- `min_confirmations`
- `retrieve_btc_min_amount`

### `get_events(start: nat64, length: nat64)`
The ckBTC minter tracks the events that change its internal state. Given a start index and a length parameter, the ckBTC minter returns all events sequentially from the event at the given `start` index up to the event at index `start+length-1`.

Note that this endpoint is used for debugging purposes and there is no guarantee that the endpoint will continue to exist in this form.

## Resources

To start building your own apps with Bitcoin see the following tutorials:

- [ckBTC wiki page](https://wiki.internetcomputer.org/wiki/Chain-key_Bitcoin).
- [Deploying your first Bitcoin dapp](../../../samples/deploying-your-first-bitcoin-dapp.md).
- [GitHub repository](https://github.com/dfinity/ic/tree/master/rs/bitcoin/ckbtc/minter).
- [Local development](./local-development.md).

-->
