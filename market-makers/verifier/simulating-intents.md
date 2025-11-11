# Simulating intents

In this chapter we have discussed several methods to execute intents in the `Verifier` smart contract. The `execute_intents` function calls a mutable and modifies the `Verifier` contract’s state.

However, the `Verifier` smart contract offers the possibility to simulate intents using [the function](https://near.github.io/intents/defuse/intents/trait.Intents.html#tymethod.simulate_intents) `simulate_intents`. Simulation of intents is the process of running the code of the intents provided, without modifying the state of the `Verifier` smart contract.&#x20;

### Examples where simulations can be useful

* Alice constructs an intent, and is not sure whether the format of the digital signature is valid
* Bob is constructing an intent to withdraw his `USDC` coins from the `Verifier` contract, and wants to ensure that it will work before executing it
* Charlie and Drake want to execute a trade using two intents of `TokenDiff`, but they are not sure about the fees that will be paid to the `Verifier` smart contract

### Example outputs for simulating intents

The following is a valid, signed intents to trade 100 `USDC` for 100 `USDT`. It has two intents, one from Charlie, expressing his intent to lose 100 `USDC` to get 100 `USDT` in return, while drake expressing the opposite. For more information about this structure, see [this section](intent-types-and-execution.md).

```
{
  "signed": [
    {
      "standard": "nep413",
      "payload": {
        "recipient": "intents.near",
        "nonce": "Vij2xgAlKBKzwJcGrQQYQhiLk1HU5AVNH1M3PhtxosE=",
        "message": "{\"deadline\":\"2025-05-23T07:40:13.735337Z\",\"intents\":
        [{\"intent\":\"token_diff\",\"diff\":{\"nep141:usdc.near\":\"-100\",\"nep141:usdt.near\":\"100\"}}],
        \"signer_id\":\"charlie.near\"}"
      },
      "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
      "signature": "ed25519:617X6QMiwFohRHqEuFwZ8aGU6Gn8PsH1DM3grArCYSKSvLz4wBPPGLzLPX3SLstLB331ESGPUToaPkUE7DvgefUu"
    },
    {
      "standard": "nep413",
      "payload": {
        "recipient": "intents.near",
        "nonce": "Vij2xgAlKBKzwJcGrQQYQhjtxxGCsZQhM2DP7btPexE=",
        "message": "{\"deadline\":\"2025-05-23T07:40:13.753085Z\",\"intents\":
        [{\"intent\":\"token_diff\",\"diff\":{\"nep141:usdc.near\":\"100\",\"nep141:usdt.near\":\"-100\"}}],
        \"signer_id\":\"drake.near\"}"
      },
      "public_key": "ed25519:C3jXhkGhEx88Gj7XKtUziJKXEBMRaJ67bWFkxJikVxZ2",
      "signature": "ed25519:3mLCEKyhofYLVakC9qgyb2FWh4L3jQxnUNyBHxYMTC13bo9y4AeqRh29dDYC4ZAQk4Z4mA2QZL8y7KGGKp5Pc3S1"
    }
  ]
}
```

Calling `simulate_intents` with the above mentioned intents list produces the following output:

```
{
  "intents_executed": [
    {
      "intent_hash": "5GpL6PsUQVHFYAk5FWEwBUaEQqcZkc2SjTvPYHgHAnx8",
      "account_id": "charlie.near"
    },
    {
      "intent_hash": "4ejradLAAPBhBVAn6tBYExpuj2VCn5f5VEBfVajNXiXk",
      "account_id": "drake.near"
    }
  ],
  "min_deadline": "2025-05-23T07:40:13.735337Z",
  "state": {
    "fee": 100
  }
}
```

The fees are expressed in pips - 100 pips is 0.01%.

Simulation outputs may include additional data in future updates. This is a basic example. Contact NEAR Intents Team if your application requires more detailed output.

### Accuracy of simulations

Simulated results are designed to closely match actual execution outcomes. This is the intended outcome from the programming and heavy testing done for simulated intents. However, due to the asynchronous nature of the NEAR blockchain, ultimate code abstraction to simulate the intents exactly like they would be in real execution is not possible.&#x20;

To date, simulation and execution results have always matched. If you ever discover such a case, please contact the NEAR Intents team and report it as a bug.

Note: Simulations reflect only side effects within the `Verifier` contract and exclude those from external asynchronous calls.
