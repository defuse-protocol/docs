---
description: How to integrate NEAR intents as a market maker
---

# Market Makers

Market Makers are active market participants that deposit liquidity in order to fill quotes issued by users.

To start market making, you are required to perform _bridging_ and _quoting_.

### Passive Deposit/Withdrawal Service

In order to deposit and withdraw liquidity, market makers need to send funds themselves or do it via one of the distribution channels (e.g.[ https://apps.near-intents.org](https://apps.near-intents.org/)) .

To send the liqudity, please get familiar with [Passive Deposit/Withdrawal Service ](passive-deposit-withdrawal-service.md)for deposits and with specific bridge (PoA, Omni or HOT) API for withdrawals. More information on that and an SDK will be come soon.

### Quoting

Once the liquidity is available for trading, use the [Message Bus API](bus/solver-relay.md) to receive and sign quotes.
