---
hidden: true
---

# Signing Intents

After creating intents as described in [this section](intent-types-and-execution.md), we have to sign them to create a valid intent to submit to the Verifier's smart contract using the `execute_intents` [function](https://near.github.io/intents/defuse/intents/trait.Intents.html#tymethod.execute_intents). This section discusses how that works.

### Account abstraction

As discussed in the [account abstraction section](account-abstraction.md), accounts in the Verifier contract are identified by their Near account (whether implicit, being derived from a public key, or named, like `alice.near`). Every account can add an arbitrary number of public keys. Every public key can be used to produce signatures that authorize intents.

## Signature types

As discussed in [this section](intent-types-and-execution.md), a signed [Transfer](https://near.github.io/intents/defuse_core/intents/tokens/struct.Transfer.html) intent that is ready to be submitted to the blockchain looks something like this:

```
{
  "standard": "nep413",
  "payload": {
    "message": "{\"signer_id\":\"alice.near\",\"deadline\":\"2025-05-21T10:34:04.254392Z\",\"intents\":[{\"intent\":\"transfer\",\"receiver_id\":\"bob.near\",\"tokens\":{\"nep141:usdc.near\":\"10\"}}]}",
    "nonce": "Op47m39Q/NzWWi8jYe4umk96OTSnY4Ao0FB/B9aPB98=",
    "recipient": "intents.near"
  },
  "public_key": "ed25519:Gxa24TGbJu4mqdhW3GbvLXmf4bSEyxVicrtpChDWbgga",
  "signature": "ed25519:52oc2FD4rMsAPNSBSx6eNYrF4atreXTZxWFhAPfmZFn1eF7jbE3BrRTL3ey1M1sAKSdK8qriZiHQnhnNBCh8vVMJ"
}
```

This object comes from an enum is called [MultiPayload](https://near.github.io/intents/defuse_core/payload/multi/enum.MultiPayload.html). A signed intent can be one of the possibilities provided by this enum.&#x20;

### NEP-413 object

The object shown above shown above is the NEP-413 [compliant standard](https://github.com/near/NEPs/blob/master/neps/nep-0413.md).

#### Encoding information

The object uses the following encoding

Nonce: base64\
public key: prefixed with the key type, then base58\
signature: prefixed with the key type, then base58

### ERC-191 object

The object of this type is compliant with the [ERC-191 standard](https://eips.ethereum.org/EIPS/eip-191).

TODO: more info about this

TODO: ERC-191 raw object

### Passkey object "WebAuthn"

This object type is meant to be used with passkeys. An object of this type looks as follows:

TODO: explain the client\_data\_json part

```
{
  "standard": "webauthn",
  "payload": "{\"signer_id\":\"0x3602b546589a8fcafdce7fad64a46f91db0e4d50\",\"verifying_contract\":\"defuse.test.near\",\"deadline\":\"2025-03-30T00:00:00Z\",\"nonce\":\"A3nsY1GMVjzyXL3mUzOOP3KT+5a0Ruy+QDNWPhchnxM=\",\"intents\":[{\"intent\":\"transfer\",\"receiver_id\":\"user1.test.near\",\"tokens\":{\"nep141:ft1.poa-factory.test.near\":\"1000\"}}]}",
  "public_key": "p256:2V8Np9vGqLiwVZ8qmMmpkxU7CTRqje4WtwFeLimSwuuyF1rddQK5fELiMgxUnYbVjbZHCNnGc6fAe4JeDcVxgj3Q",
  "signature": "p256:3KBMZ72BHUiVfE1ey5dpi3KgbXvSEf9kuxgBEax7qLBQtidZExxxjjQk1hTTGFRrPvUoEStfrjoFNVVW4Abar94W",
  "client_data_json": "{\"type\":\"webauthn.get\",\"challenge\":\"4cveZsIe6p-WaEcL-Lhtzt3SZuXbYsjDdlFhLNrSjjk\",\"origin\":\"https://defuse-widget-git-feat-passkeys-defuse-94bbc1b2.vercel.app\"}",
  "authenticator_data": "933cQogpBzE3RSAYSAkfWoNEcBd3X84PxE8iRrRVxMgdAAAAAA=="
}
```
