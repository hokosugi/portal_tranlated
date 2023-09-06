# 為替レートcanister

## 概要

XRC と呼ばれる為替レートcanister は、**uzr34 システムのサブネットで**実行されるcanister です。XRCは[HTTPSアウトコールを](https://internetcomputer.org/https-outcalls/)使用して、取引所のパブリックAPIを使用して主要な暗号通貨取引所からデータを取得し、リアルタイムまたは過去の暗号通貨価格情報を取得します。XRCはまた、外国為替レートを取得するために、世界中の外国為替データ・プロバイダーのパブリックAPIを定期的に照会します。

XRCcanister は、分散型取引所（DEX）などのdapps によって使用され、為替レートを市場レートと比較して、canister スマートコントラクトに保有されている資産の価値を決定するなどの機能を提供することができます。例えば、NNSのcycle 発行canister はXRCを使用して現在のICP/XDR為替レートを取得します。これはICPからcycles への変換に必要です。

XRCは、他のcanisters からのリクエストに応じてこのデータを提供します。他のcanister からの要求は、ベース・アセット、クォート・アセット、およびオプションの UNIX エポック・タイムスタンプで構成されます。

**アセットとは**、シンボル（「ICP」など）とクラス（「Cryptocurrency」または「FiatCurrency」）で構成されるレコードです。ベースアセットとクォートアセットは、ICP/USD、USD/EUR、BTC/ICPなど、暗号通貨とフィアット通貨のアセットを自由に組み合わせることができます。タイムスタンプパラメータは、過去のレートを要求する機能を有効にします。タイムスタンプが提供されない場合、現在時刻の為替レートがデフォルトで返されます。

## 使用法

XRC のcanister ID は`uf6dk-hyaaa-aaaaq-qaaaq-cai` です。

為替レートcanister は単一のエンドポイントを提供します：

    "get_exchange_rate": (GetExchangeRateRequest) -> (GetExchangeRateResult)

XRC にリクエストするには、以下のリクエストフォームを使用します：

```
type GetExchangeRateRequest = record {
   base_asset: Asset;
   quote_asset: Asset;
   timestamp: opt nat64;
}; 
```

このフォームを XRC に送信すると、次のような応答が返されます：

    type GetExchangeRateResult = variant {
       Ok: ExchangeRate;
       Err: ExchangeRateError;
    };

## Cycles コスト

このフォームをXRCに送信すると、次のようなレスポンスが返されます。XRCへの各リクエストには、リクエスト送信に10Bcycles のコストがかかります。そうでない場合は、`ExchangeRateError::NotEnoughCycles` エラーが返されます。リクエスト呼び出しの実際のcycles コストは、リクエストされたアセットタイプと内部為替レートキャッシュのステートという 2 つの factorに依存します。これらのパラメータは以下のとおりです：

- リクエストがキャッシュから提供できる場合、実際のコストは20Mcycles です。
- 両方の資産が不換紙幣の場合、コストは20Mcycles です。
- 資産の一方が不換紙幣または暗号通貨USDTの場合、コストは260Mcycles です。
- 両方の資産が暗号通貨の場合、コストは500Mcycles 。

残りのcycles は要求元のcanister に返されます。サービス妨害攻撃のリスクを軽減するため、エラーの場合でも少なくとも1Mcycles が課金されることに注意してください。

:::info
注意: これらのリクエストのcycles コストは、XRCの次回のアップグレードで減少する予定です。
::：

## XRCを直接呼び出す

XRC を直接呼び出すには、まずウォレットcanister ID をコマンドで環境変数としてエクスポートします：

    export WALLET=[wallet-canister-id]

ウォレットのcanister ID を取得する必要がある場合は、`dfx wallet addresses` コマンドを実行してウォレットのcanister ID を返します。

次に、次のコマンドで BTC/USD レートを取得するようなリクエストを送信できます：

    dfx canister call --wallet $WALLET --with-cycles 10000000000 xrc get_exchange_rate '(record { base_asset = record { symbol = "BTC"; class = variant { Cryptocurrency } }; quote_asset = record { symbol = "USD"; class = variant { FiatCurrency } } })'

## 為替レートcanister デモ

また、ローカルのXRCデモアプリケーションを実行して、ローカルで機能をテストすることもできます。

:::caution
このサンプルdapp は非常に単純であることは注目に値します。エラーが発生した場合、返り値が単なる浮動小数点数であるため、常に`0` が返されます。
::：

### 前提条件

- \[x\] IC SDK パッケージをダウンロードして[インストールして](/developer-docs/setup/install/index.mdx)ください。

- \[x\][Nodejsとnpmを](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)ダウンロードしてインストールしてください。

- \[x\][gitを](https://git-scm.com/downloads)ダウンロードしてインストールします。

次に、サンプルリポジトリをクローンし、依存関係をインストールし、以下のコマンドでcanisters をビルドします：

    git clone git@github.com:THLO/xrc_demo.git
    cd xrc_demo/
    npm install
    dfx canister create xrc_demo

### デプロイ

XRCデモをローカルでテストするには、まずコマンドでローカルの実行環境を起動します：

    dfx start --background

次に、canisters を次のコマンドでデプロイします：

    dfx deploy --with-cycles 10000000000

為替レートcanister へのリクエストごとにcycles を送信する必要があるため、`--with-cycles` パラメーターが必要であることに注意してください。cycles コストの詳細については、上記の[cycles コストの](#cycles-cost)セクションを参照してください。

このコマンドの出力は以下のようになります：

    Installing code for canister xrc, with canister ID br5f7-7uaaa-aaaaa-qaaca-cai
    Installing code for canister xrc_demo, with canister ID be2us-64aaa-aaaaa-qaabq-cai
    Deployed canisters.
    URLs:
      Backend canister via Candid interface:
        xrc: http://127.0.0.1:8080/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=br5f7-7uaaa-aaaaa-qaaca-cai
        xrc_demo: http://127.0.0.1:8080/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=be2us-64aaa-aaaaa-qaabq-cai

ウェブブラウザで`xrc_demo` canister のリンクを開き、Candid インターフェイスを表示します。以下の Candid UI が表示されます：

![Candid UI XRC](../_attachments/Candid-xrc.png)

あるいは、ターミナルから`xrc_demo` canister に直接コマンドを送信することもできます：

    dfx canister call --network ic xrc_demo get_exchange_rate "ICP"

要求された資産と現在の為替レートに応じて、出力は以下のようになります。この例ではICPを使用しています。

    (4.1865 : float64)

## リソース

- [為替レートの wiki ページ。](https://wiki.internetcomputer.org/wiki/Exchange_rate_canister)
- [HTTPS アウトコール。](https://internetcomputer.org/https-outcalls/)
- [為替レートcanister ブログ記事.](https://medium.com/dfinity/exchange-rate-canister-a-smart-contract-with-oracle-capabilities-f30694753c89)
- [為替レートcanister フォーラムの投稿。](https://forum.dfinity.org/t/new-exchange-rate-mechanism/14543/65)
- [為替レートcanister リポジトリ。](https://github.com/dfinity/exchange-rate-canister)

<!---
# Exchange rate canister

## Overview

The exchange rate canister, referred to as the XRC, is a canister that runs on the **uzr34 system subnet**. The XRC uses [HTTPS outcalls](https://internetcomputer.org/https-outcalls/) to fetch data from major cryptocurrency exchanges by using the exchange's public API to retrieve real time or historical cryptocurrency pricing information. The XRC also queries the public APIs for foreign exchange data providers around the world periodically in order to get forex rates. 

The XRC canister can be used by dapps such as decentralized exchanges (DEXs) to provide functionality such as comparing exchange rates against market rates to determine the value of assets that are held in a canister smart contract. For example, the cycle minting canister of the NNS uses the XRC to obtain the current ICP/XDR exchange rates, which it requires for the conversion of ICP to cycles.

The XRC provides this data in response to requests made by other canisters. A request made by another canister is composed of a base asset, a quote asset, and an optional UNIX epoch timestamp. 

An **asset** is a record consisting of a symbol (such as "ICP") and a class (either 'Cryptocurrency' or 'FiatCurrency'). The base and quote asset can be any combination of cryptocurrency and fiat currency assets, such as ICP/USD, USD/EUR, BTC/ICP, etc. The timestamp parameter enables the functionality for historic rates to be requested. If no timestamp is provided, the exchange rate for the current time is returned by default. 

## Usage
The canister ID of the XRC is `uf6dk-hyaaa-aaaaq-qaaaq-cai`. 

The exchange rate canister offers a single endpoint:

```
"get_exchange_rate": (GetExchangeRateRequest) -> (GetExchangeRateResult)
```

To make a request to the XRC, the following request form can be used: 
```
type GetExchangeRateRequest = record {
   base_asset: Asset;
   quote_asset: Asset;
   timestamp: opt nat64;
}; 
```

Once this form has been sent to the XRC, the following response is returned:

```
type GetExchangeRateResult = variant {
   Ok: ExchangeRate;
   Err: ExchangeRateError;
};
```

## Cycles cost 
Each request to the XRC costs 10B cycles to submit the request, otherwise an `ExchangeRateError::NotEnoughCycles` error will be returned. The actual cycles cost of the request call will depend on two factors: the requested asset types and the state of the internal exchange rate cache. These parameters are as follows:

- If the request can be served from the cache, the actual cost is 20M cycles.
- If both assets are fiat currencies, the cost is 20M cycles.
- If one of the assets is a fiat currency or the cryptocurrency USDT, the cost is 260M cycles.
- If both assets are cryptocurrencies, the cost is 500M cycles.

The remaining cycles are returned to the requesting canister. Note that at least 1M cycles are charged even in case of an error in order to mitigate the risk of a denial-of-service attack.

:::info
Note: The cycles cost of these requests are expected to be decreased in the next upgrade of the XRC. 
:::

## Calling the XRC directly 

T call call the XRC directly, first export your wallet canister ID as an environment variable with the command:

```
export WALLET=[wallet-canister-id]
```

If you need to get your wallet canister ID, you can run the `dfx wallet addresses` command to return your wallet's canister ID. 

Then, you can send a request, such as one to get the BTC/USD rate, with the following command:

```
dfx canister call --wallet $WALLET --with-cycles 10000000000 xrc get_exchange_rate '(record { base_asset = record { symbol = "BTC"; class = variant { Cryptocurrency } }; quote_asset = record { symbol = "USD"; class = variant { FiatCurrency } } })'
```

## Exchange rate canister demo

Alternatively, you can run a local XRC demo application to test the functionality locally. 

:::caution
It is worth noting that this sample dapp is very simple. If there is an error, it always returns `0` because the return value is simply a float.
:::

### Prerequisites

-   [x] Download and install the IC SDK package as described in the [download and install](/developer-docs/setup/install/index.mdx) page.

-   [x] Download and install [Nodejs and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).

-   [x] Download and install [git](https://git-scm.com/downloads).

Then, clone the sample repository, install the dependencies, and build the canisters with the following commands:

```
git clone git@github.com:THLO/xrc_demo.git
cd xrc_demo/
npm install
dfx canister create xrc_demo
```

### Deployment

To test the XRC demo locally, first start the local execution environment with the command:

```
dfx start --background
```

Then, deploy the canisters with the command:

```
dfx deploy --with-cycles 10000000000
```

Note that the `--with-cycles` parameter is required since cycles must be sent with every request to the exchange rate canister. For more information on the cycles cost, review the [cycles cost](#cycles-cost) section above. 

The output of this command will resemble the following:

```
Installing code for canister xrc, with canister ID br5f7-7uaaa-aaaaa-qaaca-cai
Installing code for canister xrc_demo, with canister ID be2us-64aaa-aaaaa-qaabq-cai
Deployed canisters.
URLs:
  Backend canister via Candid interface:
    xrc: http://127.0.0.1:8080/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=br5f7-7uaaa-aaaaa-qaaca-cai
    xrc_demo: http://127.0.0.1:8080/?canisterId=bw4dl-smaaa-aaaaa-qaacq-cai&id=be2us-64aaa-aaaaa-qaabq-cai
```

Open the link for the `xrc_demo` canister in a web browser to view the Candid interface. You will see the following Candid UI:

![Candid UI XRC](../_attachments/Candid-xrc.png)

Alternatively, you can send commands to the `xrc_demo` canister directly from the terminal, such as:

```
dfx canister call --network ic xrc_demo get_exchange_rate "ICP"
```

The output will resemble the following, depending asset requested and its current exchange rate. This example uses ICP.

```
(4.1865 : float64)
```

## Resources

- [Exchange rate wiki page.](https://wiki.internetcomputer.org/wiki/Exchange_rate_canister)
- [HTTPS outcalls.](https://internetcomputer.org/https-outcalls/)
- [Exchange rate canister blog post.](https://medium.com/dfinity/exchange-rate-canister-a-smart-contract-with-oracle-capabilities-f30694753c89)
- [Exchange rate canister forum post.](https://forum.dfinity.org/t/new-exchange-rate-mechanism/14543/65)
- [Exchange rate canister repository.](https://github.com/dfinity/exchange-rate-canister)

-->
