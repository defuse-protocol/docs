---
description: Deposited assets
---

# Intent flow

<figure><img src="../.gitbook/assets/off-chain-arch-4 (4).png" alt=""><figcaption><p>Intent flow for deposited assets</p></figcaption></figure>

1. Initial state: user has an account with FT1 token deposited; user wants to swap 1 FT1 to FT2
2. FE sends an RFQ to message bus
3. router receives the RFQ request and starts to search for the most optimal path for such swap by sending RFQ requests to “trader” solvers.
4. “Trader” solvers reply with signed messages “I’m ok to swap X of token A to Y of token B” (along with nonce, deadline, etc…)
5. As soon as the path FT1 -> FT2 has been found, router sends a response to the user with the final price: “Are you ok to swap 1 FT1 -> 2 FT2”?
6. user signs “I’m ok to swap 1 FT1 to 2 FT2” and replies back to router
7. router aggregates all these signed messages into a transaction and sends it to Verifier Contract
8. Verifier Contract verifies all signed state changes and commits them on-chain.

