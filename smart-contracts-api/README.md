---
description: Spot trading architecture
---

# Smart Contracts API

This document explains the architecture of Defuse smart contracts that will be used for spot trading of fungible tokens on multiple chains.

## Requirements

1. Speed. The solution prioritises speed.
2. Security & Decentralisation. The solution is as decentralised and secure as possible. Speed matters more.
3. Permissionless. The solution is permissionless (anyone can list any network or token and create a liquid market for it on their own).

Scalability. All the systems should be scalable, including on-chain contracts. NEAR architecture dictates that there should be a multitude of contracts that perform the same operation. From here we would call these contracts shards and the whole approach – sharded contracts.
