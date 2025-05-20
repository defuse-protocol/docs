# Deposits

### Depositing fungible tokens (NEP-141), including wrapped Near

The Verifier contract implements the [FungibleTokenReceiver](https://docs.near.org/primitives/ft) interface. This is [NEP-141 standard](https://nomicon.io/Standards/Tokens/FungibleToken/Core).&#x20;

Hence, to transfer tokens to the Verifier contract, the user should call the function `ft_transfer_call` (see the function signature [here](https://github.com/near/near-sdk-rs/blob/611e01ebf6c226f4e1e820a2f50f4a9acf8f1215/examples/fungible-token/ft/src/lib.rs#L103)) on the token in question, with an `msg` parameter including information about the user, whose account will be assigned as the owner of the tokens.

#### Format of `msg` parameter to assign ownership of tokens inside the Verifier contract

The `msg` parameter must adhere to a well-defined standard in order to have the funds correctly deposited into the smart contract. There are three formats for `msg`:

1. It can be empty, with size 0 bytes, which will assign ownership to the sender of the transaction (technically in Near blockchain language, the predecessor)
2. A simple string that contains an account id, for example `alice.near`
3. A json object, we refer to as `DepositMessage`, containing the following members:
   1. `receiver_id`, a string with an account id taking ownership
   2. `execute_intents`, a list of intents to be executed, right after the deposit is complete.
   3. `refund_if_fails`, a boolean, when set to `false` (the default), will execute the intents as a detached promise, decoupling any failure during the execution of the intents from the deposit operation.

Examples of `ft_transfer_call` parametrization in json:

1. With empty `msg` or non-existing `msg`, granting the tokens to the sender/creator of that transaction

```
{
    receiver_id: "alice.near",
    amount: "1000",
}
```

```
{
    receiver_id: "alice.near",
    amount: "1000",
    msg: "",
}
```

2. With an `msg`  that explicitly assigns ownership to another account:

```
{
    receiver_id: "alice.near",
    amount: "1000",
    msg: "bob.near",
}
```

3. With an `msg` that is a `DepositMessage` object

```
{
    receiver_id: "alice.near",
    amount: "1000",
    msg: "{"\"receiver_id\": \"charlie.near\", \"execute_intents\": [...], \"refund_if_fails\": false}\"",
}
```

or

```
{
    receiver_id: "alice.near",
    amount: "1000",
    msg: "{"\"receiver_id\": \"charlie.near\", \"execute_intents\": [...]\"",
}
```

Notice that `msg` is a string, even when it's a json object embedded in a string. So proper escaping of characters must be done.

### Depositing non-fungible tokens (NEP-171)

The Verifier contract implements the [NonFungibleTokenReceiver](https://docs.near.org/primitives/nft) interface. This is [NEP-171](https://nomicon.io/Standards/Tokens/NonFungibleToken/Core) standard.

Transfers to the Verifier of NFTs must use the function `nft_transfer_call`, and follow the same `msg` parameter rules specified in the the Fungible Tokens section. The `msg` parameter dictates how ownership of the transferred tokens will be assigned.
