---

title: HTTPS Outcalls from Canisters
links:
  Forum Link: https://forum.dfinity.org/t/long-term-r-d-general-integration-proposal/9383
  Proposal: https://dashboard.internetcomputer.org/proposal/35639
  IC Wiki: https://wiki.internetcomputer.org/wiki/HTTPS_outcalls
  Documentation: https://internetcomputer.org/https-outcalls
eta: 2022
is_community: true
in_beta: false
---
この機能は、Internet Computer 上のcanister スマートコントラクトが、ブロックチェーンの外部にある Web 2.0 サービスに HTTP(S)アウトコールを完全に信頼できない方法で行うことを可能にすることで、Web3 と Web2 の世界を直接統合します。この機能を使えば、ブロックチェーンのオラクルサービスが現在提供している機能のかなりの部分を、より優れたセキュリティ保証と低コストで実現することができます。想定されるユースケースとしては、DeFidApps や分散型保険サービスのために HTTP サーバーから直接マーケットデータを取得したり、従来の通信チャネルを通じてエンドユーザーに通知を送信したり、あるいは閾値 ECDSA 機能を使用することで、canister 空間全体でイーサリアムの統合を実装したりすることが挙げられます。

<!---


This feature directly integrates the Web3 with the Web2 worlds by enabling canister smart contracts on the Internet Computer to make HTTP(S) outcalls to Web 2.0 services outside the blockchain in a completely trustless manner. Using this feature, we can realize a substantial part of the functionalities currently offered by blockchain oracle services, just with better security guarantees and at a lower cost. Possible use cases include directly obtaining market data from HTTP servers for DeFi dApps and decentralized insurance services, sending notifications to end users via traditional communications channels, or implementing, by also using the threshold ECDSA feature, an Ethereum integration entirely in canister space.

-->
