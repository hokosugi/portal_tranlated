# VETKey入門

**VETKeys**機能は、Internet Computer （IC）で現在開発中です。これは「**Verifiable** **Encrypted** **Threshold**Keys（**検証可能な** **暗号化** **閾値**鍵）」の略で、IC上で多くの暗号機能を実現します。VETKeysの主な動機はオンチェーン暗号化を促進することであり、この入門書ではその例を念頭に置いています。

VETKeys機能についてあまり議論されていないことの1つは、暗号化という観点でどのようにしてここまでたどり着いたかということです。この投稿の目的は、VETKeysの講演や論文、そして今後の投稿をよりよく理解できるように、暗号の背景を説明することです。VETkeysのこれらの基礎を理解することは、アプリケーションを構築するためにVETkeysを使用するために必要なこと*では*ありませんが、より深く潜ることに興味があり、背景を理解したい人のために説明します。それでは、最初から始めましょう。

## 暗号プリミティブ

暗号技術において「[プリミティブ](https://en.wikipedia.org/wiki/Cryptographic_primitive)」とは、基本的な構成要素の一種であり、その機能のみを使用することも、他の、より複雑な暗号ツールやプロトコルを構築することもできます。
コアとなるプリミティブの例には、以下のようなものがあります：

- ブロック暗号（例：[AES）](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)
- ハッシュ関数（[SHA3](https://en.wikipedia.org/wiki/SHA-3)、[BLAKE3など）](https://en.wikipedia.org/wiki/BLAKE_\(hash_function\)#BLAKE3)
- 鍵交換（[Diffie-Hellmanなど）](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
- 署名スキーム（[ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm)、[BLSなど）](https://en.wikipedia.org/wiki/BLS_digital_signature)
- 公開鍵暗号化方式（[RSA](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\))、[ElGamalなど）](https://en.wikipedia.org/wiki/ElGamal_encryption)

VETKeys は*新しいプリミティブを*導入し、特に**VETKD を**導入しています。VETKDは、IDベース暗号化（IBE）と呼ばれる古いプリミティブを拡張したもので、それ自体が公開鍵暗号化を拡張したものです。

## 公開鍵暗号化（PKE）

*1976*年、Whit DiffieとMartin Hellmanが公開鍵暗号方式を紹介した論文「New Directions」の冒頭で述べた言葉です。

![DH ND](../_assets/dh76.png)

PKEは誰もが日常的に使用しているため、それが何であるかについてはある程度直感的に理解していると思われますが、後で構築するもののために、ここで説明します。PKE はメッセージを暗号化することで、公開された通信路で秘密裏に通信することを可能にします。アリスが暗号化されたメッセージをボブに送りたいとすると、 PKEスキームは以下のように実行されます：

- ボブは鍵生成アルゴリズム $mathsf{KG}$ を使って秘密鍵と公開鍵のペア $(\\mathit{sk\_{bob}, pk\_{bob}})$ を生成します。
- Bobは自分の公開鍵をオンライン(公開鍵基盤(PKI)など)に保存します。
- アリスはボブの公開鍵$mathit{pk\_{bob}}$を(例えばPKIから)取得し、それを使って暗号化アルゴリズム$mathsf{Enc}$を使ってボブへのメッセージを暗号化し、結果の暗号文をボブに送信します。
- ボブはアリスから暗号文を復号したいとき、自分の秘密鍵 ${mathit{sk\_{bob}}$ と復号アルゴリズム ${mathsf{Dec}}$ を使ってメッセージを復号し、取り出します。

![PKE](../_assets/pke.png)

:::info
公開鍵暗号の規格では、秘密鍵を生成し、そこから公開鍵を導出します。このため、公開鍵がどのように*見えるかを*ほとんど制御できず、 ユーザとその公開鍵のマッピングを管理するために、 信頼できる公開鍵基盤 (PKI) に頼る必要があります。これはすぐに複雑になり（暗号化された電子メールを送ろうとしたことがありますか？）、実用的なアプリケーションでの暗号の使用を妨げることになります。
::：

## IDベースの暗号化（IBE）

暗号化における多くのものと同様に、IBEもAdi Shamir \[Shamir84\]によって導入されました。具体的なインスタンス化の提供は、1984年の導入から2001年まで未解決の問題のままでした。2つのIBEスキームが提案されました。ここでは、Dan BonehとMatthew Franklinによって導入されたIBEに焦点を当てます。

![BF IBE](../_assets/BF01.png)

IBEは、PKEの使い勝手の問題のいくつかに対処しています。IBEは、任意の文字列を公開鍵（"alice@email.com "や"@alicetweets "など）とし、そこから秘密鍵を導出します。

IBE スキームがどのように機能するかを見るために、次のシナリオを考えてみましょう。アリスが$mathit{id\_{bob}}$を使ってボブへのメッセージを暗号化したいとします。典型的なシナリオでは、信頼できるKey Deriver (KD)が存在し、以下のように実行されます：

- KDはIBE鍵生成アルゴリズムを実行し、マスター鍵ペア($mathit{msk, mpk}$)を生成。
- AliceはIBE暗号化アルゴリズムを実行し、$mathit{id\_{bob}}$とKDの$mathit{mpk}$を使用してBobへのメッセージを暗号化し、結果の暗号文をBobに送信。
- Bobは$mathit{id\_{bob}}$をKDに認証し、対応する復号(秘密)鍵($mathit{sk\_{bob}}$)を要求。
- KDは$mathit{msk}$を用いて$mathit{id\_{bob}}$から$mathit{sk\_{bob}}$を導出し、Bobに送信。
- Bobは$mathit{sk\_{bob}}$と$mathit{id\_{bob}}$を使ってAliceからの暗号文を復号し、メッセージを取得。

![IBE Example](../_assets/ibe.png)

:::info

このタイプのIBEスキームについて注意すべき重要な点があります。ブロックチェーンの世界に身を置く私たちは、当然ながら信頼できるサードパーティーと仕事をすることに熱心ではありません。したがって、IBEの鍵の導出手順を分散化することが1つの中心的な目標です。

:::

## VETKD

ブロックチェーンが非常にパブリックな場所であり、完全性と可用性を得るために透明性が重要なfactor であることを考慮すると、競合しない方法で機密性やプライバシーを達成する方法はすぐには明らかではありません。これがVETKDの使命です。

### 閾値の設定

ここでは秘密***鍵の導出に***最も注意を払いますが、それは最も機密性の高い部分であり、中央の（潜在的に信頼されていない、認可されていない、または危殆化した）一人の当事者から保護したいためであり、それゆえVETKDの**KD**であることに注意してください。中央集権化という点に対処するために、分散設定に移行する必要があります。  信頼できるパーティが1つもないと仮定して、複数のパーティに信頼を分散させ、そのうちのある*閾値の*パーティが復号鍵を導出するためにマスター秘密鍵の共有について協力することを要求します。

パーティはどのようにしてマスター秘密鍵を**共有**するのでしょうか。これは分散鍵生成(DKG)プロトコルを活用することで行われ、ある閾値の誠実なパーティ(ノード)が協力してマスター鍵の共有セットを取得します。ノード間の共謀がないと仮定すると、1つのノードが完全な秘密鍵を保持することはありません。
クリックすると、[閾値暗号](https://en.wikipedia.org/wiki/Threshold_cryptosystem)、[DKG](https://en.wikipedia.org/wiki/Distributed_key_generation)、[Boneh-Shoup本の](http://toc.cryptobook.us/)第22章について詳しく知ることができます。

上記から明らかなように、中央集権的な鍵の導出プロセスは不要であり、 そのためにKDプロセスに**Tが**必要なのです。VとEについてはどうなのでしょうか？

### 構文

アリスが暗号化されたメッセージを（パブリック・ブロックチェーンを介して）ボブに送りたいとします。鍵の管理は特にWeb3では難しいので、*オンデマンドで鍵を導出*できることが望ましいです。シナリオは以下の通り：

- ネットワークのノードは$mathsf{DKGプロトコル}$に参加して、マスター秘密鍵（$mathit{msk}$）とマスター公開鍵（$mathit{mpk}$）の共有を取得。この結果、各ノード$i$は鍵共有$( \\mathit{msk\_i, mpk\_i})$を持つことになります。
- アリスはボブのアイデンティティ$mathit{id\_{bob}}$とマスター公開鍵$mathit{mpk}$でメッセージを暗号化し、結果の暗号文をボブに送信します。
- Bobは復号したいので、$mathit{id\_{bob}}$をICに認証し、復号鍵の導出を要求。停止！

このシナリオを続行すると、ノードは復号鍵を導出してBobに共有を送信することに なるが、パブリックネットワークでは、これらの共有は見ることができ、 オブザーバによって結合される可能性があることに注意。そのため、観測者や悪意のあるノードがそれらを組み合わせて$mathit{sk\_{bob}}$を取得できないように、導出された鍵共有は暗号化されて転送される必要があります。では続けましょう。

- Bobは復号化したいので、$mathit{id\_{bob}}$をICに認証。トランスポート鍵生成アルゴリズム$mathsf{TKG}$を用いてトランスポート公開鍵$mathit{tpk}$を生成・送信し、復号鍵の導出を要求。Bobは$mathit{tpk}$を送信することで、ノードに自分への応答を暗号 化する方法を与える。
- Bobの$mathit{id\_{bob}}$に対する認証が通ると(おそらくdapp)、ネットワーク内のノードは$mathsf{EKDerive}$アルゴリズムを使用して、$mathit{msk}$と$mathit{id\_{bob}}$を使用して復号鍵共有を導出し、$mathit{tpk\_{bob}}$の下で暗号化する。これはVETKDの**E**要件です。

閾値システムでは、有効な鍵を生成するのに十分な数の鍵共有が必要です。この場合、いつ有効な鍵共有が十分にあるかどうかを知ることは、 処理を停止させるために有用である。

- 誰でも$mathsf{EKSVerify}$アルゴリズムを使って、 暗号化された鍵共有に本当に正当な復号鍵共有が含まれているかどうかを 検証することができるので、いつ「十分な数の」有効な鍵共有が存在するかを知る ことができます。これがVETKDにおける**V**要件の説明です。
- ノードはまた、暗号化された共有を結合して、$mathsf{Combine}$ アルゴリズムを使用して、 完全な暗号化された派生鍵$mathit{ek}$を生成することができる。
- EKVerify}$アルゴリズムにより、誰でも$mathit{ek}$が本当に$mathit{tpk\_{bob}}$の下で暗号化された$mathit{mpk}$の下で$mathit{id\_{bob}}$の正当な派生鍵を含んでいることを検証できる。
- 最後に、復元アルゴリズム$mathsf{Recover}$により、ボブのTSKを用いて、$mathit{msk}$配下の$mathit{id\_{bob}}$に対応する派生鍵を復号可能。
- これでBobは復号できます。

この図は論文からの抜粋。
![VETKD Example](../_assets/vetkdscene.png)

言及したすべてのアルゴリズム（$mathsf{DKG, TKG, EKDerive, EKSVerify, Combine, EKVerify, Recover}$）は、VETKDプリミティブを記述する*構文を*形成します。プリミティブを完全に記述するには、正しさ（プリミティブの意図された動作の記述）、安全性（どのような敵対者からのどのような攻撃の下でプリミティブが安全であり続けるか）、および構築（望ましい構文、正しさ、および安全性をキャプチャするプロトコルを構築する方法の記述）にも注意する必要があります。正しさと安全性はアプリケーション（署名、IBEなど）によって異なるので、これらの概要を知るには論文に譲ります。

### 構築

さて、VETKDの目的とその記述方法がわかりました。次の自然な疑問は、このようなプリミティブをどのように構築できるかということです。どの構成要素が必要でしょうか？

一見したところ、ノード間でマスター秘密鍵の共有を生成・配布するための分散鍵生成スキームが必要であることが推測できます。また、ユーザのトランスポート鍵の下で派生鍵共有を暗号化する公開鍵暗号化スキームが必要であることも推測できます。残る主な問題は、復号鍵をどのように導出するかです。

重要なのは、\[BF01\]に埋もれたある観察が答えを与えてくれることです。Moni Naorは、IBEスキームが署名スキームに直接変換できることを指摘しています。Boneh-Franklin IBEの鍵導出を具体的に考えると、結果として得られる署名スキームはBLSになります。

### BLS署名

デジタル署名は、メッセージや取引、その他の情報の真正性を証明するために、暗号やブロックチェーンの世界のあらゆるところで使用されています。デジタル署名は非常に普及しているため、デジタル署名について時間をかけて学ぶ価値があります。ウィキペディア[（デジタル署名と](https://en.wikipedia.org/wiki/Digital_signature) [BLS](https://en.wikipedia.org/wiki/BLS_digital_signature)）でハイレベルな見解を得ることができ、より正式な詳細を知りたい場合は[ボネ・シュウプの本を](http://toc.cryptobook.us/)読んでください。

BLS署名は、2001年にDan Boneh、Ben Lynn、Hovav Shachamによって導入されたデジタル署名の一種です。

![BLS Signatures Abstract](../_assets/BLS01.png)

BLS署名の主な特徴は、非常に短く、一意で、計算が速く、集約可能で、分散環境に移植しやすいことです（少なくとも他の署名方式に比べて...）。
他の署名スキームと同様に、BLSは3つのアルゴリズムで構成されています。（潜在的に分散された）鍵生成アルゴリズム（(D)KG）、署名アルゴリズム（Sign）、検証アルゴリズム（Verify）です。閾値設定では、これは4番目の組み合わせアルゴリズム（Combine）を含むように拡張されます。
閾値BLS署名は、Internet Computer 、シナリオの動機付けとなる例としてそれを使用してみましょう。あるサブネットのノードが、特定のメッセージがICから送信されていることをアリスに確信させたいとします。非常に高いレベルで、シナリオは以下のように実行されます：

- ネットワークのノードはDKGプロセスに参加し、(秘密)鍵共有を取得します。
- 各ノードは署名鍵の共有を使ってメッセージ$m$の署名共有を計算。
- ノードは$mathsf{Combine}$プロセスに参加して署名共有を結合し、Aliceに送 信される単一の署名を生成。
- アリスは検証アルゴリズム$mathsf{Verify}$ を使って、ノードから送られた署名がInternet Computer の公開鍵で検証されるかどうかをチェックする。

IBEが署名を含意することは前述のとおり。BF01\]の論文から、直感的な構成は、署名スキームの秘密鍵をIBEのマスターキーに設定することです。次に署名スキームの公開鍵をIBEのシステムパラメータとします。VETKDシナリオでは、IBEスキームのマスター・キーはノード間で共有されるBLS署名キーの秘密鍵です。導出されたIDは閾値署名され、対称暗号化鍵としてだけでなく、Boneh-Franklin復号鍵としても機能する署名が得られます。

### すべてのまとめ

VETKDは、IDベースの暗号化を分散環境で拡張するために使用できる新しいプリミティブです。VETKDを構築するために必要な主なツールは、DKG、PKE（ElGamalを使用）、閾値BLS署名であり、以下のように鍵を取得するために使用されます：

- マスター鍵 - DKG発行のBLS署名鍵
- トランスポート鍵 - ElGamal鍵ペア
- 暗号化鍵共有 - ElGamal公開鍵で暗号化されたID上のBLS署名共有。
- 暗号化鍵の結合 - 有効な暗号化鍵シェアの閾値が結合され（ブロックチェーンシナリオではブロックメーカーが結合）、暗号化された派生鍵が生成されます。
- 復号鍵 - 結合された暗号化された派生鍵のElGamal復号。

これらのVETKeyを持つことで、機能のゴリマインが開かれます。VETKeyファミリーには、VETKDからVETIBE、VETSigs、VETPRF、VETVRFへの拡張など、さらなる説明があります。今後の投稿では、これらについて詳しく説明します。

## 備考

このページは、VETKDとそのビルディング・ブロックのハイレベルな見解と説明を含みます。このページのゴールは、技術的な選択肢についてもっと知りたいが、研究論文を読むのに必要な暗号学的なバックグラウンドが（今のところ）ないかもしれない、IC上で構築する開発者のための直感を構築することです。
また、VETKDを構築する1つの可能な方法を示しています。他にも、論文でより詳しく説明されている、いくつかの派手な機能を持つものがあります。VETKDを構築するための多くのユースケースや動機があり、それらは[ビデオの](https://youtu.be/baM6jHnmMq8)中で議論されています。また、コミュニティで何が必要とされているかによって、構築できる拡張機能もあります。最後に、このページはonchainでホストされていることに注意してください。

## 参考文献

- [BS23](http://toc.cryptobook.us/)- The Boneh-Shoup Book.
- [BF01](https://crypto.stanford.edu/~dabo/papers/bfibe.pdf)- IBEの論文。
- [BLS01](https://www.iacr.org/archive/asiacrypt2001/22480516.pdf)- BLSの論文。
- [DH76](https://ee.stanford.edu/~hellman/publications/24.pdf)- DiffieとHellmanのNew Directions論文。
- [VETKD Youtube](https://youtu.be/baM6jHnmMq8)- VETKD Community Conversation intro.

## 参加する

素敵な暗号ツールを作るという点で、私たちにできることは限られています。この業界で成功する最善の方法は、参加することです。ですからVETKDがあなたのプロジェクトに役立つとお考えでしたら、私たちにお知らせください。フィードバックをお聞かせください。現在、最も簡単な方法は、[フォーラムでの](https://forum.dfinity.org/t/threshold-key-derivation-privacy-on-the-ic/16560)議論に参加することです。また、「いいね！」、「シェア」、「購読」、その他全てにご協力をお願いします。

<!---
# VETKeys Primer

The **VETKeys** feature is in ongoing development on the Internet Computer (IC). It stands for ‘**V**erifiable **E**ncrypted **T**hreshold Keys’ and enables a number of cryptographic functionalities on the IC. The primary motivation for VETKeys is to facilitate onchain encryption, as such we focus this primer with that example in mind. 

One thing less discussed about the VETKeys feature is how we got here in terms of cryptography. The goal of this post is to lay some crypto background so that you can better understand the VETKeys talks, paper, and future posts. Note that understanding these foundations of VETkeys will *not* be necessary to use them for building applications, but we explain for those who are interested to dive deeper and want to understand the background. Let’s start at the start. 

## Crypto primitives
In cryptography, a ‘[primitive](https://en.wikipedia.org/wiki/Cryptographic_primitive)’ is a kind of foundational building block that can be used solely for its given functionality, or to build other, more complex, cryptographic tools and protocols.
Some examples of the core primitives include: 
* Block ciphers (eg. [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard))
* Hash functions (eg. [SHA3](https://en.wikipedia.org/wiki/SHA-3), [BLAKE3](https://en.wikipedia.org/wiki/BLAKE_(hash_function)#BLAKE3))
* Key exchange (eg. [Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange))
* Signature schemes (eg. [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), [BLS](https://en.wikipedia.org/wiki/BLS_digital_signature))
* Public key encryption schemes (eg. [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)), [ElGamal](https://en.wikipedia.org/wiki/ElGamal_encryption))

VETKeys introduces *new primitives*, and most notably **VETKD**. VETKD extends an older primitive called identity based encryption (IBE), which itself is an extension of public key encryption.

## Public key encryption (PKE)
*"We stand today on the brink of a revolution in cryptography"*, possibly the greatest opening line of any crypto paper, was penned by Whit Diffie and Martin Hellman in 1976 in their New Directions paper introducing public key cryptography. 

![DH ND](../_assets/dh76.png)

As everyone uses PKE everyday, it's assumed that you have some intuition about what it is, but we describe it here to set the stage for what we build later. PKE allows to communicate confidentially over a public channel by encrypting messages. Suppose Alice wants to send an encrypted message to Bob, a PKE scheme will run somewhat as follows: 

* Bob uses a key generation algorithm $\mathsf{KG}$ to generate a private and public key pair $(\mathit{sk_{bob}, pk_{bob}})$.
* Bob stores his public key online (eg in a public key infrastructure (PKI)).
* Alice retrieves Bob's public key $\mathit{pk_{bob}}$ (eg from the PKI) and uses it to encrypt a message to Bob using an encryption algorithm $\mathsf{Enc}$ and sends the resulting ciphertext to Bob.
* When Bob wants to decrypt the ciphertext from Alice, he uses his secret key ${\mathit{sk_{bob}}}$ with a decryption algorithm $\mathsf{Dec}$ to decrypt and retrieve the message.

![PKE](../_assets/pke.png)

:::info
The standard practice in public key cryptography is to generate a secret key, and from that, derive a public key. This gives little control over how the public key *looks* and results in us needing to rely on a trusted public key infrastructure (PKI) to manage mappings between users and their public keys. This can get complicated very quickly (have you ever tried to send an encrypted email?) and discourages use of crypto in practical applications.
::: 

## Identity based encryption (IBE)
As many things in cryptography, IBE was introduced by Adi Shamir [Shamir84]. Providing a concrete instantiation remained an open problem from its introduction 1984 to 2001, when there was a breakthrough in of Number Theory which gave some new mathematical tools to build with. Two IBE schemes were proposed, based on different hard problems. Here we will focus on the IBE introduced by Dan Boneh and Matthew Franklin which we will refer to as [BF01].￼

![BF IBE](../_assets/BF01.png)

IBE addresses some of the usability issues with PKE. It allows to take an arbitrary string as the public key (say “alice@email.com” or “@alicetweets”) and derive the secret key from that.

To see how an IBE scheme can work, let's consider the following scenario. Suppose Alice wants to encrypt a message to Bob using $\mathit{id_{bob}}$. The typical scenario requires that there is a trusted Key Deriver (KD), and runs as follows:

* KD runs the IBE key generation algorithm to generate a master (private and public) key pair ($\mathit{msk, mpk}$).
* Alice runs the IBE encryption algorithm to encrypt a message to Bob using $\mathit{id_{bob}}$ and KD’s $\mathit{mpk}$ and sends the resulting ciphertext to Bob.
* Bob authenticates $\mathit{id_{bob}}$ to KD and requests a corresponding decryption (private) key ($\mathit{sk_{bob}}$). 
* KD derives $\mathit{sk_{bob}}$ from $\mathit{id_{bob}}$ using $\mathit{msk}$ and then sends it to Bob.
* Bob uses $\mathit{sk_{bob}}$ and $\mathit{id_{bob}}$ to decrypt the ciphertext from Alice to retrieve the message.

![IBE Example](../_assets/ibe.png)

:::info

There is a crucial point to note about this type of IBE scheme; A central authority derives (decryption) keys. As we find ourselves in the blockchain world, naturally we are not keen to work with a trusted third party, so one core goal is to decentralize the key derivation procedure of IBE.

:::

## VETKD
Considering that blockchains are very public places where transparency has been a crucial factor in gaining integrity and availability, it is not immediately obvious how one would achieve confidentiality or privacy in a non-competing way. This is the mission of VETKD.

### The threshold setting
Note that we care most about the secret ***key derivation*** here, as that is the most sensitive part which we want to protect from one central (potentially untrusted, unauthorized, or compromised) party, and hence the **KD** in VETKD. To deal with the centralization point, we need to move into the distributed setting.  Assuming there is no one trusted party, we distribute trust amongst multiple parties, and require that some *threshold* of them collaborate on shares of the master secret key to derive decryption keys.

How do parties **get shares** of the master secret key? This is done by leveraging a distributed key generation (DKG) protocol, where a threshold of honest parties (or nodes) work together to obtain a set of master key shares. Assuming no collusion between nodes, at no point does any one node hold the full private key.
Click around to learn more about [threshold cryptography]( https://en.wikipedia.org/wiki/Threshold_cryptosystem), [DKG](https://en.wikipedia.org/wiki/Distributed_key_generation) and chapter 22 in the [Boneh-Shoup book](http://toc.cryptobook.us/).

It’s clear from above that we don't want a centralised key derivation process and this is why we need the **T** for the KD process, but what about **V** and **E**? Perhaps this is best highlighted by a scenario.

### Syntax
Suppose Alice wants to send an encrypted message (across a public blockchain) to Bob. We know that key management is hard, especially in the Web3 setting, so it’s desirable to be able to *derive keys on demand*. The scenario runs as follows:

* Nodes in the network participate in the $\mathsf{DKG protocol}$ to obtain shares of a master secret key ($\mathit{msk}$) and a master public key ($\mathit{mpk}$). This results in each node $i$ holding key shares $(\mathit{msk_i, mpk_i})$.
* Alice encrypts a message under Bob's identity $\mathit{id_{bob}}$ and the master public key $\mathit{mpk}$ and sends the resulting ciphertext to Bob.
* Bob wants to decrypt and authenticates $\mathit{id_{bob}}$ to the IC and requests to derive a decryption key. Stop! 

Note that if we continue in this scenario, the nodes will derive a decryption key and send the shares to Bob.. but, in a public network, those shares can be seen and can be combined by an observer. We require that derived key shares are encrypted for transport so that any observer or malicious nodes cannot combine them to obtain $\mathit{sk_{bob}}$. So, let’s continue.

* Bob wants to decrypt and authenticates $\mathit{id_{bob}}$ to the IC. He uses a transport key generation algorithm $\mathsf{TKG}$ to generate and send a transport public key $\mathit{tpk}$ and requests to derive a decryption key. By sending $\mathit{tpk}$ Bob gives the nodes a way to encrypt their responses to him.
* If Bob’s authentication to $\mathit{id_{bob}}$ passes (likely performed by a dapp), nodes in the network use an $\mathsf{EKDerive}$ algorithm derive decryption key shares using $\mathit{msk}$ and $\mathit{id_{bob}}$ and encrypt them under $\mathit{tpk_{bob}}$. Note, this is the **E** requirement in VETKD.

In a threshold system, sufficiently many key shares are required to produce a valid key. In this case it is useful to know when or if we have sufficiently many valid key shares so that the process can stop.

* Anyone can use an $\mathsf{EKSVerify}$ algorithm to verify that the encrypted keys shares do indeed contain a legitimate decryption key share, and thus can know when 'enough' valid shares exist. This explains the **V** requirement in VETKD.
* Nodes can also combine encrypted shares to produce the full encrypted derived key $\mathit{ek}$ using a $\mathsf{Combine}$ algorithm. 
* An $\mathsf{EKVerify}$ algorithm allows anyone to verify that $\mathit{ek}$ does indeed contain a legitimate derived key for $\mathit{id_{bob}}$ under $\mathit{mpk}$ encrypted under $\mathit{tpk_{bob}}$. 
* Finally, a recovery algorithm $\mathsf{Recover}$ enables Bob to decrypt the derived key corresponding to $\mathit{id_{bob}}$ under $\mathit{msk}$ using Bob’s TSK.
* Bob can now decrypt.

This picture is taken directly from the paper, where you can read the full scenario.
![VETKD Example](../_assets/vetkdscene.png)

All algorithms mentioned ($\mathsf{DKG, TKG, EKDerive, EKSVerify, Combine, EKVerify, Recover}$) form the *syntax* that describes the VETKD primitive. To describe a primitive fully, it's needed also to note the correctness (a description of the primitive's intended behavior), security (under what kinds of attacks from which kinds of adversaries will the primitive remain secure), and a construction (a description of how we can construct a protocol that captures the desired syntax, correctness and security). Correctness and Security differ depending on the application (signatures, IBE, etc) so we defer to the paper to get an overview of these.

### Construction
Now we see the aim for VETKD, and how it can be described. The next natural question is to ask how we can build such a primitive. Which building blocks do we need?

At a first glance, we could guess that we will need a distributed key generation scheme to generate and distribute master secret key shares among the nodes. We could also guess that we'll need a public key encryption scheme to encrypt derived key shares under the transport key of the user. The main question that remains is how to derive decryption keys.

Crucially, An observation buried in [BF01] gives us the answer. Moni Naor noted that an IBE scheme can be directly converted into a signature scheme. Considering the key derivation of Boneh-Franklin IBE specifically, the resulting signature scheme happens to be BLS.

### BLS signatures
Digital signatures are used everywhere in cryptography and in the blockchain world to attest to the authenticity of a message, transaction, or other pieces of information. As they are so prevalent, it’s really worth spending time getting to know them. You can get a high level view on wikipedia ([Digital Signatures](https://en.wikipedia.org/wiki/Digital_signature) and [BLS](https://en.wikipedia.org/wiki/BLS_digital_signature)), and dive into the [Boneh-Shoup book](http://toc.cryptobook.us/) when you want more formal details.

BLS signatures are a particular type of digital signature introduced in by Dan Boneh, Ben Lynn, and Hovav Shacham in 2001. 

![BLS Signatures Abstract](../_assets/BLS01.png)

The main feature of BLS signatures is that they’re very short, unique, fast to compute, aggregatable, and easy to port to the distributed setting (relative to other signature schemes at least..). This makes them a great candidate signature scheme for the blockchain setting. 
As with any signature scheme, BLS comprises three algorithms; a (potentially distributed) key generation algorithm ((D)KG), a signing algorithm (Sign) and a verification algorithm (Verify). In the threshold setting, this is extended to include a fourth combination algorithm (Combine).
Threshold BLS signatures are used a lot on the Internet Computer, so let’s use that as the motivating example for the scenario. Suppose nodes in a subnet want to convince Alice that a particular message is being sent from the IC. At a very high level, the scenario will run as follows:
* Nodes in the network participate in the DKG process and obtain (private) key shares.
* Each node computes a signature share on a message $m$ using its share of the signing key. 
* Nodes participate in a $\mathsf{Combine}$ process to combine signature shares and produce a single signature which is then sent to Alice.
* Alice uses a verification algorithm $\mathsf{Verify}$ to check whether the signature sent from the nodes verifies under the public key of the Internet Computer.

We noted above that IBE implies signatures. From the [BF01] paper the intuitive construction is to set the private key for the signature scheme to be the master key of the IBE. Then set the public key for the signature scheme to be the system parameters of the IBE. Then the signature on a message M is the IBE Decryption key for ID = M. In the VETKD scenario, the master key of the IBE scheme is a BLS signature key secret shared over the nodes. The derivation identity will be threshold signed, resulting in a signature that can act as a symmetric encryption key, but also as a Boneh-Franklin decryption key.

### Putting everything together
VETKD is a new primitive that can be used to extend identity based encryption in a decentralized setting. The main tools needed to build VETKD are a DKG, a PKE (we use ElGamal), and threshold BLS signatures, and are used to obtain keys as follows: 

* Master key - DKG issued BLS signing key
* Transport keys - ElGamal key pair
* Encrypted key share - BLS signature shares on the identity, encrypted under ElGamal public key
* Combined encrypted key - A threshold of valid encrypted key shares are combined (in the blockchain scenario likely by a block maker) to give the encrypted derived key
* Decryption key - ElGamal decryption of combined encrypted derived key

Having these VETKeys opens a goldmine of functionality. There are further descriptions the VETKey family, i.e. extending VETKD to VETIBE, to VETSigs, to a VETPRF, and VETVRF. Future posts can go into details on these.

## Remarks
This page contains a high level view and description of VETKD and its building blocks. The goal of this page is to build intuition for developers building on the IC, who are interested to know more about the technical choices, but who may lack the cryptographic background necessary to read research papers (for now). 
It also shows one possible way of building VETKD, there are others, some with fancy features, that are described more in the paper. There are many use cases and motivations for building VETKD, these are discussed in [the video](https://youtu.be/baM6jHnmMq8) and can be written up if you like. There are also extensions that could be built depending on what is needed in the community. Finally, note that this page is hosted onchain.

## References
* [BS23](http://toc.cryptobook.us/) - The Boneh-Shoup Book.
* [BF01](https://crypto.stanford.edu/~dabo/papers/bfibe.pdf) - The IBE paper.
* [BLS01](https://www.iacr.org/archive/asiacrypt2001/22480516.pdf) - The BLS paper.
* [DH76](https://ee.stanford.edu/~hellman/publications/24.pdf) - Diffie and Hellman's New Directions paper.
* [VETKD Youtube](https://youtu.be/baM6jHnmMq8) - The VETKD Community Conversation intro.

## Participate
There is only so much we can do in terms of producing nice crypto tools. It’s up to you to pick them up and use them to address real world privacy issues faced in Web3. The best way to succeed in this industry is to engage. So! Let us know what you’re building and if you think VETKD could be useful for your project. We’re happy to hear feedback and to explain more things if you need them. Currently the easiest way to engage is to join the discussion on the [forum](https://forum.dfinity.org/t/threshold-key-derivation-privacy-on-the-ic/16560). Also, like, share, subscribe, and all the rest.

-->
