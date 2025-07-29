---
description: >-
  An off chain message bus used for communication between market makers and users and sending the settlement transactions to verifier contract.
---

# Message Bus

Message Bus is an additional system component that optimizes the price discovery process. Any frontend app may use Message Bus or launch its own instance.

<p align="center">
  <img src="https://github.com/user-attachments/assets/fc27dde0-7a3d-428e-9776-8743aa6b43a5" alt="Message Bus" />
</p>

Near Intents protocol may operate without a Message Bus component:

* frontends may use any other quoting mechanisms to compose and publish signed intents
* market makers may index NEAR blockchain to find intents to fill.
