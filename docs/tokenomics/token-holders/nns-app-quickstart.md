# Network Nervous System (NNS) クイックスタートdapp 

## 概要

Internet Computer の構成と動作に対するすべての変更は、Network Nervous System (NNS) と呼ばれるアルゴリズムガバナンスシステムによって制御されます。NNSは、Internet Computer ブロックチェーン構成のあらゆる側面を制御し、多くのネットワーク管理操作を実行する責任を負います。例えば、Network Nervous System (NNS)は以下の責任を負います：

- ネットワークに計算能力を提供するノードが使用するプロトコルとゲストOSソフトウェアのアップグレード。
- 新しいノードオペレータとマシンのネットワークへの導入。
- ネットワーク容量を増やすための新しいサブネットの作成
- サブネットを分割してネットワークの負荷バランスを調整
- 計算容量に対するユーザーの支払い額を制御するパラメータの設定。
- canister のアクティビティとノードのパフォーマンスを監視し、悪意のある動作や統計的な逸脱を監視します。
- ネットワークを保護するために、悪意のあるソフトウェアやパフォーマンスの低いノードを停止します。

ネットワークに対する変更や更新の要求は、**プロポーザルという**形でNNSに提出されます。NNSは、neuron の保有者による投票活動に基づいて、提案の採用または却下を決定します。

## neuronとは何ですか？

ネットワーク参加者が提案に投票できるようにするには、ICPユーティリティ・トークンのステークを一定期間ロックし、.sと呼ばれる代表者を作成する必要があります。 **neuron**.

neuronsはICPユーティリティ・トークンのステークを表すため、**台帳canister アカウントと**台帳アカウントを管理するプリンシパルを持つアイデンティティも表します。

Neurons は、**ロックアップ**期間と呼ばれる特定の期間、それらが表す ICP ユーティリティ・トークンを交換できないようにすることで、Internet Computer の責任あるガバナンスに必要な安定性を提供します。

次の図は、neuron を作成するワークフローと、neuron と台帳canisters の関係を簡略化して示しています。

![create dissolve neuron api](../_attachments/create-dissolve-neuron-api.svg)

### ガバナンスと議決権報酬

個人または組織がICPユーティリティ・トークンをneuron にロックアップしている場合、neuron の保有者はガバナンスの問題について提案し、投票することができます。参加を促すため、neuron の保有者は、ロックアップしたICPユーティリティ・トークンの数とロックアップ期間の長さ（最長8年）に比例して、投票に対する報酬も得られます。

### 保有株式のロック解除neuron

ネットワーク参加者がneuron を作成した後、ロックされた ICP ユーティリティ・トークンの残高は、neuron を完全に**溶解**することでのみロック解除できます。**ロックされた**ステートでは、neuron にはロックアップ期間に相当する固定された非ゼロの**溶解遅延が**あります。たとえば、100 ICP ユーティリティ・トークンを 5 年間ロックしたとします。これらの設定でneuron の作成に成功すると、溶解遅延は 5 年間になります。

時間が経過するにつれて減少する溶解遅延に基づいて、**溶解タイマーは** neuron を完全に溶解するのにかかる時間を決定します。

溶解タイマーがゼロに達すると、neuron の所有者（neuron を作成した ICP ユーティリティ・トークンの保有者）、または認証されたカストディアンは、neuron を溶解し、ICP ユーティリティ・トークンの残高をアンロックできます。

溶解したneuron は存在しなくなり、neuron が表していた ICP ユーティリティ・トークンのステークが適切な元帳canister アカウントに解放されます。

## に接続します。Internet Computer

Network Nervous System (NNS)dapp を使用してInternet Computer に接続するには：

- #### ステップ 1: ブラウザを開き、[Network Nervous System (NNS)](https://nns.ic0.app) dapp に移動します。

![NNS dapp](../_attachments/nns1.png)

- #### ステップ 2:**Login**をクリックして[Internet Identity](https://identity.ic0.app) に接続します。

![Connect with Internet Identity](../_attachments/nns2.png)

登録がお済みでない場合は、**Register with Internet Identityを**クリックして登録できます。

:::info
Internet Identityに複数のデバイスと認証方法を追加することを強くお勧めします。たとえば、コンピュータや電話などの複数の物理デバイスをセキュリティキーで登録し、それらのデバイスおよびそれらのデバイス上で実行されるブラウザがサポートする認証オプションを使用します。
::：

インターネットIDの作成に関する詳細は、[インターネットIDの使用](https://internetidentity.zendesk.com/hc/en-us/articles/15430677359124-How-Do-I-Create-an-Internet-Identity-on-My-Mobile-Device-)方法を参照してください。

登録が完了したら、\[**Login\]**をクリックして、アンカーと登録した認証方法（セキュリ ティキーや指紋など）を使用して認証できます。

- #### ステップ3: \[**Proceed**\]をクリックして、Network Nervous System (NNS)dapp にアクセスします。

### アカウントの追加

インターネット ID を使用してログオンすると、Internet Computer 台帳にメインアカウントが作成されます。ICP ユーティリティ・トークンが開発者 ID、つまり SDK`dfx` コマンドライン・インターフェースで作成された ID に関連付けられている場合。メイン口座には、ICP ユーティリティ・トークンの残高として 0.00 が表示されます。たとえば、次のようになります：

![nns app main](../_attachments/nns3.png)

トークンを転送する前に、1 つ以上のリンクされたサブアカウントを作成するか、アカウントにハードウェアウォレットをアタッチできます。

ICPユーティリティトークンを管理するアカウントを追加するには：

- #### ステップ1：デフォルトの「My Tokens」タブで、「**Add Account**」をクリックします。

![Add account](../_attachments/nns3.png)

- #### ステップ 2: 追加するアカウントのタイプを選択します。
  
  - **New Linked Account (新規リンクアカウント**)\] は、元帳のメインアカウントアドレスにリンクされた新しいサブアカウントを作成します。
  
  - **ハードウェアウォレットを追加**\] は、台帳のメインアカウントアドレスにハードウェアウォレットを追加します。

![Select account](../_attachments/nns4.png)

- #### ステップ 3: \[新規リンク**アカウント**\] をクリックし、\[アカウント名\] を入力して \[**作成\]** をクリックします。

![new linked account](../_attachments/nns5.png)

## アカウント間での ICP ユーティリティ トークンの転送

### CLIの使用

ICP ユーティリティトークンの保持にセルフカストディを選択し、トークンが登録済みインターネット ID ではなく開発者 ID に関連付けられている場合、[Network Nervous System （NNS）](https://nns.ic0.app) dapp を使用して ICP ユーティリティトークンを管理するには、ICP ユーティリティトークンをアカウントに転送する必要があります。

開発者 ID が管理する ICP ユーティリティトークンを転送するには、以下の手順に従います：

- #### ステップ 1: ローカルコンピュータでターミナルシェルを開きます。

- #### ステップ 2：以下のコマンドを実行して、元帳アカウントを制御する ID を使用していることを確認します：

<!-- end list -->

``` bash
dfx identity whoami
```

ほとんどの場合、現在`default` の開発者 ID を使用していることがわかります。たとえば、次のようになります：

    default

- #### ステップ 3：次のコマンドを実行して、現在の ID のプリンシパルのテキスト表示を表示します：

<!-- end list -->

``` bash
dfx identity get-principal
```

このコマンドを実行すると、次のような出力が表示されます：

    tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe

- #### ステップ 4: 次のコマンドを実行して、ID に関連付けられた元帳口座の現在の残高を確認し ます：

<!-- end list -->

``` bash
dfx ledger --network ic balance
```

- #### ステップ 5：次のようなコマンドを実行して、ICP ユーティリティ・トークンをメイン・アカウントまたは 作成したリンク先のサブアカウントに転送します：

<!-- end list -->

``` bash
dfx ledger --network ic transfer <destination-account-id> --icp <ICP-amount> --memo <numeric-memo>
```

例えば、以下のアカウントがあるとします：

![accounts](../_attachments/accounts.png)

1つのICPユーティリティ・トークンを`Main` ：

    dfx ledger --network ic transfer dd81336dbfef5c5870e84b48405c7b229c07ad999fdcacb85b9b9850bd60766f --memo 12345 --icp 1

また、ICPユーティリティ・トークンを1つ、`pubs` アカウントに転送したい場合は、以下のコマンドを実行します：

    dfx ledger --network ic transfer 183a04888eb20e73766f082bae01587830bd3cd912544f63fda515e9d77a96dc --icp 1 --memo 12346

:::info
この例では、`--icp` コマンド行オプションを使用して、整数を使って ICP ユーティリティ・トークンを転送する方法を説明します。

- **e8sと**呼ばれるICPユーティリティ・トークンの端数単位を指定するには、`--e8s` オプションを使用します。このオプションを単独で使用することも、`--icp` オプションと組み合わせて使用することもできます。
- また、`--amount` を使用して、転送する ICP ユーティリティ・トークンの数を小数点以下 8 桁までの端数単位で指定することもできます（例えば、`5.00000025`.
  ::）：

転送先アドレスは、Internet Computer ネットワーク上で実行されている台帳canister のアドレス、 を使用して追加したアカウント、または を使用して追加したウォレットのアドレスです。 [Network Nervous System dapp](https://nns.ic0.app)を使用して追加したアカウント、または取引所にあるウォレットのアドレスです。

のアカウントにICPユーティリティ・トークンを転送する場合、このアドレスを更新する必要があるかもしれません。 [Network Nervous System dapp](https://nns.ic0.app)のアカウントにICPユーティリティトークンを送金する場合、取引が反映されるのを確認するためにブラウザを更新する必要がある場合があります。

`dfx ledger` コマンドラインオプションの使用方法の詳細については、[dfx ledger](/references/cli-reference/dfx-ledger.md) を参照してください。

### NNSの使用dapp

- #### ステップ 1: デフォルトの「My Tokens」タブで、Internet Computer トークンが選択されていることを確認し、ウィンドウ下部の「**Send**」をクリックします。

![Send](../_attachments/nns3.png)

- #### ステップ2：「Send」ウィンドウで、リンク先のアカウントまたはウォレットを送信元として選択し、送信先アドレスと送信するICPの量を入力します。

![Send ICP](../_attachments/nns6.png)

- #### ステップ3：**続行を**選択してトランザクションを送信します。

## でICPユーティリティトークンをステークします。neuron

ICP ユーティリティトークンをNetwork Nervous System dapp に送金した後、Network Nervous System dapp を使用してneurons を作成および管理し、議案に投票し、Internet Computer 上でcanisters を作成することができます。

Neuronガバナンスに参加し、報酬を得るには、ICPトークンが必要です。neuron を作成するには、ICP ユーティリティ・トークンを一定期間ロックアップする必要があります。neuron を作成するために必要な最小出資額は 1 ICP ユーティリティ・トークンです。ステークをロックする期間は、6カ月から最長8年まで設定できます。

ICPユーティリティ・トークンをステークするには

- #### ステップ1： NNSdapp で、左のナビゲーションバーから**MyNeuron Staking**を選択し、ウィンドウの下部にある**StakeNeuron**s をクリックします。

![Neuron staking](../_attachments/nns7.png)

- #### ステップ 2：ソースとして使用するウォレットを選択し、ステークする ICP ユーティリティートークンの数を入力し、\[**Create**\] をクリックします。

![Neuron staking](../_attachments/nns8.png)

- #### ステップ 3：neuron の溶解遅延を設定してステークがロックされる時間を制御し、\[**Update Delay**\] をクリックします。

例えば

![dissolve delay](../_attachments/dissolve-delay.png)

- #### ステップ 4: \[**はい、間違いありません**\] をクリックしてロックアップ期間を確認し、ウィンドウを閉じて新しく作成されたneuron プロパティを確認します。

![neuron properties](../_attachments/neuron-properties.png)

## 作成後にできることneuron

ステークをロックし、neuron を作成した後は、次のことができます：

- **ロック解除の開始**\] をクリックして、ディゾルブ遅延タイマーを開始します。
- ディゾルブ**遅延を**増やす\] をクリックして、ディゾルブ遅延時間を増やします。
- **ロックアップ**\] をクリックして、ロック解除のカウントダウンを開始した後、ディゾルブ遅延を停止します。
- ステークしているICPユーティリティ・トークンの数を増やします。

## ディゾルブ遅延の開始と停止

新しいneuron を作成しても、ディゾルブ遅延タイマーは自動的に開始されません。タイマーのカウントダウンは、\[**ロック解除を開始**\] をクリックして明示的に開始する必要があります。

例えば、ディゾルブ ディレイを 1 年に設定し、すぐにカウントダウンを開始したい場合は、neuron を作成するプロセスの一環として、\[ロック解除開**始**\] をクリックします。気が変わって現在進行中のカウントダウンを停止したい場合は、\[**ロックアップ**\] をクリックします。\[**ロックアップ**\] をクリックしてディゾルブ ディレイを停止した後、\[**ロック解除開始**\] をクリックすると、既存のディゾルブ ディレイ期間を変更せずにカウントダウンを再開できます。既に進行中のカウントダウンを継続し、ロックアップ期間を延長したい場合は、［**ディゾルブ遅延を増やす**］をクリックし、より長いディゾルブ遅延を選択できます。

## 既存の ICP ユーティリティ・トークンの追加neuron

neuron を作成した後、そのneuron に賭けている ICP ユーティリティトークンの数を増やして、投票権と報酬を増やすことができます。たとえば、最初に少数の ICP ユーティリティ・トークンをステークし、その後追加でトークンを購入することにした場合、新しいneuron を作成するか、既存のneuron に「トップアップ」するオプションがあります。

既存のneuron のステークを増やすには：

- #### ステップ 1:[ neuron で ICP ユーティリティトークンをステーク](#stake-icp)する手順に従い、[Network Nervous System (NNS)](https://nns.ic0.app) dapp を使用して元のneuron をステークします。

- #### ステップ 2:Internet Computer Association[transaction dashboard](https://dashboard.internetcomputer.org/transactions)でトランザクションを検索し、neuron アドレスを取得します。

メイン ICP 元帳アカウントのアカウント識別子を使用して、トランザクションを検索できます。

- #### ステップ 3:[Network Nervous System (NNS)](https://nns.ic0.app) dapp に戻り、「MyNeuron Staking」ウィンドウから「**New Transaction**」をクリックします。

- #### ステップ 4: 取引ダッシュボードのneuron アドレスを \[**Destination**address\] フィールドに貼り付け、\[**Continue**\] をクリックします。

- #### ステップ5: 指定したneuron に追加したいICPユーティリティトークンの量を入力し、\[**Continue**\]をクリックします。

- #### ステップ6：取引の詳細を確認し、［**Confirm and Send（確認して送信**）］をクリックします。

![confirm top up](../_attachments/confirm-top-up.png)

- #### ステップ 7：完了したトランザクションを確認し、［**閉じる**］をクリックします。

- #### ステップ 8:**Neurons タブをクリックし、増加したステークを確認します。**

## 溶解したneurons をアカウントに払い戻します。

neuron のディゾルブ遅延タイマーがゼロになると、neuronのステークを払い出し、ロックされた ICP ユーティリティ トークンの残高を指定した元帳アカウントに移すことができます。この手順を実行すると、neuron 識別子とその元帳の履歴は、ガバナンスcanister から永久に削除されます。

neuron を払い出し、その ICP ユーティリティ トークンを返却するには：

- #### ステップ 1: NNSdapp から「MyNeuron Staking」を選択し、溶解遅延期間が終了したロックされていないneuron をクリックします。例えば

![unlocked neuron](../_attachments/unlocked-neuron.png)

- #### ステップ 2:**払い戻しを**クリックします。例えば

![disburse](../_attachments/disburse.png)

- #### ステップ3: ICPユーティリティトークンを受け取るアドレスを入力するか、アカウントを選択します。例えば、`dev-projects` リンクアカウントを選択します：

- #### ステップ4： 取引情報を確認し、［**Confirm and Send**］をクリックします。たとえば、［Destination address（宛先アドレス）］が`dev-projects` リンクアカウントの意図したアドレスと一致していることを確認します：

![confirm send](../_attachments/confirm-send.png)

- #### ステップ5：完了したトランザクションを確認し、\[**Close**\]をクリックします。

例えば

![confirmation](../_attachments/confirmation.png)

ICPユーティリティ・トークンをInternet Computer 元帳canister の口座の1つに転送した場合、ICPタブをクリックして新しい残高を確認できます。例

![updated icp](../_attachments/updated-icp.png)

## 新しいneurons を生成します。

直接、または他のneurons の投票に従って提案に投票すると、neuron に関連する満期が増加し、ガバナンスに参加することで得られる報酬が増加します。ロックされたステークに対する成熟度が最低しきい値の 1 ICP に達すると、新しいneuron をスポーンできます。スポーン操作により、元帳に新しい残高の ICP をロックする新しいneuron が作成されます。

たとえば、100 ICP のユーティリティ・トークンを含むneuron があり、その満期が 10 パーセントである場合、約 10 ICP の新しいトークンを含む新しいneuron をスポーンできます。100 ICP トークンを含むneuron がスポーンするための最低閾値に達するには、その成熟度が 1 パーセントを超える必要があります。

既存のneuron から新しいneuron をスポーンすると、既存のneuron の成熟度はゼロになります。

既存のneuron から新しいneuronをスポーンするには：

- #### ステップ 1: NNSdapp から「MyNeuron Staking」を選択し、新しいneuron をスポーンするのに必要な最低成熟度に達したneuron をクリックします。

- #### ステップ**2：SpawnNeuron** をクリックします。

## 提案に対する投票

actor ガバナンスへの積極的な参加は、Internet Computer の長期的な健全性において重要なことです。提案に対する投票も、actor ICP ユーティリティトークンをneuronにロックアップする見返りとして受け取る報酬を計算する上で重要なことです。

しかし、NNSに提出されたすべての提案に直接投票するには、いくつかの課題があります。たとえば、あなたが不在のときに提案が提出されて投票が必要になったり、評価するための専門知識が不足している変更が提案されたりする可能性があります。このような課題に対処するため、neuronのグループの投票に従うことで、提案の採択または却下を自動的に投票するよう、neuronを設定することができます。

報酬を最大化するには、自分と利害が一致するアクティブなneuron ホルダーをフォローして、できるだけ多くの提案に投票する必要があります。たとえば、**SubnetManagement**などのトピックではInternet Computer Association (ICA)をフォローし、**Governance** などのトピックでは他のneuron ホルダーをフォローするとよいでしょう。

直接、またはNetwork Nervous System dapp のフィルタを使用して他のneuron 利害関係者をフォローすることで、表示および投票する提案タイプおよび提案トピックを選択できます。たとえば、データ センターの ID やノード オペレータなどのネットワーク参加者に関係するすべてのプロポーザルを確認および投票したいが、国際通貨基金（IMF）の特別引出権（SDR）で測定される ICP の現在の市場価値に関連するプロポーザルは表示したくない場合は、**ParticipantManagement**トピック フィルタを選択し、**ExchangeRate**トピック フィルタの選択を解除できます。

提案に手動で投票するには

- #### ステップ 1: NNSdapp から、左のナビゲーションバーで「提案に投票」を選択します。

![Proposals](../_attachments/nns9.png)

- #### ステップ 2：次に、どの神経系に関する提案を表示したいかを選択します。

デフォルトでは、Internet Computer NNSの提案が表示されますが、OpenChatやKinicなどのSNSの他の提案も利用可能です。

- #### ステップ 3: 提案をクリックすると、提案の概要、提案の種類、トピック、投票終了日などの詳細情報が表示されます。

![Proposal detail](../_attachments/nns8.png)

:::info
投票や投票報酬についての詳細は以下の記事をご覧ください：

- [に賭けて多額の投票報酬を得ましょう。Network Nervous System](https://medium.com/dfinity/earn-substantial-voting-rewards-by-staking-in-the-network-nervous-system-7eb5cf988182)

- [ Internet ComputerのNetwork Nervous System,neuronと ICP ユーティリティ・トークンを理解しましょう。](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8)

- [ Internet ComputerのNetwork Nervous System アプリとウォレットを始める](https://medium.com/dfinity/getting-started-on-the-internet-computers-network-nervous-system-app-wallet-61ecf111ea11)
  ::：

## プロポーザルの提出

現在、network nervous system にプロポーザルを提出できるのは、`governance` canister へのコールを使用して SDK コマンドライン・インターフェース (`dfx`) を使用する場合のみです。

`governance` canister を操作するための別のコマンドラインツール (`icx-nns`) は開発中であり、この機能は[Network Nervous System (NNS)](https://nns.ic0.app) dapp でも近日中に利用可能になる予定です。

しかし、すぐにでもプロポーザルの提出を開始したい場合は、[icx-nns](https://github.com/dfinity/icx-nns/releases)リポジトリからリリースをダウンロードすることで、`icx-nns` コマンドラインツールの暫定版にアクセスできます。

## SNS分散スワップ

NNSdapp の「Launch Pad」タブからSNS分散スワップに参加できます。SNS は分散型自律組織の高度なバージョンで、SNS の参加者は新機能、ロードマップ項目、SNS 資金の割り当てなどの提案に投票できます。

SNSの詳細については、[こちらを](https://internetcomputer.org/sns)ご覧ください。

現在および過去のSNS分散化スワップをNNSdapp から見るには：

- #### ステップ 1: NNSdapp から、左のナビゲーションバーから**Launch pad**をクリックします。

![Launch pad](../_attachments/nns15.png)

- #### ステップ 2: SNS スワップを選択すると、ステータス、参加者総数、トークン供給量、スワップ終了日などの詳細が表示されます。

![SNS swap](../_attachments/nns16.png)

## NNS からのcanisters の展開と管理dapp

スワップを作成および管理するには **cycles**スマートコントラクトに似た [canisters](/references/glossary.md#canister)これはスマートコントラクトに似ています。[Network Nervous System (NNS)](https://nns.ic0.app) dapp は、ICP ユーティリティ・トークンをcycles に変換し、特定のcanister 識別子にcycles をアタッチできるようにすることで、canisters を作成および管理する便利な方法を提供します。

新しいcanister を作成するには：

- #### ステップ 1：NNSdapp から［**MyCanisters** ］をクリックし、［**CreateCanister**］または［**LinkCanister** ］をクリックします。

![My canisters](../_attachments/nns11.png)

- #### ステップ**2：CreateCanister** を選択した場合、canister の作成に使用するソース・アカウントを選択します。

![Create a new canister](../_attachments/nns12.png)

- #### ステップ 3: 次に、canister を作成する ICP トークンの量とcycles を入力します。次に、「**ReviewCanister Creation**」を選択します。

- #### ステップ4：ICPユーティリティトークンからcycles の詳細を確認し、［**確認］を**クリックして続行します。

Confirm（確認）］をクリックすると、確認できます：

- 新しいcanister 識別子。
- canister が使用できるcycles の数。
- 新しいcanister の完全な管理権限を現在持っている管理プリンシパル。

:::info
\+canister のコントローラとして使用されるプリンシパルを変更するには、「**Change Controllers**」をクリックします。
\+cycles をcanister に追加するには、「**AddCycles** 」をクリックします。
::：

![Review canister](../_attachments/nns13.png)

- #### ステップ 5:canister をリンクするには、canister の ID を入力し、\[**Confirm\]** を選択します。

![Link canister](../_attachments/nns14.png)

- #### ステップ 6: **Canisters**タブに戻り、作成したcanisters を確認します。

例えば

![Canister list](../_attachments/nns11.png)

<!---
# Network Nervous System (NNS) dapp quick start 

## Overview
All changes to the configuration and behavior of the Internet Computer are controlled by an algorithmic governance system called the Network Nervous System (NNS). The NNS controls all aspects of the Internet Computer blockchain configuration and is responsible for performing many network management operations. For example, the Network Nervous System (NNS) is responsible for:

-   Upgrading the protocol and guest operating system software used by the nodes that provide computing capacity to the network.
-   Inducting new node operators and machines into the network.
-   Creating new subnets to increase network capacity.
-   Splitting subnets to balance network load.
-   Configuring parameters that control how much must be paid by users for compute capacity.
-   Monitoring canister activity and node performance for malicious behavior and statistical deviations.
-   De-activating malicious software or under-performing nodes to protect the network.

The requests for changes and updates to the network are submitted to the NNS in the form of **proposals**. The NNS decides to adopt or reject proposals based on voting activity by neuron holders.

## What are neurons?

For network participants to be able to vote on proposals, they need to lock up a stake of ICP utility tokens for a given period of time to create a representative called a **neuron**.

Because neurons represent a stake of ICP utility tokens, they also represent an identity with a **ledger canister account** and a principal that controls the ledger account.

Neurons provide the stability required for responsible governance of the Internet Computer by ensuring that the ICP utility tokens they represent cannot be exchanged for a specific period of time referred to as the **lock-up** period.

The following diagram provides a simplified view of the workflow for creating a neuron and the relationship between the neuron and ledger canisters.

![create dissolve neuron api](../_attachments/create-dissolve-neuron-api.svg)

### Governance and voting rewards

When a person or organization has ICP utility tokens locked up in a neuron, the neuron holder can propose and vote on governance issues. To encourage participation, neuron holders are also rewarded for voting in proportion to the number of ICP utility tokens they have locked up and the length of the lock-up period up to a maximum of eight years.

### Unlocking the stake held by a neuron

After network participants create a neuron, the locked balance of ICP utility tokens can only be unlocked by fully **dissolving** the neuron. In its **locked** state, the neuron has a fixed non-zero **dissolve delay** equivalent to the lock-up period. For example, assume you have a stake of 100 ICP utility tokens locked up for a period of five years. After successfully created the neuron with these settings, your dissolve delay is five years.

Based on the dissolve delay that decreases as time progresses, a **dissolve timer** determines how long it will take to completely dissolve a neuron.

When the dissolve timer reaches zero, the neuron owner—the ICP utility token holder who created the neuron, or an authenticated custodian, can dissolve the neuron and unlock the balance of ICP utility tokens.

The dissolved neuron ceases to exist and the stake of ICP utility tokens that the neuron represented is released to the appropriate ledger canister account.

## Connect to the Internet Computer

To connect to the Internet Computer using the Network Nervous System (NNS) dapp:

- #### Step 1:  Open a browser and navigate to the [Network Nervous System (NNS)](https://nns.ic0.app) dapp.

![NNS dapp](../_attachments/nns1.png)

- #### Step 2:  Click **Login** to connect to [Internet Identity](https://identity.ic0.app).

![Connect with Internet Identity](../_attachments/nns2.png)

If you haven’t previously registered, you can click **Register with Internet Identity** to register.

:::info
We strongly recommend you add multiple devices and authentication methods to your Internet Identity. For example, register multiple physical devices like your computer and phone with a security key and using the authentication options that those devices—and browsers running on them—support.
:::

For more information about creating an Internet Identity, see [how to use Internet Identity](https://internetidentity.zendesk.com/hc/en-us/articles/15430677359124-How-Do-I-Create-an-Internet-Identity-on-My-Mobile-Device-).

After you have registered, you can click **Login** to authenticate using your anchor and the authentication method—for example, a security key or fingerprint—you have registered.

- #### Step 3:  Click **Proceed** to access to the Network Nervous System (NNS) dapp.

### Add an account

Logging on using an Internet Identity creates a main account for you in the Internet Computer ledger. If your ICP utility tokens are associated with your developer identity; that is, the identity created by the SDK `dfx` command-line interface. Your main account displays 0.00 for your ICP utility token balance. For example:

![nns app main](../_attachments/nns3.png)

Before transferring any tokens, you can create one or more linked subaccounts or attach a hardware wallet to your account.

To add an account for managing ICP utility tokens:

- #### Step 1:  On the default 'My Tokens' tab, click **Add Account**.

![Add account](../_attachments/nns3.png)

- #### Step 2:  Select the type of account to add.

    -   **New Linked Account** creates a new subaccount linked to your Main account address in the ledger.

    -   **Attach Hardware Wallet** adds a hardware wallet to your main account address in the ledger.

![Select account](../_attachments/nns4.png)

- #### Step 3:  Click **New Linked Account**, type an Account Name, then click **Create**.

![new linked account](../_attachments/nns5.png)

## Transfer ICP utility tokens between accounts

### Using the CLI

If you have selected self-custody for holding your ICP utility tokens and the tokens are associated with your developer identity instead of your registered Internet Identity, you need to transfer ICP utility tokens to your accounts if you want to manage them using the [Network Nervous System (NNS)](https://nns.ic0.app) dapp.

To transfer ICP utility tokens controlled by your developer identity:

- #### Step 1:  Open a terminal shell on your local computer.

- #### Step 2:  Check that you are using an identity with control over the ledger account by running the following command:

``` bash
dfx identity whoami
```

In most cases, you should see that you are currently using your `default` developer identity. For example:

```
default
```

- #### Step 3:  View the textual representation of the principal for your current identity by running the following command:

``` bash
dfx identity get-principal
```

This command displays output similar to the following:

```
tsqwz-udeik-5migd-ehrev-pvoqv-szx2g-akh5s-fkyqc-zy6q7-snav6-uqe
```

- #### Step 4:  Check the current balance in the ledger account associated with your identity by running the following command:

``` bash
dfx ledger --network ic balance
```

- #### Step 5:  Transfer ICP utility tokens to your Main account or a linked subaccount you create by running a command similar to the following:

``` bash
dfx ledger --network ic transfer <destination-account-id> --icp <ICP-amount> --memo <numeric-memo>
```

For example, assume you have the following accounts:

![accounts](../_attachments/accounts.png)

If you want to transfer one ICP utility token to the `Main` account, you can run the following command:

```
dfx ledger --network ic transfer dd81336dbfef5c5870e84b48405c7b229c07ad999fdcacb85b9b9850bd60766f --memo 12345 --icp 1
```

If you also want to transfer one ICP utility token to the `pubs` account, you can run the following command:

```
dfx ledger --network ic transfer 183a04888eb20e73766f082bae01587830bd3cd912544f63fda515e9d77a96dc --icp 1 --memo 12346
```

:::info
This example illustrates how to transfer ICP utility tokens to using a whole number with the `--icp` command-line option.
-   You can also specify fractional units of ICP utility tokens—called **e8s**—using the `--e8s` option, either on its own or in conjunction with the `--icp` option.
-   Alternatively, you can use the `--amount` to specify the number of ICP utility tokens to transfer with fractional units up to 8 decimal places, for example, as `5.00000025`.
:::

The destination address can be an address in the ledger canister running on the Internet Computer network, an account you have added using the [Network Nervous System dapp](https://nns.ic0.app), or the address for a wallet you have on an exchange.

If you transfer the ICP utility tokens to an account in the [Network Nervous System dapp](https://nns.ic0.app), you might need to refresh the browser to see the transaction reflected.

For more information about using the `dfx ledger` command-line options, see [dfx ledger](/references/cli-reference/dfx-ledger.md).

### Using the NNS dapp

- #### Step 1: On the default 'My Tokens' tab, assure that The Internet Computer token has been selected, then click **Send** on the bottom of the window.

![Send](../_attachments/nns3.png)

- #### Step 2: In the 'Send' window, select your linked account or wallet for the source, then input the destination address and the amount of ICP to send. 

![Send ICP](../_attachments/nns6.png)

- #### Step 3: Then select **Continue** to send the transaction. 

## Stake ICP utility tokens in a neuron

After you transfer ICP utility tokens to the Network Nervous System dapp, you can use the Network Nervous System dapp to create and manage neurons, vote on proposals, and create canisters on the Internet Computer.

Neurons are required to participate in governance and earn rewards. To create a neuron, you must lock up some number of ICP utility tokens for a period of time. The minimum stake required to create a neuron is one ICP utility token. You can configure the period of time the stake is locked from six months up to a maximum of eight years.

To stake ICP utility tokens:

- #### Step 1:  In the NNS dapp, select **My Neuron Staking** from the left navigation bar, then click **Stake Neurons** in the bottom of the window.

![Neuron staking](../_attachments/nns7.png)

- #### Step 2 :  Select which wallet you'd like to use as the source, then type the number of ICP utility tokens to stake, then click **Create**.

![Neuron staking](../_attachments/nns8.png)

- #### Step 3:  Set the dissolve delay for the neuron to control the length of time the stake is locked, then click **Update Delay**.

For example:

![dissolve delay](../_attachments/dissolve-delay.png)

- #### Step 4:  Click **Yes, I’m sure** to confirm the lock up period, then close the window to review the newly-created neuron properties.

![neuron properties](../_attachments/neuron-properties.png)

## What you can do after creating a neuron

After you have locked the stake and created a neuron, you can:

-   Start the dissolve delay timer by clicking **Start Unlock**.
-   Increase the dissolve delay period by clicking **Increase Dissolve Delay**.
-   Stop the dissolve delay after starting the unlock countdown by clicking **Lockup**.
-   Increase the number of ICP utility tokens you have staked.

## Starting and stopping the dissolve delay

Creating a new neuron does not automatically start the dissolve delay timer. You must explicitly start the timer countdown by clicking **Start Unlock**.

For example, if you set a dissolve delay of one year and want to immediately begin the countdown, you should click **Start Unlock** as part of the process of creating the neuron. If you change your mind and want to stop a current countdown in progress, you can click **Lockup**, After you click **Lockup** to stop the dissolve delay, you can click **Start Unlock** to resume the countdown without changing the existing dissolve delay period. If you want to continue a countdown already in progress but extend the lock up period, you can click **Increase Dissolve Delay** then select a longer dissolve delay.

## Adding ICP utility tokens to an existing neuron

After you create a neuron, you can increase the number of ICP utility tokens you have staked in that neuron to increase your voting power and rewards. For example, if you initially stake a small number of ICP utility tokens, then decide to purchase additional tokens, you have the option to create a new neuron or "top-up" your existing neuron.

To increase the stake in an existing neuron:

- #### Step 1:  Follow the steps in [stake ICP utility tokens in a neuron](#stake-icp) to stake the original neuron using the [Network Nervous System (NNS)](https://nns.ic0.app) dapp.

- #### Step 2:  Look up the transaction in the Internet Computer Association [transaction dashboard](https://dashboard.internetcomputer.org/transactions) to get the neuron address.

You can use the account identifier for your main ICP ledger account to search for your transactions.

- #### Step 3:  Return to the [Network Nervous System (NNS)](https://nns.ic0.app) dapp, then from the 'My Neuron Staking' window, click **New Transaction**.

- #### Step 4:  Paste the neuron address from the transaction dashboard into the **Destination** address field, then click **Continue**.

- #### Step 5:  Type the amount of ICP utility tokens you want to add to the specified neuron, then click **Continue**.

- #### Step 6:  Verify the transaction details, then click **Confirm and Send**.

![confirm top up](../_attachments/confirm-top-up.png)

- #### Step 7:  Verify the completed transaction, then click **Close**.

- #### Step 8:  Click the **Neurons** tab to see the increased stake.

## Disburse dissolved neurons into an account

When the dissolve delay timer for a neuron reaches zero, you can disburse the neuron’s stake and transfer its locked ICP utility token balance to the ledger account you specify. After you take this step, the neuron identifier and its ledger history are permanently removed from the governance canister.

To disburse a neuron and return its ICP utility tokens:

- #### Step 1:  From the NNS dapp, select 'My Neuron Staking', then click on the unlocked neuron that has reach the ended of its dissolve delay period. For example:

![unlocked neuron](../_attachments/unlocked-neuron.png)

- #### Step 2:  Click **Disburse**. For example:

![disburse](../_attachments/disburse.png)

- #### Step 3:  Type an address or select an account to receive the ICP utility tokens. For example, you might select the `dev-projects` linked account:

- #### Step 4:  Verify the transaction information, then click **Confirm and Send**. For example, check that the Destination address matches the intended address of the `dev-projects` linked account:

![confirm send](../_attachments/confirm-send.png)

- #### Step 5:  Verify the completed transaction, then click **Close**.

For example:

![confirmation](../_attachments/confirmation.png)

If you transferred the ICP utility tokens to one of your accounts in the Internet Computer ledger canister, you can click the ICP tab and see your new balance reflected. For example:

![updated icp](../_attachments/updated-icp.png)

## Spawn new neurons

As you vote on proposals—either directly or by following the votes of other neurons—the maturity associated with your neuron increases, which in turn increases the rewards you earn for participating in governance. When the maturity for a locked stake reaches a minimum threshold of one ICP, you can spawn a new neuron. The spawn operation creates a new neuron that locks a new balance of ICP on the ledger.

For example, if you have a neuron that contains 100 ICP utility tokens and it has a maturity of 10 percent, you can spawn a new neuron that contains approximately 10 new ICP tokens. For the neuron with 100 ICP tokens to reach the minimum threshold for spawning, its maturity would need to be greater than one percent.

After you spawn a new neuron from an existing neuron, the maturity for the existing neuron falls to zero.

To spawn new neurons from an existing neuron:

- #### Step 1:  From the NNS dapp, select 'My Neuron Staking', then click the neuron that has reached the minimum maturity required to spawn a new neuron.

- #### Step 2:  Click **Spawn Neuron**.

## Vote on proposals 

Active participation in governance is an important factor in the long-term health of the Internet Computer. Voting on proposals is also an important factor in calculating the rewards you receive in return for locking up ICP utility tokens in neurons.

However, voting directly on every proposal submitted to the NNS presents several challenges. For example, proposals might be submitted and require a vote when you are unavailable or propose changes that you lack the expertise to evaluate. To address these challenges, you can configure neurons to vote automatically to adopt or reject proposals by following the votes of a group of neurons.

To maximize your rewards, you should vote on as many proposals as possible by following the active neuron holders who have interests aligned with your own. For example, you might follow the Internet Computer Association (ICA) on some topics such **SubnetManagement** and other neuron holders on topics such as **Governance**.

You can choose the proposal types and proposal topics that you see and vote on either directly or by following other neuron stakeholders using filters in the Network Nervous System dapp. For example, if you want to review and vote on all proposals that involve network participants such as data center identities and node operators, but aren’t interested in viewing proposals related to the current market value of ICP, as measured by an International Monetary Fund (IMF) Special Drawing Right (SDR), you can select the **ParticipantManagement** topic filter and deselect the **ExchangeRate** topic filter.

To manually vote on proposals:

- #### Step 1:  From the NNS dapp, select 'Vote on Proposals' from the left navigation bar. 

![Proposals](../_attachments/nns9.png)

- #### Step 2: Then, select which Nervous System you'd like to view proposals for. 

By default, the Internet Computer NNS proposals are shown, but other proposals for SNS's like OpenChat and Kinic are also available.

- #### Step 3:  Click on any proposals to view more information on it, such as the proposals summary, the type of proposals, the topic, and when the proposal's voting ends. 

![Proposal detail](../_attachments/nns8.png)

:::info
For more information about voting and voting rewards, see the following articles:

-   [Earn substantial voting rewards by staking in the Network Nervous System](https://medium.com/dfinity/earn-substantial-voting-rewards-by-staking-in-the-network-nervous-system-7eb5cf988182)

-   [Understanding the Internet Computer’s Network Nervous System, neurons, and ICP utility tokens](https://medium.com/dfinity/understanding-the-internet-computers-network-nervous-system-neurons-and-icp-utility-tokens-730dab65cae8)

-   [Getting started on the Internet Computer’s Network Nervous System app and wallet](https://medium.com/dfinity/getting-started-on-the-internet-computers-network-nervous-system-app-wallet-61ecf111ea11)
:::

## Submit a proposal

Currently, you can only submit proposals to the network nervous system by using the SDK command-line interface (`dfx`) using calls to the `governance` canister.

A separate command-line tool (`icx-nns`) for working with the `governance` canister is in development and this functionality will also be available in the [Network Nervous System (NNS)](https://nns.ic0.app) dapp soon.

If you want to start submitting proposals right away, however, you can access a preliminary version of the `icx-nns` command-line tool by downloading a release from the [icx-nns](https://github.com/dfinity/icx-nns/releases) repository.


## SNS decentralization swaps

You can participate in SNS decentralization swaps from the NNS dapp using the 'Launch Pad' tab. An SNS is an advanced version of a decentralized autonomous organization, where the participants of the SNS can vote on proposals, such as a new feature, roadmap item, or allocation of SNS funds. 

You can learn more about SNS [here](https://internetcomputer.org/sns)

To view current and past SNS decentralization swaps from the NNS dapp:

- #### Step 1:  From the NNS dapp, click **Launch pad** from the left navigation bar.

![Launch pad](../_attachments/nns15.png)

- #### Step 2: Select any SNS swap to view the details, such as the status, total participants, token supply, and when the swap ended.

![SNS swap](../_attachments/nns16.png)

## Deploying and managing canisters from the NNS dapp

You must have **cycles** available to create and manage [canisters](/references/glossary.md#canister), which are similar to smart contracts. The [Network Nervous System (NNS)](https://nns.ic0.app) dapp provides a convenient way for you to create and manage canisters by enabling you to convert ICP utility tokens into cycles and attach cycles to specific canister identifiers.

To create a new canister:

- #### Step 1:  From the NNS dapp, click **My Canisters**, then click **Create Canister** or **Link Canister**.

![My canisters](../_attachments/nns11.png)

- #### Step 2: If you select **Create Canister**, select the source account you'd like to use to create the canister.

![Create a new canister](../_attachments/nns12.png)

- #### Step 3: Then, enter the amount of ICP tokens and cycles to create the canister with. Then select **Review Canister Creation**. 

- #### Step 4:  Review the ICP utility tokens to cycles details, then click **Confirm** to continue.

After you click Confirm, you can review: 
- The new canister identifier.
- The number of cycles available for the canister to use. 
- The controlling principal that currently has full management rights for the new canister.

:::info
\+ To change the principal used as the controller of the canister, click **Change Controllers**.
\+ To add cycles to the canister, click **Add Cycles**.
:::

![Review canister](../_attachments/nns13.png)

- #### Step 5: To link a canister, input the canister's ID, then select **Confirm**.

![Link canister](../_attachments/nns14.png)

- #### Step 6: Return to the **Canisters** tab to see the canisters you have created.

For example:

![Canister list](../_attachments/nns11.png)

-->
