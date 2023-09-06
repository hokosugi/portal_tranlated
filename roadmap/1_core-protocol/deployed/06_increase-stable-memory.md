---

title: Increase Stable Memory Limit to 32GiB
links:
  Forum Link: https://forum.dfinity.org/t/increased-canister-smart-contract-memory/6148/128
  Proposal:
eta: October 2022
is_community: false
---
現在、canister は 8GiB の安定したメモリにアクセスできます。この機能により、canisters の上限がすべて 32GiB になり、canisters がより多くのステートを保持できるようになります。APIは変更されません。しかし、1つのメッセージは8GiB以上には書き込めません。

<!---


Currently, a canister can access 8GiB of stable memory. This feature will increase that limit to 32GiB for all canisters, allowing canisters to hold more state. The API will remain unchanged. A single message however will not be able to write to more than 8GiB.

-->
