# ホットキー生成

## 概要

このページでは、neuron 管理用のホットキーを生成する方法について説明します。
ホットキーを取得する推奨方法は、公開されている`ic` リポジトリで使用されているのと同じプロセスを使用して、プログラムによって
ホットキーを生成することです。
ただし、以下に説明するように手動でホットキーを生成することも可能です。
このページでは、期待されるデータ形式を示すいくつかの例とともに、ステップバイステップのガイドを示します。

## 手動プロセス

手動でのホットキー生成プロセスは、PEMファイルを生成し、対応する秘密鍵と公開鍵を生成します。

### PEMの生成

鍵を生成するには、まずプライベートPEMファイルを生成します：

`openssl genpkey -algorithm ed25519 -outform PEM -out private.pem`

出力例

    -----BEGIN PRIVATE KEY-----
    MC4CAQAwBQYDK2VwBCIEIHAOt4HGrNdcIFhBy7N9p6iq3iRowd4NZjDZ8aaaDCcX
    -----END PRIVATE KEY-----

次に、そこから公開 PEM を作成します：

`openssl pkey -in private.pem -pubout > public.pem`

出力例：

    -----BEGIN PUBLIC KEY-----
    MCowBQYDK2VwAyEA4JKtE2KNVUTo96cl202FgWv5ctwP7f1ds1O73PZ6+VE=
    -----END PUBLIC KEY-----

## 16進数キー表現の生成

プライベートDERファイルを作成します：

`openssl pkey -inform pem -outform der -in private.pem -out private.der`

公開DERファイルを作成：

`openssl pkey -inform pem -outform der -in private.pem -out public.der -pubout`

### 16進数表現

しかし、両方の鍵の16進数表現を得ることができます：

### 秘密鍵

`xxd -p private.der`

出力例：

    302e020100300506032b657004220420700eb781c6acd75c205841cbb37d
    a7a8aade2468c1de0d6630d9f1a69a0c2717

### 公開鍵

`xxd -p public.der`

    302a300506032b6570032100e092ad13628d5544e8f7a725db4d85816bf9
    72dc0fedfd5db353bbdcf67af951

公開鍵の最後の32バイトだけを保持する必要があります：

`xxd -s 12 -c 32 -p public.der`

出力例：

    e092ad13628d5544e8f7a725db4d85816bf972dc0fedfd5db353bbdcf67af951

これが、Rosettaの操作でホットキーを識別するために使用できる公開鍵です！

-----

## よくある質問

- #### PEMファイルを生成する際に、"Algorithm ed25519 not found "と表示されるのはなぜですか？

MacOSに含まれているOpenSSLのバージョンは、デフォルトではed25519をサポートしていません。
別のバージョンのOpenSSLをインストールするか（例えば*brewで*）、Linuxマシンからコマンドを実行する必要があるかもしれません。

<!---
# Hotkeys generation

## Overview
This page will explain how to generate a hotkey for neuron management.
The recommended way to get hotkeys is to programmatically 
generate them using the same process used in the public `ic` repository.
However, it is possible to generate hotkeys manually as described
below.
This page is a step-by-step guide with some examples showing the data format to expect.

## Manual process

The manual hotkey generation process consists in generating a PEM file, then deriving the corresponding private and public keys.

### Generate PEM

To generate the keys, start with generating a private PEM file:

`openssl genpkey -algorithm ed25519 -outform PEM -out private.pem`

Example output:
```
-----BEGIN PRIVATE KEY-----
MC4CAQAwBQYDK2VwBCIEIHAOt4HGrNdcIFhBy7N9p6iq3iRowd4NZjDZ8aaaDCcX
-----END PRIVATE KEY-----
```

Then create the public PEM from it:

`openssl pkey -in private.pem -pubout > public.pem`

Example output:
```
-----BEGIN PUBLIC KEY-----
MCowBQYDK2VwAyEA4JKtE2KNVUTo96cl202FgWv5ctwP7f1ds1O73PZ6+VE=
-----END PUBLIC KEY-----
```


## Generating hex key representation

Create the private DER file:

`openssl pkey -inform pem -outform der -in private.pem -out private.der`

Create the public DER file:

`openssl pkey -inform pem -outform der -in private.pem -out public.der -pubout`


### Hex representation

The generated DER files are in a binary format not intended to be readable by humans.
But we can get a hex representation of both keys:


### Private key

`xxd -p private.der`

Example output:

```
302e020100300506032b657004220420700eb781c6acd75c205841cbb37d
a7a8aade2468c1de0d6630d9f1a69a0c2717
```

### Public key

`xxd -p public.der`

```
302a300506032b6570032100e092ad13628d5544e8f7a725db4d85816bf9
72dc0fedfd5db353bbdcf67af951
```

We need to keep only the last 32 bytes of the public key:

`xxd -s 12 -c 32 -p public.der`

Example output:

```
e092ad13628d5544e8f7a725db4d85816bf972dc0fedfd5db353bbdcf67af951
```

This is the public key you can use in Rosetta operations to identify your hotkey!


---------

## Frequently asked questions

- #### Why do I get "Algorithm ed25519 not found" while generating the PEM file?

The version of OpenSSL included in MacOS doesn't support ed25519 by default. 
You may have to install another version of OpenSSL (for example through *brew*), or run the command from a Linux machine.



-->
