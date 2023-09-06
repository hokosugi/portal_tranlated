# NFTウォレット

## 概要

dapp これはRust dip721-nft-containerから発行されたNFTを利用するNFTウォレットの例です。このウォレットはNFTを登録し、NFTを送金し、NFTの数を確認することができます。このdapp には、インタラクション用のフロントエンドUIが含まれています。

## 前提条件

この例では以下のインストールが必要です：

- \[x\][IC SDKを](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx)インストールします。
- \[x\] GitHubから以下のプロジェクトファイルをダウンロードしてください。https://github.com/dfinity/examples/

ターミナル・ウィンドウを開いて開始します。

### ステップ 1:`start.sh` スクリプトを使用してdapp をデプロイします：

``` bash
cd examples/rust/nft-wallet
./start.sh
```

こ の ス ク リ プ ト は、 依存関係や ロ ー カルのイ ン タ ーネ ッ ト ID を イ ン ス ト ール し 、 ロ ー カ ルで NFT ウォレットをデプロイします。

または、dapp をコマンドを使用して手動でデプロイすることもできます：

``` bash
dfx start --background
cd internet-identity
npm install
II_FETCH_ROOT_KEY=1 dfx deploy
cd ..
./deploy.sh
```

### ステップ 2: IC ネットワーク上にデプロイする場合は、コマンドを実行します：

``` bash
./deploy.sh --network ic
```

### ステップ3：NFTウォレットcanister に対して電話をかけます：

例えば、NFTを送金するには次のコマンドを使用します：

``` bash
dfx canister call nftwallet transfer '(record {canister = principal "<NFT canister id>"; index = 1:nat64}, principal "<recipient canister id>", opt true)'
```

<!---
# NFT wallet

## Overview

This is an NFT wallet example dapp that utilizes minted NFTs from the Rust dip721-nft-container. Among some of its essential features, the wallet can register NFTs, transfer out NFTs and check how many NFTs it contains. This dapp includes a frontend UI for interaction. 

## Prerequisites
This example requires an installation of:

- [x] Install the [IC SDK](https://internetcomputer.org/docs/current/developer-docs/setup/install/index.mdx).
- [x] Download the following project files from GitHub: https://github.com/dfinity/examples/

Begin by opening a terminal window.

### Step 1: You can deploy the dapp using the `start.sh` script:

```bash
cd examples/rust/nft-wallet
./start.sh
```

This script installs dependencies as well as a local Internet Identity, then deploys the NFT wallet locally.

Alternatively, the dapp can be deployed manually with the commands:

```bash
dfx start --background
cd internet-identity
npm install
II_FETCH_ROOT_KEY=1 dfx deploy
cd ..
./deploy.sh
```

### Step 2: If you'd like to deploy on the IC network run the command:

```bash
./deploy.sh --network ic
```

### Step 3: Make calls against NFT wallet canister:

For example, to to transfer an NFT use the command:

```bash
dfx canister call nftwallet transfer '(record {canister = principal "<NFT canister id>"; index = 1:nat64}, principal "<recipient canister id>", opt true)'
```

-->
