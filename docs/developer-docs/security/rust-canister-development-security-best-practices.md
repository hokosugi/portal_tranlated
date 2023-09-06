---

sidebar_position: 2
sidebar_label: "Canister development"
---
# Canister 開発セキュリティのベストプラクティス

## 概要

この文書には、Motoko と Rust の両方に関するcanister 開発のベストプラクティスが含まれています。

## スマートコントラクトcanister 制御

### SNS のような分散型ガバナンスシステムを使用して、canister に分散型コントローラーを持たせます。

#### セキュリティ上の懸念

canister のコントローラーは、好きなときにcanister を変更または更新できます。canister が例えばICPのようなアセットを保存している場合、これは事実上、コントローラーがcanister を更新することでこれらを盗み、cycles を自分のアカウントに転送できることを意味します。

#### 推奨

- Internet Computer の Service Nervous System (SNS) のような分散型ガバナンスシステムにcanister の制御を渡すことを検討し、canister への変更が SNS コミュニティが投票によって一括承認した場合にのみ実行されるようにします。SNSを使用する場合、SNSサブネット上のSNSを使用してください。これは、SNSがNNSに祝福されたバージョンを実行し、ICの一部として保守されていることを保証するためです。これらのSNSは間もなく利用可能になります。ロードマップは[こちら](https://dfinity.org/roadmap/)、設計案は[こちらを](https://forum.dfinity.org/t/open-governance-canister-for-sns-design-proposal/10224)ご覧ください。
- もう一つの選択肢は、canister コントローラーを完全に削除して、イミュータブルなcanister スマートコントラクトを作成することです。しかし、これはcanister をアップグレードできないことを意味し、バグが見つかった場合などに深刻な影響を及ぼす可能性があることに注意してください。分散型ガバナンスシステムを使用し、スマートコントラクトをアップグレードできるというオプションは、他のブロックチェーンと比較して、Internet Computer のエコシステムの大きな利点です。
  :::info
  他のいくつかのブロックチェーンとは異なり、イミュータブルスマートコントラクトも実行するためにcycles を必要とし、cycles を受け取ることができることに注意してください。
  ::：
- ゼロからIC上にDAO[分散型自律組織を](https://en.wikipedia.org/wiki/Decentralized_autonomous_organization)実装することも可能です。これを行うことに決めた場合（例えば、[基本的なDAOの例に沿って](https://internetcomputer.org/docs/current/samples/dao)）、これはセキュリティクリティカルであり、慎重にセキュリティレビューを行わなければならないことに注意してください。さらに、ユーザーはDAOが自分自身でコントロールされていることを検証する必要があります。

### 依存するスマートコントラクトの所有権の検証

#### セキュリティ上の懸念

canister が別のcanister スマートコントラクトに依存する（すなわち、そのスマートコントラクトに対してインターcanister コールを行う）場合、依存するcanister スマートコントラクトが分散型ガバナンスシステムによって所有されていることが不可欠です。そうでない場合、つまりコントローラーがいる場合、彼らは他の人に気づかれることなくスマートコントラクトを修正することができ、例えばcanister が保有する資産を盗むことができます。

#### 推奨

分散型であることが要求されるcanister とやり取りする場合、それが NNS、サービス神経システム (SNS)、または分散型ガバナンスシステムによって制御されていることを確認し、スマートコントラクトがどのような条件で、誰によって変更されるかを確認してください。

## 認証

### 特定のユーザーしか行えないアクションには認証が必要であることを確認します。

#### セキュリティ上の懸念

そうでない場合、攻撃者がユーザーの代わりに機密性の高いアクションを実行し、ユーザーのアカウントを危険にさらす可能性があります。

#### 推奨

- 設計上、canister のすべての呼び出しについて、呼び出し元を識別できます。呼び出し[元プリンシパルには](../../references/ic-interface-spec.md#principals)、システムAPIのメソッド`ic0.msg_caller_size` および`ic0.msg_caller_copy` を使用してアクセスできる([こちらを](../../references/ic-interface-spec.md#system-api-imports)参照)。Internet Identityなどが使用される場合、プリンシパルは、この特定のオリジンの ユーザーIDである[。](../../references/ii-spec.md#identity-design-and-data-model)一部のアクション (ユーザーのアカウントデータへのアクセスやアカウント固有の操作など) をプリンシパルまたはプリンシパルのセットに制限する必要がある場合は、canister 呼び出しで明示的にチェックする必要があります。たとえば、Rust では以下のようになります：

<!-- -->

```
    // Let pk be the public key of a principal that is allowed to perform
    // this operation. This pk could be stored in the canister's state.
    if caller() != Principal::self_authenticating(pk) {  ic_cdk::trap(...) }

    // Alternatively, if the canister keeps data for different principals
    // in e.g. a map such as BTreeMap<Principal, UserData>, then the canister
    // must ensure that each caller can only access and perform operations
    // on their own data:
    if let Some(user_data) = user_data_store.get_mut(&caller()) {
        // perform operations on the user's data
    }
```

- Rust では、`ic_cdk` クレートを使用して、`ic_cdk::api::caller` を使用して呼び出し元を認証できます。返されるプリンシパルが`Principal::self_authenticating` 型であることを確認し、そのプリンシパルの公開鍵を使用してユーザのアカウントを識別します。

- 認証されないアクションや、認証前に高価になる可能性のある操作を避けるため、認証は呼び出しのできるだけ早い段階で行いましょう。また、[匿名ユーザーへのサービスを拒否](#disallow-the-anonymous-principal-in-authenticated-calls)するのもよい考えです。

- [イングレス・メッセージの検査](#do-not-rely-on-ingress-message-inspection)中に実行される認証に依存しないでください。

### 認証された呼で匿名プリンシパルを許可しないようにします。

#### セキュリティ上の懸念

システムAPIからの呼び出し元(例えばRustの`ic0::api::caller` )は、`Principal::anonymous()` を返すかもしれません。認証された呼び出しでは、これはおそらく望ましくない(そしてセキュ リティに影響を与える可能性がある)。

#### 推奨

認証された呼では、呼び出し元が匿名でないことを確認し、匿名であればエラーま たはトラップを返すようにすること。これは、例えばヘルパーメソッドを使って一元的に行うことができます。Rustでは、例えば以下のようになります：

    fn caller() -> Result<Principal, String> {
        let caller = ic0::api::caller();
        // The anonymous principal is not allowed to interact with canister.
        if caller == Principal::anonymous() {
            Err(String::from(
                "Anonymous principal not allowed to make calls.",
            ))
        } else {
            Ok(caller)
        }
    }

## アセット認証

### HTTP資産認証を使用し、dApp 。`raw.icp0.io`

#### セキュリティ上の懸念

Dapps IC上で[アセット認証を](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification)使用すると、ブラウザに配信されるHTTPアセットが本物であることを確認できます（つまり、サブネットによって閾値署名されている）。アプリがアセット認証を行わない場合、アセット認証がチェックされない を通してのみ安全に提供できます。これは、単一の悪意のあるノードまたは境界ノードがブラウザに配信されるアセットを自由に変更できるため、安全ではありません。`raw.icp0.io` 

アプリが`icp0.io` に加えて`raw.icp0.io` を通して提供される場合、敵対者はユーザーを騙して（フィッシング）安全でない raw.icp0.io を使わせるかもしれません。

#### 推奨

- サービスワーカーがアセット認証を確認する`<canister-id>.icp0.io` 経由でのみアセットを提供してください。`<canister-id>.raw.icp0.io` を使ってはいけません。

- アセットcanister (アセット認証を自動的に作成する)を使用してアセットを提供するか、[NNSdapp](https://github.com/dfinity/nns-dapp)または[Internet Identityなどで](https://github.com/dfinity/internet-identity)行われているように、アセット認証を含む`ic-certificate` ヘッダーを追加してください。

- canisterの`http_request` メソッドで、リクエストがrawで来たかどうかをチェック。その場合、エラーを返し、アセットを提供しません。

## Canister ストレージ

### Rust：ステート変数には`thread_local!` と`Cell/RefCell` を使用し、すべてのグローバルを1つのバスケットに入れます。

#### セキュリティ上の懸念

Canisters Rustでは、グローバルで変更可能なステート変数が必要です。Rustでは、これを実現する方法がいくつかあります。しかし、いくつかの方法は、例えばメモリ破壊につながる可能性があります。

#### 推奨

- [ステート変数には、`thread_local!` と`Cell/RefCell` ](https://mmapped.blog/posts/01-effective-rust-canisters.html#use-threadlocal)を使いましょう ([effective](https://mmapped.blog/posts/01-effective-rust-canisters.html) Rust[ canisters から)。](https://mmapped.blog/posts/01-effective-rust-canisters.html)

- [すべてのグローバルを1つのバスケットに](https://mmapped.blog/posts/01-effective-rust-canisters.html#clear-state)入れましょう（effective[Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) より）。

### ユーザごとにcanister に格納できるデータ量を制限しましょう。

#### セキュリティ上の懸念

ユーザがcanister に大量のデータを保存できる場合、これを悪用してcanister のストレージがいっぱいになり、canister が使用できなくなる可能性があります。

#### 推奨事項

ユーザーごとにcanister に保存できるデータ量を制限します。この制限は、アップデートコールでユーザーのデータが保存されるたびにチェックされなければなりません。

### 安定したメモリを使用することを検討し、バージョンアップし、テストしてください。

#### セキュリティ上の懸念

Canister メモリはアップグレードをまたいで永続化されません。アップグレードをまたいでデータを保持する必要がある場合、 で メモリをシリアライズし、 でデシリアライズするのが自然な方法です。メモリが大きくなりすぎると、 は更新できなくなります。`pre_upgrade` canister `post_upgrade` canister 

#### 推奨事項

- 安定したメモリは、アップグレード後も持続するので、この問題に対処するために使用できます。

- ([効果的な Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) から)[安定したメモリの使用を検討して](https://mmapped.blog/posts/01-effective-rust-canisters.html#stable-memory-main)ください。そこで説明されているデメリットも参照してください。

- 安定[メモリのバージョンアップ](https://mmapped.blog/posts/01-effective-rust-canisters.html#version-stable-memory)([effective Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- [アップグレードフックをテストして](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades)ください ([effective R](https://mmapped.blog/posts/01-effective-rust-canisters.html)ust[ canisters から )。](https://mmapped.blog/posts/01-effective-rust-canisters.html)

- [ Internet Computer canister ](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)の[監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)方法のアップグレードに関するセクションも参照してください (Motoko に焦点を当てていますが)。

- バグを避けるために、安定したメモリのテストを書いてください。

- 人々が取り組んでいるいくつかのライブラリ(ほとんどが進行中または一部未完成)：
  
  - <https://github.com/dfinity/stable-structures/>
  
  - HashMap:[https://github.com/dfinity](https://github.com/dfinity/stable-structures/pull/1)/stable-structures/pull/1 (現在、製品化には至っていません。)
  
  - <https://github.com/seniorjoinu/ic-stable-memory>

- [ Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer) の[現在の制限事項](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer)、「長時間実行されるアップグレード」および「追加のwasmメモリを必要とする\[de\]serialiser」のセクションを参照。

- たとえば、[Internet Identityは](https://github.com/dfinity/internet-identity)、ユーザーデータの保存に安定メモリを直接使用し ています。

### の機密データの暗号化を検討してください。canisters

#### セキュリティ上の懸念

デフォルトでは、canisters は完全性を提供しますが、機密性は提供しません。canisters に保存されたデータは、ノード/レプリカによって読み取られる可能性があります。

#### 推奨事項

- canisters 上のプライベートまたは個人的なデータ（ユーザーの個人情報やプライベート情報など）をエンドツーエンドで暗号化することを検討してください。

- dapp の[暗号化されたノートの](https://github.com/dfinity/examples/tree/master/motoko/encrypted-notes-dapp)例は、エンドツーエンドの暗号化の方法を示しています。

### バックアップの作成

#### セキュリティ上の懸念

canister が使用不能になり、二度とアップグレードできなくなる可能性があります：

- アップグレードプロセスに欠陥がある (dapp 開発者のバグが原因)。

- データを永続化するコードにバグがあるため、ステートに一貫性がない / 破損している。

#### 推奨事項

- canister アップグレードに使われるメソッドがテスト済みであることを確認してください。

- canister を再インストールできるような災害復旧戦略を立てておくと便利でしょう。

- [ Internet Computer を監査する](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)方法の「バックアップとリカバリ」の項を参照してください。[ canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)

## canister 間の呼び出しとロールバック

### メッセージ実行の基本

非同期インターcanister 呼び出しにまつわる問題を理解するためには、メッセージ実行に関するいくつかの特性を理解する必要があります。これは、[セキュリティのベストプラクティスに関するコミュニティの会話でも](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s)説明されています。

いくつかの定義を示します。**コールとは**、canister が公開する[アップデート](/references/ic-interface-spec.md#http-call) [コールやクエリーコールの](/references/ic-interface-spec.md#http-query)実装のことです。例えば、Rust CDK を使用する場合、これらは通常、それぞれ`#[query]` または`#[update]` でアノテーションされます。**メッセージは**、サブネットがcanister に対して実行する、連続した命令 のセットです。canister 間で呼び出しが行われる場合、呼び出しは複数のメッセー ジに分割できることを、以下で説明します。以下の特性は必須です：

- **プロパティ1**:canister 1回に処理されるメッセージは1つだけです。つまり、メッセージの実行は逐次的であり、並列的ではありません。

- 特性**2**：各コール（クエリー/アップデート）はメッセージをトリガーします。`await` を使ってcanister 間で呼び出しが行われると、呼び出しの後のコード（青字で強調表示され ているコールバック）は別のメッセージとして実行されます。

:::info
コードがレスポンスを`await` しない場合、コールバック後のコード（`await` を使用して次のインターcanister コールがトリガーされるまで）は同じメッセージで実行されることに注意してください。

たとえば、次のMotoko ：

![example](./_attachments/example_highlighted_code.png)

ここで実行される最初のメッセージは2-3行目で、`await` 構文（オレンジ色のボックス）を使ってインターcanister 呼び出しが行われるまでです。2つ目のメッセージは、3～5行目を実行します。canister の呼び出しがリターンしたときです（青いボックス）。この部分をインターcanister 呼び出しの*コールバックと*呼びます。この例に関係する2つのメッセージは、常に順番にスケジュールされます。
::：

- **特性3**: 正常に配送されたリクエストは、送信された順番に受け取られま す。特に、canister Aが`m1` と`m2` をこの順番でcanister Bに送る場合、両方が受け入れられると、`m1` は`m2` の前に実行されます。

:::info
この性質は、メッセージがいつ実行されるかについての保証を与えるだけで、 受け取った応答の順序については保証がないことに注意。
::：

- **特性4**：インターリーブ呼からのメッセージは、信頼できる実行順序を持ちません。

canisterしかし、複数の呼がインターリーブする場合、これらのインターリーブ呼に ついて、追加の順序保証を仮定することはできない。これを説明するために、上記のコード例をもう一度考えてみましょう。`example` メソッドが2回並列に呼び出され、その結果、呼び出しがCall 1とCall 2になるとします。次の図は、2つの可能なメッセージ順序を示しています。左側では、最初の呼び出しのメッセージが最初にスケジューリングされ、その後に2回目の呼び出しのメッセージが実行されます。右側は、各コールの最初のメッセージが最初に実行される、別のメッセージスケジューリン グの可能性を示しています。あなたのコードは、メッセージの順序に関係なく、正しいステートになるはずです。

![example](./_attachments/example_orderings.png)

- **プロパティ 5**: トラップ/パニックが発生した場合、現在のメッセージに対するcanister ステートの変更は適用されません。

例えば、上の例の2番目のメッセージ（青いボックス）でトラップが発生した場合、canister 、そのメッセージから生じたステート変更は、青いボックスの中でより前のものであっても、破棄されます。しかし、それ以前のメッセー ジ、特に最初のメッセージ（オレンジ色のボックス）のステート変更は、そのメッセー ジが正常に実行されたため、適用されていることに注意してください。

- **特性6**: インターcanister の呼は、デスティネーションcanister に到達することは保証されず、呼がデスティネーションcanister に到達した場合、デスティネーションcanister は、呼の処理中にトラップするか、拒否応答を返すことができる。

すべての呼間(canister )は、canister 、またはプロトコ ルによって合成的に生成された応答を受け取ることが保証されている。ただし、応答は成功する必要はなく、拒否応答でもよい。canister Internet Computerこのようなシステム生成の拒否は、呼がcalle-canister に到達する前にいつでも発生する可能性があるだけでなく、呼がcalle-canister に到達した後でも、呼の処理中にcalle-canister がトラップした場合にも発生する可能性がある。呼がcalle-canister に到達した場合、calle-canister は応答または拒否応答を生成でき、システムは、calle-canister が生成した応答または拒否応答が発呼側に戻ることを保証する(canister)。したがって、呼び出し側canister が拒否応答も処理することが重要です。拒否応答は、メッセージが受信側で正常に処理されなかったことを意味しますが、 受信側のステートが変更されなかったことを保証するものではありません。

詳しくは、インターフェース仕様の[順序保証のセクションと](/references/ic-interface-spec.md#ordering_guarantees)、メッセージの実行をより詳細に定義した[抽象的な振る舞いの](/references/ic-interface-spec.md#abstract-behavior)セクションを参照してください。

### await後のトラップの回避

#### セキュリティ上の懸念

トラップ/パニックはcanister のステートをロールバックします。そのため、トラップやパニックに続くステート変更は危険です。これは、canister 間で呼び出しが行われる場合の重要な懸念事項でもあります。パニック/トラップが、`await` から inter-canister 呼び出しの後に発生した場合、ステート は、呼び出し全体の前ではなく、inter-canister 呼び出しのコールバック呼び出しの前のスナッ プショットに戻されます！

これは、例えば以下のような問題につながる可能性があります：

- ステート変更が適用され、その後インターcanister コールが発行されたとします。また、これらのステート変更によって、canister は一貫性のないステートになり、そのステートはコールバックでのみ再び 一貫性のある状態になるとします。ここで、コールバックにトラップがあると、canister は一貫性のないステートになります。

- この種の具体的なバグは以下のとおり。資金を送金するためにcanister 。コールバックでは、canister 、その事実をcanister ストレージに反映することで、その送金を行ったことを説明します。しかし、コールバックは使用統計データも更新し、最終的に、あるデータ構 造が一杯になったときにトラップにつながるとします。そうなると、canister 、コールバックのステート変更が適用されなくなり、転送が正しく説明されなくなるため、一貫性のないステートになってしまいます。
  ![example](./_attachments/example_trap_after_await.png)
  この例も、この[コミュニティの会話で](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s)議論されています。

- 別の例：たとえば、canister ステートの一部がインターcanister コールの前にロックされ、コールバックで解放された場合、コールバックがトラップすると、ロックが解放されない可能性があります。

- 一般的に、開発者が期待したときにデータが永続化されないバグが発生する可能性があります。

Rustでは、Rust CDKバージョン0.5.1から、トラップ時にローカル変数がスコープ外になることに注意してください。CDKは、これらのリソースを解放するために、実際に`ic0.call_on_cleanup` APIを呼び出します。例えば、[「信頼できるメッセージ順序付けがないことに注意](#be-aware-that-there-is-no-reliable-message-ordering)」で説明したように、ロックされたリソースを解放するために Rust の`Drop` 実装を使用することが可能です。

#### 推奨

- [セキュリティのベストプラクティスに関するコミュニティの会話を見て](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s)ください。

- [awaitの境界を越えて共有リソースをロックしないで](https://mmapped.blog/posts/01-effective-rust-canisters.html#dont-lock)ください（[効果的なRustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) から）。

- [ `await`](https://mmapped.blog/posts/01-effective-rust-canisters.html#panic-await)の[後にパニックを](https://mmapped.blog/posts/01-effective-rust-canisters.html#panic-await)起こさないようにしましょう（[effective R](https://mmapped.blog/posts/01-effective-rust-canisters.html)ust[ canisters から）。](https://mmapped.blog/posts/01-effective-rust-canisters.html)

- コンテキスト[メッセージ実行に関するICインターフェース仕様](/references/ic-interface-spec.md#message-execution)。

- こちらも参照してください：[ Internet Computer canister を監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)する方法の「インターcanister 呼び出し」セクションも参照してください。

### 信頼できるメッセージの順序付けがないことに注意してください。

#### セキュリティ上の懸念

上記の[メッセージ実行の基本で](#message-execution-basics)説明したように、メッセージは（呼び出し全体ではなく）アトミックに 処理されます。特に、上記のプロパティ4で説明したように、インターリーブ呼からのメッ セージは、信頼できる実行順序を持ちません。canister そのため、canister （および他のcanisters ）のステー トは、インターリーブ呼が開始されてからリターンするまでの間に変化する可 能性があり、正しく処理されないと問題が発生する可能性があります。このような問題は一般的に「リエントランシー・バグ」と呼ばれます（[リエントランシーに関するイーサリアムのベストプラクティスなどを](https://consensys.github.io/smart-contract-best-practices/attacks/reentrancy/)参照）。しかし、Internet Computer のメッセージング保証（ひいてはバグ）はイーサリアムとは異なることに注意してください。

潜在的なリエントランシーのセキュリティ問題を説明するために、2つの具体的でやや類似したタイプのバグを提供します：

- **Time-of-check time-of-use問題：**これは、canister の呼び出しの前に、グローバル・ステートに関する何らかの条件がチェックされ、呼び出しが戻ったときにその条件がまだ保持されていると誤って仮定する場合に発生します。たとえば、ある口座に十分な残高があるかどうかを確認し、次にインターcanister 呼を発行し、最後にコールバックメッセージの一部として転送を実行する場合。2回目のインターcanister 呼び出しが開始すると、最初の呼び出しのコールバックが実行される前に、他の元帳の移 転が発生している可能性があるため、最初にチェックした条件が保持されなくなる可 能性があります(上記のプロパティ4も参照)。

- **ダブルスペンディングの問題**：このような問題は、転送が2回発行されたときに発生します。例えば、呼び出し元が払い戻しの対象かどうかをチェックし、払い戻しの対象であれば、いくらかの払い戻し額を振り込むとします。払い戻し元帳の呼び出しが正常に戻ると、canister ストレージに呼び出し元が払い戻されたことを示すフラグを設定します。これは、払い戻しメソッドが呼び出し元から2回並行して呼び出される可能性があり、その場合、転送を発行する前のメッセージ(適格性チェックを含む)が両方のコールバックの前にスケジュールされている可能性があるため、二重支出の脆弱性があります。この問題の詳細な説明は、[セキュリティのベストプラクティスに関するコミュニティの会話に](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s)あります。

#### 推奨事項

非同期のインターcanister 呼び出し（`await` ）を行うcanister コードを注意深く見直すことを強く推奨します。2つのメッセージが同じステートにアクセス（読み取りまたは書き込み）する場合、これらのメッセージのスケジューリングが不正なトランザクションや一貫性のないステートにつながる可能性がないか、見直してください。

こちらも参照してください：[ Internet Computer canister ](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister) の[監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)方法の「canister 間呼び出し」の項も参照してください。

バグにつながる可能性のあるメッセージの順序に関する問題に対処するために、通常はロッ キングメカニズムを使用して、例えば呼び出し元（または誰でも）が、（複数のメッセージを含む）呼 び出し全体を一度に一度しか実行できないようにします。簡単な例は、前述の[コミュニティーの会話でも](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s)紹介されています。

通常、ロックはコールバックで解放されます。この場合、[await後のトラップの](#avoid-traps-after-await)回避で説明したように、コールバックがトラップした場合にロックが解放されないというリスクがあります。Rustでは、Rustの`Drop` 実装を使用することで、この問題を回避する良いパターンがあります。以下のコード例では、`Drop` の実装を使用して、呼び出し元ごとのロック (`CallerGuard`) を実装する方法を示しています。Rust CDKバージョン0.5.1以降では、コールバックがトラップした場合でもローカル変数はスコープ外に出るので、その場合でも呼び出し元のロックは解放されます。

    pub struct State {
        pending_requests: BTreeSet<Principal>,
    }
    
    thread_local! {
        static STATE: RefCell<State> = RefCell::new(State{pending_requests: BTreeSet::new()});
    }
    
    pub struct CallerGuard {
        principal: Principal,
    }
    
    impl CallerGuard {
        pub fn new(principal: Principal) -> Result<Self, String> {
            STATE.with(|state| {
                let pending_requests = &mut state.borrow_mut().pending_requests;
                if pending_requests.contains(&principal){
                    return Err(format!("Already processing a request for principal {:?}", &principal));
                }
                pending_requests.insert(principal);
                Ok(Self { principal })
            })
        }
    }
    
    impl Drop for CallerGuard {
        fn drop(&mut self) {
            STATE.with(|state| {
                state.borrow_mut().pending_requests.remove(&self.principal);
            })
        }
    }
    
    #[update]
    #[candid_method(update)]
    async fn example_call_with_locking_per_caller() -> Result<(), String> {
        let caller = ic_cdk::caller();
        // using `?`, we return an error immediately if there is already a call in progress for `caller`.
        let _ = CallerGuard::new(caller)?;
        // do anything, call other canisters
        Ok(())
    } // here the guard goes out of scope and is dropped
    
    mod test {
        use super::*;
    
        #[test]
        fn should_obtain_guard_for_different_principals() {
            let principal_1 = Principal::anonymous();
            let principal_2 = Principal::management_canister();
            let caller_guard = CallerGuard::new(principal_1);
            assert!(caller_guard.is_ok());
            assert!(CallerGuard::new(principal_2).is_ok());
        }
    
        #[test]
        fn should_not_obtain_guard_twice_for_same_principal() {
            let principal = Principal::anonymous();
            let caller_guard = CallerGuard::new(principal);
            assert!(caller_guard.is_ok());
            assert!(CallerGuard::new(principal).is_err());
        }
    
        #[test]
        fn should_release_guard_on_drop() {
            let principal = Principal::anonymous();
            {
                let caller_guard = CallerGuard::new(principal);
                assert!(caller_guard.is_ok());
            } // drop caller_guard as it goes out of scope here
            // it is possible to get a guard again:
            assert!(CallerGuard::new(principal).is_ok());
        }
    }

このパターンを拡張することで、たとえば次のようなユースケースに対応できます：

- 呼び出し元ごとにロックしないグローバルロック。この場合、`BTreeSet<Principal>` を使用する代わりに、canister ステートでブーリアン・フラグを設定します。
- 限られた数のプリンシパルだけが同時にメソッドを実行できるようにするガード。このために、`pending_requests.len() >= MAX_NUM_CONCURRENT_REQUESTS` の場合、`CallerGuard::new()` でエラーを返すことができます。
- メソッドが並列に呼び出される回数を制限するガード。これには、`CallerGuard::new()` でチェックされて増加し、`Drop` で減少する、canister ステートのカウンタを使用します。

最後に、メソッドの並列実行を制限するために、複数のメソッドで同じガードを使用できることに注意してください。

### 拒否されたインターcanister 呼び出しを正しく処理します。

#### セキュリティ上の懸念

上記のプロパティ 6 でステートされているように、inter-canister 呼び出しは失敗する可能性があり、その場合は**reject** になります。詳細については[拒否](/references/ic-interface-spec.md#reject-codes)コードを参照してください。例えば、送信側または受信側のcycles が不十分であったり、（メッセー ジキューのような）いくつかのデータ構造が満杯であったりすることが原因。

たとえば、元帳の転送がエラーになった場合、そのエラーを処理するコールバックは、そのエラーを正しく解釈しなければなりません（転送は行わ**れませんでした**）。

#### 推奨事項

canister 間で呼び出しを行う場合は、常にエラー・ケース（拒否）を正しく処理します。これらのエラーは、メッセージが正常に実行されなかったことを意味します。

### canister 間通話は、信頼できる相手に対してのみ行ってください。canisters

#### セキュリティ上の懸念

- 潜在的に悪意のあるcanisters に対してインターcanister 呼び出しを行った場合、DoS問題につながる可能性があり、また率直なデコードに関連する問 題が発生する可能性があります。また、canister の呼 び出しから返されるデータは、信頼できるものでないにもかかわらず、信頼できるも のと見なされる可能性があります。

- canister 、コールバック付きで呼び出された場合、ピア が応答しないと、レシーバーが無期限にストールし、DoSになる可能性があります。canister がそのステートでは、もはやアップグレードできません。復旧には、canister のステー トを消去して再インストールする必要があります。

- 要約すると、これはcanister をDoSにしたり、リソースを過剰に消費したり、canister の動作がインターcanister 呼の応答に依存する場合、ロジックバグにつながる可能性があります。

#### 推奨

- 信頼できるcanisters に対してのみ、インターcanister 呼び出しを行います。

- インターcanister 呼び出しから返されるデータをサニタイズします。

- [ Internet Computer canister ](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister) を[監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)する方法の「悪意のあるcanisters との会話」のセクションを参照。

- [ Internet Computer の現在の制限](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer)、「潜在的に悪意のある、あるいはバグのあるcanisters を呼び出すと、canisters のアップグレードを妨げる可能性がある」セクションを参照してください。

### コールグラフにループがないことを確認してください。

#### セキュリティ上の懸念

コールグラフにループがあると (例:canister A が B を呼び出し、B が C を呼び出し、C が A を呼び出す)、canister デッドロックにつながる可能性があります。

#### 推奨

- このようなループは避けてください！

- 詳細については、[ Internet Computer の](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer) [現在の制限事項](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer)、セクション「コールグラフのループ」を参照してください。

## Canister アップグレード

### アップグレード中のパニックに注意してください

#### セキュリティ上の懸念

`pre_upgrade` でcanister がトラップやパニックを起こすと、canister が恒久 的にブロックされ、アップグレードが失敗したり、まったくできなくなったりします。

#### 推奨事項

- 本当に回復不可能な場合を除き、`pre_upgrade` フックでのパニックやトラップは避けてください。アップグレード前のフックでパニックを起こすとアップグレードができなくなり、アップグレード前のフックは古いコードによって制御されているため、アップグレードが永久にブロックされる可能性があります。

- `post_upgrade` フックのパニックは、ステートが無効な場合に、アップグレードを再試行して無効な状態を修正しようとすることができるようにします。アップグレード後のフックでパニックを起こすと、アップグレードは中止されますが、新しいコードで再試行できます。

- [アップグレードフックをテスト](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades)しましょう ([Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) から)。

- [ Internet Computer canister ](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)の[監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)方法のアップグレードに関するセクションも参照してください (Motoko に焦点を当てていますが)。

- [ Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer) の[現在の制限](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer)、「`pre_upgrade` フックのバグ」セクションを参照してください。

### アップグレード中のタイマーの再定義

#### セキュリティ上の懸念

グローバルタイマーは、canister のWasmモジュールの変更時に無効化されます。[ICの仕様には](../../references/ic-interface-spec#timer)次のようにステートされています：

> 「タイマーは、canister の Wasm モジュールの変更（管理canister の install\_code メソッド、uninstall\_code メソッドの呼び出し、またはcanister がcycles を使い果たした場合）にも無効化されます。特に、canister\_global\_timer関数は、canister （システムAPI関数ic0.global\_timer\_setを使用して）グローバルタイマーを再び設定しない限り、再びスケジュールされることはありません。"

アップグレードは`install_code` のモードであるため、アップグレード中はタイマーが非アクティブになります。

このため、セキュリティ制御やその他の重要な機能がこれらのタイマーの機能に依存している場合、脆弱性が生じる可能性があります。例えば、通貨の為替レートを更新するためにタイマーに依存している DEX は、為替レートが更新されなくなると、裁定取引の機会に対して脆弱になる可能性があります。

グローバルタイマーはMotoko `Timer` メカニズムで内部的に使用されているため、Motoko タイマーも同様です。[プルリクエストの](https://github.com/dfinity/motoko/pull/3542)「アップグレードの話」で説明したように、グローバルタイマーはアップグレード時に破棄され、タイマーはアップグレード後のフックで設定する必要があります。

[このプルリクエストの](https://github.com/dfinity/motoko/pull/3542)「オプトアウト」で説明されているように、Motoko を使用する場合と、`system func timer` を実装する場合では動作が異なります。`timer` 関数はアップグレード後に呼び出されます。canister が定期的なタスクにタイマーを使っていた場合、`timer` 関数は後日グローバルタイマーを再び設定するでしょう。しかし、アップグレードによって`timer` への「予期しない」呼び出しが発生するため、`timer` の呼び出し間の時間は一定ではありません。

rust CDK を使用すると、[set\_timer\_interval](https://docs.rs/ic-cdk/0.6.9/ic_cdk/timer/fn.set_timer_interval.html) の API ドキュメントで説明されているように、アップグレード時に再起動のタイマーも失われます。

#### 推奨

- `pre_upgrade` フックでグローバルタイマーを追跡しましょう。ステートを安定変数に保存します。
- `post_upgrade` フックでタイマーを設定します。
- [recurringTimer](../../motoko/main/base/Timer.md#function-recurringtimer) についてはMotoko のドキュメントを参照してください。
- [set\_timer\_interval](https://docs.rs/ic-cdk/0.6.9/ic_cdk/timer/fn.set_timer_interval.html) については Rust のドキュメントを参照してください。

## HTTP アウトコール

### に機密データ (API キーなど) を保存しないでください。canisters

#### セキュリティ上の懸念

機密データとは、アプリケーションのロジックや動作によって異なる、広い意味でのデータです。ここでは、API キーやトークンのような、一般的に機微とみなされる秘密について、網羅的でないリストを示します：

- 非公開のエンドポイントとのやりとりを許可する秘密。
- 機密データを持つエンドポイントへの問い合わせや変更を許可するシークレット。
- 有料のAPIトークン。

デフォルトでは、canister 内に保存されたデータは暗号化されていません。そのため、canister が悪意のあるレプリカにインストールされると、鍵、トークン、秘密をプレーンテキストで簡単に取得し、盗むことができます。

#### 推奨事項

canister 内に機密データを保存しないようにしてください。

[詳細はこちらをご覧ください。](../integrations/https-outcalls/https-outcalls-how-it-works#compromised-replicas)

[データの機密性セキュリティに関する推奨事項](general-security-best-practices#data-confidentiality-on-the-internet-computer)

### あなたのcanisters が HTTP サーバーで十分に大きなクォータを持っていることを確認してください。

#### セキュリティ上の懸念

HTTPアウトコールが実行されると、サブネット内のレプリカの数によって増幅されます。ターゲットのウェブサーバは1つのリクエストだけでなく、サブネット内のノード数と同じ数のリクエストを受け取ることになります。

ほとんどのウェブサーバは、ある種のレート制限を実装しています。これは、特定の時間内にクライアントがウェブサーバに対して行うことができるリクエストの数を制限するために使用されるメカニズムで、API の乱用や過剰な使用を防止します。

#### 推奨事項

canisters を設計・実装する際には、このようなレート制限を考慮する必要があります。レート制限は、秒単位や分単位など、さまざまな時間単位で実施されます。秒単位で実施する場合は、すべてのサブネットレプリカによる同時リクエストがクォータに違反しないようにしてください。違反すると、一時的あるいは恒久的に禁止される可能性があります。

[詳細](../integrations/https-outcalls/https-outcalls-how-it-works#rate-limiting-by-servers)

### idempotentエンドポイントへのHTTP outcallリクエストのみ

#### セキュリティ上の懸念

前述のように、HTTP outcallが実行されると、サブネット内のレプリカの数によって増幅されます。つまり、問い合わせを受けたエンドポイントは同じリクエストを何度も受け取ることになります。これはエンドポイントのステートを変更するリクエストでは特に危険で、1回の HTTP outcall がエンドポイントのステートを意図せずに何度も変更してしまう可能性があります。

#### 推奨

HTTP outcall によって呼び出されるエンドポイントが idempotent であること、つまりクエリーコールされるエンドポイントが何度呼び出されても同じリクエストペイロードで同じ振る舞いをすることを確認してください。

サーバーによっては idempotency キーの使用をサポートしています。これらのキーは HTTP リクエストのヘッダーとして送信されるランダムな一意文字列です。HTTP outcalls 機能を使うと、それぞれの(正直な)レプリカが送るリクエストはすべて同じ idempotency key を含むことになります。これにより、サーバは重複したリクエスト (同じ idempotency key を持つリクエスト) を認識し、1つだけを処理し、サーバのステートを1度だけ変更することができます。これはサーバーがサポートしなければならない機能であることに注意してください。

[詳細はこちら。](../integrations/https-outcalls/https-outcalls-how-it-works#post-requests-must-be-idempotent)

### HTTP レスポンスが同一であることを保証

#### セキュリティ上の問題

サブネットのレプリカがHTTPレスポンスを受信する場合、これらのレスポンスは同一でなければなりません。そうでないと、コンセンサスが得られず、HTTP レスポンスは拒否されますが、それでも課金されます。

#### 推奨事項

コンセンサスレイヤーに送信される HTTP レスポンスが同一であることを確認します。

理想的には、照会されたエンドポイントから返される HTTP レスポンスは常に同じです。しかし、ほとんどの場合、これを制御することは不可能であり、レスポンスにはランダムなデータが含まれます（例えば、レスポンスにはタイムスタンプ、クッキー値、または何らかの識別子が含まれます）。そのような場合は、[変換関数を](../integrations/https-outcalls/https-outcalls-how-it-works#transformation-function)使用して、ランダムなデータを取り除いたり、関連するデータだけを抽出したりして、各レプリカが受け取るレスポンスが同一であることを保証してください。

これは HTTP レスポンスボディとヘッダに適用されます。変換関数を適用する際には、その両方を考慮するようにしてください。レスポンスヘッダは見落とされがちで、コンセンサスに失敗して失敗につながります。

[詳細はこちら](../integrations/https-outcalls/https-outcalls-how-it-works#responses-must-be-similar)

### HTTP リクエストとレスポンスのサイズを意識しましょう

#### セキュリティに関する懸念

HTTPS アウトコールの[価格は](../integrations/https-outcalls/https-outcalls-how-it-works#pricing)、とりわけ HTTP リクエストのサイズと最大レスポンス・サイズによって決まります。したがって、大きなリクエストが行われた場合、canisterのcycles の残高がすぐになくなる可能性があります。HTTPアウトコールが（ハートビートまたはタイマー呼び出しではなく）ユーザーアクションによってトリガーされる場合など、これは危険です。

#### 推奨事項

HTTPS アウトコールを使用する場合は、HTTP リクエストとレスポンスのサイズに注意してください。発行されるリクエストのサイズとサーバーからの HTTP レスポンスのサイズが適切であることを確認してください。

HTTPアウトコールを行う場合、`max_response_bytes` パラメータを定義して、最大許容応答サイズを設定することが可能であり、強く推奨されます。このパラメータが定義されていない場合、デフォルトは2MBです（HTTPSアウトコール機能のハードレスポンスサイズ制限）。レスポンスのcycle コストは、常に`max_response_bytes` または設定されていない場合は 2MB に基づいて課金されます。

最後に、ユーザーの操作によってこれらの呼び出しがトリガーされる可能性がある場合、ユーザーはHTTPアウトコールのためのcycles コストが発生する可能性があることに注意してください。

[詳細はこちら。](../integrations/https-outcalls/https-outcalls-how-it-works#recipe-for-coding-a-canister-http-call)

### HTTPアウトコールでの入力検証の実行

#### セキュリティ上の懸念

ユーザーが送信したデータを使用する HTTP アウトコールは、さまざまなインジェクション攻撃を受けやすくなります。これは、先に述べたようないくつかの問題につながる可能性があります。

#### 推奨事項

HTTPアウトコールでユーザー送信データを使用する場合は、入力検証を実行してください。

[詳細はこちら](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)

## その他

### システムAPIコールがある場合でも、canister 。

#### セキュリティ上の懸念

canisters はシステム API と相互作用するので、ユニットテストはシステム API を呼び出すことができないため、コードをテストするのが難しくなります。これは単体テストの不足につながるかもしれません。

#### 推奨

- システム API に依存しない疎結合モジュールを作成し、それらをユニットテストしてください。この[推奨を](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent)参照してください ([effective Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html))。

- まだシステムAPIと相互作用する部分については: システムAPIの薄い抽象化を作成し、単体テストでフェイクします。[推奨](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent)事項 ([effective Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)) を参照してください。例えば、以下のように「Runtime」を実装し、テストでは「MockRuntime」を使います（Dimitris Sarlis氏のコード）：

<!-- -->

```
    use ic_cdk::api::{
        call::call, caller, data_certificate, id, print, time, trap,
    };

    #[async_trait]
    pub trait Runtime {
        fn caller(&self) -> Result<Principal, String>;
        fn id(&self) -> Principal;
        fn time(&self) -> u64;
        fn trap(&self, message: &str) -> !;
        fn print(&self, message: &str);
        fn data_certificate(&self) -> Option<Vec<u8>>;
        (...)
    }

    #[async_trait]
    impl Runtime for RuntimeImpl {
        fn caller(&self) -> Result<Principal, String> {
            let caller = caller();
            // The anonymous principal is not allowed to interact with the canister.
            if caller == Principal::anonymous() {
                Err(String::from(
                    "Anonymous principal not allowed to make calls.",
                ))
            } else {
                Ok(caller)
            }
        }

        fn id(&self) -> Principal {
            id()
        }

        fn time(&self) -> u64 {
            time()
        }

        (...)

    }

    pub struct MockRuntime {
        pub caller: Principal,
        pub canister_id: Principal,
        pub time: u64,
        (...)
    }

    #[async_trait]
    impl Runtime for MockRuntime {
        fn caller(&self) -> Result<Principal, String> {
            Ok(self.caller)
        }

        fn id(&self) -> Principal {
            self.canister_id
        }

        fn time(&self) -> u64 {
            self.time
        }

        (...)

    }
```

### canister のビルドを再現可能にします。

#### セキュリティ上の懸念

canister 、それが主張することを実行するかどうかを検証することができるはずです。ICはデプロイされたWASMモジュールのSHA256ハッシュを提供します。これが有用であるためには、canister のビルドが再現可能でなければなりません。

#### 推奨事項

canister のビルドを再現可能にしましょう。この[勧告を](https://mmapped.blog/posts/01-effective-rust-canisters.html#reproducible-builds)参照してください（[effective Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) より）。[これについては開発者向けドキュメントも](/developer-docs/backend/reproducible-builds.md)参照してください。

### あなたのcanister

#### セキュリティ

攻撃された場合、canisters から、アカウント数、内部データ構造のサイズ、安定したメモリなど、関連するメトリクスを取得できると便利です。

#### 推奨

[ canister からメトリクスを公開](https://mmapped.blog/posts/01-effective-rust-canisters.html#expose-metrics)しましょう（[有効な Rustcanisters](https://mmapped.blog/posts/01-effective-rust-canisters.html) から）。

### 時間が厳密に単調であることに依存しないでください。

#### セキュリティ上の懸念

System APIから読み取られる時間は単調ですが、厳密には単調ではありません。従って、2つの呼び出しが同じ時刻を返す可能性があり、時刻APIを使用する際にセキュリティバグにつながる可能性があります。

#### 推奨事項

[ Internet Computer canister を監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)する方法の「時間は厳密には単調ではありません」のセクションを参照してください。

### cycles 残高の枯渇を防ぐ

#### セキュリティ上の懸念

Canisters cycles そのため、 をすべて消費する攻撃に対して、本質的に脆弱になります。cycles

#### 推奨

- これを軽減するために、canister レベルでの監視、早期認証、レート制限を検討してください。また、攻撃者は最も多くのcycles を消費する呼を狙うことに注意。[ Internet Computer canister を監査](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)する方法の「Cycle balance drain attacks」の項を参照。

- 大きな計算が発生し、ステートを変更しないクエリーコールでは、メソッドがアップデートとして呼び出される場合、高価な計算を実行しないことが推奨されます。しかし、クエリーコールは[真正性を保証](general-security-best-practices#certify-query-responses-if-they-are-relevant-for-security)しないので、これはトレードオフであることに留意してください。残念ながら、クエリの実行モード（クエリとしてコールされたかアップデートとしてコールされたか）は、現在のところユーザーコードに直接公開されていません。しかし、例えば、クエリーとしてコールされた場合は`1` を返し、アップデートメソッドの場合は`0` を返す`ic0.data_certificate_present()` をコールすることができます。[認証データについては](/references/ic-interface-spec.md#system-api-certified-data)、インターフェース仕様の[セクションを](/references/ic-interface-spec.md#system-api-certified-data)参照してください。

- 他のcanisters から呼び出す必要があるだけの高価な呼び出しは、実行によって消費されるcycles を補うために、呼び出しと一緒に送信されるある程度のcycles を必要とすることができます。

- 最後に、イングレスメッセージに課金するオプションもありますが、これは現在プラットフォーム自体でサポートされておらず、カスタムソリューションを設計する必要があります。

### イングレス・メッセージ検査に頼らない

#### セキュリティ上の懸念

[canister](../../references/ic-interface-spec#system-api-inspect-message)\_inspect\_message は[クエリーコールとして](../../references/ic-interface-spec#http-query)実行されるため、悪意のあるノードが存在する場合、正しい実行が保証されません。

#### 推奨

あなたのcanisters は、`canister_inspect_message` の正しい実行に依存すべきではありません。これは特に、[アクセス制御チェッ クの](#make-sure-any-action-that-only-a-specific-user-should-be-able-to-do-requires-authentication)ようなセキュリティ上重要なコードを、このメソッドだけで実行すべきではないことを意味します。このようなチェックは、信頼できる実行を保証するために、更新メソッドの一部として実行さ**れなければ**なりません。理想的には、`canister_inspect_message` 関数とガード関数の両方で実行されます。

また、canister 間の呼び出しでは、`canister_inspect_message` は呼び出されないことに注意してください。これは、ガードを使用してアップデートコールの一部としてコードを実行するもう1つの理由です。

## に固有ではありません。Internet Computer

このセクションのベストプラクティスは非常に一般的なものであり、Internet Computer に特化したものではありません。このリストは決して完全なものではなく、過去に問題になった非常に具体的な懸念事項をいくつか挙げているだけです。

### 入力の検証

#### セキュリティ上の懸念

[クエリーコールとアップデートコールで](/references/ic-interface-spec.md#http-interface)送信されるデータは一般的に信頼されません。メッセージサイズの制限は数MBです。これは例えば以下のような問題を引き起こします：

- 検証されていないデータがウェブ UI でレンダリングされたり、他のシステムで表示されたりすると、インジェクション攻撃（XSS など）につながる可能性があります。

- 大きなサイズのメッセージが送信され、canister に保存される可能性があり、ストレージを過剰に消費する可能性があります。

- 大きな入力(例えば大きなリストや文字列)は過剰な計算を引き起こし、DoSを引き起こし、多くのcycles を消費する可能性があります。[ cycles バランスの消耗からの保護も](#protect-against-draining-the-cycles-balance)参照してください。

#### 推奨

- 入力妥当性検証を実施してください。

- [ Internet Computer canister を監査する](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)方法の「大規模データ攻撃」の項を参照（Candid space bomb に注意）。

- [ASVS](https://owasp.org/www-project-application-security-verification-standard/)5.1.4：ASVS5.1.4：構造化されたデータが強く型付けされ、許容される文字、長さ、パターンを含む定義されたスキーマに照らして検証されること（例えば、クレジットカード番号や電話番号、あるいは、郊外と郵便番号/郵便番号が一致することをチェックするなど、関連する2つのフィールドが妥当であることを検証すること）。

### Rust：安全でないRustコードを使わないでください

#### セキュリティ上の懸念

安全でないRustコードは、メモリ破壊の問題を引き起こす可能性があるため危険です。

#### 推奨

- 安全でないコードは可能な限り避けてください。

- [Rust セキュリティガイドラインを](https://anssi-fr.github.io/rust-guide/04_language.html#unsafe-code)参照してください。

- [DFINITY Rustガイドラインを](https://docs.dfinity.systems/dfinity/spec/meta/rust.html#_avoid_unsafe_code)参照してください。

### Rust：整数オーバーフローを避ける

#### セキュリティ上の懸念

Rustの整数はオーバーフローする可能性があります。このようなオーバーフローは、デバッグの設定ではパニックになりますが、リリースのコンパイルでは、その値はただ黙って折り返されます。これは、例えば、整数をインデックスや一意な ID として使ったり、cycles や ICP を計算したりする場合に、セキュリティ上の大きな問題を引き起こす可能性があります。

#### 推奨

- ラップアラウンドする可能性のある整数演算がないか、コードを注意深く見直してください。

- `saturated_add`,`saturated_sub`,`checked_add`,`checked_sub` など、これらの演算の`saturated` または`checked` バリアントを使用してください。`u32` については、[Rust のドキュメントなどを](https://doc.rust-lang.org/std/primitive.u32.html#method.saturating_add)参照してください。

### Rust：金融情報の浮動小数点演算は避けてください

#### セキュリティ上の懸念

Rust の浮動小数点は予期しない動作をする可能性があります。特定の状況下では、精度が低下する可能性があります。ゼロで除算する場合、結果は`-inf` 、`inf` 、`NaN` になる可能性があります。 整数に変換する場合、これは予期しない結果につながる可能性があります。(浮動小数点数には`checked_div` はありません)。

#### 推奨

を使用します。 [`rust_decimal::Decimal`](https://docs.rs/rust_decimal/latest/rust_decimal/)または [`num_rational::Ratio`](https://docs.rs/num-rational/latest/num_rational/).Decimalは10進数を分母とする固定小数点表現を使用し、Ratioは有理数を表します。どちらも`checked_div` を実装しており、ゼロによる除算を扱うことができます。0.1や0.2のようなよく使われる数は、Decimalを使えばより直感的に表現でき、Ratioを使えば正確に表現できます。Rust の浮動小数点数では`0.1 + 0.2 != 0.3` のような丸め誤差が発生しますが、 Decimal では発生しません (https://0.30000000000000004.com/ を参照ください)。Ratio では、希望する精度を明示することができます。Decimal と Ratio のどちらを使っても、精度を管理する必要は残りますが、算術演算を簡単にすることができます。

### 高価な呼び出しについては、キャプチャやプルーフ・オブ・ワークの使用を検討してください。

#### セキュリティ上の懸念

アップデートコールやクエリーコールが、メモリの使用量やcycles の消費量などの点で高価な場合、ボットがcanister を（ストレージを一杯にするなどして）簡単に使えなくしてしまう可能性があります。

#### 推奨事項

dApp がそのような操作を提供する場合、Captchas やプルーフ・オブ・ワークの追加など、ボット対策テクニックを検討してください。例えば、[Internet Identityに](https://github.com/dfinity/internet-identity)キャプチャの実装があります。

<!---

# Canister development security best practices

## Overview

This document contains canister development best practices for both Motoko and Rust. 

## Smart contracts canister control

### Use a decentralized governance system like SNS to make a canister have a decentralized controller

#### Security concern

The controller of a canister can change or update the canister whenever they like. If a canister e.g. stores assets such as ICP, this effectively means that the controller can steal these by updating the canister and transfer the cycles to their account.

#### Recommendation

- Consider passing canister control to a decentralized governance system such as the Internet Computer's Service Nervous System (SNS), so that changes to the canister are only executed if the SNS community approves them collectively through voting. If an SNS is used, use an SNS on the SNS subnet as this guarantees that the SNS is running an NNS-blessed version and maintained as part of the IC. These SNSs will be available soon. See the roadmap [here](https://dfinity.org/roadmap/) and the design proposal [here](https://forum.dfinity.org/t/open-governance-canister-for-sns-design-proposal/10224).
- Another option would be to create an immutable canister smart contract by removing the canister controller completely. However, note that this implies that the canister cannot be upgraded, which may have severe implications in case e.g. a bug were found. The option to use a decentralized governance system and thus being able to upgrade smart contracts is a big advantage of the Internet Computer ecosystem compared to other blockchains.
:::info
Note that, contrary to some other blockchains, also immutable smart contracts need cycles to run, and they can receive cycles.
:::
- It is also possible to implement a DAO [decentralized autonomous organization](https://en.wikipedia.org/wiki/Decentralized_autonomous_organization) on the IC from scratch. If you decide to do this (e.g. along the lines of the [basic DAO example](https://internetcomputer.org/docs/current/samples/dao)), be aware that this is security critical and must be security reviewed carefully. Furthermore, users will need to verify that the DAO is controlled by itself.

### Verify the ownership of smart contracts you depend on

#### Security concern

If a canister depends on another canister smart contract (i.e. makes inter-canister calls to it), it is essential that the canister smart contract that one depends on is owned by a decentralized governance system. Otherwise, i.e. if it has a controller, they could modify the smart contract without others noticing, e.g. to steal assets held by the canister.

#### Recommendation

If you interact with a canister that you require to be decentralized, make sure it is controlled by the NNS, a service nervous system (SNS) or a decentralized governance system, and review under what conditions and by whom the smart contract can be changed.

## Authentication

### Make sure any action that only a specific user should be able to do requires authentication

#### Security concern

If this is not the case, an attacker may be able to perform sensitive actions on behalf of a user, compromising their account.

#### Recommendation

- By design, for every canister call the caller can be identified. The calling [principal](../../references/ic-interface-spec.md#principals) can be accessed using the system API’s methods `ic0.msg_caller_size` and `ic0.msg_caller_copy` (see [here](../../references/ic-interface-spec.md#system-api-imports)). If e.g. Internet Identity is used, the principal is the user identity for this specific origin, see [here](../../references/ii-spec.md#identity-design-and-data-model). If some actions (e.g. access to user’s account data or account specific operations) should be restricted to a principal or a set of principals, then this must be explicitly checked in the canister call, for example as follows in Rust:

<!-- -!->

        // Let pk be the public key of a principal that is allowed to perform
        // this operation. This pk could be stored in the canister's state.
        if caller() != Principal::self_authenticating(pk) {  ic_cdk::trap(...) }

        // Alternatively, if the canister keeps data for different principals
        // in e.g. a map such as BTreeMap<Principal, UserData>, then the canister
        // must ensure that each caller can only access and perform operations
        // on their own data:
        if let Some(user_data) = user_data_store.get_mut(&caller()) {
            // perform operations on the user's data
        }

- In Rust, the `ic_cdk` crate can be used to authenticate the caller using `ic_cdk::api::caller`. Make sure the returned principal is of type `Principal::self_authenticating` and identify the user’s account using the public key of that principal, see the example code above.

- Do authentication as early as possible in the call to avoid unauthenticated actions and potentially expensive operations before authentication. It is also a good idea to [deny service to anonymous users](#disallow-the-anonymous-principal-in-authenticated-calls).

- Do not rely on authentication performed during [ingress message inspection](#do-not-rely-on-ingress-message-inspection).

### Disallow the anonymous principal in authenticated calls

#### Security concern

The caller from the system API (e.g. `ic0::api::caller` in Rust) may also return `Principal::anonymous()`. In authenticated calls, this is probably undesired (and could have security implications) since this would behave like a shared account for anyone that does unauthenticated calls.

#### Recommendation

In authenticated calls, make sure the caller is not anonymous and return an error or trap if it is. This could e.g. be done centrally by using a helper method. In Rust it could e.g. look as follows:

    fn caller() -> Result<Principal, String> {
        let caller = ic0::api::caller();
        // The anonymous principal is not allowed to interact with canister.
        if caller == Principal::anonymous() {
            Err(String::from(
                "Anonymous principal not allowed to make calls.",
            ))
        } else {
            Ok(caller)
        }
    }

## Asset certification

### Use HTTP asset certification and avoid serving your dApp through `raw.icp0.io`

#### Security concern

Dapps on the IC can use [asset certification](https://wiki.internetcomputer.org/wiki/HTTP_asset_certification) to make sure the HTTP assets delivered to the browser are authentic (i.e. threshold-signed by the subnet). If an app does not do asset certification, it can only be served insecurely through `raw.icp0.io` , where no asset certification is checked. This is insecure since a single malicious node or boundary node can freely modify the assets delivered to the browser.

If an app is served through `raw.icp0.io` in addition to `icp0.io`, an adversary may trick users (phishing) into using the insecure raw.icp0.io.

#### Recommendation

- Only serve assets through `<canister-id>.icp0.io` where the service worker verifies asset certification. Do not serve through `<canister-id>.raw.icp0.io`.

- Serve assets using the asset canister (which creates asset certification automatically), or add the `ic-certificate` header including the asset certification as e.g. done in the [NNS dapp](https://github.com/dfinity/nns-dapp) or [Internet Identity](https://github.com/dfinity/internet-identity).

- Check in the canister’s `http_request` method if the request came through raw. If so, return an error and do not serve any assets.

## Canister storage

### Rust: Use `thread_local!` with `Cell/RefCell` for state variables and put all your globals in one basket

#### Security concern

Canisters need global mutable state. In Rust, there are several ways to achieve this. However, some options can lead e.g. to memory corruption.

#### Recommendation

- [Use `thread_local!` with `Cell/RefCell` for state variables](https://mmapped.blog/posts/01-effective-rust-canisters.html#use-threadlocal) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- [Put all your globals in one basket](https://mmapped.blog/posts/01-effective-rust-canisters.html#clear-state) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

### Limit the amount of data that can be stored in a canister per user

#### Security concern

If a user is able to store a big amount of data on a canister, this may be abused to fill up the canister storage and make the canister unusable.

#### Recommendation

Limit the amount of data that can be stored in a canister per user. This limit has to be checked whenever data is stored for a user in an update call.

### Consider using stable memory, version it, test it

#### Security concern

Canister memory is not persisted across upgrades. If data needs to be kept across upgrades, a natural thing to do is to serialize the canister memory in `pre_upgrade`, and deserialize it in `post_upgrade`. However, the available number of instructions for these methods is limited. If the memory grows too big, the canister can no longer be updated.

#### Recommendation

- Stable memory is persisted across upgrades and can be used to address this issue.

- [Consider using stable memory](https://mmapped.blog/posts/01-effective-rust-canisters.html#stable-memory-main) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)). See also the disadvantages discussed there.

- [Version stable memory](https://mmapped.blog/posts/01-effective-rust-canisters.html#version-stable-memory) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- [Test the upgrade hooks](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- See also the section on upgrades in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister) (though focused on Motoko).

- Write tests for stable memory to avoid bugs.

- Some libraries (mostly work in progress or partly unfinished) that people work on:

  - <https://github.com/dfinity/stable-structures/>

  - HashMap: <https://github.com/dfinity/stable-structures/pull/1> (currently not production ready.)

  - <https://github.com/seniorjoinu/ic-stable-memory>

- See [current limitations of the Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer), sections "Long running upgrades" and "\[de\]serialiser requiring additional wasm memory".

- For example, [Internet Identity](https://github.com/dfinity/internet-identity) uses stable memory directly to store user data.

### Consider encrypting sensitive data on canisters

#### Security concern

By default, canisters provide integrity but not confidentiality. Data stored on canisters can be read by nodes / replicas.

#### Recommendation

- Consider end-to-end encrypting any private or personal data (e.g. user’s personal or private information) on canisters.

- The example dapp [encrypted notes](https://github.com/dfinity/examples/tree/master/motoko/encrypted-notes-dapp) illustrates how end-to-end encryption can be done.

### Create backups

#### Security concern

A canister could be rendered unusable so it could never be upgraded again e.g. due to the following reasons:

- It has a faulty upgrade process (due to some bug from the dapp developer).

- The state becomes inconsistent / corrupt because of a bug in the code that persists data.

#### Recommendation

- Make sure methods used in upgrading are tested or the canister becomes immutable.

- It may be useful to have a disaster recovery strategy that makes it possible to reinstall the canister.

- See the "Backup and recovery" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister)

## Inter-canister calls and rollbacks

### Message execution basics

To understand the issues around async inter-canister calls, one needs to understand a few properties about message execution. This is also explained in the [community conversation on security best practices](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s).

We fist provide a few definitions. A **call** is a canister's implementation of either an [update](/references/ic-interface-spec.md#http-call) or [query call](/references/ic-interface-spec.md#http-query) that it exposes. For example, if the Rust CDK is used, these are usually annotated with `#[query]` or `#[update]`, respectively. A **message** is a set of consecutive instructions that a subnet executes for a canister. We'll see in the following that a call can be split into several messages if inter-canister calls are made. The following properties are essential:

- **Property 1**: only a single message is processed at a time per canister. So message execution is sequential, and never parallel.

- **Property 2**: each call (query / update) triggers a message. When an inter-canister call is made using `await`, the code after the call (the callback, highlighted in blue) is executed as a separate message.

:::info
Note that if the code does not `await` the response, the code after the callback (until the next inter-canister call is triggered using `await`) is executed in the same message.


For example, consider the following Motoko code:

![example_highlighted_code](./_attachments/example_highlighted_code.png)

The first message that is executed here are lines 2-3, until the inter-canister call is made using the `await` syntax (orange box). The second message executes lines 3-5: when the inter-canister call returns (blue box). We call this part the _callback_ of the inter-canister call. The two messages involved in this example will always be scheduled sequentially.
:::

- **Property 3**: successfully delivered requests are received in the order in which they were sent. In particular, if a canister A sends `m1` and `m2` to canister B in that order, then, if both are accepted, `m1` is executed before `m2`.

:::info
Note that this property only gives a guarantee on when the messages are executed, but there is no guarantee on the ordering of the responses received.
:::

- **Property 4**: messages from interleaving calls have no reliable execution ordering.

Property 3 provides a guarantee on the execution order of messages on a target canister. However, if multiple calls interleave, one cannot assume additional ordering guarantees for these interleaving calls. To illustrate this, let's consider the above example code again, and assume the method `example` is called twice in parallel, the resulting calls being Call 1 and Call 2. The following illustration shows two possible message orderings. On the left, the first call's messages are scheduled first, and only then the second call's messages are executed. On the right, we see another possible message scheduling, where the first messages of each call are executed first. Your code should result in a correct state regardless of the message ordering.

![example_orderings](./_attachments/example_orderings.png)

- **Property 5**: on a trap / panic, modifications to the canister state for the current message are not applied.

For example, if a trap in the second message (blue box) of the above example occurs, canister state changes resulting from that message, even earlier in the blue box, are discarded. However, note that any state changes from earlier messages and in particular the first message (orange box) have been applied, as that message executed successfully.

- **Property 6**: inter-canister calls are not guaranteed to make it to the destination canister, and if a call does reach the destination canister, the destination canister can trap or return a reject response while processing the call. 

Every inter-canister call is guaranteed to receive a response, either from the canister, or synthetically produced by the protocol. However, the response does not have to be successful, but can also be a reject response. The reject may come from the called canister, but it may also be generated by the Internet Computer. Such system-generated rejects can occur at any time before the call reaches the callee-canister, as well as once the call does reach the callee-canister if the callee-canister traps while processing the call. If the call reaches the callee-canister, the callee-canister can produce a reply or reject response and the system guarantees that the callee-canister's generated reply or reject response gets back to the caller-canister. Thus, it's important that the calling canister handles reject responses as well. A reject response means that the message hasn't been successfully processed by the receiver but doesn't guarantee that the receivers state wasn't changed. 

For more details, refer to the Interface Specification [section on ordering guarantees](/references/ic-interface-spec.md#ordering_guarantees) and the section on [abstract behaviour](/references/ic-interface-spec.md#abstract-behavior) which defines message execution in more detail.

### Avoid traps after await

#### Security concern

Traps / panics roll back the canister state, as described in Property 5 above. So any state change followed by a trap or panic can be risky. This is also an important concern when inter-canister calls are made. If a panic/trap occurs after an `await` to an inter-canister call, then the state is reverted to the snapshot before the inter-canister call callback invocation, and not before the entire call!

This may e.g. lead to the following issues:

- Suppose some state changes are applied and then an inter-canister call is issued. Also, assume that these state changes leave the canister in an inconsistent state, and that state is only made consistent again in the callback. Now if there is a trap in the callback, this leaves the canister in an inconsistent state.
- A concrete bug of this kind is the following. Assume an inter-canister call is issued to transfer funds. In the callback, the canister accounts for having made that transfer by reflecting that fact in the canister storage. However, suppose the callback also updates some usage statistics data, which eventually leads to a trap when some data structure becomes full. As soon as that is the case, the canister ends up in an inconsistent state because the state changes in the callback are no longer applied and thus the transfers are not correctly accounted for.
  ![example_highlighted_code](./_attachments/example_trap_after_await.png)
  This example is also discussed in this [community conversation](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s).
- Another example: if e.g. part of the canister state is locked before an inter-canister call and released in the callback, the lock may never be released if the callback traps.

- Generally, there can be bugs where data is not persisted when the developer expected it to be.

Note that in Rust, from Rust CDK version 0.5.1, any local variables still go out of scope on trap. The CDK actually calls into the `ic0.call_on_cleanup` API to release these resources. This helps to prevent some of the above issues, as e.g. it is possible to use Rust's `Drop` implementation to release locked resources, as we discuss in ["Be aware that there is no reliable message ordering"](#be-aware-that-there-is-no-reliable-message-ordering).

#### Recommendation

- Watch the [community conversation on security best practices](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s) which shows a concrete example of an issue as described here.

- [Don’t lock shared resources across await boundaries](https://mmapped.blog/posts/01-effective-rust-canisters.html#dont-lock) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- [Don’t panic after `await`](https://mmapped.blog/posts/01-effective-rust-canisters.html#panic-await) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- For context: [IC interface spec on message execution](/references/ic-interface-spec.md#message-execution).
- See also: "Inter-canister calls" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister).

### Be aware that there is no reliable message ordering

#### Security concern

As described in the [message execution basics](#message-execution-basics) above, messages (but not entire calls) are processed atomically. In particular, as described in Property 4 above, messages from interleaving calls do not have a reliable execution ordering. Thus, the state of the canister (and other canisters) may change between the time an inter-canister call is started and the time when it returns, which may lead to issues if not handled correctly. These issues are generally called 'Reentrancy bugs' (see e.g. the [Ethereum best practices on reentrancy](https://consensys.github.io/smart-contract-best-practices/attacks/reentrancy/)). Note however that the messaging guarantees (and thus the bugs) on the Internet Computer are different from Ethereum.

We provide two concrete and somewhat similar types of bugs to illustrate potential reentrancy security issues:

- **Time-of-check time-of-use issues:** these occur when some condition on global state is checked before an inter-canister call, and then wrongly assuming the condition still holds when the call returns. For example, one might check if there is sufficient balance on some account, then issue an inter-canister call and finally make a transfer as part of the callback message. When the second inter-canister call starts, it is possible that the condition which was checked initially no longer holds, because other ledger transfers may have happened before the callback of the first call is executed (see also Property 4 above).

- **Double-Spending issues.**: such issues occur when a transfer is issued twice, often because of unfavorable message scheduling. For example, suppose we check if a caller is eligible for a refund and if so, transfer some refund amount to them. When the refund ledger call returns successfully, we set a flag in the canister storage indicating that the caller has been refunded. This is vulnerable to double-spending because the refund method can be called twice by the caller in parallel, in which case it is possible that the messages before issuing the transfer (including the eligibility check) are scheduled before both callbacks. A detailed explanation of this issue can be found in the [community conversation on security best practices](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s).

#### Recommendation

We highly recommend to carefully review any canister code that makes async inter-canister calls (`await`). If two messages access (read or write) the same state, review if there is a possible scheduling of these messages that leads to illegal transactions or inconsistent state.

See also: "Inter-canister calls" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister).

To address issues around message ordering that can lead to bugs, one usually employs locking mechanisms to ensure that e.g. a caller (or anyone) can only execute an entire call (which involves several messages) once at a time. A simple example is also given in the [community conversation](https://www.youtube.com/watch?v=PneRzDmf_Xw&list=PLuhDt1vhGcrez-f3I0_hvbwGZHZzkZ7Ng&index=2&t=4s) mentioned above.

The locks would usually be released in the callback. That bears the risk that the lock may never be released in case the callback traps, as we discussed in [avoid traps after await](#avoid-traps-after-await). In Rust, there is a nice pattern to avoid this issue by using Rust's `Drop` implementation. The example code below shows how one can implement a lock per caller (`CallerGuard`) with a `Drop` implementation. From Rust CDK version 0.5.1, any local variables still go out of scope if the callback traps, so the lock on the caller is released even in that case.

    pub struct State {
        pending_requests: BTreeSet<Principal>,
    }

    thread_local! {
        static STATE: RefCell<State> = RefCell::new(State{pending_requests: BTreeSet::new()});
    }

    pub struct CallerGuard {
        principal: Principal,
    }

    impl CallerGuard {
        pub fn new(principal: Principal) -> Result<Self, String> {
            STATE.with(|state| {
                let pending_requests = &mut state.borrow_mut().pending_requests;
                if pending_requests.contains(&principal){
                    return Err(format!("Already processing a request for principal {:?}", &principal));
                }
                pending_requests.insert(principal);
                Ok(Self { principal })
            })
        }
    }

    impl Drop for CallerGuard {
        fn drop(&mut self) {
            STATE.with(|state| {
                state.borrow_mut().pending_requests.remove(&self.principal);
            })
        }
    }

    #[update]
    #[candid_method(update)]
    async fn example_call_with_locking_per_caller() -> Result<(), String> {
        let caller = ic_cdk::caller();
        // using `?`, we return an error immediately if there is already a call in progress for `caller`.
        let _ = CallerGuard::new(caller)?;
        // do anything, call other canisters
        Ok(())
    } // here the guard goes out of scope and is dropped

    mod test {
        use super::*;

        #[test]
        fn should_obtain_guard_for_different_principals() {
            let principal_1 = Principal::anonymous();
            let principal_2 = Principal::management_canister();
            let caller_guard = CallerGuard::new(principal_1);
            assert!(caller_guard.is_ok());
            assert!(CallerGuard::new(principal_2).is_ok());
        }

        #[test]
        fn should_not_obtain_guard_twice_for_same_principal() {
            let principal = Principal::anonymous();
            let caller_guard = CallerGuard::new(principal);
            assert!(caller_guard.is_ok());
            assert!(CallerGuard::new(principal).is_err());
        }

        #[test]
        fn should_release_guard_on_drop() {
            let principal = Principal::anonymous();
            {
                let caller_guard = CallerGuard::new(principal);
                assert!(caller_guard.is_ok());
            } // drop caller_guard as it goes out of scope here
            // it is possible to get a guard again:
            assert!(CallerGuard::new(principal).is_ok());
        }
    }

This pattern can be extended e.g. to work for the following use cases:

- A global lock that does not only lock per caller. For this, set a boolean flag in the canister state instead of using a `BTreeSet<Principal>`.
- A guard that makes sure that only a limited number of principals are allowed to execute a method at the same time. For this, one can return an error in `CallerGuard::new()` in case `pending_requests.len() >= MAX_NUM_CONCURRENT_REQUESTS`.
- A guard that limits the number of times a method can be called in parallel. For this, use a counter in the canister state that is checked and increased in `CallerGuard::new()` and decreased in `Drop`.

Finally, note that the same guard can be used in several methods to restrict parallel execution of them.

### Handle rejected inter-canister calls correctly

#### Security concern

As stated by the Property 6 above, inter-canister calls can fail in which case they result in a **reject**. See [reject codes](/references/ic-interface-spec.md#reject-codes) for more detail. The caller must correctly deal with the reject cases, as they can happen in normal operation, e.g. because of insufficient cycles on the sender or receiver side, or because some data structures (like message queues) are full.

Not handling the error cases correctly is risky: for example, if a ledger transfer results in an error, the callback dealing with that error must interpret it correctly (the transfer did **not** happen).

#### Recommendation

When making inter-canister calls, always handle the error cases (rejects) correctly. These errors imply that the message has not been successfully executed.

### Only make inter-canister calls to trustworthy canisters

#### Security concern

- If inter-canister calls are made to potentially malicious canisters, this can lead to DoS issues or there could be issues related to candid decoding. Also, the data returned from a canister call could be assumed to be trustworthy when it is not.

- If a canister is called with a callback, the receiver can stall indefinitely if the peer does not respond, resulting in DoS. A canister can no longer be upgraded if it is in that state. Recovery would involve reinstalling, wiping the state of the canister.

- In summary, this can DoS a canister, consume an excessive amount of resources, or lead to logic bugs if the behavior of the canister depends on the inter-canister call response.

#### Recommendation

- Only make inter-canister calls to trustworthy canisters.

- Sanitize data returned from inter-canister calls.

- See "Talking to malicious canisters" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister).

- See [current limitations of the Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer), section "Calling potentially malicious or buggy canisters can prevent canisters from upgrading".

### Make sure there are no loops in call graphs

#### Security concern

Loops in the call graph (e.g. canister A calling B, B calling C, C calling A) may lead to canister deadlocks.

#### Recommendation

- Avoid such loops!

- For more information, see [current limitations of the Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer), section "Loops in call graphs".

## Canister upgrades

### Be careful with panics during upgrades

#### Security concern

If a canister traps or panics in `pre_upgrade`, this can lead to permanently blocking the canister, resulting in a situation where upgrades fail or are no longer possible at all.

#### Recommendation

- Avoid panics / traps in `pre_upgrade` hooks, unless it is truly unrecoverable, so that any invalid state can fixed by upgrading. Panics in the pre-upgrade hook prevent upgrade, and since the pre-upgrade hook is controlled by the old code, it can permanently block upgrading.

- Panic in the `post_upgrade` hook if state is invalid, so that one can retry the upgrade and try to fix the invalid state. Panics in the the post-upgrade hook abort the upgrade, but one can retry with new code.

- [Test the upgrade hooks](https://mmapped.blog/posts/01-effective-rust-canisters.html#test-upgrades) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- See also the section on upgrades in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister) (though focused on Motoko).

- See [current limitations of the Internet Computer](https://wiki.internetcomputer.org/wiki/Current_limitations_of_the_Internet_Computer), section "Bugs in `pre_upgrade` hooks".

### Reinstantiate timers during upgrades

#### Security Concern

Global timers are deactivated upon changes to the canister's Wasm module. The [IC specification](../../references/ic-interface-spec#timer) states this as follows:

> "The timer is also deactivated upon changes to the canister's Wasm module (calling install_code, uninstall_code methods of the management canister or if the canister runs out of cycles). In particular, the function canister_global_timer won't be scheduled again unless the canister sets the global timer again (using the System API function ic0.global_timer_set)."

Upgrade is a mode of `install_code` and hence the timers are deactivated during an upgrade.

This could result in a vulnerability in certain cases where security controls or other critical features rely on these timers to function. For example, a DEX which relies on timers to update the exchange rates of currencies could be vulnerable to arbitraging opportunities if the rates are no longer updated.

Since global timers are used internally by the Motoko `Timer` mechanism, the same holds true for Motoko Timer. As explained in the [pull request](https://github.com/dfinity/motoko/pull/3542) under "The upgrade story", the global timer gets jettisoned on upgrade, and the timers need to be set up in the post-upgrade hook.

As explained in [this pull request](https://github.com/dfinity/motoko/pull/3542) under "Opting out", the behavior is different when using Motoko and implementing `system func timer`. The `timer` function will be called after an upgrade. In case your canister was using timers for recurring tasks, the `timer` function would likely set the global timer again for a later time. However, the time between invocations of `timer` would not be consistent as the upgrade triggered an "unexpected" call to `timer`.

Using the rust CDK, the reccuring timer is also lost on upgrade as explained in the API documentation of [set_timer_interval](https://docs.rs/ic-cdk/0.6.9/ic_cdk/timer/fn.set_timer_interval.html).

#### Recommendation

- Keep track of global timers in the `pre_upgrade` hook. Store any state in stable variables.
- Set timers in the `post_upgrade` hook.
- See the Motoko documentation on [recurringTimer](../../motoko/main/base/Timer.md#function-recurringtimer).
- See the Rust documentation on [set_timer_interval](https://docs.rs/ic-cdk/0.6.9/ic_cdk/timer/fn.set_timer_interval.html).

## HTTP Outcalls

### Do not store sensitive data (e.g. API keys) in canisters

#### Security concern

Sensitive data is a broad term that varies depending on your application logic and behaviour. Here is a non-exhaustive list of secrets that are typically considered sensitive, such as API keys or tokens:
* Secrets that allow interaction with non-public endpoints.
* Secrets that allow querying or modifying endpoints with confidential data.
* API tokens that are fee-based.

By default, the data stored inside your canister is unencrypted. Therefore if your canister is installed in a malicious replica, it can easily retrieve and steal your keys, tokens, and secrets in plain text.

#### Recommendation

Make sure you don’t store sensitive data inside your canister.

[More information.](../integrations/https-outcalls/https-outcalls-how-it-works#compromised-replicas)

[Data confidentiality security recommendations.](general-security-best-practices#data-confidentiality-on-the-internet-computer)

### Ensure your canisters have a sufficiently large quota with the HTTP server

#### Security concern

When an HTTP outcall is performed, it is amplified by the number of replicas in the subnet. The target web server will receive not only one request, but as many requests as the number of nodes in the subnet.

Most web servers implement some sort of rate limiting; this is a mechanism used to restrict the number of requests a client can make to a web server within a specific time period, preventing abuse or excessive usage of their API(s).

#### Recommendation

You should consider such rate limits when designing and implementing your canisters. Rate limits are enforced using different time granularities, e.g., seconds or minutes. For second-granularity enforcement, make sure that the simultaneous requests by all subnet replicas do not violate the quota. Violations may lead to temporary or permanent bans.

[More information.](../integrations/https-outcalls/https-outcalls-how-it-works#rate-limiting-by-servers)

### Only make HTTP outcall requests to idempotent endpoints

#### Security concern

As mentioned before, if an HTTP outcall is performed it is amplified by the number of replicas in the subnet. That means the queried endpoint will receive the same request several times. This is especially risky in requests that change the endpoint state, given that one HTTP outcall could lead to unintentionally changing the endpoint state several times.

#### Recommendation

Make sure the endpoints, called by an HTTP outcall, are idempotent, i.e. the queried endpoint has the same behaviour with the same request payload, no matter the number of times it is called.

Some servers support the use of idempotency keys. These keys are random unique strings submitted in the HTTP request as headers. If used with the HTTP outcalls feature, all requests sent by each (honest) replica will contain the same idempotency key. This allows the server to recognize duplicated requests (i.e requests with the same idempotency key), handle just one and modify the server state only once. Note that this is a feature that must be supported by the server.

[More information.](../integrations/https-outcalls/https-outcalls-how-it-works#post-requests-must-be-idempotent)

### Ensure HTTP responses are identical

#### Security concern

When replicas of a subnet receive HTTP responses, these responses must be identical. Otherwise, consensus won’t be achieved and the HTTP response will be rejected, but still charged.

#### Recommendation

Make sure the HTTP responses sent to the consensus layer are identical.

Ideally the HTTP responses returned by the queried endpoint would always be the same. However, most of the time this is not possible to control and the responses include random data (e.g the response includes timestamps, cookie values or some sort of identifiers). In those cases make sure to use the [transformation functions](../integrations/https-outcalls/https-outcalls-how-it-works#transformation-function) to guarantee that the responses received by each replica are identical by removing any random data or extracting only the relevant data.

This applies to the HTTP response body and headers. Make sure to consider both when applying the transformation functions. Response headers are often overlooked and lead to failure because of failed consensus.

[More information.](../integrations/https-outcalls/https-outcalls-how-it-works#responses-must-be-similar)

### Be aware of HTTP request and response sizes

#### Security concern

The [pricing](../integrations/https-outcalls/https-outcalls-how-it-works#pricing) of HTTPS outcalls is determined by, among other variables, the size of the HTTP request and the maximal response size. Thus, if big requests are made, this could quickly drain the canister’s cycles balance. This can be risky e.g. if HTTP outcalls are triggered by user actions (rather than a heartbeat or timer invocation).

#### Recommendation

When using HTTPS outcalls be mindful of the HTTP request and response sizes. Ensure that the size of the request issued and the size of the HTTP response coming from the server are reasonable.

When making an HTTP outcall it is possible – and highly recommended – to define the `max_response_bytes` parameter, which allows you to set the maximum allowed response size. If this parameter is not defined, it defaults to 2MB (the hard response size limit of the HTTPS outcalls functionality). The cycle cost of the response is always charged based on the `max_response_bytes` or 2MB if not set.

Finally, be aware that users may incur cycles costs for HTTP outcalls in case these calls can be triggered by user actions.

[More information.](../integrations/https-outcalls/https-outcalls-how-it-works#recipe-for-coding-a-canister-http-call)

### Perform input validation in HTTP outcalls

#### Security concern

HTTP outcalls that use user-submitted data are susceptible to various injection attacks. This may lead to several issues, such as the ones previously mentioned.

#### Recommendation

Perform input validation when using user-submitted data in the HTTP outcalls.

[More information.](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)

## Miscellaneous

### Test your canister code even in presence of system API calls

#### Security concern

Since canisters interact with the system API, it is harder to test the code because unit tests cannot call the system API. This may lead to lack of unit tests.

#### Recommendation

- Create loosely coupled modules that do not depend on the system API and unit test those. See this [recommendation](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

- For the parts that still interact with the system API: create a thin abstraction of the System API that is faked in unit tests. See the [recommendation](https://mmapped.blog/posts/01-effective-rust-canisters.html#target-independent) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)). For example, one can implement a ‘Runtime’ as follows and then use the ‘MockRuntime’ in tests (code by Dimitris Sarlis):

<!-- -!->

        use ic_cdk::api::{
            call::call, caller, data_certificate, id, print, time, trap,
        };

        #[async_trait]
        pub trait Runtime {
            fn caller(&self) -> Result<Principal, String>;
            fn id(&self) -> Principal;
            fn time(&self) -> u64;
            fn trap(&self, message: &str) -> !;
            fn print(&self, message: &str);
            fn data_certificate(&self) -> Option<Vec<u8>>;
            (...)
        }

        #[async_trait]
        impl Runtime for RuntimeImpl {
            fn caller(&self) -> Result<Principal, String> {
                let caller = caller();
                // The anonymous principal is not allowed to interact with the canister.
                if caller == Principal::anonymous() {
                    Err(String::from(
                        "Anonymous principal not allowed to make calls.",
                    ))
                } else {
                    Ok(caller)
                }
            }

            fn id(&self) -> Principal {
                id()
            }

            fn time(&self) -> u64 {
                time()
            }

            (...)

        }

        pub struct MockRuntime {
            pub caller: Principal,
            pub canister_id: Principal,
            pub time: u64,
            (...)
        }

        #[async_trait]
        impl Runtime for MockRuntime {
            fn caller(&self) -> Result<Principal, String> {
                Ok(self.caller)
            }

            fn id(&self) -> Principal {
                self.canister_id
            }

            fn time(&self) -> u64 {
                self.time
            }

            (...)

        }

### Make canister builds reproducible

#### Security concern

It should be possible to verify that a canister does what it claims to do. the IC provides a SHA256 hash of the deployed WASM module. In order for this to be useful, the canister build has to be reproducible.

#### Recommendation

Make canister builds reproducible. See this [recommendation](https://mmapped.blog/posts/01-effective-rust-canisters.html#reproducible-builds) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)). See also the [developer docs on this](/developer-docs/backend/reproducible-builds.md).

### Expose metrics from your canister

#### Security concern

In case of attacks, it is great to be able to obtain relevant metrics from canisters, such as number of accounts, size of internal data structures, stable memory, etc.

#### Recommendation

[Expose metrics from your canister](https://mmapped.blog/posts/01-effective-rust-canisters.html#expose-metrics) (from [effective Rust canisters](https://mmapped.blog/posts/01-effective-rust-canisters.html)).

### Don’t rely on time being strictly monotonic

#### Security concern

The time read from the System API is monotonic, but not strictly monotonic. Thus, two subsequent calls can return the same time, which could lead to security bugs when the time API is used.

#### Recommendation

See the "Time is not strictly monotonic" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister).

### Protect against draining the cycles balance

#### Security concern

Canisters pay for their cycles which makes them inherently vulnerable to attacks that consume all their cycles.

#### Recommendation

* Consider monitoring, early authentication, rate limiting on canister level to mitigate this. Also, be aware that an attacker will aim for the call consuming most cycles. See the "Cycle balance drain attacks section" in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister).

* For query calls that cause significant computation and don't modify the state, it is advisable to not execute the expensive computation if the method is called as update. However, keep in mind that query calls [don't provide authenticity guarantees](general-security-best-practices#certify-query-responses-if-they-are-relevant-for-security), so this is a trade-off. Unfortunately, the execution mode of the query (whether it was called as query or update) is currently not directly exposed to the user code. However, one can e.g. call  `ic0.data_certificate_present()` which returns `1` when called as query, and `0` for update methods. See the Interface Specification [section on certified data](/references/ic-interface-spec.md#system-api-certified-data). 

* Expensive calls that only need to be called from other canisters can require some amount of cycles to be sent along with the call to compensate for the cycles consumed by the execution.  

* Finally, it is also an option to charge for ingress messages, but that is not currently supported by the platform itself and a custom solution would need to be designed. 

### Do not rely on ingress message inspection

#### Security concern

The correct execution of [canister_inspect_message](../../references/ic-interface-spec#system-api-inspect-message) is not guaranteed in the presence of a malicious node, because it is executed as a [query call](../../references/ic-interface-spec#http-query).  

#### Recommendation

Your canisters should not rely on the correct execution of `canister_inspect_message`. This in particular means that no security critical code, such as [access control checks](#make-sure-any-action-that-only-a-specific-user-should-be-able-to-do-requires-authentication), should be solely performed in that method. Such checks **must** be performed as part of an update method to guarantee reliable execution. Ideally, they are executed both in the `canister_inspect_message` function and a guard function. 

Also note that for inter-canister calls `canister_inspect_message` is not invoked which is another reason to execute the code as part of the update call by using a guard.

## Nonspecific to the Internet Computer

The best practices in this section are very general and not specific to the Internet Computer. This list is by no means complete and only lists a few very specific concerns that have led to issues in the past.

### Validate inputs

#### Security concern

The data sent in [query and update calls](/references/ic-interface-spec.md#http-interface) is generally untrusted. The message size limit is a few MB. This can e.g. lead the following issues:

- If unvalidated data is rendered in web UIs or displayed in other systems, this can lead to injection attacks (e.g. XSS).

- Messages of big size could be sent and potentially stored in the canister, consuming an excessive amount of storage.

- Big inputs (e.g. big lists or strings) could trigger an excessive amount of computation, resulting in DoS and consuming many cycles. See also [protect against draining the cycles balance](#protect-against-draining-the-cycles-balance).

#### Recommendation

- Perform input validation, see e.g. the [OWASP cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html).

- "Large data attacks" section in [how to audit an Internet Computer canister](https://www.joachim-breitner.de/blog/788-How_to_audit_an_Internet_Computer_canister) (be aware of Candid space bombs).

- [ASVS](https://owasp.org/www-project-application-security-verification-standard/) 5.1.4: Verify that structured data is strongly typed and validated against a defined schema including allowed characters, length and pattern (e.g. credit card numbers or telephone, or validating that two related fields are reasonable, such as checking that suburb and zip/postcode match).

### Rust: Don’t use unsafe Rust code

#### Security concern

Unsafe Rust code is risky because it may introduce memory corruption issues.

#### Recommendation

- Avoid unsafe code whenever possible.

- See the [Rust security guidelines](https://anssi-fr.github.io/rust-guide/04_language.html#unsafe-code).

- Consider the [DFINITY Rust guidelines](https://docs.dfinity.systems/dfinity/spec/meta/rust.html#_avoid_unsafe_code).

### Rust: Avoid integer overflows

#### Security concern

Integers in Rust may overflow. While such overflows lead to panics in the debug configuration, the values are just wrapped around silently in release compilation. This can cause major security issues e.g. when the integers are used as indices, unique IDs, or if cycles or ICP amounts are computed.

#### Recommendation

- Review your code carefully for any integer operations that may wrap around.

- Use the `saturated` or `checked` variants of these operations, such as `saturated_add`, `saturated_sub`, `checked_add` , `checked_sub`, etc. See e.g. the [Rust docs](https://doc.rust-lang.org/std/primitive.u32.html#method.saturating_add) for `u32`.

### Rust: Avoid floating point arithmetic for financial information

#### Security concern

Floats in Rust may behave unexpectedly. There can be undesirable loss of precision under certain circumstances. When dividing by zero, the result could be `-inf`, `inf`, or `NaN`. When converting to integer, this can lead to unexpected results. (There is no `checked_div` for floats.)

#### Recommendation

Use [`rust_decimal::Decimal`](https://docs.rs/rust_decimal/latest/rust_decimal/) or [`num_rational::Ratio`](https://docs.rs/num-rational/latest/num_rational/). Decimal uses a fixed-point representation with base 10 denominators, and Ratio represents rational numbers. Both implement `checked_div` to handle division by zero, which is not available for floats. Numbers in common use like 0.1 and 0.2 can be represented more intuitively with Decimal, and can be represented exactly with Ratio. Rounding oddities like `0.1 + 0.2 != 0.3`, which happen with floats in Rust, do not arise with Decimal (see https://0.30000000000000004.com/ ). With Ratio, the desired precision can be made explicit. With either Decimal or Ratio, although one still has to manage precision, the above make arithmetic easier to reason about.

### For expensive calls, consider using captchas or proof of work

#### Security concern

If an update or query call is expensive e.g. in terms of memory used or cycles consumed, this may make it easy for bots to render the canister unusable (e.g. by filling up it’s storage).

#### Recommendation

If the dApp offers such operations, consider bot prevention techniques such as adding Captchas or proof of work. There is e.g. a captcha implementation in [Internet Identity](https://github.com/dfinity/internet-identity).

-->
