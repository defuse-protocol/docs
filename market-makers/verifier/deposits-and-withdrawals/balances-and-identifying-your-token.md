# Balances, and identifying your token

In this section we will discuss how to check your balance and how tokens are organized inside the Verifier smart contract.

### The Multi Token standard as a tool to manage tokens in the Verifier contract

After successful deposits, the Verifier contract manages the deposited tokens using the [Multi Token standard, NEP-245](https://nomicon.io/Standards/Tokens/MultiToken/Core). This allows to manage the tokens deposited in the Verifier contract regardless of their type.

### Token id in the Verifier contract

To distinguish between different token types, we define a token id that is identified as a string. The string is prefixed with the type of the token.

Examples:

* Wrapped near is an NEP-141 fungible token with id `wrap.near`. In the Verifier contract, it's addressed as `nep141:wrap.near`.
* An NFT with issuer/collection contract id `coolnfts.near` and token id `rock.near` is addressed in the Verifier contract with `nep171:coolnfts.near:rock.near`.
* A generic token that uses the NEP-245 standard, originating from contract `mygame.near` and token id `shield.near` is addressed in the Verifier contract with `nep245:mygame.near:shield.near`&#x20;

### Checking your balance

After a successful deposit of any token, say `wrap.near`, you can check your balance [using the function](https://near.github.io/intents/defuse_nep245/trait.MultiTokenCore.html#tymethod.mt_balance_of) `mt_balance_of`, which adheres to the NEP-245 standard. The following are the parameters of this function:

```json
{
    account_id: "alice.near",
    token_id: "nep141:wrap.near"
}
```

Using near-cli, you can make this query using the command:

```
near contract call-function as-read-only intents.near mt_balance_of json-args '{ "account_id": "alice.near", "token_id": "nep141:wrap.near" }' network-config mainnet now
```

