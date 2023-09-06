# 16: IDによるアクセス制御の追加

## 概要

Dapps 多くの場合、異なるユーザが実行できる操作を制御するために、ロールベースのパーミッションが必要になります。

ユーザIDを作成して切り替える方法を説明するために、このガイドでは、異なるロールに割り当てられたユーザに対して異なる挨拶を表示する単純なdapp 。

この例では、3つの名前付きロールがあります：`owner` `admin` および`authorized` です。

- `admin` ロールを割り当てられたユーザには、`You have a role with administrative privileges` と表示される挨拶が表示されます。

- `authorized` ロールが割り当てられているユーザには、`Would you like to play a game?` と表示される挨拶が表示されます。

- これらのロールのいずれかが割り当てられていないユーザーには、`Nice to meet you!` と表示される挨拶が表示されます。

さらに、canister を初期化したユーザ ID にのみ`owner` ロールが割り当てられ、`owner` および`admin` ロールにのみ、他のユーザにロールを割り当てることができます。

高レベルでは、各ユーザは公開鍵と秘密鍵のペアを持ちます。公開鍵とユーザがアクセスするcanister 識別子が組み合わされ、Internet Computer ブロックチェーン上で実行されるcanister への関数呼び出しを認証するためのメッセージ呼び出し元として使用できるセキュリティプリンシパルが形成されます。以下の図は、ユーザーIDがメッセージ呼び出し元を認証する方法を簡略化して示しています。

![principal identities](_attachments/principal-identities.svg)

## 前提条件

始める前に、[開発者環境ガイドの](./dev-env.md)指示に従って開発者環境をセットアップしていることを確認してください。

## 新規プロジェクトの作成

まだ開いていない場合は、ローカル・コンピュータでターミナル・ウィンドウを開きます。

以下のコマンドを実行して、新しいプロジェクトを作成します：

    dfx new access_hello

以下のコマンドを実行して、プロジェクト・ディレクトリに移動します：

    cd access_hello

## デフォルトのプロジェクトを変更します。dapp

このガイドでは、テンプレート・ソース・コード・ファイルを、ロールの割り当てと取得の機能を持つdapp 。

テキストエディタで`src/access_hello_backend/main.mo` ファイルを開き、既存の内容を削除します。

このコードをコピーしてファイルに貼り付けます：

    // Import base modules
    import AssocList "mo:base/AssocList";
    import Error "mo:base/Error";
    import List "mo:base/List";
    
    shared({ caller = initializer }) actor class() {
    
        // Establish role-based greetings to display
        public shared({ caller }) func greet(name : Text) : async Text {
            if (has_permission(caller, #assign_role)) {
                return "Hello, " # name # ". You have a role with administrative privileges."
            } else if (has_permission(caller, #lowest)) {
                return "Welcome, " # name # ". You have an authorized account. Would you like to play a game?";
            } else {
                return "Greetings, " # name # ". Nice to meet you!";
            }
        };
    
        // Define custom types
        public type Role = {
            #owner;
            #admin;
            #authorized;
        };
    
        public type Permission = {
            #assign_role;
            #lowest;
        };
    
        private stable var roles: AssocList.AssocList<Principal, Role> = List.nil();
        private stable var role_requests: AssocList.AssocList<Principal, Role> = List.nil();
    
        func principal_eq(a: Principal, b: Principal): Bool {
            return a == b;
        };
    
        func get_role(pal: Principal) : ?Role {
            if (pal == initializer) {
                ?#owner;
            } else {
                AssocList.find<Principal, Role>(roles, pal, principal_eq);
            }
        };
    
        // Determine if a principal has a role with permissions
        func has_permission(pal: Principal, perm : Permission) : Bool {
            let role = get_role(pal);
            switch (role, perm) {
                case (?#owner or ?#admin, _) true;
                case (?#authorized, #lowest) true;
                case (_, _) false;
            }
        };
    
        // Reject unauthorized user identities
        func require_permission(pal: Principal, perm: Permission) : async () {
            if ( has_permission(pal, perm) == false ) {
                throw Error.reject( "unauthorized" );
            }
        };
    
        // Assign a new role to a principal
        public shared({ caller }) func assign_role( assignee: Principal, new_role: ?Role ) : async () {
            await require_permission( caller, #assign_role );
    
            switch new_role {
                case (?#owner) {
                    throw Error.reject( "Cannot assign anyone to be the owner" );
                };
                case (_) {};
            };
            if (assignee == initializer) {
                throw Error.reject( "Cannot assign a role to the canister owner" );
            };
            roles := AssocList.replace<Principal, Role>(roles, assignee, principal_eq, new_role).0;
            role_requests := AssocList.replace<Principal, Role>(role_requests, assignee, principal_eq, null).0;
        };
    
        public shared({ caller }) func request_role( role: Role ) : async Principal {
            role_requests := AssocList.replace<Principal, Role>(role_requests, caller, principal_eq, ?role).0;
            return caller;
        };
    
        // Return the principal of the message caller/user identity
        public shared({ caller }) func callerPrincipal() : async Principal {
            return caller;
        };
    
        // Return the role of the message caller/user identity
        public shared({ caller }) func my_role() : async ?Role {
            return get_role(caller);
        };
    
        public shared({ caller }) func my_role_request() : async ?Role {
            AssocList.find<Principal, Role>(role_requests, caller, principal_eq);
        };
    
        public shared({ caller }) func get_role_requests() : async List.List<(Principal,Role)> {
            await require_permission( caller, #assign_role );
            return role_requests;
        };
    
        public shared({ caller }) func get_roles() : async List.List<(Principal,Role)> {
            await require_permission( caller, #assign_role );
            return roles;
        };
    };

このdapp の重要な要素をいくつか見てみましょう：

- `greet` 関数が、以前のガイドで見た`greet` 関数のバリエーションであることにお気づきでしょう。しかし、このdapp 、`greet` 関数では、メッセージ呼び出し元を使用して、適用すべきパーミッションを決定し、呼び出し元に関連付けられたパーミッションに基づいて、どの挨拶を表示するかを決定します。

- dapp は、2つのカスタムタイプを定義します。1つは`Roles` 用、もう1つは`Permissions` 用です。

- `assign_roles` 関数を使用すると、メッセージ発信者は ID に関連付けられたプリンシパルにロールを割 り当てることができます。

- `callerPrincipal` 関数を使用すると、ID に関連付けられたプリンシパルを返すことができます。

- `my_role` 関数を使用すると、ID に関連付けられたロールを返すことができます。

変更を保存し、`main.mo` ファイルを閉じて続行します。

## ローカルのcanister 実行環境の開始

`access_hello` プロジェクトをビルドする前に、開発環境で実行されているローカルのcanister 実行環境、またはInternet Computer ブロックチェーンメインネットに接続する必要があります。

ローカルコンピューターで新しいターミナルウィンドウまたはタブを開きます。

以下のコマンドを実行して、コンピュータ上でローカルcanister 実行環境を起動します：

    dfx start --background

ローカルのcanister 実行環境が起動操作を完了したら、次のステップに進むことができます。

## を登録、ビルド、およびデプロイします。dapp

dapp 開発環境で実行されているローカルのcanister 実行環境に接続したら、次の [`dfx deploy`](/references/cli-reference/dfx-deploy.md)コマンドを実行します。

これらの各ステップは、別々の [`dfx canister create`](/references/cli-reference/dfx-canister.md#dfx_canister_create), [`dfx build`](/references/cli-reference/dfx-build.md)および [`dfx canister install`](/references/cli-reference/dfx-canister.md#dfx_canister_install)コマンドを使用して個別に実行することもできます。

プロジェクトのディレクトリで以下のコマンドを実行して、`access_hello` バックエンドdapp を登録、ビルド、デプロイします：

    dfx deploy

出力は以下のようになります：

    ...
    Committing batch.
    Committing batch with 18 operations.
    Deployed canisters.
    URLs:
    Frontend canister via browser
        access_hello_frontend: http://127.0.0.1:8080/?canisterId=cuj6u-c4aaa-aaaaa-qaajq-cai
    Backend canister via Candid interface:
        access_hello_backend: http://127.0.0.1:8080/?canisterId=cbopz-duaaa-aaaaa-qaaka-cai&id=ctiya-peaaa-aaaaa-qaaja-cai

## 現在の ID コンテキストの確認

追加の ID を作成する前に、`owner` ID に関連付けられたプリンシパル識別子と、`owner` ID 用のcycles ウォレットを確認しましょう。この`owner` ID は、canisters がデプロイされたときに作成されます。

Internet Computer ブロックチェーンでは、プリンシパルはユーザー、canister 、ノード、またはサブネットの内部代表です。プリンシパルのテキスト表現は、プリンシパルデータタイプを使用する際に表示される外部識別子です。

現在の ID とプリンシパルを確認するには、以下のコマンドを実行して現在アクティブな ID を確認し ます：

    dfx identity whoami

コマンドは以下のような出力を表示します：

    owner

コマンドが`default` 以外を表示した場合は、コマンドを使用して`default` ID に切り替えることができます：

    dfx identity use owner

次のコマンドを実行して、`default` ユーザ ID のプリンシパルを確認します：

    dfx identity get-principal

次のような出力が表示されます：

    zen7w-sjxmx-jcslx-ey4hf-rfxdq-l4soz-7ie3o-hti3o-nyoma-nrkwa-cqe

次のコマンドを実行して、`default` ユーザ ID に関連付けられたロールを確認します：

    dfx canister call access_hello_backend my_role

次のような出力が表示されます：

    (opt variant { owner })

## 新しいユーザ ID の作成

新しいユーザIDを作成する 新しいユーザIDを作成する 新しいユーザIDを作成する 新しいユーザIDを作成する 新しいユーザIDを作成するdapp でアクセス制御のテストを開始するために、新しいユーザIDをいくつか作成し、それらのユーザをさまざまなロールに割り当ててみましょう。

新しいユーザ ID を作成するには、以下のコマンドを実行して新しい管理ユーザ ID を作成します：

    dfx identity new ic_admin

このコマンドにより、以下のような出力が表示されます：

    Your seed phrase for identity 'ic_admin': void thought frown volcano reject claw weekend waste boost mouse hen genre crop real tooth tray hero perfect swarm nest demand green when fade
    This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
    Created identity: "ic_admin".

`my_role` 関数を呼び出して、新しいユーザ ID がどのロールにも割り当てられていないことを確認します。

    dfx --identity ic_admin canister call access_hello_backend my_role

コマンドを実行すると、次のような出力が表示されます：

    Creating a wallet canister on the local network.
    The wallet canister on the "local" network for user "ic_admin" is "ryjl3-tyaaa-aaaaa-aaaba-cai"
    (null)

次のコマンドを実行して、現在アクティブな ID コンテキストを切り替えて新しい`ic_admin` ユーザ ID を使用し、`ic_admin` ユーザに関連付けられているプリンシパルを表示します：

    dfx identity use ic_admin && dfx identity get-principal

このコマンドにより、以下のような出力が表示されます：

    Using identity: "ic_admin".
    scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe

次のコマンドを実行して、`access_hello_backend` canister の呼び出しに使用されるプリンシパルを確認します：

    dfx canister call access_hello callerPrincipal

以下のような出力が表示されます：

    (principal "scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe")

返されるプリンシパルは、ユーザコンテキストに関連付けられているプリンシパルであることがわかります。

## ID への役割の割り当て

ic\_admin ID に admin ロールを割り当てるには、以下のコマンドを実行して、現在アクティブな ID コンテキストを`default` ユーザ ID の使用に切り替えます：

    dfx identity use default

Candid 構文を使用して次のようなコマンドを実行し、`ic_admin` プリンシパルに`admin` ロールを割り当てます：

    dfx canister call access_hello_backend assign_role '((principal "scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe"),opt variant{admin})'

必ず、`principal` ハッシュを、`ic_admin` ID 用の`dfx identity get-principal` コマンドによって返されるハッシュに置き換えてください。

オプションで、コマンドを再実行して`my_role` 関数を呼び出し、ロールの割り当てを検証することができます。

    dfx --identity ic_admin canister call access_hello_backend my_role

コマンドには、以下のような出力が表示されます：

    (opt variant { admin })

以下のコマンドを実行して、`admin` ロールを割り当てた`ic_admin` ユーザ ID を使用して`greet` 関数を呼び出します：

    dfx --identity ic_admin canister call access_hello_backend greet "Internet Computer Admin"

以下のような出力が表示されます：

    (
        "Hello, Internet Computer Admin. You have a role with administrative privileges.",
    )

## 権限のあるユーザ ID の追加

この時点で、`owner` ロールを持つ`owner` ユーザ ID と、`admin` ロールを持つ`ic_admin` ユーザ ID があります。もう 1 つのユーザ ID を追加して、`authorized` ロールに割り当ててみましょう。ただしこの例では、環境変数を使用してユーザのプリンシパルを格納します。

新しい認可ユーザIDを追加するには、以下のコマンドを実行して新しい認可ユーザIDを作成します：

    dfx identity new alice_auth

コマンドは以下のような出力を表示します：

    Your seed phrase for identity 'alice_auth': patch chronic blossom cup speed tape velvet coconut romance salad chicken castle refuse pepper first display jewel olive flight scene window diagram option shadow
    This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
    Created identity: "alice_auth".

以下のコマンドを実行して、現在アクティブな ID コンテキストを切り替えて、新しい`alice_auth` ユーザ ID を使用します：

    dfx identity use alice_auth

以下のコマンドを実行して、`alice_auth` ユーザのプリンシパルを環境変数に格納します：

    ALICE_ID=$(dfx identity get-principal)

以下のコマンドを実行すると、格納されたプリンシパルを確認できます：

    echo $ALICE_ID

コマンドは、以下のような出力を表示します：

    b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe

以下のコマンドを実行することで、`authorized` ロールを`alice_auth` に割り当てるために`ic_admin` ID を使用します：

    dfx --identity ic_admin canister call access_hello_backend assign_role "(principal \"$ALICE_ID\", opt variant{authorized})"

`my_role` 関数を呼び出して、ロールの割り当てを検証します。

    dfx --identity alice_auth canister call access_hello_backend my_role

コマンドは以下のような出力を表示します：

    (opt variant { authorized })

以下のコマンドを実行して、`authorized` ロールを割り当てた`alice_auth` ユーザ ID を使用して`greet` 関数を呼び出します：

    dfx canister call access_hello_backend greet "Alice"

以下のような出力が表示されます：

    (
        "Welcome, Alice. You have an authorized account. Would you like to play a game?",
    )

## 権限のないユーザIDの追加

これで、特定のロールと権限を持つユーザを作成する簡単な例を示しました。次のステップでは、ロールが割り当てられておらず、特別な権限も与えられていないユーザIDを作成します。

必要に応じて、以下のコマンドを実行して、現在アクティブなIDを確認してください：

    dfx identity whoami

以下のコマンドを実行して、新しいユーザ ID を作成します：

    dfx identity new bob_standard

コマンドは、以下のような出力を表示します：

    Your seed phrase for identity 'bob_standard': sting length child airport select tube crane render march flee notable sheriff gown fitness absorb shoot cry history brisk throw rapid accident helmet coast
    This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
    Created identity: "bob_standard".

次のコマンドを実行して、`bob_standard` ユーザのプリンシパルを環境変数に格納します：

    BOB_ID=$(dfx --identity bob_standard identity get-principal)

`bob_standard` ID を使用してロールを割り当てようとします。

    dfx --identity bob_standard canister call access_hello_backend assign_role "(principal \"$BOB_ID\", opt variant{authorized})"

このコマンドは`unauthorized` エラーを返します。

以下のコマンドを実行して、`default` ユーザ ID を使用して`bob_standard` に`owner` ロールを割り当てようとします：

    dfx --identity default canister call access_hello_backend assign_role "(principal \"$BOB_ID\", opt variant{owner})"

ユーザには`owner` ロールを割り当てることができないため、このコマンドは失敗します。

以下のコマンドを実行して、`bob_standard` ユーザ ID を使用して`greet` 関数を呼び出します：

    dfx --identity bob_standard canister call access_hello_backend greet "Bob"

このコマンドでは、以下のような出力が表示されます：

    ("Greetings, Bob. Nice to meet you!")

## 複数のコマンドに対するユーザ ID の設定

これまでは、個々のコマンド用にユーザ ID を作成し、切り替える方法について説明しました。使用したいユーザ ID を指定し、そのユーザ ID のコンテキストで複数のコマンドを実行することもできます。

以下のコマンドを実行して、現在使用可能なユーザIDを一覧表示します：

    dfx identity list

このコマンドでは、現在アクティブなユーザ ID を示すアスタリスクが付いた以下のような出力が表 示されます。

    alice_auth
    bob_standard
    default *
    ic_admin
    owner

この例では、明示的に別の ID を選択しない限り、`default` のユーザ ID が使用されます。

次のようなコマンドを実行して、リストから新しいユーザ ID を選択し、それをアクティブなユー ザコンテキストにします：

    dfx identity use ic_admin

このコマンドにより、次のような出力が表示されます：

    Using identity: "ic_admin".

`dfx identity list` コマンドを再実行すると、`ic_admin` ユーザ ID にアスタリ スクが表示され、現在アクティブなユーザコンテキストであることが示されます。

これで、コマンドラインで`--identity` を指定しなくても、選択したユーザ ID を使用してコマンドを実行できるようになります。

## リソース

ID と認証に関する詳細情報をお探しの場合は、以下の関連リソースを参照してください：

- [dfx identity（コマンド・リファレンス）](/references/cli-reference/dfx-identity.md)。

## 次のステップ

次に、[ canisters を](candid-ui.md) [使用して Candid UI](candid-ui.md) を使用してみましょう。

<!---
# 16: Add access control with identities

## Overview

Dapps often require role-based permissions to control the operations different users can perform.

To illustrate how to create and switch between user identities, this guide creates a simple dapp that displays a different greeting for users who are assigned to different roles.

In this example, there are three named roles: `owner`, `admin`, and `authorized`.

- Users who are assigned an `admin` role see a greeting that displays: `You have a role with administrative privileges`.

- Users who are assigned an `authorized` role see a greeting that displays: `Would you like to play a game?`.

- Users who are not assigned one of these roles see a greeting that displays: `Nice to meet you!`.

In addition, only the user identity that initialized the canister is assigned the `owner` role and only the `owner` and `admin` roles can assign roles to other users.

At a high-level, each user has a public/private key pair. The public key combined with the canister identifier the user accesses forms a security principal that can then be used as a message caller to authenticate function calls made to the canister running on the Internet Computer blockchain. The following diagram provides a simplified view of how user identities authenticate message callers.

![principal identities](_attachments/principal-identities.svg)

## Prerequisites

Before getting started, assure you have set up your developer environment according to the instructions in the [developer environment guide](./dev-env.md).

## Create a new project

Open a terminal window on your local computer, if you don’t already have one open.

Create a new project by running the following command:

```
dfx new access_hello
```

Navigate into your project directory by running the following command:

```
cd access_hello
```

## Modify the default dapp

For this guide, you are going to replace the template source code file with a dapp that has functions for assigning and retrieving roles.

Open the `src/access_hello_backend/main.mo` file in a text editor and delete the existing content.

Copy and paste this code into the file:

```
// Import base modules
import AssocList "mo:base/AssocList";
import Error "mo:base/Error";
import List "mo:base/List";

shared({ caller = initializer }) actor class() {

    // Establish role-based greetings to display
    public shared({ caller }) func greet(name : Text) : async Text {
        if (has_permission(caller, #assign_role)) {
            return "Hello, " # name # ". You have a role with administrative privileges."
        } else if (has_permission(caller, #lowest)) {
            return "Welcome, " # name # ". You have an authorized account. Would you like to play a game?";
        } else {
            return "Greetings, " # name # ". Nice to meet you!";
        }
    };

    // Define custom types
    public type Role = {
        #owner;
        #admin;
        #authorized;
    };

    public type Permission = {
        #assign_role;
        #lowest;
    };

    private stable var roles: AssocList.AssocList<Principal, Role> = List.nil();
    private stable var role_requests: AssocList.AssocList<Principal, Role> = List.nil();

    func principal_eq(a: Principal, b: Principal): Bool {
        return a == b;
    };

    func get_role(pal: Principal) : ?Role {
        if (pal == initializer) {
            ?#owner;
        } else {
            AssocList.find<Principal, Role>(roles, pal, principal_eq);
        }
    };

    // Determine if a principal has a role with permissions
    func has_permission(pal: Principal, perm : Permission) : Bool {
        let role = get_role(pal);
        switch (role, perm) {
            case (?#owner or ?#admin, _) true;
            case (?#authorized, #lowest) true;
            case (_, _) false;
        }
    };

    // Reject unauthorized user identities
    func require_permission(pal: Principal, perm: Permission) : async () {
        if ( has_permission(pal, perm) == false ) {
            throw Error.reject( "unauthorized" );
        }
    };

    // Assign a new role to a principal
    public shared({ caller }) func assign_role( assignee: Principal, new_role: ?Role ) : async () {
        await require_permission( caller, #assign_role );

        switch new_role {
            case (?#owner) {
                throw Error.reject( "Cannot assign anyone to be the owner" );
            };
            case (_) {};
        };
        if (assignee == initializer) {
            throw Error.reject( "Cannot assign a role to the canister owner" );
        };
        roles := AssocList.replace<Principal, Role>(roles, assignee, principal_eq, new_role).0;
        role_requests := AssocList.replace<Principal, Role>(role_requests, assignee, principal_eq, null).0;
    };

    public shared({ caller }) func request_role( role: Role ) : async Principal {
        role_requests := AssocList.replace<Principal, Role>(role_requests, caller, principal_eq, ?role).0;
        return caller;
    };

    // Return the principal of the message caller/user identity
    public shared({ caller }) func callerPrincipal() : async Principal {
        return caller;
    };

    // Return the role of the message caller/user identity
    public shared({ caller }) func my_role() : async ?Role {
        return get_role(caller);
    };

    public shared({ caller }) func my_role_request() : async ?Role {
        AssocList.find<Principal, Role>(role_requests, caller, principal_eq);
    };

    public shared({ caller }) func get_role_requests() : async List.List<(Principal,Role)> {
        await require_permission( caller, #assign_role );
        return role_requests;
    };

    public shared({ caller }) func get_roles() : async List.List<(Principal,Role)> {
        await require_permission( caller, #assign_role );
        return roles;
    };
};
```

Let's take a look at a few key elements of this dapp:

- You might notice that the `greet` function is a variation on the `greet` function you have seen in previous guides. In this dapp, however, the `greet` function uses a message caller to determine the permissions that should be applied and, based on the permissions associated with the caller, which greeting to display.

- The dapp defines two custom types—one for `Roles` and one for `Permissions`.

- The `assign_roles` function enables the message caller to assign a role to the principal associated with an identity.

- The `callerPrincipal` function enables you to return the principal associated with an identity.

- The `my_role` function enables you to return the role that is associated with an identity.

Save your changes and close the `main.mo` file to continue.

## Start the local canister execution environment

Before you can build the `access_hello` project, you need to connect to the local canister execution environment running in your development environment or to the Internet Computer blockchain mainnet.

Open a new terminal window or tab on your local computer.

Start the local canister execution environment on your computer by running the following command:

```
dfx start --background
```

After the local canister execution environment completes its startup operations, you can continue to the next step.

## Register, build, and deploy the dapp

After you connect to the local canister execution environment running in your development environment, you can register, build, and deploy your dapp in a single step by running the [`dfx deploy`](/references/cli-reference/dfx-deploy.md) command. 

You can also perform each of these steps independently using separate [`dfx canister create`](/references/cli-reference/dfx-canister.md#dfx_canister_create), [`dfx build`](/references/cli-reference/dfx-build.md), and [`dfx canister install`](/references/cli-reference/dfx-canister.md#dfx_canister_install) commands.

Register, build, and deploy the `access_hello` backend dapp by running the following command in your project's directory:

```
dfx deploy
```

The output should resemble the following:

```
...
Committing batch.
Committing batch with 18 operations.
Deployed canisters.
URLs:
Frontend canister via browser
    access_hello_frontend: http://127.0.0.1:8080/?canisterId=cuj6u-c4aaa-aaaaa-qaajq-cai
Backend canister via Candid interface:
    access_hello_backend: http://127.0.0.1:8080/?canisterId=cbopz-duaaa-aaaaa-qaaka-cai&id=ctiya-peaaa-aaaaa-qaaja-cai
```

## Check the current identity context

Before we create any additional identities, let’s review the principal identifiers associated with the `owner` identity and the cycles wallet for your `owner` identity. This `owner` identity is created when your canisters were deployed. 

On the Internet Computer blockchain, a principal is the internal representative for a user, canister, node, or subnet. The textual representation for a principal is the external identifier you see displayed with working with the principal data type.

To review your current identity and principal, verify the currently-active identity by running the following command:

```
dfx identity whoami
```

The command displays output similar to the following:

```
owner
```

If the command displays anything other than `default`, you can switch to the `default` identity with the command:

```
dfx identity use owner
```

Check the principal for the `default` user identity by running the following command:

```
dfx identity get-principal
```

The command displays output similar to the following:

```
zen7w-sjxmx-jcslx-ey4hf-rfxdq-l4soz-7ie3o-hti3o-nyoma-nrkwa-cqe
```

Check the role associated with the `default` user identity by running the following command:

```
dfx canister call access_hello_backend my_role
```

The command displays output similar to the following:

```
(opt variant { owner })
```

## Create a new user identity

To begin testing the access controls in our dapp, let’s create some new user identities and assign those users to different roles.

To create a new user identity, create a new administrative user identity by running the following command:

```
dfx identity new ic_admin
```

The command displays output similar to the following:

```
Your seed phrase for identity 'ic_admin': void thought frown volcano reject claw weekend waste boost mouse hen genre crop real tooth tray hero perfect swarm nest demand green when fade
This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
Created identity: "ic_admin".
```

Call the `my_role` function to see that your new user identity has not been assigned to any role.

```
dfx --identity ic_admin canister call access_hello_backend my_role
```

The command displays output similar to the following:

```
Creating a wallet canister on the local network.
The wallet canister on the "local" network for user "ic_admin" is "ryjl3-tyaaa-aaaaa-aaaba-cai"
(null)
```

Switch your currently-active identity context to use the new `ic_admin` user identity and display the principal associated with the `ic_admin` user by running the following command:

```
dfx identity use ic_admin && dfx identity get-principal
```

The command displays output similar to the following:

```
Using identity: "ic_admin".
scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe
```

Check the principal used to call the `access_hello_backend` canister by running the following command:

```
dfx canister call access_hello callerPrincipal
```

The command displays output similar to the following:

```
(principal "scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe")
```

You will notice that the principal returned is the principal associated with the user context. 

## Assign a role to an identity

To assign the admin role to the ic_admin identity, switch your currently-active identity context to use the `default` user identity by running the following command:

```
dfx identity use default
```

Assign the `ic_admin` principal the `admin` role by running a command similar to the following using Candid syntax:

```
dfx canister call access_hello_backend assign_role '((principal "scc3r-hhpnt-264cn-t2ud3-sx74o-5txbl-arwi5-h7c4s-wx7zc-sl54q-dqe"),opt variant{admin})'
```

Be sure to replace the `principal` hash with the one returned by the `dfx identity get-principal` command for the `ic_admin` identity.

Optionally, you can rerun the command to call the `my_role` function to verify the role assignment.

```
dfx --identity ic_admin canister call access_hello_backend my_role
```

The command displays output similar to the following:

```
(opt variant { admin })
```

Call the `greet` function using the `ic_admin` user identity that you just assigned the `admin` role by running the following command:

```
dfx --identity ic_admin canister call access_hello_backend greet "Internet Computer Admin"
```

The command displays output similar to the following:

```
(
    "Hello, Internet Computer Admin. You have a role with administrative privileges.",
)
```

## Add an authorized user identity

At this point, you have a `owner` user identity with the `owner` role and an `ic_admin` user identity with the `admin` role. Let’s add another user identity and assign it to the `authorized` role. For this example, however, we’ll use an environment variable to store the user’s principal.

To add a new authorized user identity, create a new authorized user identity by running the following command:

```
dfx identity new alice_auth
```

The command displays output similar to the following:

```
Your seed phrase for identity 'alice_auth': patch chronic blossom cup speed tape velvet coconut romance salad chicken castle refuse pepper first display jewel olive flight scene window diagram option shadow
This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
Created identity: "alice_auth".
```

Switch your currently-active identity context to use the new `alice_auth` user identity by running the following command:

```
dfx identity use alice_auth
```

Store the principal for the `alice_auth` user in an environment variable by running the following command:

```
ALICE_ID=$(dfx identity get-principal)
```

You can verify the principal stored by running the following command:

```
echo $ALICE_ID
```

The command displays output similar to the following:

```
b5quc-npdph-l6qp4-kur4u-oxljq-7uddl-vfdo6-x2uo5-6y4a6-4pt6v-7qe
```

Use the `ic_admin` identity to assign the `authorized` role to `alice_auth` by running the following command:

```
dfx --identity ic_admin canister call access_hello_backend assign_role "(principal \"$ALICE_ID\", opt variant{authorized})"
```

Call the `my_role` function to verify the role assignment.

```
dfx --identity alice_auth canister call access_hello_backend my_role
```

The command displays output similar to the following:

```
(opt variant { authorized })
```

Call the `greet` function using the `alice_auth` user identity that you just assigned the `authorized` role by running the following command:

```
dfx canister call access_hello_backend greet "Alice"
```

The command displays output similar to the following:

```
(
    "Welcome, Alice. You have an authorized account. Would you like to play a game?",
)
```

## Add an unauthorized user identity

You have now seen a simple example of creating users with specific roles and permissions. The next step is to create a user identity that is not assigned to a role or given any special permissions.

Check your currently-active identity, if needed, by running the following command:

```
dfx identity whoami
```

Create a new user identity by running the following command:

```
dfx identity new bob_standard
```

The command displays output similar to the following:

```
Your seed phrase for identity 'bob_standard': sting length child airport select tube crane render march flee notable sheriff gown fitness absorb shoot cry history brisk throw rapid accident helmet coast
This can be used to reconstruct your key in case of emergency, so write it down in a safe place.
Created identity: "bob_standard".
```

Store the principal for the `bob_standard` user in an environment variable by running the following command:

```
BOB_ID=$(dfx --identity bob_standard identity get-principal)
```

Attempt to use the `bob_standard` identity to assign a role.

```
dfx --identity bob_standard canister call access_hello_backend assign_role "(principal \"$BOB_ID\", opt variant{authorized})"
```

This command returns an `unauthorized` error.

Attempt to use the `default` user identity to assign `bob_standard` the `owner` role by running the following command:

```
dfx --identity default canister call access_hello_backend assign_role "(principal \"$BOB_ID\", opt variant{owner})"
```

This command fails because users cannot be assigned the `owner` role.

Call the `greet` function using the `bob_standard` user identity by running the following command:

```
dfx --identity bob_standard canister call access_hello_backend greet "Bob"
```

The command displays output similar to the following:

```
("Greetings, Bob. Nice to meet you!")
```

## Set the user identity for multiple commands

So far, you have seen how to create and switch between user identities for individual commands. You can also specify a user identity you want to use, then run multiple commands in the context of that user identity.

List the user identities currently available by running the following command:

```
dfx identity list
```

The command displays output similar to the following with an asterisk indicating the currently-active user identity.

```
alice_auth
bob_standard
default *
ic_admin
owner
```

In this example, the `default` user identity is used unless you explicitly select a different identity.

Select a new user identity from the list and make it the active user context by running a command similar to the following:

```
dfx identity use ic_admin
```

The command displays output similar to the following:

```
Using identity: "ic_admin".
```

If you rerun the `dfx identity list` command, the `ic_admin` user identity displays an asterisk to indicate it is the currently active user context.

You can now run commands using the selected user identity without specifying `--identity` on the command-line.

## Resources

If you are looking for more information about identity and authentication, check out the following related resources:

- [dfx identity (command reference)](/references/cli-reference/dfx-identity.md).

## Next steps

Next, let's dive into using the [Candid UI with canisters](candid-ui.md).

-->
