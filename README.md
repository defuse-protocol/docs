---
description: NEAR Intents is a protocol for multichain financial products.
---

# Overview

{% hint style="info" %}
The NEAR intents protocol and the documentation are under active development.

The protocol has been renamed from Defuse to "NEAR Intents".&#x20;
{% endhint %}

```mermaid
sequenceDiagram
    participant User
    participant Solver Bus
    participant NEAR

    User->>Solver Bus: Request a quote
    note right of Solver Bus: Solvers sign intents <br> to fill the quote
    Solver Bus-->>Solver Bus: 
    Solver Bus-->>User: Return solvers' quotes
    User->>Solver Bus: User signs an intent <br> to execute the best quote
    Solver Bus->>NEAR: Call the verifier contract <br> to execute intents


    note over NEAR: Smart contract <br> verifies signatures <br> and settles <br> matched intents
    NEAR ->>User: 
    note right of User: Intent Fulfilled! ✅

```

## Terminology

1. Entities:
   1. Distribution channels. Applications that have the users, who are interested in decentralised spot trading.
   2. Solvers. Active market participants that fill in the intents issued by users
2. Intent Settlement:
   1. [Solver Bus.](solver-relayer-api/introduction.md) an off chain message bus used for sending quotes and signed intents between solvers and users. Each distribution channel can run their own Solver Bus with their own set of solvers.
   2. [Verifier](verifier/). Smart contract that verifies intents expressed as state changes (“diffs”) signed by corresponding owners. The combination of state changes is committed as long as the invariant (total delta is zero) was kept for each token after these changes were applied. Deployed on NEAR mainnet.

