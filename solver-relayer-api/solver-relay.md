# API

Solver Bus is an additional system component that optimizes frontends←→solvers quoting, and intent discovery process. Any frontend app may use a generic foundation-hosted Solver Bus or launch its own instance to customize preferred solver accounts.

Defuse protocol may operate without a Solver Relay component:

* frontends may use any other quoting mechanisms to compose an intent for the end user
* solvers may index Near the blockchain to find intents to fill.

However, using Solver Relay is recommended for speed optimization goals.



***

### **OpenAPI Specification**

{% file src="../.gitbook/assets/solver-relay.openapi.json" %}

***

### Frontend **JSON-RPC Endpoint**&#x20;

**POST:** `https://solver-relay-v2.chaindefuser.com/rpc`

***



<mark style="color:orange;">**1. Quote asset prices from solvers**</mark>

Allow the front end to estimate potential output for a given exact input or the required input for a given exact output based on user intent. The solver relay will forward a quote request to all connected solvers, wait for up to 1200 ms, and return all available quote options to the user. Only one of `exact_amount_in` or `exact_amount_out` should be provided in the request, not both.

<details>

<summary>Parameters</summary>

* `defuse_asset_identifier_in` -  asset to trade from
* `defuse_asset_identifier_out`- asset to trade to
* `exact_amount_in`- amount of _token\_in_ for exchange
* `exact_amount_out`- amount of _token\_out_ for exchange
* `quote_id`_(optional)  -_ An additional identifier for the quote. This is particularly useful when also listening for quotes
* `min_deadline_ms`_(optional)_ - minimum validity time for an offer from solvers. The shorter the time, the better the price solvers can offer.

</details>

<details>

<summary>Response</summary>

* `quotes[]`
  * `quote_hash`- quote identifier
  * `defuse_asset_identifier_in` - asset to trade from
  * `defuse_asset_identifier_out` - asset to trade to
  * `amount_in`:
    * If `exact_amount_in` is defined: Specifies _token\_in_ amount for the exchange.
    * If `exact_amount_in` is **not** defined: Represents a proposed amount of _token\_in_.
  * `amount_out`:
    * If `exact_amount_out` is defined: Specifies _token\_out_ amount for the exchange.
    * If `exact_amount_out` is **not** defined: Represents a proposed amount of _token\_out_.
  * `expiration_time` - expiration date of the offer from the solver

</details>

<details>

<summary>Example</summary>

```javascript
// Request
{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "quote",
    "params":  [
        {
            "defuse_asset_identifier_in": "nep141:ft1.near",
            "defuse_asset_identifier_out": "nep141:ft2.near",
            "exact_amount_in": "1000", 
            "quote_id": "00000000-0000-0000-0000-000000000000", // OPTIONAL. default will be generated randomly
            "min_deadline_ms": "60000" // OPTIONAL. default 60_000ms / 1min
        }
    ]
}


// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "quotes":[
            {
                "quote_hash": "00000000000000000000000000000000",
                "defuse_asset_identifier_in": "nep141:ft1.near",
                "defuse_asset_identifier_out": "nep141:ft2.near",
                "amount_in": "1000", 
                "amount_out": "2000"
                "expiration_time": "1727784627" //UNIX TIMESTAMP
            }, 
            // ...
        ]
    }
}
```

</details>



<mark style="color:orange;">**2. Publish intent**</mark>

If the user is satisfied with the offer provided by the solver, they can sign the intent and send it to the solver relay for execution. Relay supports this types of signatures for publicsh intent: `nep413`, `erc191`, `raw_ed25519`\


<details>

<summary>Parameters(NEP413)</summary>

* `quote_hashes` - quote hashes to execute all merged intents
* `signed_data` - intent content with signature
  * `standard` - content signature standard
  * `payload`- content of the intent
    * `message`
      * `signer_id` - Defuse account id
      * `intents[]` - Intent data
        * `intent` - Intent type
      * `deadline` - deadline until this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `recipient` - Defuse contract address
    * `callbackUrl`_(optional)_ - needed for some wallets
  * `signature` - signed content
  * `public_key` - signer's public key

</details>

<details>

<summary>Parameters(ERC191)</summary>

* `quote_hashes` - quote hashes to execute all merged intents
* `signed_data` - intent content with signature
  * `standard` -  content signature standard
  * payload - **STRINGIFIED** payload
    * `signer_id` - Defuse account id
    * `intents[]` - Intent data
      * `intent` - Intent type
    * `deadline` - deadline until this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `veryfying_contract` - use "intents.near"
  * `signature` - signed content

</details>

<details>

<summary>Parameters(RAW_ED25519)</summary>

* `quote_hashes` - quote hashes to execute all merged intents
* `signed_data` - intent content with signature
  * `standard` -  content signature standard
  * payload - **STRINGIFIED** payload
    * `signer_id` - Defuse account id
    * `intents[]` - Intent data
      * `intent` - Intent type
    * `deadline` - deadline until this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `veryfying_contract`- use "intents.near"
  * `signature` - signed content
  * `public_key` - signer's public key

</details>

<details>

<summary>TokenDiff Intent</summary>

* `intent` - Intent type(equals `token_diff`)
* `diff` -  map where:
  * **Keys**: Represent `defuse_asset_identifiers` of tokens.
  * **Values**: Represent amounts for the tokens as strings.
    * Positive values indicate tokens to be received by the account.
    * Negative values (prefixed with "-") indicate tokens to be transferred from the account.

</details>

<details>

<summary>Response</summary>

* `status` - intent receiving status
* `reason` - error reason (if the status is FAILED)
* `intent_hash` - intent identifier

</details>

<details>

<summary>Example</summary>

```javascript
// Request
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "publish_intent",
  "params": [
    {
      "quote_hashes": ["00000000000000000000000000000000", ...],
      "signed_data": {
        "standard": "nep413",
        "message": {
          "signer_id": "user.near",
          "deadline": "2024-10-14T12:53:40.000Z",
          "intents": [
            {
              "intent": "token_diff",
              "diff": {
                "nep141:ft1.near": "300",
                "nep141:ft2.near": "-500"
              }
            },
            {
              "intent": "mt_batch_transfer",
              "receiver_id": "referral.near",
              "token_id_amounts": {
                "nep141:ft1.near": "1"
              }
            },
            {
              "intent": "ft_withdraw",
              "token": "ft1.near",
              "receiver_id": "omni-bridge.near",
              "amount": "299",
              "msg": "{\"chain_id\": 1, \"withdraw_to\": \"0x123\"}" // omni-bridge args
            }
          ]
        },
        "nonce": "87878609949187467518642340675777309892619814715982488116035839611029208327842",
        "recipient": "defuse.near",
        "signature": "ZuVBFXIGfndYn7pNyz+dhfNgsVoUqnGiNSBL6knztPsAc5REjbr5PrrDKdGlA7akg97U8RNtg0nDparXxI9+Dg==",
        "public_key": "7RuVD5I4g/cYwpGQt5zb2FEPaiGBU3WfZZkgvbcRN5M="
      }
    }
  ]
}

// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result":
    {
        "status": "OK", // OR FAILED
        "reason": "expired", // expired | internal | etc
        "intent_hash": "00000000000000000000000000000000"
    }
}
```

</details>



<mark style="color:orange;">**3. Get intent status**</mark>

Check the status of the intent execution.

<details>

<summary>Parameters</summary>

* intent\_hash - Intent identifier

</details>

<details>

<summary>Response</summary>

* `intent_hash` - Intent identifier
* `status` - Intent status
* `data` - intent additional data
  * `hash` - transaction hash (if exists)

</details>

<details>

<summary>Statuses</summary>

* `PENDING` - the relay successfully received a signed intent
* `TX_BROADCASTED` - transactions have been successfully sent to be added to the block
* `SETTLED` - Intent executed successfully
* `NOT_FOUND_OR_NOT_VALID_ANYMORE` -  Intent not found or no valid anymore

</details>

<details>

<summary>Example</summary>

```javascript
// Request
{
    "id": 1,
    "jsonrpc": "2.0",
    "method": "get_status",
    "params":  [
        {
            "intent_hash": "00000000000000000000000000000000"
        }
    ]
}

// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "intent_hash": "00000000000000000000000000000000"
        "status": "SETTLED",
        "data": {
            "hash": "0x..." // if exists
        }
    }
}
```

</details>



***

### Solver WS Endpoint

`wss://solver-relay-v2.chaindefuser.com/ws`



<mark style="color:orange;">**1. Subscribe to quote/quote\_status requests**</mark>

<details>

<summary>Parameters</summary>

* `method` - subscribe
* `params[0]` - subscription name ("**quote","quote\_status"**)

</details>

<details>

<summary>Response</summary>

* `result` - subscription identifier which can be used to track following events

</details>

<details>

<summary>Example</summary>

<pre class="language-javascript"><code class="lang-javascript"><strong>// Request
</strong>{
    "jsonrpc": "2.0", 
    "id": 1, 
    "method": "subscribe", 
    "params": ["quote"]
}

// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "00000000-0000-0000-0000-000000000000", // subscriptionId
}
</code></pre>

* `defuse_asset_identifier_out` - asset to trade to

</details>



<mark style="color:orange;">**2. Receive quote requests**</mark>&#x20;

<details>

<summary>Event data</summary>

* `subscription` - subscription identifier
* `quote_id` - quote identifier
* `defuse_asset_identifier_in` - asset to trade from
* `exact_amount_in`- amount of _token\_in_ for exchange
* `exact_amount_out`- amount of _token\_out_ for exchange
  * **Condition**: Only one of `exact_amount_in` or`exact_amount_out` can will be specified in a request.
* `min_deadline_ms` - minimum validity time for an offer from solvers. The shorter the time, the better the price solvers can offer.

</details>

<details>

<summary>Example</summary>

<pre class="language-javascript"><code class="lang-javascript"><strong>// Event
</strong>{
    "jsonrpc": "2.0"
    "method": "subscribe",
    "params": {
        "subscription": "00000000-0000-0000-0000-000000000000",
        "quote_id":"00000000-0000-0000-0000-000000000000",
        "defuse_asset_identifier_in": "nep141:ft1.near",
        "defuse_asset_identifier_out": "nep141:ft2.near",
        "exact_amount_in": "1000", 
        "min_deadline_ms": "60000" // OPTIONAL. default 120_000ms / 2min
    }
}
</code></pre>

</details>



<mark style="color:orange;">**3. Respond to quote requests**</mark>

<details>

<summary>Parameters</summary>

* `quote_id` - user's quote identifier
* `quote_output` - quote result
  * amount\_out - proposed `amount`for `exact_amount_in` requests
  * amount\_in - proposed `amount`for `exact_amount_out` requests
* `signed_data` - intent content with signature
  * `standard` - content signature standard
  * `payload`- content of the intent
    * `message`
      * `signer_id` - Defuse account id
      * `intents[]` - Intent data
        * `intent` - Intent type
      * `deadline` - deadline until this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `recipient` - Defuse contract address
  * `signature` - signed content
  * `public_key` - signer's public key
* `other_quote_hashes` - hashes of other quote results to fulfill the current intent

</details>

<details>

<summary>Response</summary>

* `result` - proposal receipt status

</details>

<details>

<summary>Example</summary>

```javascript
// Request
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "quote_response",
  "params": [
    {
      "quote_id":"00000000-0000-0000-0000-000000000000",
      "quote_output": {
        "amount_out": "300",
      },
      "signed_data": {
        "standard": "nep413",
        "message": {
          "signer_id": "solver.near",
          "deadline": "2024-10-14T12:53:40.000Z",
          "intents": [
            {
              "intent": "token_diff",
              "diff": {
                "nep141:ft2.near": "-300",
                "nep141:ft1.near": "500"
              }
            },
          ]
        },
        "nonce": "87878609949187467518642340675777309892619814715982488116035839611029208327842",
        "recipient": "defuse.near",
        "signature": "ZuVBFXIGfndYn7pNyz+dhfNgsVoUqnGiNSBL6knztPsAc5REjbr5PrrDKdGlA7akg97U8RNtg0nDparXxI9+Dg==",
        "public_key": "7RuVD5I4g/cYwpGQt5zb2FEPaiGBU3WfZZkgvbcRN5M="
      }
    },
    "other_quote_hashes": ["00000000000000000000000000000000", ...] //optional
  ]
}

// Response
{
    "jsonrpc": "2.0", 
    "id": 1, 
    "result": "OK", 
}
```

</details>



<mark style="color:orange;">**4.Receive quote\_status event**</mark>

<details>

<summary>Event data</summary>

* `quote_hash` - solver intent hash used to identify quote
* `intent_hash` - user intent hash(identifier)
* `tx_hash` - hash of mined intent

</details>

<details>

<summary>Example</summary>

<pre class="language-javascript"><code class="lang-javascript"><strong>// Event
</strong>{
    "jsonrpc": "2.0"
    "method": "subscribe",
    "params": {
        "quote_hash": "00000000000000000000000000000000",
        "intent_hash":"00000000000000000000000000000000",
        "tx_hash": "00000..."
    }
}
</code></pre>

</details>



<mark style="color:orange;">**5. Unsubscribe**</mark>

<details>

<summary>Parameters</summary>

* `method` - unsubscribe
* `params[0]` - subscription identifier

</details>

<details>

<summary>Example</summary>

<pre class="language-javascript"><code class="lang-javascript"><strong>// Request
</strong>{
    "jsonrpc": "2.0", 
    "id": 1, 
    "method": "unsubscribe", 
    "params": ["00000000-0000-0000-0000-000000000000"] //subscriptionId
}

// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "OK"
}
</code></pre>

</details>

