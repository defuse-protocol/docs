# Withdrawals

After having successful [deposits](deposits.md), tokens will be assigned to a user's account in the Verifier contract. The user has complete ownership and control of these tokens. Besides executing trades through intents, the user has the liberty to withdraw their tokens whenever they want.

Given the way tokens are stored in the Verifier smart contract, as explain in the [balances section](balances-and-identifying-your-token.md), there are two ways to withdraw a fungible token from the contract. Same applies to non-fungible tokens, but the difference shall become clear.

1. Using the Verifier smart contract [function call](https://near.github.io/intents/defuse/tokens/nep141/trait.FungibleTokenWithdrawer.html#tymethod.ft_withdraw) `ft_withdraw`, where the token id is used without a prefix (see the [balances section](balances-and-identifying-your-token.md))
2. Using a signed intent, where the token id is also prefixed with `nep141`.

### Examples

1. Assuming Alice has enough balance to cover her wrapped Near withdrawal, she can create a transaction that signs the following withdrawal request using near-cli

```
near contract call-function as-transaction intents.near ft_withdraw json-args '{ "token": "wrap.near", "receiver_id": "alice.near", "amount": "1000" }' prepaid-gas '100.0 Tgas' attached-deposit '1 yoctoNEAR' sign-as alice.near network-config mainnet sign-with-keychain send
```

2. Using a signed withdraw intent. The following is an example of an intent to withdraw an NEP-141 token:

```
{
  "standard": "nep413",
  "payload": {
    "message": "{\"signer_id\":\"user1.test.near\",\"deadline\":\"2025-05-20T13:29:34.360380Z\",\"intents\":[{\"intent\":\"ft_withdraw\",\"token\":\"wrap.near\",\"receiver_id\":\"alice.near\",\"amount\":\"1000\"}]}",
    "nonce": "D9JkDUi93utMQZ1hPAOV7gfnzx3fjehp6oNoCQURPe4=",
    "recipient": "defuse.test.near"
  },
  "public_key": "ed25519:Gxa24TGbJu4mqdhW3GbvLXmf4bSEyxVicrtpChDWbgga",
  "signature": "ed25519:52yFEvWpttuFiPwjJR2PdMzBrT34UCH4DX4rtuVeFBdVXt3C4ndA8Xxe7BczHuyC1ACqB24de97oW5U8YUty7omF"
}
```

### Withdrawal of other types of tokens

Just like described above for fungible tokens, NEP-141, the same applies to other types of tokens. All kinds of tokens can be withdrawn either using an intent, or using a native Near blockchain function call. The following is a table with this information summarized

| Token type/standard          | Withdraw function                                                                                                                | Withdraw Intent                                                                                        |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| NEP-141 - Fungible Token     | [ft\_withdraw](https://near.github.io/intents/defuse/tokens/nep141/trait.FungibleTokenWithdrawer.html#tymethod.ft_withdraw)      | [FtWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.FtWithdraw.html)         |
| NEP-171 - Non-fungible Token | [nft\_withdraw](https://near.github.io/intents/defuse/tokens/nep171/trait.NonFungibleTokenWithdrawer.html#tymethod.nft_withdraw) | [NftWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.NftWithdraw.html)       |
| NEP-245 - Multi Token        | [mt\_withdraw](https://near.github.io/intents/defuse/tokens/nep245/trait.MultiTokenWithdrawer.html#tymethod.mt_withdraw)         | [MtWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.MtWithdraw.html)         |
| Native Near Token            | ❌                                                                                                                                | [NativeWithdraw](https://near.github.io/intents/defuse_core/intents/tokens/struct.NativeWithdraw.html) |
