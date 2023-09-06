# 用語集

## A

#### 帳簿

元帳**アカウントは**、[ 元帳
canister のエントリーのセットです。これは、通常の銀行口座の装いと振る舞いを模倣したスマートコントラクト](#ledger-canister)
で、その単位
は[ICP](#icp)(Internet Computer Protocol)
ユーティリティトークンです。元帳のアカウントは[プリンシパル（
](#principals) ）によって所有され、その所有権は時間の経過とともに変化しません（
）。元帳のすべての口座は、[
](#balance)ICPで測定された正の[残高を](#balance)持ち、その精度は8
小数点以下です。

#### アドレス

元帳の[トランザクションの](#transaction)コンテキストでは、
**アドレスは** [ アカウントと](#account)同義です。

#### actor

アドレス **actor**[actor
 ](https://en.wikipedia.org/wiki/Actor-model) モデルにおけるプリミティブ。これは、
カプセル化されたステートを持つプロセスであり、逐次受信される非同期メッセージを通じて、同時に実行されている他の
actor s と通信します。
actor モデルが[IC](#internet-computer-ic-ic)
に関連するのは、次の理由からです。 [canisters](#canister)IC（スマート
契約の一種）は、actor モデルに従って、並行および非同期
計算を行うからです。

## B

#### 残高

台帳上の[口座の](#account) **残高は**、
すべての預金の合計からすべての引き出しの合計を引いたものです。
退化したケースとして、
元帳に存在しない口座の残高がゼロであると言うことが有用な場合があります。

元帳口座の残高はICPで表示され、
、小数点以下8桁で表されます。したがって、ある口座の最小プラス残高は
0.00000001 または 10^-8[ICP](#icp) です。この金額は
1**e8** と呼ばれることもあります。

#### バッチ

**バッチとは**、[コンセンサスによって](#consensus)
順番が合意された[メッセージの](#messages)集まりのこと。

#### 受益者

[口座の](#account) **受益**者とは、その口座の[
残高を](#balance)所有する[
プリンシパルの](#principal)ことです。
口座の受益者は変更できません。口座の受益者は、
、
口座上で[取引を](#transaction)行うことが許可される場合と許可されない場合があります（[受託](#fiduciary)者を参照）。

#### ブロックチェーン

**ブロックチェーンとは**、
[コンセンサスによって](#consensus)合意された、暗号的にリンクされたブロックの増加するリストのこと。[インターネット
コンピュータでは](#internet-computer-ic)、すべての[サブネットが](#subnet)ブロックチェーンであり、これらのブロックチェーン
は[チェーンキー暗号を](#chain-key)使用して相互作用します。

#### バウンダリノード

**バウンダリ・ノードは** Internet Computer への Nginx ゲートウェイです。これらのノード
は、ネットワークの
[サブネットの](#subnet)ブロックチェーン・ノードに到達できる HTTP ルータとして機能します。
バウンダリ・ノードにはいくつかの目的があります：発見可能性を補助する（
`icp0.io` ドメイン名がバウンダリ・ノードのセットを指す）、
ジオ・アウェアであり、入ってくるリクエストを関係するブロックチェーンをホストする最も近いサブネット
 ノードにルーティングすることができる、クエリの負荷分散を助けることができる、などです。 [canister](#canister)
クエリ
[トランザクションの](#transaction)負荷分散を助けることができ、コンテンツ配信
ネットワークの役割として
暗号的に検証されたデータをキャッシュすることができ、外部の IP アドレス
からの過剰なインタラクションをスロットルすることができ、サービスワーカーをブートストラップ/インストールすることができ、
DDoS 攻撃からサブネットを保護することができます。

#### バーントランザクション

**バーニングトランザクションとは**、[
ICPを](#icp)「バーン」するプロセスのことで、それによって一定量のICPが破棄されます。
主なユースケースは購入です。 [cycles](#cycles)
ICP
と[(XDR](#XDR))の間の現在の為替レートを使用して、1 XDRが
1兆(10E12)cycles に対応するように、ICPが破壊されると同時に、対応するcycles の
量が生成されます。これは、供給元[
口座から](#account) [ ICP 供給
口座への](#icp-supply-account) [
トランザクションとして](#transaction)表されます。

## C

#### キャンディッド

**Candidは**、Internet Computer のために特別に作成されたIDLです。
異なるプログラミング言語（
）で書かれたサービス間の通信（
）を容易にするために、アプリケーション・インターフェースの共通言語を提供します。

#### canister

A **canister**は、**コードと**
**ステートを**バンドルしたスマートコントラクトの一種です。Acanister は[スマート
コントラクトとして](#smart-contract) [インターネット
コンピュータ](#internet-computer-ic)上にデプロイされ、インターネット経由でアクセスすることができます。

#### canister アカウント

**canister 口座は**、以下の者が所有する元帳口座です。 [
canister](#canister)(すなわち、その
 受託者はcanister ）。非canister
 口座とは、受託者が非canister
[プリンシパル](#principal)である元帳口座のことです。

#### canister 開発キット（CDK）

**canister 開発キットは**、canisters の作成と管理に必要な機能を備えたプログラミング言語を提供する IC SDK で使用されるアダプタです。IC SDKにはいくつかのCDKがすでにインストールされているので、お好みの言語で使用することができます。

#### canister 識別子

**canister 識別子**または**canister ID**は、グローバルに一意な
識別子です。 [ canister](#canister)を識別し、
を使用して対話できます。

#### canister 署名

**canister 署名は**、[認証された
変数に](#certified-variables)基づく署名スキームを使用します。公開「鍵」には、
[canister IDと](#canister-identifier)シードが含まれます（したがって、
はすべて、多くの公開鍵を持ちます）。 [canister](#canister)署名
は、canister が署名付きメッセージ
をステートツリーの特定の場所に置いたことを証明する証明書です。詳細は[Internet Computer インタフェース仕様に](/references/ic-interface-spec.md#canister-signatures)記載。

#### canister ステート

**canister ステートとは**、ある時点における
[canister](#canister)の状態全体。canisterの
ステートは、ユーザー・**ステートと** **システム・ステートに**分けられます。ユーザー・ステートは
a [WebAssembly](#WebAssembly)
モジュール・インスタンスであり、システム
ステートは、[インターネット
コンピュータが](#internet-computer-ic-ic) canister に代わって維持する補助ステートです。 [cycles](#cycles)
入出力キュー、その他のメタデータなど。canister は、cycles 、
を消費するときなど暗黙的に、またはメッセージを送信するときなどシステムAPIを通して、
自身のシステムステートと相互作用します。

#### キャッチアップパッケージ (CUP)

**キャッチアップパッケージは**、[サブネット](#subnet)
[レプリカを](#replica)ブートストラップするために必要なすべてのもの
を含むデータバンドルです。

#### 認証済みクエリ

認証**済みクエリとは**、応答が
認証されているクエリコールです。

#### 認証済み変数

認証された変数。 [canister](#canister)
アップデートコール(またはインターcanister コール)の処理で、
[サブネットの](#subnet)正規ステート内に格納することができます。これにより、
[クエリーコールの](#query)処理中に、canister その値に本当にコミットしたことを証明する証明書
をユーザーに返すことができます。

#### チェーン・キー

**チェーン・キー**暗号は、
を構成する[ノードを](#node)オーケストレーションする一連の暗号プロトコルから構成されます。
[Internet Computer](#internet-computer-ic).チェーン・キー暗号の最も目に見える
革新的な点は、Internet Computer が
単一公開鍵を持つことです。これは、スマートウォッチや携帯電話を含む
あらゆるデバイスが、Internet Computer からの
アーティファクトの真正性を検証できるようになるという大きな利点です。

#### コンセンサス

分散コンピューティングにおいて、**コンセンサスとは**、フォールト・トレラントなメカニズムです。

コンセンサスは、[レプリカソフトウェア](#replica)
のコアコンポーネントです。コンセンサスレイヤーは、ピアツーピアのアーティファクトプールから[メッセージ](#messages)
[を](#messages)選択し、他の[サブネットの](#subnet)クロスネットワークストリーム
からメッセージを引き出し、
[バッチに](#batch)整理して、[メッセージルーティングレイヤー](#message-routing)
に配信します。

#### コントローラ

の**コントローラとは**[canister](#canister)のコントローラとは、
canister の管理権限を持つ個人、
組織、その他のcanister のことです。コントローラは、
[プリンシパルによって](#principal)識別される。たとえば、
canister のコントローラは、 のコードをアップグレードできます。 [WebAssembly](#WebAssembly)
 canister のコードをアップグレードしたり、canister を削除できます。

#### cycle

を削除できます。 [Internet Computer](#internet-computer-ic), a **cycle**
は、
処理、メモリ、ストレージ、ネットワーク帯域幅の形で消費されるリソースの測定単位です。すべてのcanister には
cycles アカウントがあり、canister によって消費されたリソースが課金されます。
 Internet Computer のユーティリティ・トークン[（ICP](#icp)）は
 cycles に変換してcanister に転送することができます。Cycles は、**canister**
 メッセージ**間に**添付することによって、canisters 間で
転送することもできます。

ICPは常に、1兆cycles
 が1**XDRに**対応するという慣例を使用して**XDRで**測定されたICP
の現在の価格を使用して、cycles に変換することができます。

## D

#### dapp

A **dapp**分散型アプリケーションは
[canister](#canister)[インターネット](#internet-computer-ic)上で実行される[
コンピュータ](#internet-computer-ic)。

#### データセンター

**データセンター**（DC）は、[インターネット
](#internet-computer-ic) コンピュータに貢献する
[ノードを](#node)ホストする物理的なサイトです。データセンターには、ノードのデプロイに必要なハードウェアと
ソフトウェアのインフラストラクチャが含まれます。DC は、一意の識別子によってInternet Computer 上で
識別されます。

#### ディゾルブ遅延

**ディゾルブ遅延**とは、
[neuron s が](#neuron) [
](#dissolving-state) [ディゾルブに](#dissolved-state) [なる](#dissolving-state)までに費やさなければならない時間のこと。

#### 溶解ステート

溶解**ステート**。 [neuron](#neuron)状態
 ゼロに等しい[溶解遅延を](#dissolve-delay)特徴とするステート。
(従来は、このステートにあるneuron は溶解遅延を「持たない」と言われていました)。このステートでは、neuron は、
 「払い戻し」され、したがってその賭け金は他の場所に移動され、対応する
[neuron 口座は](#neuron-account)閉鎖されます。解散したneuron の
[年齢は](#neuron-age)
ゼロとみなされます。

#### 解散ステート

解散**ステートとは、 の直後に続くステート**。 [neuron](#neuron)
そのオーナーが「解散開始」コマンドを発行した直後に続くステートで、
そして「解散停止」コマンドが発行されるまで、または
解散遅延タイマーが切れるまで続きます。[ディゾルブするneuron 年齢は](#neuron-age)ゼロとみなされます。

## E

#### 実行環境

**実行環境は**、
[レプリカ・ソフトウェアの](#replica)中核レイヤーの1つです。

## F

#### 受託者

ある[口座の](#account) **受託**者は、その口座で
[取引を](#transaction)行うことを許可されている[プリンシパル](#principal)です。そのため、
口座の**所有**者と考えることが有用かもしれません。ただし、
、
口座の[受益者](#beneficiary)である場合もあれば、そうでない場合もあります。[neuron ](#neuron-account)
[口座は](#neuron-account)、受益者と受託者 が一致しない口座の顕著な例です（受益者は  保有者であるのに対し、受託者は
neuron [ガバナンスcanister です）。元帳）口座の受託者は 時間の経過とともに変わることはありません。](#governance-canister)

受託者と受益者の区別は、IC台帳と相互作用する
DeFidapps (canisters)においても重要です。この場合、
受託者はDeFicanister であり、受益者は
DeFicanisterのサービスを利用する個人または組織の[プリンシパル](#principal)です。

## G

#### ガバナンスcanister

**ガバナンスcanister**は、
[](#network-nervous-system-\(NNS\))NNS ガバナンス・システムを実装する[システムcanister](#system-canister)です。すなわち、
とりわけ、[neuron](#neuron)s および
[提案を](#proposal)保存および管理し、NNS
[投票](#voting)環境を実装します。

## I

#### ICP

トークン **Internet Computer Protocol**トークン（ティッカー「ICP」）は、
のユーティリティ・トークンです。 [Internet Computer](#internet-computer-ic).ICP
により、より広範なインターネット・コミュニティは、ICPを
[neuron にロックすることで、](#neuron) Internet Computer ブロックチェーン・ネットワークのガバナンス
に参加することができます。また、ICPは
[cycles](#cycles)に変換することもできます。
[canisters](#canister).

#### ICP供給アカウント

**ICP**供給口座は、残高が常にゼロである準仮想台帳
[口座](#account)です。この口座は、[ICPの](#icp) [発行](#burning)
および[鋳造](#minting)業務において
中心的な役割を果たします。

#### ID

**ID**は、[プリンシパルなど](#principal)、ICP とやり取りするエンティティ（
）を識別するために使用されるバイト文字列です。
[Internet Computer](#internet-computer-ic).ユーザーの場合、
IDは、ユーザーのDERエンコードされた公開鍵のSHA-224ハッシュである。
[ Internet Computer インタフェース仕様の](/references/ic-interface-spec.md)詳細
。

#### インターネットアイデンティティ

I**nternet Identity**は匿名化ブロックチェーン認証システムです。 [Internet Computer](#internet-computer-ic).

#### 誘導プール

[サブネットの](#subnet)ブロックチェーンの誘導**プールは**、
、サブネット上に存在するすべての
[canisters](#canister)のすべての入力キューを集めたものです。

#### イングレスメッセージ

**イングレスメッセージとは** [、](#message)
のエンドユーザーから [canister](#canister)
に送信されるメッセージです。メッセージはエンドユーザーの
[IDに](#identity)対応する
秘密鍵によって署名され、サブネットに参加する
[レプリカの](#replica)1つに送信されます。

#### イングレス・メッセージ履歴

**イングレス・メッセージ履歴**は、
[レプリカによって](#replica)処理されたすべての
[イングレス・メッセージの](#ingress-message)現在のステータスを記録し、メッセージが
[誘導
プ](#induction-pool)ールに正常に含まれたかどうか、および実行された
メッセージの応答を追跡します。

#### 入力キュー

の**入力キューには**[canister](#canister)の入力キューには、canister にバインドされたすべての
[メッセージが](#message)格納されます。
[誘導プール](#induction-pool) も参照してください。canister が実行のために
スケジュールされると、その入力キューからのメッセー ジが実行されます。

#### inter-canister メッセージ

**inter-canister メッセージとは**、あるものから別のものへ
送られる[メッセージ](#message)です。 [canister](#canister)から別のものに送られるメッセージです。inter-canister
 メッセージは、ユーザ主導の[ingress
メッセージとは](#ingress-message)異なります。

#### Internet Computer (IC)

(IC)は **Internet Computer**(IC)は分散型ブロックチェーンで、
、スケーラブルな計算能力を提供します。
[canisters](#canister)地理的に分散した[データセンターで](#data-center) [ノード](#node)
[を](#node)稼働させる独立[ノード
プロバイダーを通じて](#node-provider)、スケーラブルな計算能力を提供します。

## L

#### 台帳canister

**台帳canister**は[システム
canister で、その主な役割は](#system-canister)
[アカウントと](#account)それに対応する
[トランザクションを](#transaction)保存することです。

## M

#### メッセージ

**メッセージとは**[canister](#canister)から
へ、またはユーザーからcanister へ送信されるデータ。

#### メッセージ・ルーティング

メッセージ・**ルーティング**層は
[コンセンサス](#consensus)層から[バッチを](#batch)受け取り、
[インダクション・プールに](#induction-pool)インダクションします。メッセージ・ルーティングは、
のセットをスケジュールします。 [canisters](#canister)をスケジュールして、[入力キューから](#input-queue)
メッセージを実行します。

[メッセージが](#message)実行された後、メッセージ・ルーティング層
は、実行ラウンドで生成されたすべてのメッセージを
出力キューから受け取り、それらのメッセージを
他の[サブネット](#subnet)上のcanisters によって消費されるように、送信ストリームに入れます。

#### 発行トランザクション

**ミンティングトランザクションとは**、
[ICPを](#icp)「発行」するプロセスのことで、これによって一定量のICPが
存在するようになります。
ICP は
[neuron の](#neuron) [投票に](#voting)報いるために発行され、
[IC](#internet-computer-ic)に参加する[ノードプロバイダに](#node-provider)報いるために、[ノードの](#node)稼動を通じて計算能力
を提供します。発行
トランザクションは、[ICP供給アカウントから](#icp-supply-account)
宛先[アカウントへの](#account) [トランザクション](#transaction)
として表されます。

#### Motoko

**Motoko**は、[インターネット・コンピュータ
](#internet-computer-ic) の
プログラミング・モデルを直接サポートするように設計されたプログラミング言語であり、
アプリケーションを効率的に構築し、スマート
契約のためのactor モデルや、WebAssembly へのコンパイルなど、このプラットフォームのより
珍しい機能のいくつかを利用することを容易にします。

## N

#### 非溶解ステート

A [neuron](#neuron)
[溶解して](#dissolved-state)いない、または[
溶解して](#dissolving-state)いない状態は、
**非溶解ステート**（または「エージング」）と呼ばれます。非溶解状態neuronの
はこのように「エイジ」を蓄積します。ただし、いつでも溶解を開始する
と、このエイジはゼロに戻ります。
非溶解(別名 "老化")neuron の溶解遅延パラメータはゼロにはなり得ない。なぜならそのような
neuron はすでに溶解していなければならないから。

#### Network Nervous System (NNS)

(NNS)は **Network Nervous System**(NNS)は[システム
canisters (別名「NNS](#system-canister) canisters 」)の集合体であり、[インターネット
コンピュータの](#internet-computer-ic)すべての側面を管理するシステム
に組み立てられたものです。

#### neuron

A **neuron**は、
、[インターネット
コンピュータ](#internet-computer-ic)・プラットフォームのガバナンス
に関連する[提案の提案](#proposal)および投票を行うことができる[IC](#internet-computer-ic)エンティティです。

責任あるガバナンスに必要な安定性を提供するために、neuronの
は、提案を行い投票できるようにするために、
で一定量の[ICP](#icp)を保管（「ステーク」）する必要があります。
 neuron の ICP ステークは [neuron
アカウントに](#neuron-account)保管されます。 所有者は ガバナンスに関する問題を提案し投票する権利を有し、neuron
[投票に対して](#voting)、ステークされた ICP の金額、 および
[解散
](#non-dissolving-state) 期間に応じて報酬が付与されます。

#### neuron アカウント

**neuron 口座は**、[canister
 口座で](#canister-account)、その[受益者は](#beneficiary)
[](#beneficiary)[neuron](#neuron)
(またはneuron'の所有者）です。[ガバナンス
canister は、すべての](#governance-canister) neuron 口座の
 受託者です。

#### neuron 年齢

**neuron 年齢**は [neuron](#neuron)おおよそ、
その作成から、または
それが最後に[非溶解
ステートに入ってからの](#non-dissolving-state)経過時間を示すパラメータ。neuronの年齢
の計算には、neuron が[溶解に](#dissolving-state)時間を費やしたか、
[が溶解したかを](#dissolved-state)考慮する必要があります。どちらもこの
パラメータをリセットします。

#### ノード

**ノードは**、[インターネット
コンピュータに](#internet-computer-ic)参加するために必要なすべての
ハードウェア、[レプリカ・ソフトウェア](#replica)、および構成
設定をホストする物理または仮想ネットワーク・エンドポイントです。

#### ノードオペレータ

**ノードオペレータ**(**N**[O)と](#internet-computer-ic-ic)は、canister[](#principal)
 
[ ノードを](#node)
 ICへ/から追加/削除する権限を持つ非[プリンシパルの](#principal)こと。

[ノード・プロバイダは](#node-provider)
ハードウェア・セキュリティ・モジュール（HSM）を所有し、
[](#network-nervous-system-\(NNS\)) NNSにHSMを登録します。(HSM登録
プロセスは、HSMに格納された鍵
からICプリンシパルIDを導出し、そのIDをNNSに永続化することから本質的に構成される)。NPは、
登録されたHSMを法人に引き渡し、その法人は、
物理的に「ノードを操作する」（別名、
[レプリカを](#replica)インストールする）権限を得ます。注意点は、
 「通常の」プリンシパルとは対照的に、
1つのプリンシパルIDが1人の人物にしか対応しないように細心の注意が払われますが、HSMは日常的に
手を交換することができるため、
異なるタイミングで多くの人物が同じNOプリンシパルとして行動できることです。

#### ノード・プロバイダ

**ノード・プロバイダ**（NP）とは、canister[](#principal)
 [IC](#internet-computer-ic)
 （別名「ペイアウト・プリンシパル」）に対するノードの参加
から生じる報酬を受け取る非[プリンシパルの](#principal)ことです。
通常、必ずしもそうではありませんが、ノード提供者は[ノードの](#node)所有者であり、
ノードの運用や関連タスクに関与することもあります。ノード・プロバイダは、
複数の[データ
センターの](#data-center)複数のノードから報酬を受け取ることができます。

## O

#### 出力キュー

各 [canister](#canister)canistersは、他の
[メッセージの](#message) **出力キューを**持ちます。

## P

#### ピアツーピア（P2P）

一般的な用法では、**ピアツーピア**（P2P）コンピューティングまたはネットワーキングは、
分散アプリケーションアーキテクチャであり、
参加者がアプリケーションのワークロードを処理するために、処理能力、ディスク
ストレージ、またはネットワーク帯域幅などのリソースを提供できるように、同等の特権を持つコンピュータ[ノードの](#node)
ネットワークにワークロードを分割します。

**ピアツーピア層は**、ユーザーおよび他の
ノードからの
[メッセージと](#message)成果物を収集し、配信します。

[サブネットの](#subnet) [ノードは](#node)、
専用のピアツーピアブロードキャストネットワークを形成し、アーティファクト[（](#ingress-message)
[イングレスメッセージ](#ingress-message)、制御メッセージ、
その署名シェアなど）の安全な
**限定時間／仮想配信**ブロードキャストを容易にします。[コンセンサス・](#consensus)レイヤ
は、この機能の上に構築されます。

#### プリンシパル

**プリンシパルとは**、[インターネット
コンピュータで](#internet-computer-ic)認証できるエンティティ。
これは[Wikipedia
の定義と](https://en.wikipedia.org/wiki/Principal-\(computer-security\))同じ意味です。
 Internet Computer とやりとりするプリンシパルは、
特定の[IDを](#identity)使用します。

#### 提案

**プロポーザルとは**、[IC](#internet-computer-ic)またはそのサブシステム（
）の特定のパラメータ（
）を変更するアクションを記述するステートメントです。プロポーザルは、ID、URL、概要などのさまざまな属性（
）を持つICエンティティとして実装されます。提案は、
[neuron](#neuron)
、ICコミュニティの検討のために提出され、[投票](#voting)プロセスを経て、
、採用または却下されます。採択されたプロポーザルは、
、実行されます。プロポーザルにはいくつかの分類法があり、その中で最も著名なもの
は、プロポーザルを「トピック」にグループ分けし、その採用が
順に、
[サブネットの](#subnet)作成、
[ノードの](#node)サブネットへの追加、
[ICP](#icp)交換レートの変更などの特定のカテゴリのアクションの引き金となります。

#### プロト・ノード

**プロト・ノードは**、ハードウェアとソフトウェアの組み合わせからなる[ICの](#internet-computer-ic)エンティティ
で、
[ノードとは](#node)異なり、
ICにまだ登録されていません。プロトノードは、要するに「待機中のノード」であり、したがって、
ソフトウェアの[レプリカを](#replica)除き、ノードであるために必要なすべての
を持っています。

## Q

#### クエリー

**クエリとは**、ノード上で操作を実行する最適化された方法です。
[canister](#canister)ここで、ステート変更は
保存されません。クエリーは同期的であり、canister をホストしている
 どのノードに対しても行うことができます。クエリーは、結果を検証するための
[コンセンサスを](#consensus)必要としません。

## R

#### レプリカ

**レプリカは**、
[ノードが](#node)
[サブネットに](#subnet)参加するために必要なプロトコル・コンポーネントの集まりです。

#### レジストリ

IC**レジストリは**、
network nervous system [(NNS](#network-nervous-system-\(NNS\)))
で管理され、すべての[サブネットの](#subnet)ブロックチェーンによってアクセスされるシステムのメタデータを管理します。

## S

#### スマートコントラクト

**スマートコントラクトは**、契約や合意の条件に従って、関連するイベントやアクション
を
自動的に実行、制御、または文書化するように設計されたステートフルなコンピュータプログラムです。スマートコントラクト
は、データとコードをバンドルした形で[インターネット
コンピュータ](#internet-computer-ic)上にデプロイすることができます。
[canister](#canister)データとコードを束ねた形で。

canister は、canister のコードを修正することを許可された1つまたは複数の[コントローラー](#controller)
を持つことができ、それによってスマートコントラクトの条件
を修正することができます。canister スマートコントラクトが
不変のコードを持つためには、そのコントローラのリストは空でなければなりません。

#### ステート変更

**ステート変更とは**、
[トランザクション](#transaction)、関数呼び出し、または操作の結果であり、
スマートコントラクトに格納されている情報を変更します。 [canister](#canister)
たとえば、関数が 2 つの数値を足すアップデートコール
を行ったり、リストから名前を削除したりすると、その結果は
canister ステートの変更になります。

#### ステート・マネージャ

**ステートマネージャの**役割は以下の通りです：

- [メッセージ
  ルーティングと](#message-routing) [実行
  環境によって](#execution-environment)実装された
  決定論的ステートマシンが動作する、複製されたステート（の複数のバージョン）の維持。
- 複製されたステートとその
  正式なバージョン（後者は
  具体的な実装とは無関係に理解することができます）との間で行ったり来たりする変換。
- 
  、他の[サブネット](#subnet)
  および/またはユーザーなどの他の利害関係者が、あるステートの一部が本当に有効なサブネットワークから
  発信されたものであることを検証できるようにする、正規化されたステートの一部の証明書の取得。
- 遅れをとったレプリカ
  が追いつけるように、同じサブネット内の他の
  [レプリカと](#replica)標準ステートを同期させる機能を提供すること。

#### サブネット

**サブネット**（サブネットワーク）とは、[コンセンサス](#consensus)
アルゴリズムの独自のインスタンスを実行し、[チェーン
キー](#chain-key)暗号を使用して[ICの](#internet-computer-ic)他の
サブネットと相互作用するサブネットブロックチェーンを生成する[ノード](#node)
の集まりです。

#### システムcanister

**システムcanister**はプリインストールされたものです。
[canister](#canister)
を維持するために必要な特定のタスクを実行するもの。 [Internet Computer](#internet-computer-ic).

## T

#### トランザクション

元帳口座**トランザクションとは**、
[ICP](#icp)をある[口座から](#account)
別の[口座に](#account)移転するプロセス：

- 通常の振替取引。
- [バーン](#burning)取引。
- [発行](#minting)取引。

#### 振替取引

移管**取引とは**、ICPを任意の
通常の元帳[勘定](#account)（すなわち、[ICP供給勘定](#icp-supply-account)を除く任意の元帳勘定
）から
別の通常の元帳勘定に移管するプロセス。

## U

#### ユーザ

**ユーザーとは**、[インターネット
コンピュータと](#internet-computer-ic)相互作用するあらゆるエンティティのこと。ユーザーには、
[IC](#internet-computer-ic) 上に配置されたdapps を使用するエンドユーザー、dapp
 開発者、[ICP](#icp)ユーティリティ・トークンの保有者、および ICP ユーティリティ・トークンの保有者が含まれます。
[neuron](#neuron)ホルダー。

## V

#### 有効セット規則

**有効セット規則とは**、有効な[誘導
プールを](#induction-pool)決定する規則。[イングレス
メッセージと](#ingress-message) [インターcanister
 メッセージは、](#inter-canister-message)
誘導プールに追加される前に、有効なセットルールが維持されるように、特定のチェック
を通過する必要があります。

#### 投票

**投票は**、
[提案が](#proposal)採用および
実装のために選択されるプロセス。その直接の参加者は、
[neuron の両者です：](#neuron)

- 提案の提出。
- 
  投票プロセスはかなり複雑な事業であり、neuron 資格、投票権、
  neuron 従者の連鎖などの側面を含む
  です。これはセキュリティと
  信頼性を念頭に設計されており、
  議決権が少数の
  neuron 所有者の手に集中するのを防ぐために、継続的に改善されています。

## W

#### WebAssembly

**WebAssembly**(略称Wasm）とは、
スタックベースの仮想マシン用のバイナリ命令フォーマット。

## X

#### XDR

**XDRは** *特別引出権（SDR*）の通貨コード。SDRは、国際通貨基金（IMF）が定義・管理する補助的な外国為替資産。SDR は通貨そのものではありませんが、IMF 加盟国が保有する通貨と交換できる権利を表します。したがって、このドキュメントでは SDR を通貨コード**XDR**として参照しています。

<!---
# Glossary

## A

#### account

A ledger **account** is a set of entries in the [ ledger
canister](#ledger-canister), which is a smart contract that
mimics the guise and behavior of a regular banking account, whose unit
of measure is [ICP](#icp) (Internet Computer Protocol)
utility tokens. Ledger accounts are owned by [
principals](#principals), and their ownerships do not change
over time. Every account on the ledger has a positive [
balance](#balance) measured in ICP with a precision of eight
decimals.

#### address

In the context of [transactions](#transaction) on the ledger,
**address** is synonymous with [ account](#account).

#### actor

An **actor** is a primitive in the [actor
model](https://en.wikipedia.org/wiki/Actor-model). It is a process with
encapsulated state that communicates with other concurrently running
actors through asynchronous messages that are received sequentially. The
actor model is relevant to the [IC](#internet-computer-ic-ic)
because [canisters](#canister) on the IC (a type of smart
contract) follow the actor model for concurrent and asynchronous
computation.

## B

#### balance

The **balance** of an [account](#account) on the ledger is
the sum of all deposits minus the sum of all withdrawals. As a
degenerate case, it is sometimes useful to say that an account which is
not present in the ledger has a balance of zero.

The balance of a ledger account is denominated in ICP and is represented
with eight decimals. Thus, the minimum positive balance of an account is
0.00000001 or 10^-8 [ICP](#icp); this amount is sometimes
referred to as one **e8**.

#### batch

A **batch** is a collection of [messages](#messages) whose
order is agreed upon by [consensus](#consensus).

#### beneficiary

The **beneficiary** of an [account](#account) is the [
principal](#principal) who owns the [
balance](#balance) of the account. The beneficiary of an
account cannot be changed. The beneficiary of an account may or may not
be allowed to make [transactions](#transaction) on the
account (see [fiduciary](#fiduciary)).

#### blockchain

A **blockchain** is a growing list of cryptographically linked blocks,
agreed upon by [consensus](#consensus). On the [Internet
Computer](#internet-computer-ic) every [subnet](#subnet) is a blockchain and these blockchains
interact using [chain key cryptography](#chain-key).

#### boundary nodes

**Boundary nodes** are Nginx gateways to the Internet Computer. These nodes
act as HTTP routers through which the network’s
[subnet](#subnet) blockchain nodes can be reached. The
boundary nodes have several purposes: they aid in discover-ability (the
`icp0.io` domain name points to a set of boundary nodes), they are
geo-aware and can route incoming requests to the nearest subnet
[node](#node) that hosts the [canister](#canister)
involved, they can help load balance query
[transactions](#transaction), they can cache
cryptographically verified data in the role of a content distribution
network, they can throttle excessive interactions from an outside IP
address, bootstrapping/installing the service worker, and they can help
protect subnets from DDoS attacks.

#### burning transaction

A **burning transaction** is the process of "burning" [
ICP](#icp), whereby a certain amount of ICP are destroyed.
The main use case is that of purchasing [cycles](#cycles),
through which ICP are destroyed while at the same time a corresponding
amount of cycles is created, using the current exchange rate between ICP
and ([XDR](#XDR)), in such a way that one XDR corresponds to
one trillion (10E12) cycles. It is represented as a [
transaction](#transaction) from the source [
account](#account) to the [ ICP supply
account](#icp-supply-account).

## C

#### Candid

**Candid** is an IDL crafted specifically for the Internet Computer,
providing a common language for application interfaces to facilitate
communication between services that are written in different programming
languages.

#### canister

A **canister** is a type of smart contract that bundles **code** and
**state**. A canister can be deployed as a [smart
contract](#smart-contract) on the [Internet
Computer](#internet-computer-ic) and accessed over the Internet.

#### canister account

A **canister account** is a ledger account owned by a [
canister](#canister) (i.e. whose
[fiduciary](#fiduciary) is a canister). A non-canister
account is a ledger account whose fiduciary is a non-canister
[principal](#principal).

#### canister development kit (CDK)

A **canister development kit** is an adapter used by the IC SDK that provides a programming language with the features necessary to create and manage canisters. The IC SDK comes with a few CDKs already installed for you so you can use them in the language of your choice. 

#### canister identifier

The **canister identifier** or **canister ID** is a globally-unique
identifier that identifies a [ canister](#canister) and can
be used to interact with it.

#### canister signature

A **canister signature** uses a signature scheme based on [certified
variables](#certified-variables). Public “keys” include a
[canister id](#canister-identifier) plus a seed (so that
every [canister](#canister) has many public keys); signatures
are certificates that prove that the canister has put the signed message
at a specific place in its state tree. Details can be found in the [Internet Computer interface specification](/references/ic-interface-spec.md#canister-signatures).

#### canister state

A **canister state** is the entire state of a
[canister](#canister) at a given point in time. A canister’s
state is divided into **user state** and **system state**. The user state is
a [WebAssembly](#WebAssembly) module instance and the system
state is the auxiliary state maintained by the [Internet
Computer](#internet-computer-ic-ic) on behalf of the canister, such
as its compute allocation, balance of [cycles](#cycles),
input and output queues, and other metadata. A canister interacts with
its own system state either implicitly, such as when consuming cycles,
or through the System API, such as when sending messages.

#### catch-up package (CUP)

A **catch-up package** is a data bundle that contains everything needed
to bootstrap a [subnet](#subnet)
[replica](#replica).

#### certified query

A **certified query** is a query call for which the response is
certified.

#### certified variable

A piece of data that a [canister](#canister) can store in its
[subnet](#subnet)’s canonical state in the processing of an
update call (or inter-canister call), so that during the handling of a
[query](#query) call, the canister can return a certificate
to the user that proves that it really committed to that value.

#### chain key

**Chain key** cryptography consists of a set of cryptographic protocols
that orchestrate the [nodes](#node) that make up the
[Internet Computer](#internet-computer-ic). The most visible
innovation of chain key cryptography is that the Internet Computer has a
single public key. This is a huge advantage as it allows any device,
including smart watches and mobile phones, to verify the authenticity of
artifacts from the Internet Computer.

#### consensus

In distributed computing, **consensus** is a fault tolerant mechanism by
means of which a number of [nodes](#node) can reach agreement
about a value or state.

Consensus is a core component of the [replica](#replica)
software. The consensus layer selects [messages](#messages)
from the peer-to-peer artifact pool and pulls messages from the
cross-network streams of other [subnets](#subnet) and
organizes them into a [batch](#batch), which is delivered to
the [message routing](#message-routing) layer.

#### controller

A **controller** of a [canister](#canister) is a person,
organization, or other canister that has administrative rights over the
canister. Controllers are identified by their
[principals](#principal). For example, a controller of a
canister can upgrade the [WebAssembly](#WebAssembly) code of
the canister or delete the canister.

#### cycle

On the [Internet Computer](#internet-computer-ic), a **cycle**
is the unit of measurement for resources consumed in the form of
processing, memory, storage, and network bandwidth. Every canister has a
cycles account to which resources consumed by the canister are charged.
The Internet Computer’s utility token ([ICP](#icp)) can be
converted to cycles and transferred to a canister. Cycles can also be
transferred between canisters by attaching them to an **inter-canister**
message.

ICP can always be converted to cycles using the current price of ICP
measured in **XDR** using the convention that one trillion cycles
correspond to one **XDR**.

## D

#### dapp

A **dapp**, or decentralised application, is a
[canister](#canister) running on the [Internet
Computer](#internet-computer-ic).

#### data center

A **data center** (DC) is a physical site that hosts
[nodes](#node) which contribute to the [Internet
Computer](#internet-computer-ic). It includes the hardware and
software infrastructure required for node deployment. A DC is identified
on the Internet Computer by a unique identifier.

#### dissolve delay

The **dissolve delay** is the amount of time that
[neurons](#neuron) must spend [
dissolving](#dissolving-state) before becoming [dissolved](#dissolved-state).

#### dissolved state

The **dissolved state** is a [neuron](#neuron) state
characterized by a [dissolve delay](#dissolve-delay) equal to
zero. (It is conventionally said that a neuron in this state does not
"have" a dissolve delay.) It is in this state that a neuron can be
"disbursed," hence its stake moved elsewhere, and its corresponding
[neuron account](#neuron-account) closed. The
[age](#neuron-age) of a dissolved neuron is considered to be
zero.

#### dissolving state

A **dissolving state** is a [neuron](#neuron) state that
follows immediately after its owner issues a "start dissolving" command,
and continues until a "stop dissolving" command is issued, or until the
dissolve delay timer runs out. The [age of a dissolving neuron](#neuron-age) is considered to be zero.

## E

#### execution environment

The **execution environment** is one of the core layers of the
[replica](#replica) software.

## F

#### fiduciary

The **fiduciary** of an [account](#account) is the
[principal](#principal) allowed to make
[transactions](#transaction) on the account; as such, it may
be useful to think of it as the **owner** of the account, with the caveat
that it may or may not be the [beneficiary](#beneficiary) of
the account. The [neuron account](#neuron-account) is a
prominent example of an account for which the beneficiary and fiduciary
do not coincide (the fiduciary is the [governance canister](#governance-canister) while the beneficiary is the
neuron holder). The fiduciary of a (ledger) account does not change over
time.

The distinction between fiduciary and beneficiary is also important for
DeFi dapps (canisters) that interact with the IC ledger: in this case,
the fiduciary is the DeFi canister while the beneficiary is the
individual or organisation [principal](#principal) that uses the
DeFi canister’s services.

## G

#### governance canister

The **governance canister** is a [system canister](#system-canister) that implements the
[NNS](#network-nervous-system-(NNS)) governance system, i.e.,
among others, stores and manages [neurons](#neuron) and
[proposals](#proposal), and implements the NNS
[voting](#voting) environment.

## I

#### ICP

The **Internet Computer Protocol** token (ticker "ICP") is the utility
token of the [Internet Computer](#internet-computer-ic). ICP
allows the broader internet community to participate in the governance
of the Internet Computer blockchain network by locking ICP in
[neurons](#neuron). ICP can also be converted into
[cycles](#cycles), which are then used to power
[canisters](#canister).

#### ICP supply account

The **ICP supply account** is a quasi-fictitious ledger
[account](#account) whose balance is always zero. It has a
central role in [ICP](#icp) [burning](#burning)
and [minting](#minting) operations.

#### identity

An **identity** is a byte string that is used to identify an entity,
such as a [principal](#principal), that interacts with the
[Internet Computer](#internet-computer-ic). For users, the
identity is the SHA-224 hash of the DER-encoded public key of the user.
[The Internet Computer interface specification](/references/ic-interface-spec.md) has more
detail.

#### Internet Identity

**Internet Identity** is an anonymizing blockchain authentication system
running on the [Internet Computer](#internet-computer-ic).

#### induction pool

The **induction pool** of a [subnet](#subnet) blockchain is
the collection of all [input queues](#input-queue) of all
[canisters](#canister) residing on the subnet.

#### ingress message

An **ingress message** is a [message](#message) sent by an
end-user to a [canister](#canister) running on a
[subnet](#subnet) blockchain. The message is signed by the
secret key corresponding to the end-user’s
[identity](#identity) and sent to one of the
[replicas](#replica) that participate in the subnet.

#### ingress message history

The **ingress message history** records the current status of every
[ingress message](#ingress-message) processed by a
[replica](#replica) and keeps track of whether messages were
successfully included in the [induction
pool](#induction-pool) and the responses of executed
messages.

#### input queue

The **input queue** of a [canister](#canister) contains all
[messages](#message) bound for the canister. See also
[induction pool](#induction-pool). When the canister is
scheduled for execution, messages from its input queue will be executed.

#### inter-canister message

An **inter-canister message** is a [message](#message) sent
from one [canister](#canister) to another. Inter-canister
messages are different from user-initiated [ingress
messages](#ingress-message).

#### Internet Computer (IC)

The **Internet Computer** (IC) is a decentralized blockchain that
provides scalable compute capacity for running
[canisters](#canister) through independent [node
providers](#node-provider) running [nodes](#node)
in geographically distributed [data centers](#data-center).

## L

#### ledger canister

The **ledger canister** is a [system
canister](#system-canister) whose main role is to store
[accounts](#account) and their corresponding
[transactions](#transaction).

## M

#### message

A **message** is data sent from one [canister](#canister) to
another or from a user to a canister.

#### message routing

The **message routing** layer receives [batches](#batch) from
the [consensus](#consensus) layer and inducts them into the
[induction pool](#induction-pool). Message routing then
schedules a set of [canisters](#canister) to execute messages
from their [input queues](#input-queue).

After [messages](#message) have been executed, the message
routing layer takes any messages produced in the execution round from
the output queues and puts those messages into the outgoing streams to
be consumed by canisters on other [subnets](#subnet).

#### minting transaction

A **minting transaction** is the process of "minting"
[ICP](#icp), whereby a certain amount of ICP comes into
existence. ICP is minted in order to reward
[neurons](#neuron) for [voting](#voting), and
reward [node providers](#node-provider) for participating in
the [IC](#internet-computer-ic) by providing compute
capacity through the running of [nodes](#node). A minting
transaction is represented as a [transaction](#transaction)
from the [ICP supply account](#icp-supply-account) to a
destination [account](#account).

#### Motoko

**Motoko** is a programming language designed to directly support the
programming model of the [Internet
Computer](#internet-computer-ic), making it easier to
efficiently build applications and take advantage of some of the more
unusual features of this platform, including the actor model for smart
contracts and compilation to WebAssembly.

## N

#### non-dissolving state

A [neuron](#neuron) that is not
[dissolved](#dissolved-state) or [
dissolving](#dissolving-state) is said to be in a
**non-dissolving state** (or "aging"). Non-dissolving neurons thus
accrue "age", with the caveat that beginning to dissolve at any time
reduces this age back to zero. The dissolve delay parameter of a
non-dissolving (aka "aging") neuron cannot be zero, because such a
neuron would have to already be dissolved.

#### Network Nervous System (NNS)

The **Network Nervous System** (NNS) is a collection of [system
canisters](#system-canister) (aka "NNS canisters") assembled
into a system that governs all aspects of the [Internet
Computer](#internet-computer-ic).

#### neuron

A **neuron** is an [IC](#internet-computer-ic) entity that
can make [proposals](#proposal) and vote on proposals related
to the governance of the [Internet
Computer](#internet-computer-ic) platform.

To provide the stability required for responsible governance, neurons
need to store ("stake") a certain amount of [ICP](#icp) in
order to be able to make and vote on proposals. This
[locks](#non-dissolving-state) the tokens for a period of
time, after which it starts [dissolving](#dissolving-state).
The ICP stake of a neuron is stored in a [neuron
account](#neuron-account). The neuron owner has the right to
propose and vote on governance issues, and is granted rewards for
[voting](#voting) in proportion to the amount of ICP staked,
and the duration of the [dissolve
period](#non-dissolving-state).

#### neuron account

A **neuron account** is a [canister
account](#canister-account) whose
[beneficiary](#beneficiary) is a [neuron](#neuron)
(or the neuron’s owner). The [governance
canister](#governance-canister) is the
[fiduciary](#fiduciary) of all neuron accounts.

#### neuron age

The **neuron age** is a [neuron](#neuron) parameter roughly
indicative of the time that has passed since its creation or since when
it last entered into a [non-dissolving
state](#non-dissolving-state). Calculation of a neuron’s age
needs to take into account whether the neuron has spent time [dissolving](#dissolving-state) or
[dissolved](#dissolved-state), both of which reset this
parameter.

#### node

A **node** is a physical or virtual network endpoint that hosts all the
hardware, [replica](#replica) software, and configuration
settings required to participate in the [Internet
Computer](#internet-computer-ic).

#### node operator

A **node operator** (NO) is a non-canister
[principal](#principal) who has the authority to add/remove
[nodes](#node) to/from the
[IC](#internet-computer-ic-ic).

[Node providers](#node-provider) come in possession of
Hardware Security Modules (HSM), and register the HSMs with the
[NNS](#network-nervous-system-(NNS)). (The HSM registration
process consists essentially in deriving an IC principal ID from the key
stored on the HSM, and persisting that ID with the NNS.) NPs hand
registered HSMs over to legal persons, who now gain the authority to
physically “operate nodes” (aka install
[replicas](#replica)). The caveat is that, as opposed to
"regular" principals, where a great deal of care goes into making sure
that one principal ID corresponds to only one person, HSMs can routinely
exchange hands, hence many persons can act as the same NO principal at
different times.

#### node provider

A **node provider** (NP) is a non-canister
[principal](#principal) that receives the rewards stemming
from node participation to the [IC](#internet-computer-ic)
(aka “payout principal”). Usually, though not necessarily, a node
provider is the owner of the [node](#node), and may also be
involved in node operation and related tasks. A node provider may
receive rewards from multiple nodes in multiple [data
centers](#data-center).

## O

#### output queue

Each [canister](#canister) has an **output queue** of
[messages](#message) bound for other canisters.

## P

#### peer-to-peer (P2P)

In common usage, **peer-to-peer** (P2P) computing or networking is a
distributed application architecture that partitions workload across a
network of equally-privileged computer [nodes](#node) so that
participants can contribute resources such as processing power, disk
storage, or network bandwidth to handle application workload.

The **peer-to-peer layer** collects and disseminates
[messages](#message) and artifacts from users and from other
nodes.

The [nodes](#node) of a [subnet](#subnet) form a
dedicated peer-to-peer broadcast network that facilitates the secure
**bounded-time/eventual delivery** broadcast of artifacts (such as
[ingress messages](#ingress-message), control messages and
their signature shares). The [consensus](#consensus) layer
builds upon this functionality.

#### principal

A **principal** is an entity that can be authenticated by the [Internet
Computer](#internet-computer-ic). This is the same sense of the
word principal as the [Wikipedia
definition](https://en.wikipedia.org/wiki/Principal-(computer-security)).
Principals that interact with the Internet Computer do so using a
certain [identity](#identity).

#### proposal

A **proposal** is a statement describing an action to modify certain
parameters of the [IC](#internet-computer-ic), or of any of
its subsystems. It is implemented as an IC entity having various
attributes, such as an ID, a URL, a summary etc. Proposals are submitted
by eligible [neuron](#neuron) owners for the consideration of
the IC community, and undergo a [voting](#voting) process,
following which they can be adopted or rejected. Adopted proposals are
then executed. There are several taxonomies of proposals, the most
prominent of which groups proposals into "topics," whose adoption, in
turn, triggers certain categories of actions, such as the creation of a
[subnet](#subnet), the addition of a
[nodes](#node) to a subnet, and the modification of the
[ICP](#icp) exchange rate.

#### proto-node

A **proto-node** is an [IC](#internet-computer-ic) entity
consisting of a combination of hardware and software, that differs from
a [node](#node) in that it has not yet been registered with
the IC. A proto-node is, in short, a "node-in-waiting," hence has all
that it takes to be a node except the [replica](#replica)
software.

## Q

#### query

A **query** is an optimised way to execute operations on a
[canister](#canister) where the state changes are not
preserved. Queries are synchronous and can be made to any
[node](#node) that hosts the canister. Queries do not require
[consensus](#consensus) to verify the result.

## R

#### replica

The **replica** is a collection of protocol components that are
necessary for a [node](#node) to participate in a
[subnet](#subnet).

#### registry

The IC **registry** manages the system meta-data maintained on the
network nervous system ([NNS](#network-nervous-system-(NNS)))
and accessed by all [subnet](#subnet) blockchains.

## S

#### smart contract

A **smart contract** is a stateful computer program designed to
automatically execute, control or document relevant events and actions
according to the terms of a contract or an agreement. A smart contract
can be deployed on the [Internet
Computer](#internet-computer-ic) in the form of a
[canister](#canister) bundling data and code.

A canister can have one or more [controllers](#controller)
that are permitted to modify the code of the canister, thereby modifying
the terms of the smart contract. For a canister smart contract to have
immutable code, its list of controllers must be empty.

#### state change

A **state change** is the result of any
[transaction](#transaction), function call, or operation that
changes the information stored in a [canister](#canister).
For example, if a function makes an update call that adds two numbers
together or removes a name from a list, the result is a change to the
canister state.

#### state manager

The **state manager** is responsible for:

- Maintaining (multiple versions of) the replicated state the
    deterministic state machine implemented by [message
    routing](#message-routing) and the [execution
    environment](#execution-environment) operates on.
- Converting back and forth between the replicated state and its
    canonical version (latter can be understood independent of the
    concrete implementation).
- Obtaining certifications of parts of the canonical state, which
    allow other stakeholders such as other [subnets](#subnet)
    and/or users, to verify that some piece of state indeed originates
    from a valid subnetwork.
- Providing capabilities to sync the canonical state with other
    [replicas](#replica) in the same subnet so that replicas
    that have fallen behind can catch up.

#### subnet

A **subnet** (subnetwork) is a collection of [nodes](#node)
that run their own instance of the [consensus](#consensus)
algorithm to produce a subnet blockchain that interacts with other
subnets of the [IC](#internet-computer-ic) using [chain
key](#chain-key) cryptography.

#### system canister

A **system canister** is a pre-installed
[canister](#canister) that performs certain tasks needed to
maintain the [Internet Computer](#internet-computer-ic).

## T

#### transaction

A ledger account **transaction** is the process of transferring
[ICP](#icp) from one [account](#account) to
another; it can be of three types: 
- Regular transfer transaction.
- [Burning](#burning) transaction
- [Minting](#minting) transaction.

#### transfer transaction

A **transfer transaction** is the process of transferring ICP from any
regular ledger [account](#account) (i.e. any ledger account
except the [ICP supply account](#icp-supply-account)) to
another regular ledger account.

## U

#### user

A **user** is any entity that interacts with the [Internet
Computer](#internet-computer-ic). Users include end-users that
use dapps deployed on the [IC](#internet-computer-ic), dapp
developers, holders of [ICP](#icp) utility tokens, and
[neuron](#neuron) holders.

## V

#### valid set rule

The **valid set rule** is the rule that determines a valid [induction
pool](#induction-pool). [Ingress
messages](#ingress-message) and [inter-canister
messages](#inter-canister-message) must pass certain checks
to ensure that the valid set rule is upheld before they can be added to
the induction pool.

#### voting

**Voting** is the process through which
[proposals](#proposal) are selected for adoption and
implementation. Its direct participants are the
[neurons](#neuron), who both:
-  Submit proposals.
-  Vote on proposals. 
The voting process is a rather intricate undertaking,
involving aspects such as neuron eligibility, voting power, chains of
neuron followees etc. This has been designed with security and
dependability in mind, and is being continuously improved in order to
prevent the concentration of voting power in the hands of just a few
neuron owners.

## W

#### WebAssembly

**WebAssembly** (abbreviated Wasm) is a binary instruction format for a
stack-based virtual machine.

## X

#### XDR

**XDR** is the currency code for *special drawing rights (SDR)*. SDRs are supplementary foreign exchange assets that are defined and maintained by the International Monetary Fund (IMF). SDRs are not a currency themselves, but represent a claim to a currenty that is held by IMF member countries in which they may be exchanged. The IC developer docs refer to currencies based on their currency codes, therefore SDRs are referenced as its currency code **XDR** in this documentation. 

-->
