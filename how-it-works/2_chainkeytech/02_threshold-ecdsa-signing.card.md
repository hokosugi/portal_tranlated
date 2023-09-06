---

title: Chain-key signatures
abstract: This feature will enable canister smart contracts to sign with regard to an ECDSA public key while their host subnet has a threshold shared secret key.
coverImage: /img/how-it-works/chain-key-signature.webp
---
![](/img/how-it-works/chain-key-signature.webp)

# チェーンキー署名

連鎖鍵*署名は*連鎖鍵技術を拡張し、他のブロックチェーンを対象とした取引をInternet Computer Protocol を使って完全にオンチェーンで計算できるようにします。
連鎖鍵署名を使うことで、ICはブリッジを必要とせず、完全にトラストレスな方法でBitcoinやEthereumなどの他のブロックチェーンと統合することができます。Canisters 、Bitcoinを安全に保管し、取引することができるようになりました。ビットコインの秘密鍵は、canister を実行するすべてのノード間で共有されます。canister は、ノードの少なくとも2/3が取引に同意した場合にのみ、連鎖鍵署名取引を使用してビットコインを取引できます。実際、連鎖鍵署名の使用は、2つのブロックチェーンの他に追加の信頼前提が必要なく、特に署名鍵やその共有を管理する追加の当事者も必要ないため、ブロックチェーンを統合する最も強力で分散化された方法です。

[より深く](/how-it-works/threshold-ecdsa-signing/)

<!---


![](/img/how-it-works/chain-key-signature.webp)

# Chain-key signatures

*Chain-key signatures* extends chain-key technology to allow transactions targeted at other blockchains to be computed fully on-chain using the Internet Computer Protocol.
Using chain-key signatures, the IC can integrate with other blockchains such as Bitcoin and Ethereum in a completely trustless manner without needing any bridges. Canisters can now securely store and transact Bitcoin. The secret key of the Bitcoin is shared between all the nodes running the canister. The canister can trasact Bitcoin using a chain-key signed transaction only when at least 2/3rd of the nodes agree to make the transaction. Indeed, using chain-key signatures is the strongest, most decentralized way of integrating blockchains as no additional trust assumptions besides that of the two blockchains are required, particularly no additional parties that manage signature keys or their shares. 

[Go deeper](/how-it-works/threshold-ecdsa-signing/)


-->
