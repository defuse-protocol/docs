# Intent types and execution

### Introduction

An intent is a desired action done on your account in the `Verifier` smart contract, performed by submitting a transaction to the NEAR blockchain. There are multiple possible actions that can be done, and the `Verifier` allows submitting a list of these actions to be done using [the function](https://near.github.io/intents/defuse/intents/trait.Intents.html#tymethod.execute_intents) `execute_intents`.

### Note on ordering of intent execution and atomicity

Multiple intents can be submitted to `execute_intents` in a list, as will be seen later in the examples, where all of them will be executed in the order they are provided. Because NEAR is an asynchronous and sharded blockchain, intents submitted in a sequence does not mean that they will complete in that sequence. This is because while individual intents will be executed in order, there is no guarantee that [calls/promises (asynchronous calls)](https://docs.near.org/smart-contracts/anatomy/crosscontract) originating from the `Verifier` will end up finishing in order.

For example: The user submits a list of intents that will do two things:

1. Intent 1: Perform a [storage deposit](https://nomicon.io/Standards/StorageManagement) on usdc.near to create an account on it
2. Intent 2: Withdraw native NEAR from the user's account in the `Verifier` to `usdc.near` account

In this example, there is no guarantee that the whole process will be successful, given that we quantify the success of these intent by having them all pass. The `usdc.near` contract requires a storage deposit before tokens can be deposited. While the intent for storage deposit will be executed first, there is no guarantee that the calls emitted from it into the `usdc.near` smart contract will finish by the time the withdrawal function call is made.

### Structure of intents call and their encoding

Intents are submitted as JSON objects written as strings in a payload object. The following is an example of a [Transfer](https://near.github.io/intents/defuse_core/intents/tokens/struct.Transfer.html) intent `USDC` tokens from Alice to Bob.

```json
{
  "intent": "transfer",
  "receiver_id": "bob.near",
  "tokens": {
    "nep141:wrap.near": "10"
  }
}
```

The intent does not mention Alice, as _the signer_ of the intent defines the account that performs transfer.

This intent is part of the payload, which should have the following members:
- signer_id - signer acount id
- verifying_contract - contract address to which intents are sent
- deadline - timestamp in ISO 8601 format
- nonce - 256 bit value. It is very important to include it with the correct structure.which has clear [requirements](https://github.com/near/intents/tree/main/defuse/README.md) for structure
- intents - array of intents

The "intents" in there contains the same intent from above, but in an array, because users can submit multiple intents to be executed as a batch in order. Keep in mind the [caveats explained here](intent-types-and-execution.md#note-on-ordering-of-intent-execution-and-atomicity) about the order of execution.

```json
{
  "signer_id": "alice.near",
  "verifying_contract": "intents.near",
  "deadline": "2025-05-21T12:23:04.252814Z",
  "nonce": "Vij2xgAlKBKzAMcLzEqKQRhRHXp3ThAEFTYtBmfhzvE=",
  "intents": [
    {
      "intent": "transfer",
      "receiver_id": "bob.near",
      "tokens": {
        "nep141:wrap.near": "10"
      }
    }
  ]
}
```

Finally, to create a valid, signed intent to submit to the `Verifier` contract with the `execute_intents` function, we put the payload above in a message string. Note that the message is the same JSON as above, but serialized as a one-liner with escaped quotes. This is very important.

```json
{
 "standard": "nep413",
  "payload": {
    "recipient": "intents.near",
    "nonce": "Vij2xgAlKBKzgNPJFViEQRiSQad02gXP6pv9IQRCFeg=",
    "message": "{\"deadline\":\"2025-05-21T10:34:04.254392Z\",\"intents\":
    [{\"intent\":\"transfer\",\"receiver_id\":\"bob.near\",\"tokens\":{\"nep141:usdc.near\":\"10\"}}],
    \"signer_id\":\"alice.near\"}"
  },
  "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
  "signature": "ed25519:2o7Cg7N8bEAKtEDbC9Nja8Ks1bJ9Nunh5Ems51G8oV6n96ckUVFeT81vr3TouE47R24HJSrLyxdeBEbvWeuizVBZ"
}
```

The field "recipient" prevents replay attacks on other copies of the `Verifier` on the NEAR blockchain. The exact cryptographic mechanisms for signing will be discussed in the [following sections](signing-intents.md).

### Types of intents

There are multiple [possible intents](https://near.github.io/intents/defuse_core/intents/enum.Intent.html) that can be submitted for execution in the `Verifier` contract. Note that every intent has its own parametrization. The parameters needed to execute the intent can be seen in the linked docs. Rust [PascalCase](https://en.wikipedia.org/wiki/Camel_case) object names are usually converted to [snake\_case](https://en.wikipedia.org/wiki/Snake_case) in JSON. Hence, an intent like `TokenDiff` becomes `token_diff`.

Let's discuss the available intents:

* [add\_public\_key](https://near.github.io/intents/defuse/accounts/trait.AccountManager.html#tymethod.add_public_key): Given an account id in the `Verifier` contract, the user can add public keys to this account. The added public key's private key can sign intents on behalf of this account, even to add new ones. Warning: Implicit account ids, by default, have their corresponding public keys added. This means if a private key is leaked for an implicit account used in intents, the user must manually rotate the public key in the Verifier.

Note that public keys can be added through transactions too. See [this section](account-abstraction.md) for more information.

Example of an intent to add a public key:

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzwKyKBC58QRjK3paApkNO6LU77m306Vw=",
      "message": "{\"deadline\":\"2025-05-21T08:04:27.483198Z\",\"intents\":
      [{\"intent\":\"add_public_key\",\"public_key\":\"ed25519:5TagutioHgKLh7KZ1VEFBYfgRkPtqnKm9LoMnJMJugxm\"}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:M6gK8WMNBL9vnkuScshNuKF5Yro5gQNRLpTTbruadR54AQvi53xWw8NizyCRbdM3j6y9XZ7MJy4DtQ1JLDz4xGQ"
  }
]
```

* [remove\_public\_key](https://near.github.io/intents/defuse/accounts/trait.AccountManager.html#tymethod.remove_public_key): Removes a public key associated with a given account.&#x20;

Note that public keys can be removed through transactions too. See [this section](account-abstraction.md) for more information.

Example of an intent to remove a public key:

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzAAxeFUp9QRi4FmMX9Jj6kmrjkUTtltk=",
      "message": "{\"deadline\":\"2025-05-21T08:24:47.536976Z\",\"intents\":
      [{\"intent\":\"remove_public_key\",\"public_key\":\"ed25519:5TagutioHgKLh7KZ1VEFBYfgRkPtqnKm9LoMnJMJugxm\"}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:3E11skwith3ub3w22FnUdcqYzDFy698qBnP8FEPjZyZKbFtqUZK17AXGSLiwnHXppGrV8wbbGafX8fqXUvefoE8p"
  }
]
```

* [Transfer](https://near.github.io/intents/defuse_core/intents/tokens/struct.Transfer.html): Transfer a set of tokens from the signer to a specified account id, within the intents contract.

Note that transfers can be done using [through transactions too directly to the blockchain](https://near.github.io/intents/defuse_nep245/trait.MultiTokenCore.html#tymethod.mt_transfer).

Example of an intent to transfer coins within the `Verifier` contract from Alice's account to Bob's account:

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzgNPJFViEQRhm1GXQz/Vt+TS0PazCsJQ=",
      "message": "{\"deadline\":\"2025-05-21T10:34:04.254392Z\",\"intents\":
      [{\"intent\":\"transfer\",\"receiver_id\":\"bob.near\",\"tokens\":{\"nep141:usdc.near\":\"10\"}}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:4nY7kjYV11djwsZ9UmezX1eoVMnzTg2wN6jT67o5vyWnibQ7g34zti8wc9imafbAzH5v4rqmksiextQCas14uxm5"
  }
]
```

Note that token ids are specified in Multi Token format that was discussed in [this section](deposits-and-withdrawals/balances-and-identifying-your-token.md).

* [FtWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.FtWithdraw.html), [NftWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.NftWithdraw.html), [MtWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.MtWithdraw.html), [NativeWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.NativeWithdraw.html) are withdraw intents to move their tokens from the `Verifier` contract to an arbitrary address. More information [here](deposits-and-withdrawals/withdrawals.md).

Example of an intent to withdraw from Alice's account to Bob's account. Notice that on success, the tokens will be in `usdc.near` contract under Bob's account there. The tokens have exited the `Verifier` contract:

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzgNPJFViEQRiRYtwetaXk8zFqV//Lq3c=",
      "message": "{\"deadline\":\"2025-05-21T10:45:30.098925Z\",\"intents\":
      [{\"intent\":\"ft_withdraw\",\"token\":\"usdc.near\",\"receiver_id\":\"bob.near\",\"amount\":\"1000\"}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:XYNGWVRyGFGRVZAAn62k9r8qthzbj4a5Ct9eCjrSUW9hXpPRaBvqpLKXpaeBgsekfLFiTLdXMEitrbsZAmMmdmU"
  }
]
```

* [StorageDeposit](https://near.github.io/intents/defuse_core/intents/tokens/struct.StorageDeposit.html): Make an [NEP-145](https://nomicon.io/Standards/StorageManagement#nep-145) `storage_deposit` call for an `account_id` on `contract_id`. The `amount` will be subtracted from user’s NEP-141 `wNEAR` balance. The `wNEAR` will not be refunded in any case.&#x20;

Example of an intent to perform storage deposit that will pay for storage deposit in the usdc.near smart contract. The Near token required (and specified) will be taken from `alice.near` account, and paid to `bob.near` in the `usdc.near` contract:

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzgNPJFViEQRhA/qs878LtXEL9iIjJC14=",
      "message": "{\"deadline\":\"2025-05-21T11:06:28.803408Z\",\"intents\":
      [{\"intent\":\"storage_deposit\",\"contract_id\":\"usdc.near\",\"account_id\":\"bob.near\",\"amount\":\"1250000000000000000000\"}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:4rAYXBa9UY6Zw32dKcGrXgydParzzdygEwdAaaKRg9TxXSP9541rikLWhVCtJqFHJh4Rcvpp2YrbRQ4LBaay7Xa6"
  }
]
```

[TokenDiff](https://near.github.io/intents/defuse_core/intents/token_diff/struct.TokenDiff.html): The user declares the will to have a set of changes done to set of tokens. For example, a simple trade of 100 of token A for 200 of token B, can be represented by `TokenDiff` of {“A”: -100, “B”: 200} (this format is just for demonstration purposes). In general, the user can submit multiple changes with many tokens, not just token A for token B.

Example of two intents submitted from two users to be used with the `Verifier's` smart contract's `execute_intents` function. In the first intent, Alice declares that she is willing to lose 10 `USDC` to get 10 `USDT` in return. In the second intent, Bob declares their will to lose 10 `USDT` and get 10 `USDC` in return. As mentioned in the [introduction](introduction.md), there are many different ways to put these intents together for submission to the blockchain, such as in the [Message Bus](../bus/) or with third parties or any off-chain communication channel. Once a transaction is submitted calling the function `execute_intents` in the `Verifier's` smart contract, the `Verifier` solves the `TokenDiff` orders and converts them into transfers from Alice to Bob and Bob to Alice.

```json
[
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzgMh0PGuHQRiOD4cMhPLCgZfb8bCmR7s=",
      "message": "{\"deadline\":\"2025-05-21T11:30:25.042157Z\",\"intents\":
      [{\"intent\":\"token_diff\",\"diff\":{\"nep141:usdc.near\":\"-10\",\"nep141:usdt.near\":\"10\"}}],
      \"signer_id\":\"alice.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:2tKuCpxqtx6YUY6LRkQGeqmAizA8CKhkFtYdsHsAGRjwm4tFcPHHhBRGtuQAAbAkUkwJ6n2UptTP4Mpot1cTvf2u"
  },
  {
    "standard": "nep413",
    "payload": {
      "recipient": "intents.near",
      "nonce": "Vij2xgAlKBKzgMh0PGuHQRhE5M+mHoAocV57AzeIPuQ=",
      "message": "{\"deadline\":\"2025-05-21T11:30:25.054132Z\",\"intents\":
      [{\"intent\":\"token_diff\",\"diff\":{\"nep141:usdc.near\":\"10\",\"nep141:usdt.near\":\"-10\"}}],
      \"signer_id\":\"bob.near\"}"
    },
    "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
    "signature": "ed25519:gTrJn3CaESvDJ765SDZ7JdkXupeGApjU6t66XpwrCXKM5K7b4VEeZXxL6NSL7i5zJ2Dn1aracg9xubktcPLwHEq"
  }
]
```

\


\
\


\


