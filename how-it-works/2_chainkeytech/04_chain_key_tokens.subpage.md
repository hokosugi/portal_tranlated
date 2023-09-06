---

title: Chain-key Tokens
abstract:
shareImage: /img/how-it-works/ck-tokens-content.600x300.jpg
slug: chain-key-tokens
---
# チェーンキートークン

異なるブロックチェーンが協力する重要な方法の1つは、あるブロックチェーンから別のブロックチェーンにトークンを持ち込むことです。従来のブロックチェーンアーキテクチャでは、これは主にラッピングによって達成されます。より分散化された（そして安全な）代替手段が存在し、ラッピングに取って代わることができます：*チェーンキー暗号と* *チェーンキートークン*です！

*ラッピングトークンは*原資産を表しますが、その原資産は通常、ラッピングトークンとは別のブロックチェーン上に存在します。例えば、ラッピングされたビットコイントークンは本物のビットコインを表しますが、ビットコインとは異なるトークンであり、イーサリアムのブロックチェーンなど、異なるチェーン上で利用可能です。従来のラッピングは、常に信頼される必要のある仲介者を伴います。

トークンをラッピングするより安全な代替手段は、高度な閾値暗号を使用して*チェーンキートークンを*取得することです。Internet Computer 、最初の主要なチェーンキートークンであるチェーンキートークンBitcoinは、ICP上のトークンであり、スマートコントラクトによってICP上のチェーン上に100%保持される本物のビットコインと1:1の裏付けを持ちます。従って、Internet Computer 上のビットコイン「ツイン」であり、低い取引手数料とレイテンシ、高いスループットを特徴とし、ビットコインのレイヤ 2 と同様の特性を持ちます。このページでは、チェーンキートークンの例としてチェーンキービットコインを使用します。チェーンキー型トークンは、ICRC-1 トークン規格を実装し、例えばウォレットや DEX などのサービスに簡単に統合できるようにする必要があります。これにより、チェーンキートークンを幅広いサービスで容易に利用できるようになります。

## 従来のラップトークン

従来のブロックチェーンアーキテクチャでは、トークンのラッピングにはオフチェーンの信頼できる仲介者とトークン台帳のスマートコントラクトが関与します。特定の種類のトークン、例えばラッピングされたビットコイントークンを持ちたいユーザーは、ビットコインなどの原資産のトークンを仲介者に送ります。仲介者は、トークンのネイティブブロックチェーン上で原資産トークンの移動を確認すると、受け取ったトークンを保管し、トークン台帳に原資産トークンを受け取ったのと同量のラップトークンを発行（*造幣*）するよう指示します。発行により、ラップトークンの供給量が増加します。新しく発行されたラップトークンは、トークンのブロックチェーン上で使用することができ、トークン台帳はすべてのアカウントを追跡します。

ユーザーがラッピングトークンを原資産と交換したい場合、これにも仲介業者が関与します：ユーザーは仲介者が管理するアドレスにラップされたトークンを送り、アンラップのリクエストを行います。仲介者は受け取ったラップトークンの量をラップトークンの供給量から取り除き、原資産をネイティブにホストするブロックチェーン上で対応する量の原資産トークンをユーザーに返します。

一般ユーザーは、ラッピングされたトークンを使用するだけでよく、通常、ラッピングやアンラッピングに煩わされる必要はありません。このように、ラップトークンはほとんどのユーザーにとって便利であり、同じブロックチェーン上のネイティブトークンと同じくらい使いやすいものです。

この伝統的なオフチェーン・ラッピング・アプローチは、機能的な観点からはうまく機能しますが、トークンのラッピングとアンラッピングのセキュリティにとって重要な完全性を持つ仲介者が関与するという大きな欠点があります。主な問題は、仲介者が危険にさらされる可能性があることです。例えば、ハッキングされたり、内部関係者に騙されたり、廃業したりする可能性があります。仲介者を分散化するために、複数の当事者が鍵を保有するマルチシグネチャースキームなどの戦略は、ある程度の緩和策を提供することはできますが、ラッピングがオフチェーンエンティティまたはエンティティのグループによって行われるという事実は変わりません。つまり、このアーキテクチャは完全には分散化されていません。全体として、ラップトークンを実現するこの伝統的な方法は、セキュリティ、リスク、およびその本質的な中央集権的性質の理由から望ましくありません。

このようなラッピングアーキテクチャには、もう1つの潜在的なリスクがあります：理想的には、ラップトークンは常に基礎となるトークンによって1対1で裏付けされています。しかし実際には、仲介者は保管されているトークンを使って、例えばリスクの高い投資に使うなどして利益を生み出すことができます。最悪の場合、物事がうまくいかないと、トークンの損失とラップトークンのデペッグにつながる可能性があります。

## チェーンキートークン

Chain-Key Bitcoin のようなチェーンキートークンは、より強固なセキュリティを提供する、ラップトークンに代わる高度な暗号ベースのトークンです：チェーンキートークンでは、すべての操作はスマートコントラクトによって完全にチェーン上で実行され、チェーン外の仲介者は一切関与しません。これにより、従来のラッピングアーキテクチャの仲介に関連するリスクが排除されます。ラッピングをオンチェーン操作に置き換えることは、ブロックチェーンネットワーク間のネイティブな統合と、特に高度な暗号技術である*チェーンキー署名の*使用によってのみ可能になります。チェーンキービットコイン（ckBTC）はICPで利用可能な最初のチェーンキートークンです。各ckBTCトークンに対して、canister スマートコントラクトによって本物のビットコインがチェーン上に保持されます。

次に、ネイティブチェーンの統合と高度な暗号技術に基づくチェーンキートークンの機能の概要を説明します。

### アーキテクチャ

ICP上のチェーンキートークンのアーキテクチャは、以下のビルディングブロックを基盤としています：

1.  基礎となるトークンをホストする他のブロックチェーン、例えばビットコインネットワークとその唯一のトークンであるビットコインと、何らかの形でネイティブに統合できること。この統合により、ICP上のcanisters 、ネイティブ・チェーン上の基礎トークンのアドレスの残高を照会したり、基礎チェーンにトランザクションを送信したりできること。このネイティブチェーンとの統合は、仲介者を必要としないように、つまり完全に分散化され、チェーン上で実現されるように行われなければなりません。
2.  原資産をホストするブロックチェーン上のトランザクションに署名するために使用される署名スキームのチェーンキー実装が利用可能でなければなりません。この機能により、仲介者を介さずに閾値暗号を使用して、原トークンのチェーン上の取引に完全に署名することが可能になります。

これら2つのビルディングブロックが、特定のブロックチェーンとのネイティブな統合を構成します。ICPはすでにこのアプローチでビットコインネットワークと統合されており、イーサリアムとの統合も計画されています。この基盤に基づいて、チェーンキートークンの実装をスマートコントラクト層で構築することができます。特定のトークン、例えばビットコインに対してチェーンキートークンを実装するために可能なアーキテクチャの1つは、2つのスマートコントラクト（canister ）を使用します：*チェーンキートークン台帳*と*チェーンキートークンスマートコントラクト*です。チェーンキート*ークン台帳には*、エコシステムの幅広い互換性のためにICRC-1トークン規格を使用する必要があります。どのようなチェーンキートークンを導入する場合でも、同じオープンソースの台帳コードベースを使用し、パラメータ化することができます。チェーンキートークン台帳に加えて、このアーキテクチャでは*チェーンキートークンスマートコントラクトが*使用されます。このスマートコントラクトcanister は、基本的に従来のラッピングアーキテクチャのオフチェーン仲介機能を、チェーンキートークンアーキテクチャのオンチェーン機能に置き換えたものです。このcanister は、流入・流出する基礎トークンに基づいてチェーンキートークンの供給を生成（および除去）する役割を担うため、*マイナーとも*呼ばれます。そのため、原資産とチェーンキートークンの1対1の裏付けが保証されます。原資産トークンによるチェーンキートークンの 1:1 の裏付けは、採掘者が保持していると主張するビットコイン UTXO が、採掘者が管理するビットコインアドレスによって実際に保持されていることを確認することで、誰でも検証できます。

<figure>
<img src="/img/how-it-works/chain_key_token_arch.png" alt="Architecture for chain-key tokens" title="Architecture for chain-key tokens" align="center" style="width:1000px">
<figcaption>High-level architecture for chain-key tokens on ICP</figcaption>
</figure>

トークン台帳と採掘者の機能は、単一のスマートコントラクトに統合することも、2つ以上のスマートコントラクトに統合することもできます。しかし、概要を説明したように機能を2つのcanisters に分割することは、モジュール性の理由から賢明なアプローチです。なぜなら、同じ台帳canister コードをどのチェーンキートークンにも再利用することができ、特にビットコインとイーサリアムのような UTXO ベースのチェーンとアカウントベースのチェーンの違いをそれぞれ考慮するために、採掘者だけが基礎となるチェーンのトークンの仕組みに適応する必要があるからです。

### 基礎トークンからのチェーンキートークンの作成

ユーザーが、例えばビットコインなどの基礎トークンのチェーンキートークンを取得したい場合、ラッピングのように仲介者を介さず、チェーンキートークンのスマートコントラクト（マイナー）が所有するオリジンのブロックチェーン上のアドレスに基礎トークンを送信します。チェーンキートークンのスマートコントラクトはオリジンブロックチェーン上のアドレスを持っており、これはチェーンキー暗号によって実現されているため、チェーンキートークン、または具体例としてチェーンキービットコインと呼ばれています：ビットコインの例では、チェーンキーECDSA署名技術（具体的には、閾値ECDSAの高度な形式）を使用して、チェーンキーBitcoinスマートコントラクトは、ビットコインネットワークの他のユーザーと同様に、ECDSAキーペアを持ち、使用することができますが、完全にチェーン上にあります。つまり、スマートコントラクトはECDSA公開鍵を取得し、その公開鍵からビットコインネットワーク上のアドレスを導き出し、そのアドレスにビットコインを送信することができます。このチェーンキートークンスマートコントラクトンがユーザーからビットコインを受け取り、その取引がビットコインネットワーク上で十分な数の確認を受けると、canister 、チェーンキートークンのトークン台帳に、受け取ったビットコイン量に相当する量のチェーンキートークンビットコイントークンを作成、つまり発行するよう指示します。このアプローチにより、チェーンキートークンは基礎となるトークンと1:1の裏付けを持つことになり、このトークンは採掘者canister スマートコントラクトによってオンチェーンで保管されます。

チェーンキートークンスマートコントラクトンが、自身が管理する基礎となるチェーンのアドレスの残高を知ることができるようにするには、ICPとビットコインネットワーク（チェーンキートークンを発行するネイティブアセットをホストするブロックチェーン）の統合が必要です：ICPノードはビットコインネットワークのノードに接続し、ビットコインブロックを取り込んで検証し、UTXOを抽出してチェーン上のビットコインUTXOセットを維持します。スマートコントラクトは、ビットコインアドレスの残高とUTXOを照会することができます。これは、上記のアーキテクチャのセクションで(1)として説明されているものです。

### チェーンキートークンと基礎トークンとの交換

チェーンキートークンはICP上で必要なだけ流通させることができます。頻繁に基礎トークンを出し入れする必要はなく、通常、ほとんどのユーザーが自分で行う必要はありません。しかし、ユーザーはある時点で保有するチェーンキートークンを換金して原資産を受け取りたいと思うかもしれません。この場合、ユーザーは交換するチェーンキートークンの量、例えばこの例ではckBTCを、トークンのチェーンキートークンcanister （マイナー）に交換要求とともに送ります。canister はまず、受け取ったチェーンキートークンをチェーンキートークンの台帳上の供給から削除します。次に、基となるトークン（この例ではビットコインネットワーク）のブロックチェーン上にトランザクションを作成し、受け取ったチェーンキートークンと同量の基となるトークン（手数料を差し引いたもの）をビットコインネットワーク上のユーザーが提供したアドレスに送金します。このステップでは、チェーンキー署名（具体的には、閾値ECDSA署名の高度な形式）が、基礎となるブロックチェーン上のトランザクションを承認します（上記の基礎（2））。連鎖鍵署名された取引は、ICPノードからビットコインノードへのチェーン間通信を介して送信されます。

### チェーンキー型ビットコインの仕様

ビットコインは口座残高のトラッキングにUTXOモデルを使用しているため、ラッピングコントラクトはUTXOの適切な処理を実装する必要があります。これには例えば、ビットコインをユーザーに送金する際に消費するUTXOをヒューリスティックに選択することや、手数料が低すぎるためにトランザクションがビットコインネットワークで採掘されないなどのエラーケースを処理することが含まれます。これを適切に実装し、すべてのエッジケースを考慮することは困難であり、チェーンキートークンcanister の実装において十分に考え抜かれたアルゴリズムが必要です。

### 未来：チェーンキーERC-20トークン

将来、Internet Computer ブロックチェーンが他のブロックチェーンと統合されると、より多くのチェーンキートークンが ICP で利用可能になります。新しいチェーンキートークンのトークン台帳は、それぞれの新しいチェーンキートークン用にパラメータ化された、同じICRC-1台帳コードベースを使用できます。チェーンキートークンcanister スマートコントラクト（マイナー）は、異なるブロックチェーン用に書き直す必要があります。次に計画されている主要なブロックチェーンの統合はICP \<\> ETHで、イーサリアムのERC-20トークンをチェーンキートークンとしてICにもたらします。これには、ERC-20トークン用のマイナーcanister 、各トークンに対して複製される新しいバリエーションが必要です。

<!---


# Chain-Key Tokens

One important way how different blockchains can cooperate is by bringing tokens from one blockchain to another one, for example, bringing Bitcoin to the Ethereum chain. In traditional blockchain architectures, this is mostly accomplished through wrapping. More decentralized (and secure) alternatives to wrapping exist and can replace it: Meet _chain-key cryptography_ and _chain-key tokens_!

A _wrapped token_ represents an underlying asset, which is typically native on a different blockchain than the wrapped token. For example, a wrapped bitcoin token represents real bitcoin, but is a different token than bitcoin and available on a different chain, for example, on the Ethereum blockchain. Traditional wrapping always involves intermediaries that need to be trusted.

A more secure alternative to wrapping tokens is to use advanced threshold cryptography to obtain _chain-key tokens_. Chain-Key Bitcoin, the first major chain-key token on the Internet Computer, is a token on ICP with a 1:1 backing with real bitcoin held 100% on chain on ICP by a smart contract. Thus, it is a Bitcoin ‘twin’ on the Internet Computer that features low transaction fees and latency and high throughput, similar in properties to a Bitcoin Layer 2. We use Chain-Key Bitcoin as an example for chain-key tokens throughout this page. Chain-key fungible tokens should implement the ICRC-1 token standard so that they can be easily integrated by services, for example, wallets and DEXs. This helps make chain-key tokens readily available for a wide range of services.

## Traditional Wrapped Tokens

In traditional blockchain architectures, token wrapping involves an off-chain trusted intermediary and a token ledger smart contract. A user who wants to have tokens of a specific type, say a wrapped bitcoin token, sends tokens of the underlying asset, such as bitcoin, to the intermediary. The intermediary, once it has confirmed the transfer of the underlying token on the token's native blockchain, keeps the received tokens in custody and instructs the token ledger to create, or _mint_, the same amount of wrapped tokens that it has received of the underlying token. Minting increases the supply of the wrapped token. The newly minted wrapped tokens can then be used on the token's blockchain, and the token ledger keeps track of all accounts.

If a user wants to redeem wrapped tokens for the underlying asset, this again involves the intermediary: The user sends the wrapped token to an address controlled by the intermediary and makes an unwrap request. The intermediary removes the amount of received wrapped tokens from the wrapped token's supply and returns the corresponding amount of underlying tokens to the user on the blockchain that natively hosts the underlying asset.

Regular users can just use the wrapped tokens and normally need not bother with the wrapping and unwrapping themselves, unless they own the underlying token and want to bring it to another chain or obtain the underlying token via its wrapped version. Thus, wrapped tokens are convenient for most users and as easy to use as any native token on the same blockchain.

This traditional off-chain approach of wrapping works well from a functional perspective, but has the major drawback of involving an intermediary whose integrity is crucial for the security of the wrapping and unwrapping of the token. The main problem is that the intermediary can get compromised, for example, hacked, defrauded by an insider, or go out of business, which may result in a total loss of the underlying tokens in the worst case. Strategies such as multi-signature schemes with keys held by multiple parties to try to decentralize the intermediary can provide some mitigation, but do not change the fact that wrapping is done by an off-chain entity or group of entities. In short, this architecture is not fully decentralized. Overall, this traditional way of realizing wrapped tokens is not desirable for reasons of security, risk, and its inherent centralized nature.

Another potential risk comes into play with those wrapping architectures: Ideally, a wrapped token is always backed 1:1 by the underlying token. In practice, however, the intermediary can use the tokens held in custody to create profit, for example, by using them for risky investments. In the worst case, if things go wrong, this can lead to the loss of tokens and a depegging of the wrapped token.

## Chain-Key Tokens

Chain-key tokens, such as Chain-Key Bitcoin, are an advanced cryptography-based replacement for wrapped tokens offering stronger security: With chain-key tokens, all operations are performed completely on chain by smart contracts, without involving any off-chain intermediaries. This eliminates the intermediary-related risks of traditional wrapping architectures. Replacing wrapping with on-chain operations only becomes possible through the native integrations between blockchain networks and particularly the use of advanced cryptography — _chain-key signatures_. Chain-Key Bitcoin (ckBTC) is the first chain-key token available on ICP. For each ckBTC token, a real bitcoin is held on chain by a canister smart contract.

We next give an overview on how chain-key tokens function based on a native chain integration and advanced cryptography.

### Architecture

The architecture of any chain-key token on ICP is using the following building blocks as its foundation:

1. Some form of native integration with another blockchain that hosts the underlying token, for example, the Bitcoin network and its only token, Bitcoin, must be available. This integration must allow canisters on ICP to query balances of addresses of the underlying token on its native chain as well as send transactions to the underlying chain. This integration with the native chain must be done such that no intermediaries are required, that is, is completely decentralized and realized on chain.
2. A chain-key implementation of the signature scheme used for signing transactions on the blockchain hosting the underlying asset must be available, for example, chain-key ECDSA for Bitcoin. This functionality makes it possible to sign transactions for the chain of the underlying token fully on chain using threshold cryptography without involving an intermediary.

Those two building blocks comprise the native integration with a particular blockchain. ICP has already been integrated with the Bitcoin network using this approach, and an integration with Ethereum is planned. Based on this foundation, chain-key token implementations can be built at the smart-contract layer. One possible architecture to implement a chain-key token for a specific underlying token, for example, Bitcoin, uses two canister smart contracts: A _chain-key token ledger_ and a _chain-key token_ smart contract. For the _chain-key token ledger_, the ICRC-1 token standard should be used for wide compatibility in the ecosystem. The same open source ledger code base can be used and parameterized for any chain-key token to be deployed. In addition to the chain-key token ledger, a _chain-key token smart contract_ is used in this architecture. This canister essentially replaces the off-chain intermediary of the traditional wrapping architecture with on-chain functionality for the chain-key token architecture. This canister is also called _minter_ as it is responsible for creating (and removing) supply of the chain-key token based on in- and out-flowing underlying tokens. It keeps any underlying tokens it receives in on-chain custody as long as corresponding chain-key tokens are in circulation, thus ensuring a 1:1 backing of the chain-key token with the underlying asset. The 1:1 backing of the chain-key token by its underlying token can be verified by anyone by checking that the Bitcoin UTXOs the minter claims to hold indeed are held by Bitcoin addresses controlled by the minter, thereby further enhancing trust.

<figure>
<img src="/img/how-it-works/chain_key_token_arch.png" alt="Architecture for chain-key tokens" title="Architecture for chain-key tokens" align="center" style="width:1000px">
<figcaption>High-level architecture for chain-key tokens on ICP</figcaption>
</figure>

Note that the token ledger and minter functionalities can also be integrated into a single smart contract or more than two — this is a question of software architecture. However, splitting the functionality into two canisters as outlined is a sensible approach for the reason of modularity as the same ledger canister code can be reused for any chain-key token and only the minter needs to be adapted to the mechanics of the token of the underlying chain, particularly to account for differences between UTXO- and account-based underlying chains, like Bitcoin and Ethereum, respectively.

### Creating Chain-key Tokens from Underlying Tokens

When a user wants to obtain chain-key tokens for some underlying tokens, for example, bitcoin, they send the underlying tokens to an address on the origin blockchain owned by the chain-key token smart contract (minter), instead of an intermediary as done for wrapping. The chain-key token smart contract has an address on the origin blockchain, which is made possible through chain-key cryptography, hence the name of chain-key tokens, or chain-key Bitcoin as a concrete example: In the Bitcoin example, using chain-key ECDSA signing technology — concretely, an advanced form of threshold ECDSA, the chain-key Bitcoin smart contract can have and use ECDSA key pairs, much like any user of the Bitcoin network, but fully on chain. That means the smart contract can obtain ECDSA public keys and from the public keys it derives addresses on the Bitcoin network, to which bitcoin can be sent on the Bitcoin network by anyone. Once this chain-key token smart contract has received Bitcoin from a user and the transaction has received a sufficient number of confirmations on the Bitcoin network, the canister instructs the token ledger for the chain-key token to create, or mint, an amount of chain-key Bitcoin tokens corresponding to the received amount of bitcoin. This approach leads to the chain-key token being 1:1 backed with the underlying token, which is held in on-chain custody by the minter canister smart contract.

Allowing the chain-key token smart contract to know about the balances of the addresses in the underlying chain it controls requires an integration between ICP and the Bitcoin network — the blockchain hosting the native asset to be issued a chain-key token for: ICP nodes connect to nodes of the Bitcoin network and pull in and validate Bitcoin blocks, extract the UTXOs and maintain the Bitcoin UTXO set on chain. Any smart contract can then query the balance and UTXOs of a Bitcoin address. This is what is described as (1) in the section on architecture above.

### Redeeming Chain-key Tokens for Underlying Tokens

A chain-key token can circulate on the ICP as long as needed. There is no need to frequently bring in and transfer out underlying tokens, and normally there is no need for most users to do this themselves. However, a user may want to redeem chain-key tokens they hold at some point to receive the underlying asset. In this case, they send the amount of chain-key tokens to redeem, for example, ckBTC in our example, to the chain-key token canister (minter) for the token with a request to redeem them. The canister first removes the received chain-key tokens from the supply on the chain-key token's ledger. Next, it creates a transaction on the blockchain of the underlying token, the Bitcoin network in this example, to transfer the same amount of underlying tokens (modulo fees) that it has received of chain-key tokens to a user-provided address on the Bitcoin network. This step involves chain-key signatures — concretely an advanced form of threshold ECDSA signing — to authorize the transaction on the underlying blockchain (foundation (2) above). The chain-key-signed transaction is then sent out via inter-chain communication from ICP nodes to Bitcoin nodes, another crucial functionality of the native blockchain integration explained in (1) above.

### Specifics of Chain-Key Bitcoin

Since Bitcoin uses the UTXO model for tracking account balances, the wrapping contract needs to implement proper handling of UTXOs, which is far from trivial. This involves, for example, a good heuristic selection of UTXOs to consume when transferring bitcoin back to users, as well as handling error cases, for example, when transactions do not get mined on the Bitcoin network due to too low fees. Implementing this properly and considering all edge cases is hard and requires well-thought-out algorithms in the implementation of the chain-key token canister.

### The Future: Chain-Key ERC-20 Tokens

When the Internet Computer blockchain will integrate with additional blockchains in the future, more chain-key tokens will become available on ICP. The token ledger of a new chain-key token can use the same ICRC-1 ledger code base, parameterized for the respective new chain-key token. The chain-key token canister smart contracts, or minter, needs to be re-written for different blockchains. The next major blockchain integration being planned is ICP <> ETH, bringing Ethereum's ERC-20 tokens to the IC as chain-key tokens. This requires a new variant of the minter canister for ERC-20 tokens that is then replicated for each token.

-->
