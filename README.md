---
description: >-
  NEAR Intents is a new multichain transaction protocol where users specify desired outcomes without defining how to achieve them; creating a unified marketplace where third parties compete to fulfill any type of request.
---

# Intents Overview

<figure>
   <img src=".gitbook/assets/overview/intents-overview.png" alt="NEAR Intents Overview" width="900px">
   <figcaption>NEAR Intents Overview</figcaption>
</figure>

{% hint style="info" %}

NEAR Intents and it's documentation are under active development and recently renamed from "Defuse" to "NEAR Intents".

While the documentation and examples here focus on multichain token swaps, NEAR Intents extend far beyond these types of requests. Whether you want to swap tokens across chains or buy a pizza with Bitcoin, NEAR Intents serves as a protocol for ANY type of request where diverse solution marketplaces can naturally emerge through competitive discovery.
{% endhint %}

## NEAR Intents Components

There are three core components of NEAR Intents:

| Name | Description |
|-----------|-------------|
| [Distribution Channels](./integration/distribution-channels/README.md) | User interfaces for creating and broadcasting their "Intent". (ex. Apps, Wallets, Exchanges)|
| [Market Makers](./market-makers/README.md) | Users or Agents that provide liquidity and compete to fulfill "Intent Requests". |
| [Verifier Smart Contract](./market-makers/verifier/README.md) | A smart contract deployed on NEAR Protocol that verifies and settles transactions.|
