# HTTPSアウトコール：技術概要

## 概要

このページでは、canister HTTPS アウトコール（canister HTTPS リクエスト）の仕組みと、API を使用する際に考慮すべき重要な点について詳しく説明します。また、通常の（Web 2.0）コンピュータプログラムが HTTP 呼び出しを行う場合と比較して、いくつかの制限や違いがあること、およびこの機能をうまく使用するためにプログラマが考慮すべき点があることにも留意してください。この機能を使おうとしているエンジニアの方は、このページを一読して、この機能を素早く理解することをお勧めします。すぐにコーディングに取りかかりたいせっかちな読者は、概念的な部分は飛ばして、すぐに[コーディングのセクションに](#coding-https-outcalls)ジャンプして始めることができます。

:::info
このドキュメントの残りの部分では、基礎となるプロトコルを指して、**HTTPと** **HTTPSの**両方に**HTTP**代表を使用する場合があることに注意してください。
::：

## アーキテクチャ

HTTPS アウトコール機能により、canisters は従来の Web 2.0 HTTP サーバーに HTTP コールを発信することができます。リクエストのレスポンスは、サブネットのレプリカ間のステート発散のリスクなしに、canister の計算で安全に使用することができます。

### ICによるHTTPSアウトコールの処理方法

canister HTTPSアウトコール機能はInternet Computer レプリカの一部として実装され、管理canister のAPIとして公開されます。次に、canister からのリクエストがどのように処理されるかを簡略化して概説します。HTTPリクエスト機能はサブネットのレベルで実現されます。つまり、各サブネットは他のサブネットから独立して、そのcanisters のcanister HTTPリクエストを処理し、HTTPリクエストが実行のために他のサブネットにルーティングされることはありません。

- #### ステップ1：canister は、`http_request` メソッドを使用して管理canister API を呼び出すことで、発信 HTTP リクエストを作成します。
- #### ステップ2：リクエストはサブネットの複製ステート内に一時的に格納されます。
- #### ステップ3：各レプリカのネットワーキング・レイヤの**アダプタが**、定期的（各ラウンド）に、レプリケートされたステートから保留中のHTTPアウトコールをフェッチします。

(実際には、セキュリティ上の理由からアダプタ自体は別のOSレベルのプロセスにサンドボックス化されているため、レプリカプロセスの内部にあるアダプタの「シム」レイヤがこれを行います)。

- #### ステップ 4: 各レプリカ上のアダプタは、HTTP リクエストをターゲットサーバに送信して実行します。
- #### ステップ 5: サーバからの対応する HTTP レスポンスは、サブネットの各レプリカ上のアダプタによって受信され、レプリカ・プロセスのコンポーネントに提供されます。

アダプタは、ネットワーク・レスポンス・サイズを`max_response_bytes` に制限します。デフォルトは $2$ MB で、これより小さい値に設定することもできます。この値はリクエストの価格に影響するので、期待される 応答に対して可能な限り低く設定することが重要です：リクエストの価格に影響を与えるので、期待される 応答に対して合理的に可能な限り低く設定することが重要です。価格はサイズ`max_response_bytes` とともに増加し、実際の応答サイズは考慮されず、最大値のみが考慮されます。

- #### ステップ6:canister の一部として実装されたオプションの変換関数は、レスポンスを 変換するために各レプリカ上のそれぞれのレスポンス上で呼び出されます。

これはレプリカプロセス内のコンポーネントによって行われます。

- #### ステップ7：変換されたレスポンスは、各レプリカのコンセンサスに渡されます。
- #### ステップ8: ICコンセンサスは、少なくとも$2/3$(正確には$2f+1$。ここで$f$はプロトコルが許容する故障レプリカの数)のレプリカが、入力されたリクエストに対して同じレスポンスを持つ場合、そのレスポンスに合意します。

この場合、コンセンサスはこのレスポンスを管理canister API に返します。コンセンサスに達しない場合やその他の問題がある場合は、エラーを返します。

- #### ステップ 9: 管理canister API は、呼び出し元のcanister に応答またはエラーを返します。

![HTTPS outcalls high-level architecture](../_attachments/HTTPS_outcalls_HL_architecture.jpg)

上図は、canister がどのように機能と相互作用し、サブネット・レプリカが外部サーバーと通信するパターンをハイレベルで示したものです。

canister要約すると、HTTP リクエストを実行するために、各レプリカはInternet Computer のコンセンサスレイヤーを通して、外部 Web サーバーから受信した HTTP レスポンスの（オプションで変換された）インスタンスをプッシュします。オプションの変換は、サーバーから異なるレプリカで受信された応答が部分的に異なっていても、それらの違いが排除され、同じ変換された応答がすべての(正直な)レプリカのコンセンサスに提供されることを保証します。これにより、すべてのレプリカでまったく同じレスポンス（またはまったく同じレスポンス）がcanister 、この機能を使用しても発散が起こらないことが保証され、サブネットの複製されたステートマシンのプロパティが保持されます。

この機能では、変換関数とICコンセンサスが重要な役割を果たします：変換機能は、レプリカが受信したレスポンスの差異を確実に除去し、異なるレプリカ上で変換されたレスポンスがまったく同じになるようにします。したがって、コンセンサスは、呼び出し側（canister ）に合意されたレスポンスを提供することができます。コンセンサスを介してレプリカが受信した応答を実行することで、すべてのレプリカがスマートコントラクトWasm実行環境に同じ応答を提供することを保証します。

### 信頼モデルとプログラミングモデル

canister HTTPアウトコールの信頼モデルは、呼び出されたHTTPサーバーの信頼モデルとICの信頼モデルに基づいています：

- HTTPサービスは正直であると仮定され、そうでなければ、サブネットの呼び出しレプリカのいずれにも任意のレスポンスを提供できます。これはWeb 2.0サービスやオラクルからサービスを呼び出す場合も同様で、ブロックチェーンがサービスを呼び出すシナリオと同じ状況になります。
- ICの信頼モデルは、少なくとも2/3$のレプリカが正直であると仮定しています。この場合、正直なレプリカは（オプションの変換後に）同じレスポンスを取得し、コンセンサスはレスポンスに合意できるため、レスポンスを呼び出し元に返すことができますcanister 。
  不正なサーバーは、リクエストを失敗させたり、間違ったデータを提供したりすることが簡単にできます。また、$1/3$以上の不正なサブネットレプリカはリクエストを失敗させることができます。間違ったデータを提供するためには、2/3$以上のレプリカを危険にさらす 必要があります。

この機能のプログラミングモデルは、HTTPアウトコールを行うcanister 、**HTTPクライアントとして**動作します。APIリクエストのようなユースケースでは、これはむしろ簡単で、規格のケースで多くの特別な考慮を必要としません。IC プロトコルスタックは概念的には、canister と従来の HTTP サーバーとの間の通信パイプとみなすことができます。このパイプは、HTTP レスポンスがコンセンサスを通過し、すべての正直なレプリカが実行時にまったく同じレスポンスを受け取ること、つまり呼び出し元のcanister が合意されたレスポンスを受け取ることを確認します。

## HTTPSアウトコールとオラクルの比較

**ブロックチェーンオラクル**、または単なる**オラクルは**、他のユーザーと同様に、クエリを実行したり、イングレスメッセージを送信したりすることでブロックチェーンと対話する外部の当事者です。オラクルはこれまで、ブロックチェーンの世界でスマートコントラクトを従来のWeb 2.0サーバーと通信させる主な手段でした。

基本的なアーキテクチャは、オラクルスマートコントラクトがチェーン上にデプロイされるというものです。ユーザーはこのオラクルコントラクトにリクエストを行い、オラクルコントラクトはリクエストを一時的に保存します。オラクル、すなわちWeb 2.0サーバーは、クエリーコールやアップデートコールを使用してオラクルスマートコントラクトと対話する通常の手段を通じて、オラクルスマートコントラクトから保存されたリクエストを取得します。次に、オラクルは通常のHTTPコールによってWeb 2.0の世界でリクエストを実行し、再びブロックチェーンとの通常の対話パラダイム（アップデートコール）を使用して、オラクルスマートコントラクトにレスポンスを返します。その後、オラクルのスマートコントラクトは、標準のオンチェーン対話を使用して、呼び出したスマートコントラクトに応答を返します。

オラクルアーキテクチャでは、単一のオラクルが元のリクエストを満たすこともあれば、オラクルの分散型ネットワークがそれぞれリクエストを発行し、合意されたレスポンス（エビデンスを含む）をオラクルコントラクトに、そして最終的には呼び出し元のスマートコントラクトに提供することもありますcanister 。見てわかるように、オラクルは事実上HTTPアウトコールと同じ結果を達成しますが、そのサービスに対して支払いを受けたい1つ以上の外部当事者を必要とするという、より複雑なアーキテクチャを持つことになります。したがって、HTTP アウトコールは多くの関連するユースケースでオラクルに取って代わることができ、より強力な信頼モデルで、より低料金、より低リクエスト実行レイテンシで実現できると想定できます。

オラクルのサービスは、追加の機能やサービスを提供することができます。1つの顕著な例は、与えられた情報項目（たとえば資産価格）に対して複数のデータ・ソースを使用し、その結果として正規化された応答（たとえば中央値）を提供することです。別の例としては、スマートコントラクトがオラクルとの対話なしに直接アクセスできるように、取得された過去の価格の選択をチェーン上で利用可能にしておくことです。これらの機能はどちらもcanister HTTP outcalls機能で構築でき、オラクルを必要としません。

全体として、canister HTTP outcalls機能は、オラクルやオラクルネットワークが行うことを、より強力なモデルで実現することを可能にします。canister HTTP outcalls 機能により、Web 2.0 サーバーとのやり取りは、よりシンプルなアーキテクチャのため、手数料の削減と低遅延の恩恵を受けることができます。

## 開発者のメリット

開発者にとって、canister HTTP リクエスト機能は、すでに信頼する必要がある IC に加えて、どの当事者（オラクル）を信頼するかを決定する必要がないという利点があります。単一のオラクルを使うか、複数のオラクルを使って信頼の前提を減らし、よりよい分散化を実現するかを決める必要はなく、むしろHTTPアウトコールですぐに利用できます。

HTTPアウトコールのコストは、確立されたオラクル・プロバイダーにサービスと関連するイングレス・コストを支払うよりもはるかに低いでしょう。開発者は、自明でない信頼に関するすべての決定から解放され、ビジネスロジックに集中し、必要なHTTPコールを単純に行うことができます（そして、対応する変換関数を記述します）。

## エンドユーザーにとっての利点

エンドユーザーにとっても、HTTPアウトコールにはさまざまなメリットがあります。

- **より強固な信頼モデル：**エンドユーザーは、さらなる障害点となるような追加的なパーティに依存しないことで、より強固なセキュリティを得ることができ、それによってより優れた分散化の恩恵を受けることができます。HTTPアウトコールを使って得られる分散化は、サブネットレプリカと呼び出される外部サービスだけで、オラクルサービスのようなサードパーティが関与しないため、達成できる最高のものです。
- **料金の**低減：オラクルの中間マージンが計算から省かれるため、ユーザーは通常、より安価なサービスを利用できます。
- canisterこれは、オラクル・プロバイダーが関連するすべてのサブネットにオラクル・スマート・コントラクトをセットアップしない限り必要であり、ICのサブネット・アーキテクチャの原則にやや反することになります。

## 既知の制限

現在のMVPにはいくつかの制限があり、エンジニアがその機能から何を期待できるのか、また特定のユースケースに適用できるかどうかを知るために、明示する必要があります。これらの制限のいくつかは、将来の機能拡張で対処されるかもしれません。

### レスポンスは "類似 "でなければなりません

サブネットのレプリカが受け取るレスポンスは、各レスポンスに同じ変換関数を適用することができ、変換の結果がどのレプリカでも等しくなるという意味で、"類似 "していなければなりません。したがって、この文脈での「類似」とは、私たちが興味を持っているいくつかのコア情報がすべてのレスポンスで等しく、他の部分は異なっていても、レスポンスには関係ないということを意味します。HTTP API の世界でよくある設定は、レスポンスは構造的には等しいが、レスポンスに含まれるタイムスタンプや識別子などのために、レスポンスで異なる特定のフィールドを含むというものです。

### `POST` リクエストは冪等でなければなりません

この機能が実装される方法は、すべてのHTTPリクエストがサブネットのすべてのレプリカによって行われなければならないことを意味します。これは`GET` だけでなく`POST` リクエストにも当てはまり、後者では`GET` リクエストにはない課題が生じます：基本的なHTTPの原則に従って適切に実装されていれば、`GET` はべき等です。HTTP の基本原則に従って適切に実装されていれば、 は冪等性です。冪等性とは、リクエストが複数回行われたとしても、同じ結果 が得られ、サーバーのステートを変更しないということです。冪等性は`POST` リクエストには適用されません。つまり、canister からの`POST` リクエストは、さらなる予防措置がなければ、$n$ をサブネットのレプリカの数として、$n$ 回コールされたサーバのアップデートにつながる可能性があります。この動作は明らかに意図したものではなく、ほとんどのシナリオで 受け入れられるものでもありません。

このようなシナリオで実際に使われる標準的な解決策の一つは、リクエストに**idempotencyキーを**使うことです。このキーは、異なるレプリカが結果的に送るすべてのリクエストと一緒に 送られるヘッダー中の一意なランダム文字列です。サーバーはリクエストの一つ以外を重複と認識し、それらのリクエストはサーバーの ステートを変更するために考慮されません。この結果、`POST` の意図した動作が、サーバーによって一度だけ適用されることになります。しかしながら、サーバーによって処理されるこの一つのリクエストは、実際には危殆化 したレプリカによって作られたリクエストである可能性があり、意図したリクエス トではないことに注意してください。また、漏洩したレプリカが冪等キーを変更することで、そのリクエストと実際の 意図したリクエストがサーバーによって処理されるかもしれません。

idempotencyキーはいくつかのサーバーでサポートされていますが、すべてのサーバーでサポートされているわけではありません。べき等キーの適用可能性はケースバイケースで評価する必要があります。idempotencyの課題は、将来的にこの機能を拡張して**定足数を**減らすことで、 部分的には軽減できるかもしれません。つまり、canister 、クォーラムサイズを1に定義し、`POST` リクエストを実行するレプリカを1つだけにすることができます。不正なレプリカの問題はまだ残っていますが、冪等性の問題はこの拡張で解決されます。

リクエストが正しく実行されたかどうかをチェックするために、`GET` を介して安全な**リードバック**操作を行うことで、いくつかの課題を軽減することができます。しかし、危殆化したレプリカがステートの全く異なる部分を更新した場合、この方法では確実に検出することはできません。

### 危殆化したレプリカ

そのようなレプリカは、正直なレプリカによって実装されたcanister がサービスに対して気づいていないだけで、いつでも任意の`POST` リクエストを行うことができます。外部サーバーを認証するためにAPIキーやパスワードが保存されている場合、漏洩したレプリカがサーバーを認証するために保存されている平文の認証情報を使用できるという問題にぶつかります。

しかし、この問題はオラクルでも簡単に解決できないことに注意してください：漏洩したオラクルは、漏洩したレプリカと全く同じことができます。`POST`この問題を解決するには、閾値TLSのような高度な(暗号)メカニズムが使用されるか、サーバー側からの何らかのサポートが必要です。例えば、サーバーは与えられたAPIキーに対して行われたすべてのステート変更のログを保持することができ、canister 、このログを安全に読み返すことができ、少なくとも漏洩したレプリカによるコールを検出することができます。リクエスト元のIPアドレスを追跡することは、欠陥のあるレプリカを特定し、それに対して法的措置やその他の措置をとるのに役立つかもしれません。

Internet Computer 、Web 2.0サーバー間の書き込み統合を安全にする一例は、チェーンキー暗号を使用したサブネット署名で`POST` 、サーバーに署名をチェックさせることです。このようなアプローチはサーバーの適応を必要としますが、不誠実なレプリカが任意のリクエストをするという残りのセキュリティ問題を解決することができます。

これまで見てきたように、不正なレプリカが`POST` の呼び出しに与える影響を完全に取り除くには、HTTP 2.0サービスによるサポートが必要かもしれません。このようなサポートがあれば、Web 3.0とWeb 2.0の間のシームレスで安全な相互運用性を実現できます。

### IPv6のみのサポート

現在、IC自体がIPv6ベースのシステムであるため、canister HTTPアウトコール機能はIPv6のみの機能です。将来的には、コミュニティからの要望に応じてIPv4サポートを追加する可能性があります。現在IPv4をサポートしていない主な理由は、ICのすべてのレプリカがそれ自身のIPv4アドレスを取得できるような十分な数のIPv4アドレスを持っていないため、境界ノード上でトラフィックをルーティングするか、IPv4通信を可能にする他のメカニズムを使用する必要があるためです。いずれのアプローチも、Web 2.0サーバーと直接通信するレプリカと比較して、分散化が低下し、信頼モデルが弱くなります。

### サーバーによるレート制限

一般ユーザーにサービスを提供するほとんどのウェブサーバーは、何らかのレート制限を実装しています。レート制限とは、あるIPv4アドレスまたはIPv6プレフィックスから、ある時間間隔でどれだけのリクエストができるかを制限することです。アドレスやプレフィックスの割り当てを使い切ると、このIPやプレフィックスからのリクエストは処理されず、代わりにエラー応答が処理されます。

問題は、サブネット上のすべてのcanisters 、このサブネットのレプリカのIPv6プレフィックスを共有するため、レート制限を実装している各サーバーでクォータを共有することです。パブリックな(認証されていない)ユーザーのためのHTTPサービスの典型的なクォータは、毎秒10$リクエストのオーダーです。actor ここで $n$ はサブネットのレプリカの数です。ここで $n$ はサブネット内のレプリカの数です。このように、クォータはすぐに消費され、レート制限によってスロットリングやレプリカが(一時的に)ブロックされる可能性があります。

登録ユーザーを使い、API キーでリクエストを認可することで、同じサーバー上のユーザーを切り離し、それぞれにクォータを与えることができます。しかしながら、APIキーは全てのレプリカに保存され、レプリカが侵害された場合に公開される可能性があることに注意してください。これはスマートコントラクトのセキュリティモデルで考慮する必要があり、特に`POST` 。詳細については、[漏洩したレプリカに関する議論を](#compromised-replicas)参照してください。

登録ユーザーを使用することで、パブリックAPIのレート制限や、すべてのcanisters がサービスのクォータを共有することで、ほとんど、あるいはすべてではないにしても、多くの問題を解決できると考えているため、現在のところICプロトコルスタックにはレート制限を実装していません。

## 将来の拡張

すでに述べたように、コミュニティの需要に基づいて、将来的に実装することを検討している複数の可能な拡張機能があります。現在のMVPは、機能セットを減らすことでより早く市場に投入できるため、それらを含んでいません。

- **IPv4のサポート：**IPv4サポートは、canisters 、IPv6では利用できずIPv4でのみ利用可能なサーバーに到達するのに役立ちます。
- **縮小クォーラム**:canister は、リクエストに対して縮小クォーラムを使用することを定義することができます。 例えば、リクエストを行うサブネットのレプリカを $n$ 個すべてではなく $1$ 個だけとすることができます。これは`POST` リクエストの冪等性の問題を解決するのに役立ちます。また、信頼は重要ではなく、リクエストの増幅を減らしたいような読み出しの 場合にも面白いかもしれません。

## HTTPS アウトコールのコーディング

次に、canister スマートコントラクトでcanister HTTP リクエストを使いたいエンジニアのために、重要な情報を提供します。canister `POST` リクエストのコンセンサスや冪等性を経由するレスポンスなどを考慮する必要があるためです。[ Internet Computer インターフェース仕様の](/references/ic-interface-spec.md#ic-http_request) API 定義も参照してください。

### canister HTTP 呼び出しのコーディングのレシピ

以下の "レシピ "は、新しいcanister HTTP outcall タイプの実装にどのように取り組むのがベストなのかの青写真を示します。

- #### ステップ1: 「手動で」(例えば、`curl` ツールを使って)、サブネットのレプリカによって行われることをエミュレートするために、同じ HTTP リクエストを短い時間枠(1-2秒)の中で2回行います。
- #### ステップ2: 2つのリクエストで異なる全ての項目を見つけるために、2つのレスポンスを差分します。

ここでは、ボディとヘッダーの両方を考慮することが重要です。あるいは、関心のある核となる情報を特定し、それがどのようにレスポンスとして 抽出されるかを特定します。

- #### ステップ3: 応答の観測された差分または意図された応答に基づいて、各レプリカで等しくなるように応答を変換する**変換関数を**実装します。
- #### ステップ4:`max_response_bytes` パラメータに入力する、このタイプのリクエストに対するサーバーのレスポンスの最大サイズを決定します。

これは多くの場合APIコールに有効で、リクエストするcanister にcycles のオーバーチャージをしないようにすることが重要です。レスポンスサイズが動的に大きく変化する場合、`HEAD` リクエストタイプを使ってこれを行うことができます。`max_response_bytes` パラメータが設定されていない場合、デフォルトのレスポンスサイズである2MBが課金されますが、これは非常に高価です。

- #### ステップ5: リクエストを実装し、ローカルのSDK環境で試してください。

ただし、ローカル環境にはレプリカが1つしかなく、ICサブネット上には$n$個(例えば$n=13$個)のレプリカがあるため、ローカル環境の挙動がICの挙動を反映しないことに注意してください。

:::info
レスポンスの可変部分を特定するときに、レスポンスヘッダーを考慮することを忘れないでくだ さい。ヘッダーにはタイムスタンプのような可変項目が含まれていることがあり、それがコンセンサスを通 過するのを妨げることがあるからです：

:::caution
オプションの`max_response_bytes` パラメータを設定しないと、2MBのレスポンスサイズが想定され、課金さ*れます*。呼のコスト(cycles )を削減するために、予想されるネット ワーク応答サイズの妥当な上限値を、常にこのパラメータに設定することを推奨 します。予想される応答サイズが不明な場合は、前もってHEADリクエストをして応答サイズを決定することができます。
::：

### 変換関数

変換関数の目的は、レプリカ$i$が受け取った各レスポンス$r\_i$を変換することです。変換関数は、ICコンセンサスの一部として応答に合意できるように、正直なレプリカのすべての$r'\_i$が等しくなることを意図して、応答$r\_i$を変換された応答$r'\_i$に変換します。変換関数はcanister のプログラマが提供する必要があり、canister がクエリとして公開します。任意の数の変換関数をcanister で定義することができます。`http_request` を管理canister に呼び出すとき、変換関数をオプションで入力として提供することができます。

作成される HTTP 要求の目的に応じて、変換関数を記述するための異なるアプローチがあります：

- **興味のある情報項目のみを抽出する：**これは例えば、canister に転送される日付と資産価格からなるペアのリスト、または単一の資産価格にすることができます。このアプローチは、完全なHTTPレスポンスがcanister で必要とされず、特定の情報項目のみが必要とされる場合に理にかなっています。
- **リクエストのすべての可変部分を個別に削除し、残りのすべての部分を保持 します。** canister 、関連する情報を抽出します。canister が HTTP リクエストの構造とヘッダーをまだ必要とするとき、これは有用でしょう。

複数の利点があるため、可能な限り最初のアプローチを使用することをお勧めします。価格設定や他の多くのAPIへの呼び出しは、ほとんどが最初のアプローチで動作するはずです。

- 結果として得られるレスポンスのサイズが小さくなります。
- つまり、関数（クエリ）はより少ないCPUcycles で実行されます。IC上のクエリは現在無料ですが、将来的に有料になる可能性があることに注意してください。
- 変換関数は、レスポンス・ボディに対する単純な、あるいはいくつかの単純なJson操作などを通じて、より簡単に実装できることがよくあります。

### エラーとデバッグ

この機能を使用する際に起こりうるエラーは数多くあります。最も重要なものを次に挙げます。

- **SysFatal - Url needs to specify https scheme:**現在、この機能は HTTPS 接続のみを許可しており、プレーン HTTP を使用するとエラーになります。
- **SysFatal - タイムアウトの期限切れ:**リクエストはタイムアウト期間内に処理されないとタイムアウトします。これが発生する重要な例として、コンセンサスを得るのに十分な数の等しい応答がない場合があります。これは、例えば、変換関数が応答のすべての可変部分を考慮するように 正確に書かれていない場合に起こります。
- **SysTransient - Failed to connect: 接続に失敗しました：Connection refused (os error 111):**このエラーは、相手サーバとのTCP接続が確立できなかったことを示しています。これは、呼び出したサーバーがIPv6をサポートしていないことが原因である可能性が高いです。
- **CanisterReject - http\_request request sent with 0cycles, but ...cycles are required**: リクエストがサブネットで処理されるためには、少なくとも必要な量のcycles がリクエストと共に送信される必要があります。
- **CanisterReject - max\_response\_bytes expected to be in the range \[0..2097152\], got ...:**このエラーは、サーバーから受け取ったネットワーク・レスポンスが大きすぎることを示します。これは、レスポンス・サイズが過小評価され、`max_response_bytes` の値が低く設定された場合に発生します。
- **SysFatal - Transformed http response exceeds limit: 2045952:**このエラーは、変換された応答サイズの上限に達したことを示します。これは現在、HTTPSアウトコール機能のハード・レスポンス・サイズ制限です。レスポンスサイズはレスポンスボディとヘッダに基づいて計算されることに注意してください。

この機能を初めて使う開発者は、最初のうちは特定の問題にぶつかる可能性があります。この機能を使い始めるときに最も顕著だと思われる問題を列挙します。

- **特定のタイプのcanister HTTP リクエストがローカルの dfx 環境で動作しても、IC では通常のアプリケーションのサブネットで $n=13$ 個のレプリカが動作しているのに対し、ローカル環境では $1$ 個のレプリカが動作しているため、IC では動作しない可能性があります。**ここで予想される主な問題は、変換関数の不足や問題です。dfx環境とIC上のデプロイメントとのこの違いは、dfx環境の動作方法に起因するものであるため、すぐに変わるものではないことに注意してください：dfx環境は、エンジニアリングプロセス中のすべての長所と短所を備えた単一のレプリカをローカルで実行します。
- **タイムアウトの受信**: HTTP サーバーから返されたリクエストが機能で要求されているように "類似" していない場合、これは変換関数のエラーが原因である可能性が高いです。つまり、変換されたレスポンスはコンセンサスを得るために十分な数の正直なレプリカでまだ等しくないため、レスポンスは IC ブロックに追加されません。最終的に、タイムアウトはこのHTTP outcallに関連するすべてのアーティファクトを削除します。この問題は、サービスに対して行われた複数のリクエストを差分し、変換関数が変換結果の変数部分を保持しないことを確認することでデバッグできます。
- **リクエストの消費量が多すぎるcycles**:canister HTTPSアウトコールは課金されるcycles 。しかし、かなり小さな応答があるリクエストでcycles のコストが頻繁に非常に大きくなる場合、`max_response_bytes` パラメータがリクエストに設定されていないことが原因である可能性が高いです。この場合、システムは最大応答サイズ$2$ MBを想定して課金します。このパラメータは常に、実際に期待される最大応答サイズにできるだけ近い 値に設定し、少なくともこれより大きく、小さくないことを確認してください。`max_response_bytes` パラメータは本文とヘッダの両方で構成され、サーバからのネットワーク応答を指し、canister への最終応答ではありません。

### 価格設定

HTTP outcallsリクエストのためのcycles のコストには、固定部分と変動部分があります。固定部分はHTTPアウトコールに関連する一定のオーバヘッ ドを説明し、一方可変部分はリクエスト中に消費されたリソースに対して課金されま す。他の機能と同様に、コストは大規模なサブネットを考慮してスケーリングされます。現在の価格は[こちらで](../../gas-cost.md)確認できます。

**計算式**

    header_len = header_1.name + header_1.value + ... + header_n.name + header_n.value
    request_size = url.len + transform.name.len + transform.context.len + body.len + header_len
    http_outcall_cost = per_call_cost + per_request_byte_cost * request_size + per_response_byte_cost * max_response_size
    scaling_factor = subnet_size / 13 
    total_cost = scaling_factor * http_outcall_cost

呼び出しで提供されるcycles は、リクエストのコストをカバーするのに十分でなければなりません。過剰なcycles は呼び出し元に返されます。

現在の価格設定はかなり保守的(高価)であると定義されており、価格は将来、価格設定モデルの更新の導入に伴って変更される可能性があります。しかし、金融APIをクエリーするために使用されるような、小さなレスポンスを伴うHTTPアウトコールのコストは、ほとんどのブロックチェーン上のオラクルによる呼び出しに課金される手数料よりも大幅に安い、1USDセントの端数しかかからないことに注意してください。

## コミュニティへの貢献

ドキュメントにまだ記載されていない問題が発生した場合、またはエンジニアがより効果的にこの機能を使用できるようにドキュメントの改善提案がある場合は、canister HTTP リクエストの[フォーラムトピックで](https://forum.dfinity.org/t/enable-canisters-to-make-http-s-requests/9670)お知らせください。

<!---
# HTTPS outcalls: technology overview

## Overview

On this page we provide details on how canister HTTPS outcalls, or canister HTTPS requests, work and important aspects to consider when using the API. We also want to note that there are some limitations and differences  compared to regular (Web 2.0) computer programs making HTTP calls and considerations for programmers for successfully using this feature. Engineers who intend to use this feature are advised to read through this page to get up to speed quickly w.r.t. the feature. The impatient reader who wants to dive into coding right away can skip the conceptual part and jump right away to the [coding section](#coding-https-outcalls) to get started.

:::info
Note that in the remainder of this documentation we may use **HTTP** representative for both **HTTP** and **HTTPS**, referring to the underlying protocol. Practically all HTTP traffic on public networks runs over secured HTTPS these days.
:::

## Architecture

The HTTPS outcalls feature allows canisters to make outgoing HTTP calls to conventional Web 2.0 HTTP servers. The response of the request can be safely used in computations of the canister, without the risk of state divergence between the replicas of the subnet.

### How an HTTPS outcall is processed by the IC

The canister HTTPS outcalls feature is implemented as part of the Internet Computer replica and is exposed as an API of the management canister. We next outline, in a simplified form, how a request made by a canister is processed. The HTTP request functionality is realized at the level of subnets, i.e., each subnet handles canister HTTP requests of its canisters independently of other subnets and HTTP requests are never routed to other subnets for execution.

- #### Step 1: A canister makes an outgoing HTTP request by calling the management canister API using the `http_request` method.
- #### Step 2: The request is stored temporarily in the replicated state of the subnet.
- #### Step 3: Periodically (each round) an **adapter** at the networking layer in each replica fetches the pending HTTP outcalls from replicated state. 
(In fact, a &lsquo;shim&rsquo; layer of the adapter that is inside the replica process does so as the adapter itself is sandboxed into a separate OS-level process for security reasons.)
- #### Step 4: The adapter on each replica executes the HTTP request by sending it to the target server.
- #### Step 5: The corresponding HTTP response from the server is received by the adapter on each replica of the subnet and provided to a component in the replica process. 
The adapter limits the network response size to `max_response_bytes` which defaults to $2$ MB and can be set to lower values. It is important to set this as low as reasonably possible for the expected response as it affects the price of the request: The price increases with the size `max_response_bytes` and the actual response size is not considered, only the maximum.
- #### Step 6: An optional transformation function implemented as part of the canister is invoked on the respective response on each replica to transform the response. 
This is done by a component within the replica process.
- #### Step 7: The transformed response is handed over to consensus on each replica.
- #### Step 8: IC consensus agrees on a response if at least $2/3$ (to be more precise, $2f+1$, where $f$ is the number of faulty replicas tolerated by the protocol) of the replicas have the same response for the request as input. 
In this case, consensus provides this response back to the management canister API, or an error if no consensus can be reached or in case of other problems.
- #### Step 9: The management canister API provides the response or error back to the calling canister.

![HTTPS outcalls high-level architecture](../_attachments/HTTPS_outcalls_HL_architecture.jpg)

The above figure shows a high-level view of how a canister interacts with the feature and the communication patterns of the subnet replicas with external servers.

To summarize, to perform an HTTP request, each replica pushes an (optionally-transformed) instance of the received HTTP response from the external web server through the Internet Computer's consensus layer, so that the replicas of the subnet can agree on the response provided to the canister, based on all server responses received by the replicas. The optional transformation ensures that, if responses received on different replicas from the server are different in some parts, those differences are eliminated and the same transformed response is provided to consensus on every (honest) replica. This guarantees that on every replica the exact same response (or none at all) is used for canister execution, thereby ensuring that divergence does not happen when using this feature and the replicated state machine properties of the subnet are preserved.

As we can see, the transformation function and IC consensus play a crucial role for this feature: The transformation function ensures that differences in the responses received by the replicas are removed and transformed responses on different replicas will be exactly the same, thus enabling consensus to provide the agreed-upon response to the calling canister. By running the responses received by the replicas through consensus, we ensure that every replica provides the same response to the smart contract Wasm execution environment.

### Trust model and programming Model

The trust model for canister HTTP outcalls is based on the trust model of the called HTTP server and that of the IC:
* It is assumed that the HTTP service is honest, otherwise, it can provide any responses to any of the calling replicas of the subnet. This is the case also for calls to the service from a Web 2.0 service or an oracle, thus, the situation is the same as in our scenario of a blockchain making the call to the service.
* The trust model of the IC assumes that at least $2/3$ of the replicas are honest. In this case, the honest replicas will obtain the same response (after optional transformation) and consensus will be able to agree on the response so that the response can be provided back to the calling canister.
A dishonest server can easily make requests fail or provide wrong data. Also, $1/3$ or more of dishonest subnet replicas can make requests fail. For providing wrong data, more than $2/3$ of the replicas need to be compromised.

The programming model for this feature is such that the canister making an HTTP outcall acts as **HTTP client**, i.e., needs to, in the general case, interpret various headers and act accordingly. For use cases such as API requests this is rather straightforward and does not require many specific considerations in the standard case. The IC protocol stack can conceptually be simply seen as a communication pipe between the canister and the conventional HTTP server that makes sure that the HTTP response makes it through consensus and all honest replicas receive the exact same response in execution, i.e., the calling canister receives an agreed-upon response.

## HTTPS outcalls and oracles compared

**Blockchain oracles**, or just **oracles**, are external parties that interact with the blockchain by making queries or sending ingress messages, just like any user. They have so far been the main means in the blockchain world of letting smart contracts communicate with traditional Web 2.0 servers.

The basic architecture is that an oracle smart contract is deployed on chain. Users make requests to this oracle contract, which stores the requests temporarily. An oracle, i.e., Web 2.0 server, obtains the stored request from the oracle smart contract through regular means of interacting with it using queries and update calls. Then the oracle executes the request in the Web 2.0 world by means of a regular HTTP call and provides back the response to the oracle smart contract, again using the normal interaction paradigm with the blockchain (update call). Then the oracle smart contract provides back the response to the calling smart contract using a standard on-chain interaction.

In the oracle architecture, a single oracle may fulfill the original request, or a decentralized network of oracles may each issue the request and then provide an agreed-upon response (including evidence) to the oracle contract and ultimately to the calling canister. As we can see, the oracle would effectively achieve the same result as HTTP outcalls, but with a more complex architecture of requiring one or more external parties that want to be paid for their services. Thus, one can assume that HTTP outcalls can replace oracles for many relevant use cases, and do so in a stronger trust model and with lower fees and lower request execution latency.

Oracle services may provide additional functionality or services. One prominent example is using multiple data sources for a given information item, e.g., an asset price, and then providing a normalized response, e.g., the median price, as a result. Another example is keeping a selection of historic prices that have been retrieved available on chain for direct access by smart contracts without any oracle interaction. Both those functionalities can be built with the canister HTTP outcalls feature and do not require oracles.

Overall, the canister HTTP outcalls feature allows us to achieve what oracles or oracle networks do, but in a stronger model without requiring additional parties that are additional points of failure and want to earn fees for their services. With the canister HTTP outcalls feature, interactions with Web 2.0 servers benefit from reduced fees and lower latency due to the simpler architecture.

## Benefits for developers

For developers, the canister HTTP requests feature has the benefit that they do not need to make a decision on which party (oracle) they want to trust in addition to the IC that they already need to trust anyway. They don't need to decide on whether they want to use a single oracle or multiple oracles to reduce trust assumptions and get better decentralization, but rather they get this out of the box with HTTP outcalls. 

The cost of the HTTP outcall is likely much lower than paying an established oracle provider for their services and the associated ingress cost, thus we have a clear economic advantage to use HTTP calls. Freed from making all those non-trivial trust decisions, a developer can focus on their business logic and simply make the HTTP call they need (and write a corresponding transform function), which is more natural.

## Benefits for end users

End users can benefit from HTTP outcalls in various ways as well.
* **Stronger trust model:** they get stronger security by not relying on any additional parties which resemble further points of failure and thereby benefit from better decentralization. The decentralization we get using HTTP outcalls is the best we can achieve as it only involves the subnet replicas and the external service to be called, but no third parties such as oracle services.
* **Lower fees:** users typically get a cheaper service as oracle middlemen are cut out in the calculation.
* **Lower latency:** the latency of direct HTTP calls is lower than making an (Xnet) request to an oracle contract that then gets polled and serviced by an external oracle service, which materializes in a better user experience when interacting with the canister. This is more pronounced for Xnet requests, which are required unless the oracle provider sets up an oracle smart contract on every relevant subnet, which would somewhat contradict the subnet architecture principle of the IC.

## Known limitations

The current MVP has some limitations that we need to make explicit s.t. engineers know what they can expect from the feature and whether they can apply it for a given use case. Some of those limitations may be addressed with future extensions of the feature.

### Responses must be &ldquo;similar&rdquo;

Responses the replicas of the subnet receive must be &ldquo;similar&rdquo; in the sense that each response can be subjected to the same transformation function and the outcome of the transformation will be equal on every replica. Thus, &ldquo;similar&rdquo; in this context means that some core information we are interested in is equal in all responses and other parts may differ, but are not relevant for the response. A common setting in the world of HTTP APIs is that the responses are structurally equivalent, but contain certain fields that differ in the responses, for example, because of timestamps and identifiers contained in the responses.

### `POST` requests must be idempotent

The way the feature is implemented implies that every HTTP request must be made by every replica of the subnet. This not only applies to `GET`, but also to `POST`, requests and for the latter creates a challenge that we do not have for `GET` requests: A `GET`, if implemented properly according to basic HTTP principles, is idempotent. Idempotency means that, if a request is made multiple times, it yields the same result and does not change the server's state. Idempotency does not apply to `POST` requests, meaning that, without further precautions, a `POST` made from a canister could result in the request leading to an update on the called server $n$ times, with $n$ the number of replicas in the subnet. This behaviour is clearly not intended, nor would it be acceptable in most scenarios.

One standard solution used in practice for such scenarios is the use of an **idempotency key** in the request. This key is a unique random string in a header sent along with all resulting requests by the different replicas. The server identifies all but one of the requests as duplicates and those are not considered for changing the state on the server, only one of them is. This results in exactly the intended behaviour of the `POST` being applied exactly once by the server. However, note that this single request being processed by the server can actually be the request made by a compromised replica and thus not be the intended request. Also, the compromised replica may change the idempotency key and thus lead to its request and the actual intended request getting processed by the server.

Idempotency keys are supported by some servers, but definitely not by all. The applicability of idempotency keys needs to be evaluated on a case-by-case basis. The challenge of idempotency can, in parts, be mitigated with a future extension of this feature allowing for a **reduced quorum**. That is, the canister could define the quorum size to be 1 and have only 1 replica execute the `POST` request. The issue with dishonest replicas clearly still persists, but the idempotency issue gets solved with this extension.

Making a secure **read back** operation via a `GET` to check whether the request has been properly executed can help mitigate some of the challenges. However, a compromised replica making an update on a completely different portion of the state can not reliably be detected using this approach.

### Compromised replicas

As already hinted at above, a compromised replica is a more general problem: Such replica can at any point in time make arbitrary `POST` requests that the canister, as implemented by the honest replicas, is not even aware about, to the service. Whenever we have an API key or password stored for authenticating to the external server, we run into the problem that a compromised replica can use the stored plaintext credentials to authenticate to the server.

Note, however, that this problem cannot be easily solved with oracles either: A compromised oracle can do exactly the same as a compromised replica. Multiple oracles do not solve the problem either for `POST`s. Solving this requires either advanced (cryptographic) mechanisms such as threshold TLS being used or some support from the side of the server. E.g., the server could keep a log of all state changes being done for a given API key and the canister can read back this log securely and at least detect calls made by compromised replicas. Tracking the IP address of the requestor may help in identifying the faulty replica and taking legal and other action against it.

One example of securing the write integration between the Internet Computer and Web 2.0 servers is to sign `POST` requests with a subnet signature using chain key cryptography and to have the server check the signature. Such approach requires adaptation of the server, but can resolve the remaining security problems of dishonest replicas making arbitrary requests.

As we have seen, completely removing the impact that dishonest replicas can have on `POST` calls may require support by the HTTP 2.0 service. With such support, seamless and secure interoperability between Web 3.0 and Web 2.0 can be achieved.

### IPv6-only support

Currently, as the IC itself is an IPv6-based system, the canister HTTP outcalls feature is therefore an IPv6-only feature. IPv4 support may be added in the future depending on demand from the community. The main reason for not supporting IPv4 now is that we do not have sufficiently many IPv4 addresses such that every replica of the IC could get its own IPv4 address and thus we would need to either route traffic over boundary nodes or use other mechanisms to allow for IPv4 communications. Any of those approaches leads to a reduced decentralization and thus a weaker trust model compared to replicas directly communicating with the Web 2.0 server.

### Rate limiting by servers

Most Web servers offering services to the general public implement some sort of rate limiting. Rate limiting means to constrain how many requests can be made from an IPv4 address or IPv6 prefix in a given time interval. Once the quota for an address of prefix is used up, requests from this IP or prefix would not be served, but an according error response would be served instead.

The problem is that all canisters on a subnet share the IPv6 prefixes of the replicas of this subnet and thus the quota at each server that implements rate limiting. Typical quotas of HTTP services for public (unauthenticated) users are in the order of $10$ requests per second. As every replica makes the same request, we have an amplification factor of $n$, where $n$ is the number of replicas in the subnet. Thus, the quotas can get consumed quickly and rate limits may lead to throttling or the replicas being (temporarily) blocked.

Using a registered user and authorizing the requests with an API key can decouple users on the same server and give each of them their own quota, thereby solving the issue with public APIs. Note, however, that the API key is stored in every replica and may be exposed in case a replica is compromised. This needs to be considered in the security model of the smart contract and is specifically relevant for `POST` operations. See the [discussion on compromised replicas](#compromised-replicas) for further information.

We do currently not implement any rate limiting in the IC protocol stack as we think the use of registered users may resolve many if not most or all of the issues with rate limiting of public APIs and all canisters sharing the service's quota.

## Future extensions

As mentioned already further above, there are multiple possible extensions we are considering to implement in the future, based on demand in the community. The current MVP does not include them as it allows us to go to market faster because of the reduced feature set.
* **IPv4 support:** IPv4 support would help canisters reach servers that are not available on IPv6, but IPv4 only.
* **Reduced quorum:** a canister can define that a reduced quorum should be used for a request, e.g., only $1$ replica of the subnet making the request instead of all $n$ replicas. This trivially helps resolve the idempotency problem with `POST` requests. It may also be interesting for making reads where trust does not matter, but where we want to reduce the request amplification instead, e.g., because of quotas the server has in place.

## Coding HTTPS outcalls

We next provide important information for engineers who want to use canister HTTP requests in their canister smart contracts. Using canister HTTP requests is somewhat harder in the general case than doing HTTP requests in a regular enterprise application as we need to consider aspects like responses going through consensus and idempotency of `POST` requests. We also refer the reader to the API definition of the feature in [the Internet Computer interface specification](/references/ic-interface-spec.md#ic-http_request).

### Recipe for coding a canister HTTP call

The following &ldquo;recipe&rdquo; gives you a blueprint of how to best tackle the implementation of a new canister HTTP outcall type.
- #### Step 1:&ldquo;Manually,&rdquo; e.g., using the `curl` tool, make the same HTTP request of interest twice within a short time frame (1-2 seconds) to emulate what would be done by the replicas of a subnet, just with a smaller $n$.
- #### Step 2: Diff the two responses to find all the items that differ in the two requests. 
Both the body and the headers are important to be considered here. Alternatively, identify the core information of interest and how it can be extracted as the response.
- #### Step 3: Implement a **transformation function** that transforms responses such that they are equal on each replica based on the observed diff of the responses or the intended response.
- #### Step 4: Determine the maximum response size of the server's response for this type of request to populate the `max_response_bytes` parameter with it. 
This often works well for API calls and is important to not overcharge the requesting canister in terms of cycles. The `HEAD` request type can be used to do this if responses change dynamically in response size considerably. If the `max_response_bytes` parameters is not set, the default response size of 2MB is charged, which is extremely expensive.
- #### Step 5: Implement the request and try it out in the local SDK environment. 
However, note that the behaviour of the local environment does not reflect that of the IC as there is only one replica in the local environment and $n$, e.g., $n=13$, replicas on an IC subnet.

:::info
Do not forget to consider response headers when identifying the variable parts of your response because headers sometimes contain variable items such as timestamps which may prevent the responses from passing through consensus.
:::

:::caution 
If you do not set the optional `max_response_bytes` parameter, a response size of 2MB will be assumed and charged, which is *extremely expensive*. We recommend to always set the parameter to a reasonable upper bound of the expected network reponse size to reduce cycles cost of the call. If you are unsure of the response size to be expected, you can make a HEAD request upfront to determine the response size.
:::

### Transformation function

The purpose of the transformation function is to transform each response $r_i$ received by a replica $i$, where the $r_i$ s received by different replicas may be different. The transformation function transforms a response $r_i$ to a transformed response $r'_i$ with the intention that all $r'_i$ s of honest replicas be equal in order to be able to agree on the response as part of IC consensus. The transformation function must be provided by the canister programmer and is exposed by the canister as a query. An arbitrary number of transformation functions can be defined by a canister. When making an `http_request` call to the management canister, a transformation function can be optionally provided as input. 

Depending on the purpose of the HTTP request being made, there are different approaches to writing the transformation function:
* **Extract only the information item(s) of interest:** this can, e.g., be a list of pairs each comprising a date and an asset price to be forwarded to the canister, or a single asset price. This approach makes sense if the full HTTP response is not required in the canister, but only specific information items are.
* **Remove all variable parts of the request individually and retain all the remaining parts:** the canister then extracts the relevant information. This may be useful when the canister still needs the structure of the HTTP request and the headers.

We recommend to go with the first approach whenever possible as it has multiple advantages. Calls to pricing and many other APIs should mostly work with the first approach.
* The resulting response is smaller in size.
* The transformation function may be faster to compute, that is, the function (query) is executing with fewer CPU cycles. Note that queries on the IC are for free currently, but are likely to be charged for in the future.
* The transformation function is often easier to implement, e.g., through a simple or few simple Json operations on the response body.

### Errors and debugging

There are a number of error cases that can happen when using this feature. The most important ones are listed next.
* **SysFatal - Url needs to specify https scheme:** the feature currently only allows for HTTPS connections and using plain HTTP leads to an error.
* **SysFatal - Timeout expired:** requests are timed out if not fulfilled within the timeout period. One important instance when this happens is when there are not sufficiently many equal responses to achieve consensus. This happens, for example, when the transformation function is not written accurately to account for all variable parts of responses.
* **SysTransient - Failed to connect: error trying to connect: tcp connect error: Connection refused (os error 111):** this error indicates that a TCP connection could not be established with the other server. This is most likely due to the fact that the server you are calling is not supporting IPv6.
* **CanisterReject - http_request request sent with 0 cycles, but ... cycles are required:** at least the required amount of cycles need to be sent with the request in order for it to get fulfilled by the subnet.
* **CanisterReject - max_response_bytes expected to be in the range [0..2097152], got ...:** this error indicates that the network response received from the server was too large. This happens if the response size is underestimated and the `max_response_bytes` value set too low.
* **SysFatal - Transformed http response exceeds limit: 2045952:** this error indicates that the limit for the transformed response size was reached. This is currently a hard response size limit of the HTTPS outcalls functionality. Note that the response size is computed based on response body and headers.

Developers new to the feature are likely to run into certain problems in the beginning, materializing in one of the following errors. We list the issues we think are the most prominent ones when starting with this feature.
* **If a specific type of canister HTTP request works in the local dfx environment, it may still not work on the IC because the local environment runs $1$ replica, whereas the IC runs $n=13$ replicas on the regular application subnets:** problems here are to be expected when developing such calls, particularly when a developer has not yet gained the necessary experience of working with the feature. The main issues to be expected here are with the lack of or problems with the transformation function. Note that this difference between the dfx environment and a deployment on the IC is not going to change any time soon as it results from the way the dfx environment works: It runs a single replica locally, with all the pros and cons during the engineering process.
* **Receiving a timeout**: if the requests returned by the HTTP server are not &ldquo;similar&rdquo; as required by the feature, this is most likely caused by an error in the transformation function, i.e., the transformed responses are still not equal on sufficiently many honest replicas in order to allow for consensus and thus no response is added to an IC block. Eventually, a timeout removes all artifacts related to this HTTP outcall. This issue is best debugged by diffing multiple requests made to the service and ensuring the transformation function does not retain any of the variable parts in the transformation result.
* **Requests consume too many cycles:** canister HTTPS outcalls are charged cycles, but if requests with rather small responses frequently cost very large amounts of cycles, the likely cause is that the `max_response_bytes` parameter is not set in the request. In this case the system assumes and charges for the maximum response size which is $2$ MB. Always set this parameter to a value as close as possible to the actual maximum expected response size, and make sure it is at least as large and not smaller. The `max_response_bytes` parameter comprises both the body and the headers and refers to the network response from the server and not the final response to the canister.

### Pricing

The cycles cost for a HTTP outcalls request has a fixed and variable component. The fixed part accounts for the constant overheads associated a HTTP outcall, whereas the variable part charges for the resources consumed during the requests. Just like with other functions, the cost is scaled to account for larger subnets. The current prices can be found [here](../../gas-cost.md).

**Formula:** 
```
header_len = header_1.name + header_1.value + ... + header_n.name + header_n.value
request_size = url.len + transform.name.len + transform.context.len + body.len + header_len
http_outcall_cost = per_call_cost + per_request_byte_cost * request_size + per_response_byte_cost * max_response_size
scaling_factor = subnet_size / 13 
total_cost = scaling_factor * http_outcall_cost
```

The cycles provided with the call must be sufficient for covering the cost of the request, excessive cycles are returned to the caller.

The current pricing is defined to be rather conservative (expensive) and prices may change in the future with the introduction of an update of the pricing model. However, note that an HTTP outcall with a small response, like the ones used for querying financial APIs, only costs fractions of a USD cent, which is substantially cheaper than fees charged for a call by oracles on most blockchains.

## Community Contributions

If you are experiencing issues that are not yet described in the documentation or have other suggestions for improvement of the documentation that may help engineers use the feature more effectively, please let us know about it in the [forum topic](https://forum.dfinity.org/t/enable-canisters-to-make-http-s-requests/9670) for canister HTTP requests.

-->
