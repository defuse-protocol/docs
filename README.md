---
description: NEAR Intents is a protocol for multichain financial products.
---

# Overview

{% hint style="info" %}
The NEAR intents protocol and the documentation are under active development.

The protocol has been renamed from Defuse to "NEAR Intents".&#x20;

Any mentions of Defuse in the source code and documentation are to be replaced
{% endhint %}

## Terminology

1. Intent Settlement:
   1. [Solver Bus.](solver-relayer-api/introduction.md) an off chain message bus used for communication and sending “permits” between solvers and users. In general, specific only to a single distribution channel with solvers that may be authorised / trusted by this distribution channel. In the beginning of the project, a single shared solver bus may exist.
   2. [Verifier](verifier.md). Smart contract that verifies intents expressed as state changes (“diffs”) signed by corresponding owners (a.k.a “permits”). The combination of state changes is committed as long as the invariant (total delta is zero) was kept for each token after these changes were applied. Deployed on NEAR mainnet.
2. Entities:
   1. Distribution channels. Applications that have the users, who are interested in decentralised spot trading.
   2. Solvers. Active market participants that fill in the intents issued by users.

