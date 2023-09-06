# SNSプロポーザル

## 概要

SNSを管理するために、SNSコミュニティはプロポーザルがどのように機能し、どのように提出され、投票され、どのような効果があるかを理解する必要があります。

この記事では、さまざまな種類の提案について説明し、それぞれを提出するために使用できるquillコマンドの例を提供します。

## 背景

canister
提案が採用されると、このメソッドが呼び出され、チェーン上で完全に実行されます。

このメソッドは SNS のガバナンスそのものにある場合もあれば、別のcanister に定義されている場合もあります。

## 提案書を提出するためのquillの使用

### 前提条件

- \[x\] インストール [`quill`](https://github.com/dfinity/quill).
- \[x\] SNSneuron を所有するプリンシパルがいて、そのプリンシパルが SNS にプロポーザルを提出できること。

### `sns make-proposal` コマンドによる投稿

neuron であれば、誰でも投稿できます。そのため、
`sns make-proposal` [の](https://github.com/dfinity/quill/blob/master/docs/cli-reference/sns/quill-sns-make-proposal.md)コマンドは、`ManageNeuron` のメッセージとなります。このコマンドにより、neuron の人は、他のneuron の人の投票にかける提案（`Motion` 提案など）を提出することができます。

コマンドの構造は以下の通りです：

``` bash
# create and sign the proposal, store it in a message.json file
quill sns --canister-ids-file <PATH_TO_CANISTER_IDS_JSON_FILE> --pem-file <PATH_TO_PEM_FILE> make-proposal <PROPOSAL_NEURON_ID> --proposal '(
    record {
        title = "lorem ipsum";
        url = "lorem ipsum";
        summary = "lorem ipsum";
        action = opt variant {
            <PROPOSAL_TYPE> = <PARAMETERS_OF_PROPOSAL_TYPE>
        };
    }
)' > message.json

# send the proposal (stored in message.json) to the network
quill send message.json
```

- `<PATH_TO_CANISTER_IDS_JSON_FILE>` は IDs JSON ファイルへのファイルパスです。canister [sns\_canister](https://github.com/dfinity/quill/blob/master/e2e/assets/sns_canister_ids.json)\_ids[.jsonの](https://github.com/dfinity/quill/blob/master/e2e/assets/sns_canister_ids.json)例を参照してください。
- `PROPOSAL_NEURON_ID` は、提案書を提出する の ID です。neuron neuron 
- `<PATH_TO_PEM_FILE>` は、提案書を提出する を所有する ID の PEM ファイルへのパスです。PEMファイルを生成するには、neuron [ここを](https://internetcomputer.org/docs/current/references/quill-cli-reference/quill-generate)参照してください。
- `title` は提案の短い説明です。
- `url` は、提案の詳細を説明する文書へのリンクです。
- `summary` は提案の簡単な要約です。
- `action` は、提案の内容を定義します。提案の種類に応じて、この部分で定義される異なるパラメータを提供する必要があります。提案は単なるメソッドの呼び出しなので、これらのパラメータはターゲットメソッドがどの引数で呼び出されるかを定義します。

#### 具体例

例えば、\<PROPOSAL\_TYPE\>`Motion` の立候補レコードを使用する場合、`Motion` の提案を提出するための CLI フレンドリーコマンドは次のようになります：

``` bash
# helpful definitions (only need to set these once). This is a sample neuron ID.
export PROPOSAL_NEURON_ID="594fd5d8dce3e793c3e421e1b87d55247627f8a63473047671f7f5ccc48eda63"
# example path for the PEM file. This is a sample PEM file path.
export PEM_FILE="/home/user/.config/dfx/identity/$(dfx identity whoami)/identity.pem"

# Note: <PROPOSAL_TYPE> is replaced with "Motion" and <PARAMETERS_OF_PROPOSAL_TYPE> with the parameters for the Motion proposal
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "SNS is great";
        url = "https://sns-examples.com/proposal/42";
        summary = "This is a motion proposal to see if people agree on the fact that the SNS is great.";
        action = opt variant {
            Motion = record {

                motion_text = "I hereby raise the motion that the use of the SNS shall commence";

            }        
        };
    }
)' > message.json

quill send message.json
```

:::warning
この記事では、それぞれの提案例について`export PROPOSAL_NEURON_ID` と`export PEM_FILE` の行を繰り返しませんが、提案を投稿する前にターミナルでこれらの変数を設定することをお勧めします。
::：

## 提案の種類

この記事では、SNSに投稿できる[プロポーザルの](#generic-proposals)種類について説明します。

## ネイティブプロポーザル

SNSには「ネイティブプロポーザル」と呼ばれるプロポーザルが組み込まれています。

以下の種類があります：

- [`Motion`](#motion).
- [`UpgradeSnsToNextVersion`](#upgradesnstonextversion).
- [`RegisterDappCanisters`](#registerdappcanisters).
- [`DeregisterDappCanisters`](#deregisterdappcanisters).
- [`TransferSnsTreasuryFunds`](#transfersnstreasuryfunds).
- [`UpgradeSnsControlledCanister`](#upgradesnscontrolledcanister).
- [`ManageSnsMetadata`](#managesnsmetadata).

[ canisterSNSを管理するために使用されるすべての提案は、SNSガバナンス](https://sourcegraph.com/github.com/dfinity/ic@4732d8281404c0a7c1e0a91937ffd0e54f2beced/-/blob/rs/sns/governance/canister/governance.did) canister 。

以下は、この記事の目的のために最も重要なタイプです：

``` candid
    type Account = record { 
        owner : opt principal; 
        subaccount : opt Subaccount 
    };

    //proposals types for managing an SNS
    type Action = variant {
        ManageNervousSystemParameters : NervousSystemParameters;
        AddGenericNervousSystemFunction : NervousSystemFunction;
        RemoveGenericNervousSystemFunction : nat64;
        UpgradeSnsToNextVersion : record {};
        RegisterDappCanisters : RegisterDappCanisters;
        TransferSnsTreasuryFunds : TransferSnsTreasuryFunds;
        UpgradeSnsControlledCanister : UpgradeSnsControlledCanister;
        DeregisterDappCanisters : DeregisterDappCanisters;
        Unspecified : record {};
        ManageSnsMetadata : ManageSnsMetadata;
        ExecuteGenericNervousSystemFunction : ExecuteGenericNervousSystemFunction;
        Motion : Motion;
    };
```

:::info
コード内の型は[こちらを](https://sourcegraph.com/github.com/dfinity/ic@4732d8281404c0a7c1e0a91937ffd0e54f2beced/-/blob/rs/sns/governance/proto/ic_sns_governance/pb/v1/governance.proto?L405)ご覧ください - コード内では「アクション」と呼ばれています。
::：

### `Motion`

すなわち、他のプロポーザルのようにメソッドの実行をトリガーしません。例えば、ある機能を開始する前の意見調査に使用することができます。

#### 関連する型シグネチャ

``` candid
    Motion: record {
        motion_text : text;
    }
```

#### まとめ

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "SNS is great";
        url = "https://sns-examples.com/proposal/42";
        summary = "This is a motion proposal to see if people agree on the fact that the SNS is great.";
        action = opt variant {
            Motion = record {

                motion_text = "I hereby raise the motion that the use of the SNS shall commence";

            }        
        };
    }
)' > message.json

quill send message.json
```

### `UpgradeSnsToNextVersion`

canister canister
新しいSNS ワームコードはNNSプロポーザルによって承認され、SNS-canister [Wに](../introduction/sns-architecture)追加されます。 各SNSコミュニティは、SNSインスタンスをSNS-Wで利用可能な次のSNSバージョンにアップグレードするかどうか、またいつアップグレードするかを決定できます。

そのためには、`UpgradeSnsToNextVersion`。この提案が採用されると、SNSルートへの呼び出しがトリガーされ、SNS-Wにどの新しいwasmバージョンが利用可能かを尋ね、この新しいバージョンにアップグレードします。

#### 関連する型シグネチャ

``` candid
    type UpgradeSnsToNextVersion : record {};
```

#### まとめ

bashでの例：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Upgrade SNS to next available version";
        url = "https://sns-examples.com/proposal/42";
        summary = "A proposal to upgrade the SNS DAO to the next available version on SNS-W";
        action = opt variant {
            UpgradeSnsToNextVersion = record {};
        };
    }
)' > message.json

quill send message.json
```

### `RegisterDappCanisters`

SNS はdapp cansiters のセットをコントロールします。SNS コミュニティは、新しいdapps を SNS のコントロールに追加することを決定できます。
提案`RegisterDappCanisters` によって、SNS は新しいdapp canisters のセットのコントロールを受け入れることができます。登録されるべき新しいcanisters はcanister ID によって識別され、canisters のリストを登録することができます（単一のものだけではありません）。

#### 関連する型シグネチャ

``` candid
    type RegisterDappCanisters : RegisterDappCanisters;

    type RegisterDappCanisters = record { canister_ids : vec principal };
```

#### まとめ方

``` candid
    type RegisterDappCanisters: record {
        canister_ids : vec principal
    };
```

bashでの例：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Register new dapp canisters";
        url = "https://sns-examples.com/proposal/42";
        summary = "Proposal to register two new dapp canisters, with ID ltyfs-qiaaa-aaaak-aan3a-cai and ltyfs-qiaaa-aaaak-aan3a-cai to the SNS.";
        action = opt variant {
            RegisterDappCanisters = record {

                canister_ids = vec {principal "ltyfs-qiaaa-aaaak-aan3a-cai", principal "ltyfs-qiaaa-aaaak-aan3a-cai"};
                
            };
        };
    }
)' > message.json

quill send message.json
```

### `DeregisterDappCanisters`

`DeregisterDappCanisters` というプロポーザルは`RegisterDappCanisters` と対になるものです。
SNS コミュニティが、指定されたdapp canister の制御を放棄したいと決定した場合、このプロポーザルを使ってそうすることができます。
この目的のために、このプロポーザルはcanister 登録解除されるdapp canister の ID と、canister が引き渡されるプリンシパルを定義します (つまり、このプリンシパルは指定されたdapp canister の新しい制御者として設定されます)。

#### 関連する型署名

``` candid
    type DeregisterDappCanisters : DeregisterDappCanisters;

    type DeregisterDappCanisters = record {
        canister_ids : vec principal;
        new_controllers : vec principal;
    };
```

#### まとめ

bashでの例です：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = deregister dapp canisters";
        url = "https://sns-examples.com/proposal/42";
        summary = "This proposal gives up the control of a canister";
        action = opt variant {
            DeregisterDappCanisters = record {
                
                canister_ids = vec {principal "ltyfs-qiaaa-aaaak-aan3a-cai", principal "ltyfs-qiaaa-aaaak-aan3a-cai"};
                
                new_controllers = vec {principal "rymrc-piaaa-aaaao-aaljq-cai", principal "suaf3-hqaaa-aaaaf-bfyob-cai"};
            };
        };
    };
)' > message.json

quill send message.json
```

### `TransferSnsTreasuryFunds`

SNSのDAOは、`TransferSnsTreasuryFunds` 提案によって他のアカウントに資金を送ることができる宝庫を制御しています。
これを行うには、以下の引数を定義する必要があります。

#### 関連する型シグネチャ

``` candid
    type TransferSnsTreasuryFunds = record {
        from_treasury : int32;
        to_principal : opt principal;
        to_subaccount : opt Subaccount;
        memo : opt nat64;
        amount_e8s : nat64;
    };

    type Subaccount = record { subaccount : vec nat8 };
```

#### まとめ

``` candid
    type TransferSnsTreasuryFunds = record {
        from_treasury : int32;
        to_principal : opt principal;
        to_subaccount : opt record { subaccount : vec nat8 };
        memo : opt nat64;
        amount_e8s : nat64;
    };
```

bashでの例：

``` bash
quill sns make-proposal <PROPOSER_NEURON_ID> --proposal '(
    record {
        title = "Transfer 41100 ICP to Foo Labs";
        url = "https://sns-examples.com/proposal/42";
        summary = "Transfer 411 ICP to Foo Labs";
        action = opt variant {
            TransferSnsTreasuryFunds = record {

                from_treasury = 1 : int32;
                
                to_principal = opt principal "ozcnp-xcxhg-inakz-sg3bi-nczm3-jhg6y-idt46-cdygl-ebztx-iq4ft-vae";
                
                to_subaccount = null;
                
                memo = null;
                
                amount_e8s = 4_110_000_000_000 : nat64;
            };
        };
    };
)' > message.json

quill send message.json
```

[アクティブなSNSの提案](https://dashboard.internetcomputer.org/sns/3e3x2-xyaaa-aaaaq-aaalq-cai/proposal/202)例を参照してください。

### `UpgradeSnsControlledCanister`

提案`UpgradeSnsControlledCanister` は、SNS DAOによって管理されているdapp canister を新しいwasmにアップグレードするものです。

#### 関連する型シグネチャ

``` candid
    type UpgradeSnsControlledCanister : UpgradeSnsControlledCanister;

    type UpgradeSnsControlledCanister = record {
        new_canister_wasm : vec nat8;
        mode : opt int32;
        canister_id : opt principal;
        canister_upgrade_arg : opt vec nat8;
    };
```

:::warning
この提案はwasmを渡す必要があり、バイナリとしてコマンドラインにコピー/ペーストするのは扱いにくいので、開発者は特別に作られた [`make-upgrade-canister-proposal`](https://github.com/dfinity/quill/blob/master/docs/cli-reference/sns/quill-sns-make-upgrade-canister-proposal.md)`quill sns`コマンドを使用することを推奨します。

``` bash
quill sns make-upgrade-canister-proposal <PROPOSER_NEURON_ID> --target-canister-id <TARGET_CANISTER_ID> --wasm-path <WASM_PATH> [option]
```

``` bash
export $WASM_PATH="/home/user/new_wasm.wasm"
quill sns make-upgrade-canister-proposal --target-canister-id "4ijyc-kiaaa-aaaaf-aaaja-cai" --wasm-path $WASM_PATH $PROPOSAL_NEURON_ID > message.json
quill send message.json
```

:::

### `ManageSnsMetadata`

各SNSは、SNSプロジェクトを定義するメタデータを持ち、例えばメインdapp canister 、ロゴ、名前、プロジェクトの目的を要約した説明を含むURLを持っています。このメタデータは、例えば、関連プロジェクトのリブランディングがあった場合など、いつでも更新することができます。そのために、SNS DAOは以下の属性を定義したプロポーザル`ManageSnsMetadata` 。

#### 関連する型シグネチャ

``` candid
    type  ManageSnsMetadata : ManageSnsMetadata;

    type ManageSnsMetadata = record {
        url : opt text;
        logo : opt text;
        name : opt text;
        description : opt text;
    };
```

#### まとめ

ユーザがメタデータの一部だけを変更したい場合があります。メタデータは現在このようになっていますが、`description` フィールドだけを変更したいとします：

```
    url = "https://sns-examples.com/proposal/42";
    logo : "https://sns-examples.com/logo/img.jpg";
    name : "SNS Example #42";
    description : "Sample SNS used for educational purposes";
```

`description` フィールドを更新するには、次のコマンドを使います（未処理のフィールドは`null` と表示されます）。

bashでの例：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "lorem ipsum";
        url = "https://sns-examples.com/proposal/42";
        summary = "lorem ipsum";
        action = opt variant {
            ManageSnsMetadata = record {
                
                url = null;
                
                logo = null;
                
                name = null;
                
                description = "UPDATED Sample SNS used for educational purposes";
            };
        };
    }
)' > message.json

quill send message.json
```

その結果、メタデータはこのようになり、`description` フィールドだけが変更されます：

```
    url = "https://sns-examples.com/proposal/42";
    logo : "https://sns-examples.com/logo/img.jpg";
    name : "SNS Example #42";
    description : "UPDATED Sample SNS used for educational purposes";
```

## 一般的な提案

各SNSコミュニティには、dapp-特有のニーズがあるかもしれません。

いくつかの例を挙げましょう：

- dapp は、dapp canisters をアップグレードするための非常に複雑な手順を持っているかもしれません。たとえば、ユーザーごとにcanister を用意し、「ユーザールートcanister」上でオーケストレーションするような場合です。このワークフローでは、canister にアップグレードすべきユーザーを伝え、canisters にアップグレードのトリガーをかけなければなりません。DAO が管理するdapp では、これはプロポーザルによって行われるはずです。
- 多くのdapps はアセットcanister を持っています。アセットの更新は、通常のcanister アップグレードではできません。したがって、アセットを更新するカスタム方法が必要です。
- 開発者はDAOがモデレーターを選出したり、特定のメソッドを呼び出したり、特定の支払いを行ったりできる唯一のエンティティであることを望むかもしれません。

このようなケースのために、SNSはいわゆる "ジェネリックプロポーザル "を持っています。これは各SNSコミュニティが独自に定義できるカスタムプロポーザルです。

プロポーザルはcanister のメソッドの呼び出しにすぎません。つまり、SNSガバナンスcanister にどのメソッドを呼び出せばよいかを教えさえすれば、プロポーザルで任意のことができるということです。

通常、一般的なプロポーザルは次のような構造をしています。開発者は、「一般的な」神経系機能を追加/実行/削除するプロポーザルを送信します(例:`AddGenericNervousSystemFunction`)。したがって、以下のプロポーザル・タイプは技術的には「ネイティブ・プロポーザル・タイプ」ですが、ジェネリック・プロポーザルを管理するために使用されます。

- [`AddGenericNervousSystemFunction`](#addgenericnervoussystemfunction).
- [`RemoveGenericNervousSystemFunction`](#removegenericnervoussystemfunction).
- [`ExecuteGenericNervousSystemFunction`](#executegenericnervoussystemfunction).

### `AddGenericNervousSystemFunction`

#### 関連する型シグネチャ

``` candid
    type AddGenericNervousSystemFunction : NervousSystemFunction;

    type NervousSystemFunction = record {
        id : nat64;
        name : text;
        description : opt text;
        function_type : opt FunctionType;
    };

    type FunctionType = variant {
        NativeNervousSystemFunction : record {};
        GenericNervousSystemFunction : GenericNervousSystemFunction;
    };

    type GenericNervousSystemFunction = record {
        validator_canister_id : opt principal;
        target_canister_id : opt principal;
        validator_method_name : opt text;
        target_method_name : opt text;
    };
```

#### まとめる

``` candid
    type AddGenericNervousSystemFunction: record {
        id : nat64;
        name : text;
        description : opt text;
        function_type : opt variant {
            GenericNervousSystemFunction : record {
                validator_canister_id : opt principal;
                target_canister_id : opt principal;
                validator_method_name : opt text;
                target_method_name : opt text;
            }
        };
    };
```

bashでの例：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Add a new custom SNS function to \"Import proposals group into community\"";          
        url = "https://github.com/open-chat-labs/open-chat/blob/252b85a1877240dfea17512647ac42ac36e969db/backend/canisters/proposals_bot/impl/src/updates/import_proposals_group_into_community.rs";        
        summary = "Adding a new generic proposal that allows to change the background colour of the dapp. Specifically, proposals of this kind will trigger a call to the method change_colour on canister dapp_canister.";
        action = opt variant {
            AddGenericNervousSystemFunction = record {
                id = 4_003 : nat64;
                name = "Import proposals group into community";
                description = opt "Import the specified proposals group into the specified community.";
                function_type = opt variant { 
                    GenericNervousSystemFunction = record { 

                        validator_canister_id = opt principal "iywa7-ayaaa-aaaaf-aemga-cai"; 

                        target_canister_id = opt principal "iywa7-ayaaa-aaaaf-aemga-cai"; 
                        
                        validator_method_name = opt "import_proposals_group_into_community_validate"; 
                        
                        target_method_name = opt "import_proposals_group_into_community";
                    } 
                };
            }
        };
    }
)' > message.json

quill send message.json
```

[アクティブなSNSの提案](https://dashboard.internetcomputer.org/sns/3e3x2-xyaaa-aaaaq-aaalq-cai/proposal/177)例を参照してください。

### `ExecuteGenericNervousSystemFunction`

`AddGenericNervousSystemFunction` プロポーザルで汎用プロポーザルが登録された後、`ExecuteGenericNervousSystemFunction` プロポーザルでそのようなプロポーザルを提出することができます。
プロポーザルは、以前に追加された汎用プロポーザルをID(いわゆる`function_id`)で識別し、さらにペイロードを定義します。
このようなプロポーザルの提出時に、定義された検証メソッドが呼び出され、指定されたペイロードがこの種のプロポーザルとして有効であるかどうかがチェックされます(このために呼び出されるメソッドは、汎用プロポーザルが追加されたときに定義されたcanister `validator_canister_id` のメソッド`validator_method_name` です)。この検証が成功すると、プロポーザルが作成されます。

その後、提案が採用されると、SNSガバナンスcanister は、ここで定義されたペイロードを持つcanister `target_canister_id` 上のメソッド`target_method_name` を呼び出します。

#### 関連する型シグネチャ

``` candid
    type ExecuteGenericNervousSystemFunction : ExecuteGenericNervousSystemFunction;

    type ExecuteGenericNervousSystemFunction = record {
        function_id : nat64;
        payload : vec nat8;
    };
```

#### まとめ

bashでの例：

``` bash
# sample payload by constructing a blob by using didc tool
export TEXT="${1:-Hoi}"
export BLOB="$(didc encode --format blob "(hello)" )"

quill sns  --canister-ids-file ./sns_canister_ids.json  --pem-file $PEM_FILE  make-proposal $DEVELOPER_NEURON_ID --proposal '(
    record { 
        title = "Execute generic functions for test canister."; 
        url = "https://example.com"; 
        summary = "This proposal executes generic functions for test canister."; 
        action = opt variant {
            ExecuteGenericNervousSystemFunction = record {

                function_id = 2000:nat64; 
                
                payload = ${BLOB}
            }
        }    
    }
)' > msg.json

quill send message.json
```

### `RemoveGenericNervousSystemFunction`

ジェネリック提案が追加される方法と同様に、SNS DAOがもはやサポートされるべきではないと考える場合、それらはまた削除することができます。
そのためには、削除されるジェネリック神経系機能のIDを指定する提案`AddGenericNervousSystemFunction` を使用することができます。

#### 関連する型シグネチャ

``` candid
    type RemoveGenericNervousSystemFunction : nat64;
```

#### まとめ

bashでの例：

``` bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Remove the generic proposal ...";
        url = "https://sns-examples.com/proposal/42";
        summary = "Proposal to remove the generic nervous system function with ID 1006 that changed the dapp background colour as this is no longer needed.";
        action = opt variant {
            RemoveGenericNervousSystemFunction = 1_006:nat64;
        };
    }
)' > message.json

quill send message.json
```

<!-- ## SNS Proposal lifecycle


(not only how generic ones are added but also how a normal proposal works (who can submit it, what they 
have to pay, when is it adopted and under which conditions etc): "At any time (even if the deadline has
not been reached), a proposal is adopted if strictly more than half of the votes are ‘yes’ and rejected 
if at least half of the votes are ‘no’. The idea is that at this point the result cannot be turned around,
so there is no reason for waiting.” "
if the voting deadline is reached, there are more yes than no votes, and at least a minimum number of 
votes have been sent (This minimum of votes is expressed as a ratio of the used voting power in favor 
of the proposal divided by the total available voting power and is a constant that is now set to 0.03)”

When voting:
WARNING
Overall careful w/ calls to unknown canisters   (for generic proposals, registering dapp etc)?

If SNSs can upgrade dapp canisters that are not registered, we might want to say that those canisters 
not necessarily trusted, also we might want to make a recommendation “to the SNS” that calling untrusted
canisters is dangerous, so they might want to verify it first (which could then be done by registering it)




## Native SNS proposals

### Dapp canister upgrades
how to upgrade dapp

SNS Governance checks this when trying to execute - if the dapp is not registered when the proposal is executed, it will fail async fn perform_upgrade_sns_controlled_canister in SNS Governance has the associated logic.


### SNS canister upgrades
Upgrading SNS canisters will be very simple as the SNS version are blessed and the deployment paths are managed by the NNS. Nevertheless, an SNS community still has to decide when to upgrade to the next version by proposal. So we should describe how such a proposal can be made.


## Generic proposals

Some notes: for generic proposals: only call things that you trust (anyways true for SNSs),
could be immutable canister / SNS controlled / controlled by s.b. you trust not to change things arbitraty. this must be verified on adding new proposal type.

They consist of verification canister & method and of target canister & method
why we need both: governance cannot interpret meaning of the proposals 

the “Verification” method is just about filtering out 
proposals that cannot be valid anyways/ rendering => cannot put security check in the validate (just checks 
that the input is “valid proposal at the time of proposal not at the time of the execution”); for actual
validation at the time of execution: you have to add those checks to the target method called by the proposal

should include how to add and remove them.

 

### Register a new generic proposal
### Submit a generic proposal  -->

<!---
# SNS proposals

## Overview

To manage an SNS, the SNS community needs to understand how proposals work, how they can be submitted, voted on, and what effect they have.

This article explains the different kinds of proposals and provides an example quill command that can be used to submit each.

## Background

On a high level, a proposal is defined by a particular method on a particular canister that is called if the proposal is adopted by the SNS.
When the proposal is adopted, this method is called and executed fully on chain.

In some cases, this method is on the SNS governance itself and in other cases, the method that is called can be defined in another canister.

## Using quill to submit proposals

### Prerequisites

* [x] Install [`quill`](https://github.com/dfinity/quill).
* [x] Have a principal that owns an SNS neuron that can make proposals for an SNS.

### Submitting via `sns make-proposal` command

Any eligible neuron can submit a proposal. Therefore, the command to [submit a proposal](https://github.com/dfinity/quill/blob/master/docs/cli-reference/sns/quill-sns-make-proposal.md)
`sns make-proposal` is a `ManageNeuron` message. With this command, neuron holders can submit proposals (such as a `Motion` proposal) to be voted on by other neuron holders.

The structure of the commands is as follows:

```bash
# create and sign the proposal, store it in a message.json file
quill sns --canister-ids-file <PATH_TO_CANISTER_IDS_JSON_FILE> --pem-file <PATH_TO_PEM_FILE> make-proposal <PROPOSAL_NEURON_ID> --proposal '(
    record {
        title = "lorem ipsum";
        url = "lorem ipsum";
        summary = "lorem ipsum";
        action = opt variant {
            <PROPOSAL_TYPE> = <PARAMETERS_OF_PROPOSAL_TYPE>
        };
    }
)' > message.json

# send the proposal (stored in message.json) to the network
quill send message.json
```

* `<PATH_TO_CANISTER_IDS_JSON_FILE>` is the file path to a canister IDs JSON file. See example [sns_canister_ids.json](https://github.com/dfinity/quill/blob/master/e2e/assets/sns_canister_ids.json).
* `PROPOSAL_NEURON_ID` is the neuron ID of the neuron that is submitting the proposal.
* `<PATH_TO_PEM_FILE>` is the path to the PEM file of the identity that owns the neuron that is submitting the proposal. To generate a PEM file, see [here](https://internetcomputer.org/docs/current/references/quill-cli-reference/quill-generate).
* `title` is a short description of the proposal.
* `url` is a link to a document that describes the proposal in more detail.
* `summary` is a short summary of the proposal.
* `action` defines what the proposal does. Depending on the kind of proposal we require to provide different parameters that are defined in this part. As a proposal is just a call to a method, these parameters define with which arguments the target method will be called.

#### Concrete example

For example, if we use the candid record for <PROPOSAL_TYPE> `Motion`, the CLI-friendly command to submit a `Motion` proposal is:

```bash
# helpful definitions (only need to set these once). This is a sample neuron ID.
export PROPOSAL_NEURON_ID="594fd5d8dce3e793c3e421e1b87d55247627f8a63473047671f7f5ccc48eda63"
# example path for the PEM file. This is a sample PEM file path.
export PEM_FILE="/home/user/.config/dfx/identity/$(dfx identity whoami)/identity.pem"

# Note: <PROPOSAL_TYPE> is replaced with "Motion" and <PARAMETERS_OF_PROPOSAL_TYPE> with the parameters for the Motion proposal
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "SNS is great";
        url = "https://sns-examples.com/proposal/42";
        summary = "This is a motion proposal to see if people agree on the fact that the SNS is great.";
        action = opt variant {
            Motion = record {

                motion_text = "I hereby raise the motion that the use of the SNS shall commence";

            }        
        };
    }
)' > message.json

quill send message.json
```

:::warning
In this article, we will not repeat the `export PROPOSAL_NEURON_ID` and `export PEM_FILE` lines for each example proposal, but it is recommended you set these variables in your terminal before submitting proposals.
:::

## Proposal types

This article describes the different kinds of proposals that can be submitted to an SNS: [Native propoposals](#native-proposals) and [generic proposals](#generic-proposals).

## Native proposals

An SNS comes with built-in proposals called “native proposals”.

There are the following types:

* [`Motion`](#motion).
* [`UpgradeSnsToNextVersion`](#upgradesnstonextversion).
* [`RegisterDappCanisters`](#registerdappcanisters).
* [`DeregisterDappCanisters`](#deregisterdappcanisters).
* [`TransferSnsTreasuryFunds`](#transfersnstreasuryfunds).
* [`UpgradeSnsControlledCanister`](#upgradesnscontrolledcanister).
* [`ManageSnsMetadata`](#managesnsmetadata).

All of the proposals used to manage an SNS are executed on the SNS governance canister so it helps to have for reference, what the [interface for the governance canister](https://sourcegraph.com/github.com/dfinity/ic@4732d8281404c0a7c1e0a91937ffd0e54f2beced/-/blob/rs/sns/governance/canister/governance.did) is.

Below are the most important types for the purpose of this article:

```candid
    type Account = record { 
        owner : opt principal; 
        subaccount : opt Subaccount 
    };

    //proposals types for managing an SNS
    type Action = variant {
        ManageNervousSystemParameters : NervousSystemParameters;
        AddGenericNervousSystemFunction : NervousSystemFunction;
        RemoveGenericNervousSystemFunction : nat64;
        UpgradeSnsToNextVersion : record {};
        RegisterDappCanisters : RegisterDappCanisters;
        TransferSnsTreasuryFunds : TransferSnsTreasuryFunds;
        UpgradeSnsControlledCanister : UpgradeSnsControlledCanister;
        DeregisterDappCanisters : DeregisterDappCanisters;
        Unspecified : record {};
        ManageSnsMetadata : ManageSnsMetadata;
        ExecuteGenericNervousSystemFunction : ExecuteGenericNervousSystemFunction;
        Motion : Motion;
    };
```

:::info
See the types in the code [here](https://sourcegraph.com/github.com/dfinity/ic@4732d8281404c0a7c1e0a91937ffd0e54f2beced/-/blob/rs/sns/governance/proto/ic_sns_governance/pb/v1/governance.proto?L405) - they are called “action” in the code.
:::

### `Motion`

A motion proposal is the only kind of proposal that does not have any immediate effect, i.e., it does not trigger the execution of a method as other proposals do. For example, it can be used for opinion polls before even starting certain features.  

#### Relevant type signature

```candid
    Motion: record {
        motion_text : text;
    }
```

#### Putting it together

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "SNS is great";
        url = "https://sns-examples.com/proposal/42";
        summary = "This is a motion proposal to see if people agree on the fact that the SNS is great.";
        action = opt variant {
            Motion = record {

                motion_text = "I hereby raise the motion that the use of the SNS shall commence";

            }        
        };
    }
)' > message.json

quill send message.json
```


### `UpgradeSnsToNextVersion`

Recall that all approved SNS canister versions are stored on the NNS canister [SNS-W](../introduction/sns-architecture).
New SNS canister wasm codes are approved by NNS proposals and then added to SNS-W.
Each SNS community can then simply decide if and when they want to upgrade their SNS instance to the next SNS version that is available on SNS-W.

To do so, they can use an `UpgradeSnsToNextVersion`proposal. If this proposal is adopted, it will trigger a call to SNS root that will ask SNS-W which new wasm version is available and then upgrade to this new version.

#### Relevant type signatures

```candid
    type UpgradeSnsToNextVersion : record {};
```

#### Putting it together

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Upgrade SNS to next available version";
        url = "https://sns-examples.com/proposal/42";
        summary = "A proposal to upgrade the SNS DAO to the next available version on SNS-W";
        action = opt variant {
            UpgradeSnsToNextVersion = record {};
        };
    }
)' > message.json

quill send message.json
```

### `RegisterDappCanisters`

An SNS controls a set of dapp cansiters. An SNS community can decide that new dapps should be added to the SNS' control.
The proposal `RegisterDappCanisters` allows the SNS to accept the control of a set of new dapp canisters. The new canisters that should be registered are identified by their canister ID and it is allowed to register a list of canisters (not just a single one).

#### Relevant type signatures

```candid
    type RegisterDappCanisters : RegisterDappCanisters;

    type RegisterDappCanisters = record { canister_ids : vec principal };
```

#### Putting it together

```candid
    type RegisterDappCanisters: record {
        canister_ids : vec principal
    };
```

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Register new dapp canisters";
        url = "https://sns-examples.com/proposal/42";
        summary = "Proposal to register two new dapp canisters, with ID ltyfs-qiaaa-aaaak-aan3a-cai and ltyfs-qiaaa-aaaak-aan3a-cai to the SNS.";
        action = opt variant {
            RegisterDappCanisters = record {

                canister_ids = vec {principal "ltyfs-qiaaa-aaaak-aan3a-cai", principal "ltyfs-qiaaa-aaaak-aan3a-cai"};
                
            };
        };
    }
)' > message.json

quill send message.json
```

### `DeregisterDappCanisters`

The proposal `DeregisterDappCanisters` is the counterpart of `RegisterDappCanisters`.
If an SNS community decides that they would like to give up the control of a given dapp canister, they can use this proposal to do so.
To this end, the proposal defines the canister ID of the dapp canister to be deregistered and a principal to whom the canister will be handed over to (i.e., this principal will be set as the new controller of the specified dapp canister).

#### Relevant type signatures

```candid
    type DeregisterDappCanisters : DeregisterDappCanisters;

    type DeregisterDappCanisters = record {
        canister_ids : vec principal;
        new_controllers : vec principal;
    };
```

#### Putting it together

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = deregister dapp canisters";
        url = "https://sns-examples.com/proposal/42";
        summary = "This proposal gives up the control of a canister";
        action = opt variant {
            DeregisterDappCanisters = record {
                
                canister_ids = vec {principal "ltyfs-qiaaa-aaaak-aan3a-cai", principal "ltyfs-qiaaa-aaaak-aan3a-cai"};
                
                new_controllers = vec {principal "rymrc-piaaa-aaaao-aaljq-cai", principal "suaf3-hqaaa-aaaaf-bfyob-cai"};
            };
        };
    };
)' > message.json

quill send message.json
```

### `TransferSnsTreasuryFunds`

The SNS DAO has control over a treasury from which funds can be sent to other accounts by `TransferSnsTreasuryFunds` proposals.
To do so, one has to define the following arguments.

#### Relevant type signatures

```candid
    type TransferSnsTreasuryFunds = record {
        from_treasury : int32;
        to_principal : opt principal;
        to_subaccount : opt Subaccount;
        memo : opt nat64;
        amount_e8s : nat64;
    };

    type Subaccount = record { subaccount : vec nat8 };
```

#### Putting it together

```candid
    type TransferSnsTreasuryFunds = record {
        from_treasury : int32;
        to_principal : opt principal;
        to_subaccount : opt record { subaccount : vec nat8 };
        memo : opt nat64;
        amount_e8s : nat64;
    };
```

Example in bash:

```bash
quill sns make-proposal <PROPOSER_NEURON_ID> --proposal '(
    record {
        title = "Transfer 41100 ICP to Foo Labs";
        url = "https://sns-examples.com/proposal/42";
        summary = "Transfer 411 ICP to Foo Labs";
        action = opt variant {
            TransferSnsTreasuryFunds = record {

                from_treasury = 1 : int32;
                
                to_principal = opt principal "ozcnp-xcxhg-inakz-sg3bi-nczm3-jhg6y-idt46-cdygl-ebztx-iq4ft-vae";
                
                to_subaccount = null;
                
                memo = null;
                
                amount_e8s = 4_110_000_000_000 : nat64;
            };
        };
    };
)' > message.json

quill send message.json
```

See example [proposal of an active SNS](https://dashboard.internetcomputer.org/sns/3e3x2-xyaaa-aaaaq-aaalq-cai/proposal/202).

### `UpgradeSnsControlledCanister` 

The proposal `UpgradeSnsControlledCanister` is to upgrade a dapp canister that is controlled by the SNS DAO to a new wasm. 

#### Relevant type signatures

```candid
    type UpgradeSnsControlledCanister : UpgradeSnsControlledCanister;

    type UpgradeSnsControlledCanister = record {
        new_canister_wasm : vec nat8;
        mode : opt int32;
        canister_id : opt principal;
        canister_upgrade_arg : opt vec nat8;
    };
```

:::warning
Because this proposal requires passing wasm, which is unwieldy to copy/paste into the command line as binary, it is recommended that developers use the specially-made [`make-upgrade-canister-proposal`](https://github.com/dfinity/quill/blob/master/docs/cli-reference/sns/quill-sns-make-upgrade-canister-proposal.md) command in `quill sns`.


```bash
quill sns make-upgrade-canister-proposal <PROPOSER_NEURON_ID> --target-canister-id <TARGET_CANISTER_ID> --wasm-path <WASM_PATH> [option]
```

```bash
export $WASM_PATH="/home/user/new_wasm.wasm"
quill sns make-upgrade-canister-proposal --target-canister-id "4ijyc-kiaaa-aaaaf-aaaja-cai" --wasm-path $WASM_PATH $PROPOSAL_NEURON_ID > message.json
quill send message.json
```

:::

### `ManageSnsMetadata`

Each SNS has metadata that defines the SNS project and includes a URL, e.g., under which the main dapp canister can be found, a logo, a name, and a description summarizing the purpose of the projects. This metadata can be updated at any time, for example if there is a rebranding for the associated project. To do so, the SNS DAO can use the proposal  `ManageSnsMetadata` which defines all of the below attributes.

#### Relevant type signatures

```candid
    type  ManageSnsMetadata : ManageSnsMetadata;

    type ManageSnsMetadata = record {
        url : opt text;
        logo : opt text;
        name : opt text;
        description : opt text;
    };
```

#### Putting it together

Sometimes a user wants to change only part of the metadata. Suppose the metadata currently looks like this, but we want to change the `description` field only:

```
    url = "https://sns-examples.com/proposal/42";
    logo : "https://sns-examples.com/logo/img.jpg";
    name : "SNS Example #42";
    description : "Sample SNS used for educational purposes";
```

To update the `description` field, we can use the following command (where untouched fields get marked `null`).

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "lorem ipsum";
        url = "https://sns-examples.com/proposal/42";
        summary = "lorem ipsum";
        action = opt variant {
            ManageSnsMetadata = record {
                
                url = null;
                
                logo = null;
                
                name = null;
                
                description = "UPDATED Sample SNS used for educational purposes";
            };
        };
    }
)' > message.json

quill send message.json
```

Then the resulting metadata will end like this where only the `description` field changed:

```
    url = "https://sns-examples.com/proposal/42";
    logo : "https://sns-examples.com/logo/img.jpg";
    name : "SNS Example #42";
    description : "UPDATED Sample SNS used for educational purposes";
```

## Generic proposals

Each SNS community might have dapp-specific needs.

Some examples:

* A dapp may have a very complicated procedure to upgrade dapp canisters. For example, they may have a canister for each user, in which case they orchestrate over a “user root canister”. For this workflow, they would have to tell this canister what the user-canisters should be upgraded to and then trigger this upgrade. In a DAO-governed dapp this should happen via proposal.
* Many dapps have an asset canister. Updating the assets cannot be done via a normal canister upgrade as the content is larger than a proposal can be. Therefore we need a custom way to update the assets.
* Developers might want the DAO to be the only entity that can elect moderators, call certain methods, make certain payments etc…

For these cases, SNSs have so called "generic proposals". These are custom proposals that each SNS community can define itself.

Here we make use of an elegant aspect of our SNS architecture design: a proposal is just a call to a method on a canister. This means that one can do arbitrary things with a proposal as long as one can tell the SNS governance canister which method it has to call.

Typically a generic proposal will have the following structure: a developer send a proposal to add/execute/remove (e.g. `AddGenericNervousSystemFunction`) a "generic" nervous system function. So even though the proposal types below are technically "native proposal types", they are used to manage generic proposals.

* [`AddGenericNervousSystemFunction`](#addgenericnervoussystemfunction).
* [`RemoveGenericNervousSystemFunction`](#removegenericnervoussystemfunction).
* [`ExecuteGenericNervousSystemFunction`](#executegenericnervoussystemfunction).

### `AddGenericNervousSystemFunction`

#### Relevant type signatures

```candid
    type AddGenericNervousSystemFunction : NervousSystemFunction;

    type NervousSystemFunction = record {
        id : nat64;
        name : text;
        description : opt text;
        function_type : opt FunctionType;
    };

    type FunctionType = variant {
        NativeNervousSystemFunction : record {};
        GenericNervousSystemFunction : GenericNervousSystemFunction;
    };

    type GenericNervousSystemFunction = record {
        validator_canister_id : opt principal;
        target_canister_id : opt principal;
        validator_method_name : opt text;
        target_method_name : opt text;
    };
```

#### Putting it together

```candid
    type AddGenericNervousSystemFunction: record {
        id : nat64;
        name : text;
        description : opt text;
        function_type : opt variant {
            GenericNervousSystemFunction : record {
                validator_canister_id : opt principal;
                target_canister_id : opt principal;
                validator_method_name : opt text;
                target_method_name : opt text;
            }
        };
    };
```

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Add a new custom SNS function to \"Import proposals group into community\"";          
        url = "https://github.com/open-chat-labs/open-chat/blob/252b85a1877240dfea17512647ac42ac36e969db/backend/canisters/proposals_bot/impl/src/updates/import_proposals_group_into_community.rs";        
        summary = "Adding a new generic proposal that allows to change the background colour of the dapp. Specifically, proposals of this kind will trigger a call to the method change_colour on canister dapp_canister.";
        action = opt variant {
            AddGenericNervousSystemFunction = record {
                id = 4_003 : nat64;
                name = "Import proposals group into community";
                description = opt "Import the specified proposals group into the specified community.";
                function_type = opt variant { 
                    GenericNervousSystemFunction = record { 

                        validator_canister_id = opt principal "iywa7-ayaaa-aaaaf-aemga-cai"; 

                        target_canister_id = opt principal "iywa7-ayaaa-aaaaf-aemga-cai"; 
                        
                        validator_method_name = opt "import_proposals_group_into_community_validate"; 
                        
                        target_method_name = opt "import_proposals_group_into_community";
                    } 
                };
            }
        };
    }
)' > message.json

quill send message.json
```

See example [proposal of an active SNS](https://dashboard.internetcomputer.org/sns/3e3x2-xyaaa-aaaaq-aaalq-cai/proposal/177).


### `ExecuteGenericNervousSystemFunction` 

After a generic proposal has been registered with a `AddGenericNervousSystemFunction` proposal, such a proposal can be submitted with a `ExecuteGenericNervousSystemFunction` proposal.
The proposal identifies the previously added generic proposal by an ID (so called `function_id`) and, in addition, defines a payload.
Upon submission of such a proposal, the defined validation method is called, which checks that the given payload is valid for this kind of proposal (the method that will be called for this is the method `validator_method_name` on the canister `validator_canister_id` as it was defined when the generic proposal was added. If this validation is successful, the proposal will be created.

Later, if the proposal is adopted, the SNS governance canister will call the method `target_method_name` on the canister `target_canister_id` (as also define in the generic proposal) with the payload defined here.

#### Relevant type signatures

```candid
    type ExecuteGenericNervousSystemFunction : ExecuteGenericNervousSystemFunction;

    type ExecuteGenericNervousSystemFunction = record {
        function_id : nat64;
        payload : vec nat8;
    };
```

#### Putting it together

Example in bash:

```bash
# sample payload by constructing a blob by using didc tool
export TEXT="${1:-Hoi}"
export BLOB="$(didc encode --format blob "(hello)" )"

quill sns  --canister-ids-file ./sns_canister_ids.json  --pem-file $PEM_FILE  make-proposal $DEVELOPER_NEURON_ID --proposal '(
    record { 
        title = "Execute generic functions for test canister."; 
        url = "https://example.com"; 
        summary = "This proposal executes generic functions for test canister."; 
        action = opt variant {
            ExecuteGenericNervousSystemFunction = record {

                function_id = 2000:nat64; 
                
                payload = ${BLOB}
            }
        }    
    }
)' > msg.json

quill send message.json
```

### `RemoveGenericNervousSystemFunction`

Similarly to how generic proposal are added, they can also be removed again if the SNS DAO thinks that they should no longer be supported. 
To do so, they can use the proposal `AddGenericNervousSystemFunction` which specifies the ID of the generic nervous system function to be removed.

#### Relevant type signatures

```candid
    type RemoveGenericNervousSystemFunction : nat64;
```

#### Putting it together

Example in bash:

```bash
quill sns --canister-ids-file ./sns_canister_ids.json --pem-file $PEM_FILE make-proposal $PROPOSAL_NEURON_ID --proposal '(
    record {
        title = "Remove the generic proposal ...";
        url = "https://sns-examples.com/proposal/42";
        summary = "Proposal to remove the generic nervous system function with ID 1006 that changed the dapp background colour as this is no longer needed.";
        action = opt variant {
            RemoveGenericNervousSystemFunction = 1_006:nat64;
        };
    }
)' > message.json

quill send message.json
```

<!-- ## SNS Proposal lifecycle


(not only how generic ones are added but also how a normal proposal works (who can submit it, what they 
have to pay, when is it adopted and under which conditions etc): "At any time (even if the deadline has
not been reached), a proposal is adopted if strictly more than half of the votes are ‘yes’ and rejected 
if at least half of the votes are ‘no’. The idea is that at this point the result cannot be turned around,
so there is no reason for waiting.” "
if the voting deadline is reached, there are more yes than no votes, and at least a minimum number of 
votes have been sent (This minimum of votes is expressed as a ratio of the used voting power in favor 
of the proposal divided by the total available voting power and is a constant that is now set to 0.03)”

When voting:
WARNING
Overall careful w/ calls to unknown canisters   (for generic proposals, registering dapp etc)?

If SNSs can upgrade dapp canisters that are not registered, we might want to say that those canisters 
not necessarily trusted, also we might want to make a recommendation “to the SNS” that calling untrusted
canisters is dangerous, so they might want to verify it first (which could then be done by registering it)




## Native SNS proposals

### Dapp canister upgrades
how to upgrade dapp

SNS Governance checks this when trying to execute - if the dapp is not registered when the proposal is executed, it will fail async fn perform_upgrade_sns_controlled_canister in SNS Governance has the associated logic.


### SNS canister upgrades
Upgrading SNS canisters will be very simple as the SNS version are blessed and the deployment paths are managed by the NNS. Nevertheless, an SNS community still has to decide when to upgrade to the next version by proposal. So we should describe how such a proposal can be made.


## Generic proposals

Some notes: for generic proposals: only call things that you trust (anyways true for SNSs),
could be immutable canister / SNS controlled / controlled by s.b. you trust not to change things arbitraty. this must be verified on adding new proposal type.

They consist of verification canister & method and of target canister & method
why we need both: governance cannot interpret meaning of the proposals 

the “Verification” method is just about filtering out 
proposals that cannot be valid anyways/ rendering => cannot put security check in the validate (just checks 
that the input is “valid proposal at the time of proposal not at the time of the execution”); for actual
validation at the time of execution: you have to add those checks to the target method called by the proposal

should include how to add and remove them.

 

### Register a new generic proposal
### Submit a generic proposal  -!->
-->
