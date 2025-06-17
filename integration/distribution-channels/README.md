---
description: NEAR Intents Distribution Channels
---

# Distribution Channels

<figure>
    <img src="../../.gitbook/assets/distribution-channels/distribution-channel.png" alt="Distribution Channel" width="400px">
    <figcaption>Distribution Channels</figcaption>
</figure>

Distribution Channels are applications, wallets, or exchanges that facilitate secure and transparent peer-to-peer transactions using NEAR Intents Protocol. Their main role is to **provide access and delivery of assets to end users**.

The easiest way to integrate with NEAR Intents as a distribution channel is with [1Click Swap API](1click-api.md). 1Click API simplifies the complexities of NEAR Intents by providing simple, easy-to-use, REST endpoints that developers can use to request and execute a User's Intent.

## How 1Click API Works

<figure>
    <img src="../../.gitbook/assets/distribution-channels/1Click-API-flow.png" alt="1Click API Flow" width="900px">
    <figcaption>1Click API Flow</figcaption>
</figure>

The application <> 1Click API flow is simple:

1) Post an "Intent Request" to 1Click via the `/quote` endpoint
2) 1Click interacts with Market Makers and returns the best quote with a unique deposit address
3) If the user is happy with the quote, they make a deposit to the provided address and the "Swap Intent" is automatically performed or tokens are refunded back to the user

For the full API and Examples see [1 Click API docs](./1click-api.md)