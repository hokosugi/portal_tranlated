# 大型ウェブアセンブリモジュール

## 概要

現在、ICにインストールできるプログラムのサイズは2MBに制限されています。
WebAssembly 、アップロード前にgzipファイル圧縮を使用することで、2MBより（わずかに）大きいモジュールをICにインストールすることができます。その後、ICはファイルを解凍し、含まれているWebAssembly モジュールをインストールします。

## gzip で圧縮されたWebAssembly モジュールのインストール

WebAssembly モジュールは`gzip` で圧縮され、`dfx install` でアップロードされます。既存のcanister にモジュールをアップロードする場合は、`--mode reinstall` または`--mode upgrade` を追加する必要があります。

``` bash
gzip my-canister.wasm
dfx canister install my-canister --wasm my-canister.wasm.gz
```

圧縮は現在`dfx deploy` ではサポートされていません。

<!---
# Large web assembly modules

## Overview

The size of programs that can be installed on the IC is currently limited to 2MB.
WebAssembly modules that are (slightly) larger than 2MB can still be installed on the IC by using gzip file compression before uploading; the IC will then decompress the file and install the contained WebAssembly module.

## Installing a gzip-compressed WebAssembly module

The WebAssembly module is compressed using `gzip` and then uploaded by `dfx install`, you may need to add `--mode reinstall` or `--mode upgrade` when uploading the module to an existing canister.

``` bash
gzip my-canister.wasm
dfx canister install my-canister --wasm my-canister.wasm.gz
```

Compression is currently not supported by `dfx deploy`.

-->
