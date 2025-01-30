---
description: Defuse is a protocol for multichain financial products.
---

# Overview

This document explains the architecture of Defuse smart contracts that will be used for spot trading of fungible tokens on multiple chains.

## High level architecture

The solution consists of the following pieces:

1. Intent Settlement:
   1. Solver Bus. an off chain message bus used for communication and sending “permits” between solvers and users. In general, specific only to a single distribution channel with solvers that may be authorised / trusted by this distribution channel. In the beginning of the project, a single shared solver bus may exist.
   2. Verifier. Verify intents expressed as state changes (“diffs”) signed by corresponding owners (a.k.a “permits”). The combination of state changes is committed as long as the invariant (total delta is zero) was kept for each token after these changes were applied. Located on NEAR.
2. Entities:
   1. Distribution channels. Applications that have the users, who are interested in decentralised spot trading.
   2. Solvers. Active market participants that fill in the intents issued by users.

