---

title: ckERC-20 Tokens
links:
  Forum Link: https://forum.dfinity.org/t/long-term-r-d-integration-with-the-ethereum-network/9382
eta: 2024
is_community: true
---
この機能は、イーサリアムのERC-20トークンのサブセットをチェーンキートークン、すなわちイーサリアムネットワーク上のオリジナルトークンのツイントークンの形でICにもたらします。特に、USDC や USDT などのステーブルコインの ckERC-20 ツインは、ICP エコシステムに潜在的な流動性をもたらすことができるため、IC にとって非常に重要です。初期実装では、イーサリアムネットワークとの通信に複数のイーサリアムJSON RPCプロバイダーへのHTTPSアウトコールを使用しています。将来的に利用可能になれば、Internet Computer の完全なイーサリアム統合が、トラストレスな方法でイーサリアムネットワークと通信するために活用されます。チェーンキーECDSA署名（threshold ECDSA）は、イーサリアムネットワーク上で必要なトランザクションをトラストレスに作成するために使用されます。

<!---


This feature brings a subset of Ethereum's ERC-20 tokens over to the IC in the form of chain-key tokens, i.e., twin tokens of the original tokens on the Ethereum network. Particularly the ckERC-20 twins of stablecoins, such as USDC and USDT, are of tremendous importance to the IC due to the potential liquidity they can bring over to the ICP ecosystem. The initial implementation is done using HTTPS outcalls to multiple Ethereum JSON RPC providers for communication with the Ethereum network. Once available in the future, the full Ethereum integration of the Internet Computer will be leveraged for communicating with the Ethereum network in a trustless way. Chain-key ECDSA signing (threshold ECDSA) is used for trustlessly creating the required transactions on the Ethereum network.

-->
