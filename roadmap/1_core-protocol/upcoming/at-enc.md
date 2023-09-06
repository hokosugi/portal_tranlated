---

title: Threshold Key Derivation
links:
  Forum Link: https://forum.dfinity.org/t/threshold-key-derivation-privacy-on-the-ic/16560
  Proposal: 
eta: 2023
is_community: false
---
canisters が閾値鍵導出インターフェースを呼び出せるようにすることで、dapps がIC上で暗号化、閾値復号化、署名を実行できるようにします。この機能により、canisters または個々のユーザがサブネットの公開鍵でメッセージを暗号化し、レプリカ間で秘密共有された対応する復号鍵の閾値鍵導出インターフェースを呼び出すことで復号できるようになります。この機能を統合することで、canisters 、ユーザー側の秘密をブラウザのストレージに依存することなく、エンドツーエンドで暗号化されたユーザーデータ（ストレージ、メッセージング、ソーシャルネットワークなど）を保存できるようになります。また、canisters （クローズドビッドオークション、フロントランニング防止など）内のトランザクションプライバシーも可能になります。

<!---


Empower dapps to perform encryption, and threshold decryption, and signing on the IC by allowing canisters to call a threshold key derivation interface. This feature will enable canisters or individual users to encrypt messages under the public key of the subnet, so that they can be decrypted by calling  the threshold key derivation interface for the corresponding decryption key that is secret-shared among the replicas. Integrating this feature will enable canisters to store end-to-end encrypted user data (e.g., storage, messaging, social networks) without having to rely on browser storage for user-side secrets, as well as enabling transaction privacy within canisters (e.g., closed-bid auctions, front-running prevention).

-->
