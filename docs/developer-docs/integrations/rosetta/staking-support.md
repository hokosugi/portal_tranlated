# 杭打ちとneuron 管理

## 概要

このドキュメントでは、Rosetta API の拡張として、Internet Computer 上での資金のステーキングとガバナンス "neuronの管理を可能にするものを規定します。

:::info
トランザクション内の操作は順番に適用されるため、操作の順番は重要です。このAPIによって提供される偶発的な操作を含むトランザクションは、24時間のウィンドウ内で再試行することができます。

ガバナンスの制限により、canister 、neuron 管理操作はチェーンに反映されません。`/construction/submit` エンドポイントから返された識別子でトランザクションを検索した場合、これらのトランザクションが存在しないか、neuron 管理操作を見逃している可能性があります。その代わりに、`/construction/submit` は、`/block/transaction` が返すのと同じ形式を使用して、`metadata` フィールドのすべての操作のステータスを返します。
::：

## neuron アドレスの導出

|  |  |
| --- | --- |
| バージョン | 1.3.0 |

メタデータ フィールド`account_type` を`"neuron"` に設定して`/construction/derive` エンドポイントを呼び出し、公開鍵で管理されているneuron に対応する元帳アドレスを計算します。

### リクエスト

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101"
  },
  "public_key": {
    "hex_bytes": "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
    "curve_type": "edwards25519"
  },
  "metadata": {
    "account_type": "neuron",
    "neuron_index": 0
  }
}
```

:::info

バージョン 1.3.0 以降、同じ鍵を使用して多数のneuronを制御できます。`neuron_index` メタデータ フィールドに異なる値を指定することで、neuronを区別できます。ロゼッタ・ノードはneuron のすべての管理操作で`neuron_index` をサポートします。`neuron_index` は`0` と`264 - 1` (`18446744073709551615`) の間の任意の整数です。指定しない場合はゼロに等しくなります。JavaScript を使用して Rosetta ノードへのリクエストを作成する場合は、  を表すために [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)`neuron_index` `Number` 型は ( ) 以下の値のみを正確に表すことができます。`253 - 1``9007199254740991`

:::

### レスポンス

``` json
{
  "account_identifier": {
    "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a"
  }
}
```

## 賭け金

|  |  |
| --- | --- |
| バージョン | 1.0.5 |
| べき？ | はい |

資金をステークするには、neuron アドレスへの転送を実行し、`STAKE` オペレーションを実行します。

`STAKE` 操作で設定する必要がある唯一のフィールドは`account` で、これはneuron コントローラの元帳口座と等しくなければなりません。`STAKE` オペレーションの`metadata` フィールドで`neuron_index` フィールドを指定できます。`neuron_index` を指定する場合、その値はneuron 口座識別子の導出に使用したものと同じでなければなりません。

### リクエスト

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101",
  },
  "operations": [
    {
      "operation_identifier": { "index": 0 },
      "type": "TRANSACTION",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "amount": {
        "value": "-100000000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 1 },
      "type": "TRANSACTION",
      "account": { "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a" },
      "amount": {
        "value": "100000000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 2 },
      "type": "FEE",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "amount": {
        "value": "-10000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 3 },
      "type": "STAKE",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "metadata": {
        "neuron_index": 0
      }
    }
  ]
}
```

### レスポンス

``` json
{
  "transaction_identifier": {
    "hash": "2f23fd8cca835af21f3ac375bac601f97ead75f2e79143bdf71fe2c4be043e8f"
  },
  "metadata": {
    "operations": [
      {
        "operation_identifier": { "index": 0 },
        "type": "TRANSACTION",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "amount": {
          "value": "-100000000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 1 },
        "type": "TRANSACTION",
        "status": "COMPLETED",
        "account": { "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a" },
        "amount": {
          "value": "100000000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 2 },
        "type": "FEE",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "amount": {
          "value": "-10000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 3 },
        "type": "STAKE",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "metadata": {
          "neuron_index": 0
        }
      }
    ]
  }
}
```

## neuronの管理

### ディゾルブ・タイムスタンプの設定

|  |  |
| --- | --- |
| バージョン | 1.1.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

この操作は、neuron が`DISSOLVED` ステートに到達できる時間を更新します。

**前提条件**

<div class="formalpara-title">

- `account.address` は コントローラーの元帳アドレスです。neuron 

</div>

ディゾルブタイムスタンプは常に単調増加します。

- neuron が`DISSOLVING` ステートにある場合、この操作によってディゾルブ タイムスタンプをさらに未来に移動できます。

- neuron が`NOT_DISSOLVING` ステートにある場合、時間Tで`SET_DISSOLVE_TIMESTAMP` を呼び出すと、neuronのディゾルブ遅延(neuron のディゾルブにかかる最小時間)を`T - current_time` に増加させようとします。

- neuron が`DISSOLVED` ステートにある場合、`SET_DISSOLVE_TIMESTAMP` を呼び出すと`NOT_DISSOLVING` ステートに移動し、それに応じてディゾルブ遅延が設定されます。

<div class="formalpara-title">

**例**

</div>

``` json
{
  "operation_identifier": { "index": 4 },
  "type": "SET_DISSOLVE_TIMESTAMP",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0,
    "dissolve_time_utc_seconds": 1879939507
  }
}
```

### ディゾルブ開始

|  |  |
| --- | --- |
| バージョン | 1.1.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

`START_DISSOLVNG` 操作は、neuron のステートを`DISSOLVING` に変更します。

<div class="formalpara-title">

**前提条件**

</div>

- `account.address` は コントローラの元帳アドレスです。neuron 

<div class="formalpara-title">

**事後条件：**

</div>

- neuron が`DISSOLVING` ステート。

<div class="formalpara-title">

**例**

</div>

``` json
{
  "operation_identifier": { "index": 5 },
  "type": "START_DISSOLVING",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0
  }
}
```

### 溶解停止

|  |  |
| --- | --- |
| バージョン | 1.1.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

`STOP_DISSOLVNG` 操作は、neuron のステートを`NOT_DISSOLVING` に変更します。

<div class="formalpara-title">

**前提条件**

</div>

- `account.address` が コントローラの元帳アドレスであること。neuron 

<div class="formalpara-title">

**事後条件：**

</div>

- neuron が`NOT_DISSOLVING` ステート。

<div class="formalpara-title">

**例**

</div>

``` json
{
  "operation_identifier": { "index": 6 },
  "type": "STOP_DISSOLVING",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0
  }
}
```

### ホットキーの追加

|  |  |
| --- | --- |
| バージョン | 1.2.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

`ADD_HOTKEY` neuronGovernancecanister では、コントローラのキーではなく、ホットキーで署名できる非クリティカルな操作があります(投票や満期の問い合わせなど)。

<div class="formalpara-title">

**前提条件**

</div>

- `account.address` は、 コントローラの元帳アドレス。neuron 

- neuron のホットキーが10個未満であること。

このコマンドには2つの形式があります。1つは[ICプリンシパルを](/references/ic-interface-spec.md#principal)ホットキーとして受け付ける形式、もう1つは[公開鍵を](https://www.rosetta-api.org/docs/models/PublicKey.html)受け付ける形式です。

#### ホットキーとしてプリンシパルを追加します。

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "ADD_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
  }
}
```

#### ホットキーとして公開鍵を追加

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "ADD_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "public_key": {
      "hex_bytes":  "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
      "curve_type": "edwards25519"
    }
  }
}
```

### ホットキーの削除

|  |  |
| --- | --- |
| バージョン | 1.2.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

`REMOVE_HOTKEY` 操作は、以前に追加したホットキーをneuron から削除します。

<div class="formalpara-title">

**前提条件**

</div>

- `account.address` は コントローラの元帳アドレスです。neuron 

- ホットキーはneuron にリンクされています。

このコマンドには2つの形式があります。1つは[ICプリンシパルを](/references/ic-interface-spec.md#principal)ホットキーとして受け付ける形式、もう1つは[公開鍵を](https://www.rosetta-api.org/docs/models/PublicKey.html)受け付ける形式です。

#### ホットキーとしてのプリンシパルの削除

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "REMOVE_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
  }
}
```

#### ホットキーとして公開鍵を削除

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "REMOVE_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "public_key": {
      "hex_bytes":  "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
      "curve_type": "edwards25519"
    }
  }
}
```

### neuronを起動します。

|  |  |
| --- | --- |
| バージョン | 1.3.0 |
| べき？ | はい |
| 最小アクセスレベル | コントローラ |

`SPAWN` オペレーションは、十分な成熟度を持つ既存のneuron から新しいneuron を作成します。このオペレーションは、既存のneuron のすべての成熟度を、新しく生成されたneuron のステーク量に移します。

<div class="formalpara-title">

**前提条件：**

</div>

- `account.address` が コントローラの元帳アドレスであること。neuron 

- 親neuron が少なくとも 1 ICP 分の満期を持っていること。

<div class="formalpara-title">

**事後条件：**

</div>

- 親neuron の満期が`0` に設定されていること。

- 転送された満期と同じ残高を持つ新しいneuron が生成されます。

<!-- end list -->

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "SPAWN",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "controller": {
      "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
    },
    "spawned_neuron_index": 1,
    "percentage_to_spawn": 75
  }
}
```

:::info

- `spawned_neuron_index` メタデータフィールドは必須です。ロゼッタ・ノードはこのインデックスを使用して、生成された のサブアカウントを計算します。すべてのスポーンされた は、 の値が異なっている必要があります。neuron neuron `spawned_neuron_index`

- `controller` metadata フィールドはオプションで、デフォルトでは既存の コントローラと等しくなります。neuron 

- `percentage_to_spawn` metadata フィールドは省略可能で、デフォルトは 100 です。指定する場合、値は 1 から 100 (境界を含む) までの整数でなければなりません。

:::

### neuron をマージします。

|  |  |
| --- | --- |
| バージョン | 1.4.0 |
| べき乗か？ | いいえ |
| 最小アクセスレベル | コントローラ |

`MERGE_MATURITY` オペレーションは、neuron の既存の満期をそのステークにマージします。マージするマチュリティのパーセンテージを指定することができ、指定しない場合はマチュリティ全体がマージされます。

<div class="formalpara-title">

**前提条件**

</div>

- `account.address` は、 コントローラの元帳アドレス。neuron 

- neuron は、マージする満期がゼロでないこと。

<div class="formalpara-title">

**事後条件：**

</div>

- 満期がマージされた分だけ減少。

- Neuron ステークがマージされた分だけ増加。

<div class="formalpara-title">

**例**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "MERGE_MATURITY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "percentage_to_merge": 14
  }
}
```

:::info

`percentage_to_merge` メタデータ・フィールドはオプションで、デフォルトでは100です。指定された場合、値は 1 ～ 100 の整数でなければなりません（境界を含む）。

:::

### neuron。

|  |  |
| --- | --- |
| バージョン | 1.5.0 |
| べき乗か？ | はい |
| 最小アクセスレベル | ホットキー |

`FOLLOW` オペレーションは、neuron のフォロールールを設定します。
Governancecanister スマートコントラクトは、投票中のフォロワーの投票から、次のneuronの投票を自動的に推測します。

`followees` メタデータフィールドには、フォローするneurons のリストが含まれます。
リストに複数のneuron が含まれている場合、neuron はフォローされたneurons の投票の過半数に従って投票します（引き分けの場合は棄権）。
リストが空の場合、このトピックのルールは破棄されます（つまり、neuron はこのタイプの提案について他のneuron をフォローしません）。

`topic` メタデータフィールドを指定することで、ルールを特定のトピックに制限できます。
トピックは、0 ～ 10 (含む) の整数です。
 `topic` フィールドのデフォルト値は 0 です。
各トピックには、最大 1 つのルールを関連付けることができます。
トピックコードを以下に示します。

0.  未定義 (すべてのトピック)。
1.  Neuron 管理。
2.  為替レート。
3.  ネットワーク経済学。
4.  ガバナンス。
5.  ノード管理。
6.  参加者管理
7.  サブネット管理
8.  ネットワークcanister の管理。
9.  KYC。
10. ノードプロバイダー報酬

<div class="formalpara-title">

**前提条件：**

</div>

- `account.address` は、 コントローラまたはホットキーの元帳アドレスです。neuron 
- `metadata.followees` 有効な 識別子の配列。neuron 
- `metadata.topic` 有効なトピック識別子。

<div class="formalpara-title">

**事後条件：**

</div>

- Neuron 指定されたフォロールールに従って投票。

<div class="formalpara-title">

**コントローラとして`FOLLOW` を呼び出す場合：**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "FOLLOW",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "topic": 0,
    "followees": [4169717477823915596, 7814871076665269296],
    "neuron_index": 0
  }
}
```

<div class="formalpara-title">

** `FOLLOW` をホットキーで呼び出します：**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "FOLLOW",
  "account": { "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8" },
  "metadata": {
    "topic": 0,
    "followees": [4169717477823915596, 7814871076665269296],
    "neuron_index": 0,
    "controller": {
      "public_key": {
        "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
        "curve_type": "edwards25519"
      }
    }
  }
}
```

:::info

`followees` メタデータフィールドには、呼び出し元が選択したneuron インデックスのリストではなく、Governancecanister スマートコントラクトによって割り当てられた一意のneuron 識別子のリストが含まれます。
 `STAKE` および`NEURON_INFO` 操作の`neuron_id` メタデータフィールドから、自分のneuronの一意のneuron 識別子を取得できます。

:::

## neuron 属性へのアクセス

### 公開情報へのアクセス

|  |  |
| --- | --- |
| バージョン | 1.3.0 |
| 最小アクセス・レベル | 公開 |

`/account/balance` エンドポイントを呼び出して、賭け金額と公開されているneuron メタデータにアクセスします。

<div class="formalpara-title">

**前提条件**

</div>

- `public_key` neuronのコントローラの公開鍵が含まれていること。

:::info

- この操作はオンラインモードでのみ使用できます。

- エンドポイントは常にneuron の最新のステートを返すので、リクエストはブロック識別子を指定すべきではありません。

:::

#### リクエスト

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101"
  },
  "account_identifier": {
    "address": "a4ac33c6a25a102756e3aac64fe9d3267dbef25392d031cfb3d2185dba93b4c4"
  },
  "metadata": {
    "account_type": "neuron",
    "neuron_index": 0,
    "public_key": {
      "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
      "curve_type": "edwards25519"
    }
  }
}
```

#### レスポンス

``` json
{
  "block_identifier": {
    "index": 1150,
    "hash": "ca02e34bafa2f58b18a66073deb5f389271ee74bd59a024f9f7b176a890039b2"
  },
  "balances": [
    {
      "value": "100000000",
      "currency": {
        "symbol": "ICP",
        "decimals": 8
      }
    }
  ],
  "metadata": {
    "verified_query": false,
    "retrieved_at_timestamp_seconds": 1639670156,
    "state": "DISSOLVING",
    "age_seconds": 0,
    "dissolve_delay_seconds": 240269355,
    "voting_power": 195170955,
    "created_timestamp_seconds": 1638802541
  }
}
```

### 保護された情報へのアクセス

|  |  |
| --- | --- |
| バージョン | 1.5.0 |
| べき？ | はい |
| 最小アクセスレベル | ホットキー |

`NEURON_INFO` オペレーションは、ガバナンスcanister からneuron のステートを取得します。この操作は、neuron のステートを変更しません。neuron コントローラまたはホットキーのいずれかが、この操作を実行できます。

<div class="formalpara-title">

**コントローラとして`NEURON_INFO` を呼び出します：**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "NEURON_INFO",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0
  }
}
```

- `account.address` は、 コントローラの元帳アドレスです。neuron 

<div class="formalpara-title">

**ホットキーで`NEURON_INFO` を呼び出します：**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "NEURON_INFO",
  "account": { "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8" },
  "metadata": {
    "neuron_index": 0,
    "controller": {
      "public_key": {
        "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
        "curve_type": "edwards25519"
      }
    }
  }
}
```

- `account.address` は、 ホットキーの元帳アドレスです。neuron 
- `metadata.controller.public_key` は、 コントローラの公開鍵です。neuron 

:::info

Rosetta API は、コントローラの公開鍵とneuron インデックスによってneuronを識別するため、ホットキーを使用して操作を実行する場合、呼び出し元は公開鍵を指定する必要があります。

:::

Rosetta API は、neuron のステートを操作メタデータとして`/construction/submit` エンドポイントに返します。

<div class="formalpara-title">

**construction/submitエンドポイントからの応答例：**

</div>

``` json
{
  "transaction_identifier": {
    "hash": "0000000000000000000000000000000000000000000000000000000000000000"
  },
  "metadata": {
    "operations": [
      {
        "operation_identifier": { "index": 0 },
        "type": "NEURON_INFO",
        "status": "COMPLETED",
        "account": {
          "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8"
        },
        "metadata": {
          "controller": {
            "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
          },
          "followees": [
            "0": [111, 222],
            "8": [555, 666]
          ],
          "hotkeys": [
            "6xpcx-hldf5-4ddrg-onbug-4e2kw-rc25g-rxknf-p2wij-hxhqj-azcii-oqe"
          ],
          "kyc_verified": true,
          "maturity_e8s_equivalent": 1000,
          "neuron_fees_e8s": 0,
          "neuron_id": 18089972080608815000,
          "neuron_index": 0,
          "state": "DISSOLVING"
        }
      }
    ]
  }
}
```

<!---
# Staking and neuron management

## Overview

This document specifies extensions of the Rosetta API enabling staking funds and managing governance "neurons" on the Internet Computer.

:::info
Operations within a transaction are applied in order, so the order of operations is significant. Transactions that contain idempotent operations provided by this API can be re-tried within the 24-hour window.

Due to limitations of the governance canister, neuron management operations are not reflected on the chain. If you lookup transactions by identifier returned from the `/construction/submit` endpoint, these transactions might not exist or miss neuron management operations. Instead, `/construction/submit` returns the statuses of all the operations in the `metadata` field using the same format as `/block/transaction` would return.
:::

## Deriving neuron address

|               |       |
|---------------|-------|
| Since version | 1.3.0 |

Call the `/construction/derive` endpoint with metadata field `account_type` set to `"neuron"` to compute the ledger address corresponding to the neuron controlled by the public key.

### Request

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101"
  },
  "public_key": {
    "hex_bytes": "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
    "curve_type": "edwards25519"
  },
  "metadata": {
    "account_type": "neuron",
    "neuron_index": 0
  }
}
```

:::info

Since version 1.3.0, you can control many neurons using the same key. You can differentiate between neurons by specifying different values of the `neuron_index` metadata field. The rosetta node supports `neuron_index` in all neuron management operations. `neuron_index` is an arbitrary integer between `0` and `264 - 1` (`18446744073709551615`). It is equal to zero if not specified. If you use JavaScript to construct requests to the Rosetta node, consider using the [`BigInt`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) type to represent the `neuron_index`. The `Number` type can precisely represent only values below `253 - 1` (`9007199254740991`).

:::

### Response

``` json
{
  "account_identifier": {
    "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a"
  }
}
```

## Stake funds

|               |       |
|---------------|-------|
| Since version | 1.0.5 |
| Idempotent?   | yes   |

To stake funds, execute a transfer to the neuron address followed by a `STAKE` operation.

The only field that you must set for the `STAKE` operation is `account`, which should be equal to the ledger account of the neuron controller. You can specify `neuron_index` field in the `metadata` field of the `STAKE` operation. If you do specify the `neuron_index`, its value must be the same as you used to derive the neuron account identifier.

### Request

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101",
  },
  "operations": [
    {
      "operation_identifier": { "index": 0 },
      "type": "TRANSACTION",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "amount": {
        "value": "-100000000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 1 },
      "type": "TRANSACTION",
      "account": { "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a" },
      "amount": {
        "value": "100000000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 2 },
      "type": "FEE",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "amount": {
        "value": "-10000",
        "currency": { "symbol": "ICP", "decimals": 8 }
      }
    },
    {
      "operation_identifier": { "index": 3 },
      "type": "STAKE",
      "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
      "metadata": {
        "neuron_index": 0
      }
    }
  ]
}
```

### Response

``` json
{
  "transaction_identifier": {
    "hash": "2f23fd8cca835af21f3ac375bac601f97ead75f2e79143bdf71fe2c4be043e8f"
  },
  "metadata": {
    "operations": [
      {
        "operation_identifier": { "index": 0 },
        "type": "TRANSACTION",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "amount": {
          "value": "-100000000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 1 },
        "type": "TRANSACTION",
        "status": "COMPLETED",
        "account": { "address": "531b163cd9d6c1d88f867bdf16f1ede020be7bcd928d746f92fbf7e797c5526a" },
        "amount": {
          "value": "100000000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 2 },
        "type": "FEE",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "amount": {
          "value": "-10000",
          "currency": { "symbol": "ICP", "decimals": 8 }
        }
      },
      {
        "operation_identifier": { "index": 3 },
        "type": "STAKE",
        "status": "COMPLETED",
        "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
        "metadata": {
          "neuron_index": 0
        }
      }
    ]
  }
}
```

## Managing neurons

### Setting dissolve timestamp

|                      |            |
|----------------------|------------|
| Since version        | 1.1.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

This operation updates the time when the neuron can reach the `DISSOLVED` state.

**Prerequisites:**

<div class="formalpara-title">

-   `account.address` is the ledger address of the neuron contoller.

</div>


The dissolve timestamp always increases monotonically.

-   If the neuron is in the `DISSOLVING` state, this operation can move the dissolve timestamp further into the future.

-   If the neuron is in the `NOT_DISSOLVING` state, invoking `SET_DISSOLVE_TIMESTAMP` with time T will attempt to increase the neuron’s dissolve delay (the minimal time it will take to dissolve the neuron) to `T - current_time`.

-   If the neuron is in the `DISSOLVED` state, invoking `SET_DISSOLVE_TIMESTAMP` will move it to the `NOT_DISSOLVING` state and will set the dissolve delay accordingly.

<div class="formalpara-title">


**Example**

</div>

``` json
{
  "operation_identifier": { "index": 4 },
  "type": "SET_DISSOLVE_TIMESTAMP",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0,
    "dissolve_time_utc_seconds": 1879939507
  }
}
```

### Start dissolving

|                      |            |
|----------------------|------------|
| Since version        | 1.1.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

The `START_DISSOLVNG` operation changes the state of the neuron to `DISSOLVING`.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is the ledger address of the neuron contoller.

<div class="formalpara-title">

**Postconditions:**

</div>

-   The neuron is in the `DISSOLVING` state.

<div class="formalpara-title">

**Example**

</div>

``` json
{
  "operation_identifier": { "index": 5 },
  "type": "START_DISSOLVING",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0
  }
}
```

### Stop dissolving

|                      |            |
|----------------------|------------|
| Since version        | 1.1.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

The `STOP_DISSOLVNG` operation changes the state of the neuron to `NOT_DISSOLVING`.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is a ledger address of a neuron contoller.

<div class="formalpara-title">

**Postconditions:**

</div>

-   The neuron is in `NOT_DISSOLVING` state.

<div class="formalpara-title">

**Example**

</div>

``` json
{
  "operation_identifier": { "index": 6 },
  "type": "STOP_DISSOLVING",
  "account": {
    "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d"
  },
  "metadata": {
    "neuron_index": 0
  }
}
```

### Adding hotkeys

|                      |            |
|----------------------|------------|
| Since version        | 1.2.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

The `ADD_HOTKEY` operation adds a hotkey to the neuron. The Governance canister allows some non-critical operations to be signed with a hotkey instead of the controller’s key (e.g., voting and querying maturity).

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is a ledger address of a neuron controller.

-   The neuron has less than 10 hotkeys.

The command has two forms: one form accepts an [IC principal](/references/ic-interface-spec.md#principal) as a hotkey, another form accepts a [public key](https://www.rosetta-api.org/docs/models/PublicKey.html).

#### Add a principal as a hotkey

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "ADD_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
  }
}
```

#### Add a public key as a hotkey

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "ADD_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "public_key": {
      "hex_bytes":  "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
      "curve_type": "edwards25519"
    }
  }
}
```

### Removing hotkeys

|                      |            |
|----------------------|------------|
| Since version        | 1.2.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

The `REMOVE_HOTKEY` operation remove a previously added hotkey from the neuron.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is a ledger address of a neuron controller.

-   The hotkey is linked to the neuron.

The command has two forms: one form accepts an [IC principal](/references/ic-interface-spec.md#principal) as a hotkey, another form accepts a [public key](https://www.rosetta-api.org/docs/models/PublicKey.html).

#### Remove a principal as a hotkey

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "REMOVE_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
  }
}
```

#### Remove a public key as a hotkey

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "REMOVE_HOTKEY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "public_key": {
      "hex_bytes":  "1b400d60aaf34eaf6dcbab9bba46001a23497886cf11066f7846933d30e5ad3f",
      "curve_type": "edwards25519"
    }
  }
}
```

### Spawn neurons

|                      |            |
|----------------------|------------|
| Since version        | 1.3.0      |
| Idempotent?          | yes        |
| Minimal access level | controller |

The `SPAWN` operation creates a new neuron from an existing neuron with enough maturity. This operation transfers all the maturity from the existing neuron to the staked amount of the newly spawned neuron.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is a ledger address of a neuron controller.

-   The parent neuron has at least 1 ICP worth of maturity.

<div class="formalpara-title">

**Postconditions:**

</div>

-   Parent neuron maturity is set to `0`.

-   A new neuron is spawned with a balance equal to the transferred maturity.

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "SPAWN",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "controller": {
      "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
    },
    "spawned_neuron_index": 1,
    "percentage_to_spawn": 75
  }
}
```

:::info

- `spawned_neuron_index` metadata field is required. The rosetta node uses this index to compute the subaccount for the spawned neuron. All spawned neurons must have different values of `spawned_neuron_index`.

- `controller` metadata field is optional and equal to the existing neuron controller by default.

- `percentage_to_spawn` metadata field is optional and equal to 100 by default. If specified, the value must be an integer between 1 and 100 (bounds included).

:::

### Merge neuron maturity

|                      |            |
|----------------------|------------|
| Since version        | 1.4.0      |
| Idempotent?          | no         |
| Minimal access level | controller |

The `MERGE_MATURITY` operation merges the existing maturity of the neuron into its stake. The percentage of maturity to merge can be specified, otherwise the entire maturity is merged.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `account.address` is the ledger address of the neuron controller.

-   The neuron has non-zero maturity to merge.

<div class="formalpara-title">

**Postconditions:**

</div>

-   Maturity decreased by the amount merged.

-   Neuron stake increased by the amount merged.

<div class="formalpara-title">

**Example**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "MERGE_MATURITY",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0,
    "percentage_to_merge": 14
  }
}
```

:::info

`percentage_to_merge` metadata field is optional and equal to 100 by default. If specified, the value must be an integer between 1 and 100 (bounds included).

:::

### Follow neurons

|                      |            |
|----------------------|------------|
| Since version        | 1.5.0      |
| Idempotent?          | yes        |
| Minimal access level | hotkey     |


The `FOLLOW` operation sets a follow rule for a neuron.
The Governance canister smart contract will automatically deduce the vote of the following neurons from the votes of the followees during the voting.

The `followees` metadata field contains the list of neurons to follow.
If the list contains more than one neuron, the neuron will vote according to the majority of followed neurons votes (or abstain in case of draw).
If the list is empty, the rule for this topic will be discarded (i.e., the neuron will not follow any other neuron for proposals of this type).

You can restrict the rule to a specific topic by specifying the `topic` metadata field.
The topic is an integer between 0 and 10 (inclusive).
The default value for the `topic` field is 0.
Each topic can have at most one rule associated with it.
The topic codes are listed below.

0. Undefined (all topics).
1. Neuron management.
2. Exchange rate.
3. Network economics.
4. Governance.
5. Node administration.
6. Participant management.
7. Subnet management.
8. Network canister management.
9. KYC.
10. Node provider rewards.

<div class="formalpara-title">

**Prerequisites:**

</div>

* `account.address` is the ledger address of the neuron controller or hotkey.
* `metadata.followees` contains an array of valid neuron identifiers.
* `metadata.topic` is a valid topic identifier.


<div class="formalpara-title">

**Postconditions:**

</div>

* Neuron votes according to specified follow rule.

<div class="formalpara-title">

**Calling `FOLLOW` as a controller:**

</div>

```json
{
  "operation_identifier": { "index": 0 },
  "type": "FOLLOW",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "topic": 0,
    "followees": [4169717477823915596, 7814871076665269296],
    "neuron_index": 0
  }
}
```

<div class="formalpara-title">

**Calling `FOLLOW` with a hotkey:**

</div>

```json
{
  "operation_identifier": { "index": 0 },
  "type": "FOLLOW",
  "account": { "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8" },
  "metadata": {
    "topic": 0,
    "followees": [4169717477823915596, 7814871076665269296],
    "neuron_index": 0,
    "controller": {
      "public_key": {
        "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
        "curve_type": "edwards25519"
      }
    }
  }
}
```

:::info

The `followees` metadata field contains list of unique neuron identifiers assigned by the Governance canister smart contract, not the list of neuron indices chosen by the caller.
You can obtain unique neuron identifiers of you your neurons from the `neuron_id` metadata field of the `STAKE` and `NEURON_INFO` operations.

:::


## Accessing neuron attributes

### Accessing public information

|                      |        |
|----------------------|--------|
| Since version        | 1.3.0  |
| Minimal access level | public |

Call the `/account/balance` endpoint to access the staked amount and publicly available neuron metadata.

<div class="formalpara-title">

**Prerequisites:**

</div>

-   `public_key` contains the public key of a neuron’s controller.

:::info

-   This operation is available only in online mode.

-   The request should not specify any block identifier because the endpoint always returns the latest state of the neuron.

:::

#### Request

``` json
{
  "network_identifier": {
    "blockchain": "Internet Computer",
    "network": "00000000000000020101"
  },
  "account_identifier": {
    "address": "a4ac33c6a25a102756e3aac64fe9d3267dbef25392d031cfb3d2185dba93b4c4"
  },
  "metadata": {
    "account_type": "neuron",
    "neuron_index": 0,
    "public_key": {
      "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
      "curve_type": "edwards25519"
    }
  }
}
```

#### Response

``` json
{
  "block_identifier": {
    "index": 1150,
    "hash": "ca02e34bafa2f58b18a66073deb5f389271ee74bd59a024f9f7b176a890039b2"
  },
  "balances": [
    {
      "value": "100000000",
      "currency": {
        "symbol": "ICP",
        "decimals": 8
      }
    }
  ],
  "metadata": {
    "verified_query": false,
    "retrieved_at_timestamp_seconds": 1639670156,
    "state": "DISSOLVING",
    "age_seconds": 0,
    "dissolve_delay_seconds": 240269355,
    "voting_power": 195170955,
    "created_timestamp_seconds": 1638802541
  }
}
```

### Accessing protected information

|                      |        |
|----------------------|--------|
| Since version        | 1.5.0  |
| Idempotent?          | yes    |
| Minimal access level | hotkey |

The `NEURON_INFO` operation retrieves the state of the neuron from the governance canister, including protected fields such as maturity. This operation does not change the state of the neuron. Either the neuron controller or a hotkey can execute this operation.

<div class="formalpara-title">

**Calling `NEURON_INFO` as a controller:**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "NEURON_INFO",
  "account": { "address": "907ff6c714a545110b42982b72aa39c5b7742d610e234a9d40bf8cf624e7a70d" },
  "metadata": {
    "neuron_index": 0
  }
}
```

- `account.address` is the ledger address of the neuron controller.

<div class="formalpara-title">

**Calling `NEURON_INFO` with a hotkey:**

</div>

``` json
{
  "operation_identifier": { "index": 0 },
  "type": "NEURON_INFO",
  "account": { "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8" },
  "metadata": {
    "neuron_index": 0,
    "controller": {
      "public_key": {
        "hex_bytes": "ba5242d02642aede88a5f9fe82482a9fd0b6dc25f38c729253116c6865384a9d",
        "curve_type": "edwards25519"
      }
    }
  }
}
```

- `account.address` is the ledger address of the neuron hotkey.
- `metadata.controller.public_key` is the public key of the neuron controller.

:::info

Since Rosetta API identifies neurons by the controller’s public key and neuron index, the caller has to specify the public key when executing the operation using a hotkey.

:::

The Rosetta API returns the state of the neuron as operation metadata in the `/construction/submit` endpoint.

<div class="formalpara-title">

**Example response from the /construction/submit endpoint:**

</div>

``` json
{
  "transaction_identifier": {
    "hash": "0000000000000000000000000000000000000000000000000000000000000000"
  },
  "metadata": {
    "operations": [
      {
        "operation_identifier": { "index": 0 },
        "type": "NEURON_INFO",
        "status": "COMPLETED",
        "account": {
          "address": "8af54f1fa09faeca18d294e0787346264f9f1d6189ed20ff14f029a160b787e8"
        },
        "metadata": {
          "controller": {
            "principal": "sp3em-jkiyw-tospm-2huim-jor4p-et4s7-ay35f-q7tnm-hi4k2-pyicb-xae"
          },
          "followees": [
            "0": [111, 222],
            "8": [555, 666]
          ],
          "hotkeys": [
            "6xpcx-hldf5-4ddrg-onbug-4e2kw-rc25g-rxknf-p2wij-hxhqj-azcii-oqe"
          ],
          "kyc_verified": true,
          "maturity_e8s_equivalent": 1000,
          "neuron_fees_e8s": 0,
          "neuron_id": 18089972080608815000,
          "neuron_index": 0,
          "state": "DISSOLVING"
        }
      }
    ]
  }
}
```

-->
