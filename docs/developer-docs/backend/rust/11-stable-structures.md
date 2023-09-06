# 11: 安定した構造

## 概要

安定した構造体は、バッキングストアとして安定したメモリを使用するように設計されており、`pre_upgrade`/`post_upgrade` フックを必要とせずにギガバイトサイズまで大きくすることができます。

[安定した構造体ライブラリが](https://github.com/dfinity/stable-structures#readme)存在し、安定したメモリでのデータ構造の直接管理を簡素化することを目的としています。

安定構造体の詳細については、[安定構造体ライブラリと](https://github.com/dfinity/stable-structures) [Roman のチュートリアルを](https://mmapped.blog/posts/14-stable-structures.html)参照してください。

## 次のステップ

次のステップでは、[レコードの保存と検索について](12-searching-records.md)説明します。

<!---
# 11: Stable structures

## Overview

Stable structures are designed to use stable memory as the backing store, allowing them to grow to gigabytes in size without the need for `pre_upgrade`/`post_upgrade` hooks.

A [stable structure library](https://github.com/dfinity/stable-structures#readme) exists which aims to simplify managing data structures directly in stable memory and provides example code templates.

For more information about stable structures, please see the [stable structures library](https://github.com/dfinity/stable-structures) and [Roman's tutorial on stable structures](https://mmapped.blog/posts/14-stable-structures.html).

## Next steps

For the next step, let's dive into [storing and searching records](12-searching-records.md).

-->
