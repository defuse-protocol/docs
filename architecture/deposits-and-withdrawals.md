---
description: Bridge
---

# Deposits & Withdrawals

Deposits and withdrawals from other chains into NEAR FTs are handled by the omni-bridge. NEAR intents uses omni-bridge as a bridging service. For the assets that are not supported by omni-bridge yet, there is an option to use PoA bridge.

Users need to deposit funds in NEAR intents account to trade. This is different from the ordinary AMMs. Reasoning for this step is the following:

1. Users anyway need to bridge all crypto outside from NEAR into NEAR, which is kind of a deposit; so the only part of the UX that would change is the necessity of deposits from NEAR.
2. Depositing tokens into a single NEAR intents contract allows for single transaction atomic settlement with no asyncronisity implications.
3. The single point of failure that is created at this point is not much worse than NEP-141 USDT (or any other stablecoin NEP-141), since it would concentrate the majority of trades.

The proper time to migrate to fully sharded architecture would be at the point when the sharded FT standard is developed and in place. Probably this standard should take into account the needs of the intent settlement.

### Omni-bridge overview

1. Networks support:
   1. Bitcoin
   2. Ethereum
   3. Arbitrum
   4. Base
   5. Solana
   6. TON
2. Support for ft\_transfer\_call() with \`msg\` field (active deposit option)
3. Passive deposit functionality
   1. NEAR-side smart-contract which will own a bunch of depositing addresses
   2. A protocol of correspondence of type of bridging (ft\_transfer or ft\_transfer\_call) with all the necessary parameters and the derivation path for a specific deposit address (say, derivation=hash(type, params)
