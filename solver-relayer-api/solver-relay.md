# API

Solver Bus is an additional system component that optimizes frontends←→solvers quoting, and intent discovery process. Any frontend app may use a generic foundation-hosted Solver Bus or launch its own instance to customize preferred solver accounts.

Near Intents protocol may operate without a Solver Relay component:

* frontends may use any other quoting mechanisms to compose an intent for the end user
* solvers may index Near blockchain to find intents to fill.

However, using Solver Relay is recommended for speed optimization goals.

***

### Frontend **JSON-RPC Endpoint**

**POST:** `https://solver-relay-v2.chaindefuser.com/rpc`

***

<mark style="color:orange;">**1. Quote asset prices from solvers**</mark>

Allow the front end to estimate potential output for a given exact input or the required input for a given exact output based on user intent. The solver relay will forward a quote request to all connected solvers, wait for up to 3000 ms, and return all available quote options to the user. Only one of `exact_amount_in` or `exact_amount_out` should be provided in the request, not both.

<details>

<summary>Parameters</summary>

* `defuse_asset_identifier_in` - asset to trade from
* `defuse_asset_identifier_out`- asset to trade to
* `exact_amount_in` - amount of _token\_in_ for exchange
* `exact_amount_out` - amount of _token\_out_ for exchange
* `min_deadline_ms`_(optional)_ - minimum validity time for an offer from solvers (in milliseconds). The shorter the time, the better the price solvers can offer. Default is 60000 (1 minute).

</details>

<details>

<summary>Response</summary>

* array of quote responses from solvers
  * `quote_hash` - quote response hash
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
            "min_deadline_ms": 60000 // OPTIONAL. default 60_000ms / 1min
        }
    ]
}


// Response
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
            {
                "quote_hash": "00000000000000000000000000000000",
                "defuse_asset_identifier_in": "nep141:ft1.near",
                "defuse_asset_identifier_out": "nep141:ft2.near",
                "amount_in": "1000", 
                "amount_out": "2000"
                "expiration_time": "2024-10-01T12:10:27Z"
            }, 
            // ...
    ]
}
```

</details>

<mark style="color:orange;">**2. Publish intent**</mark>

If the user is satisfied with the offer provided by the solver, they can sign an intent and send it to the Solver Relay for execution along with the solver's quote hash(es). Relay supports these types of signatures for publishing intents: `nep413`, `erc191`, `raw_ed25519`

<details>

<summary>Parameters(NEP413)</summary>

* `quote_hashes` - quote response hashes from solvers
* `signed_data` - data containing user intents with a signature
  * `standard` - content signature standard (`nep413`)
  * `payload` - payload that was signed
    * `message`
      * `signer_id` - signer's account ID in Near Intents
      * `intents[]` - array of user intents
        * `intent` - intent type
        * other data depending on intent type
      * `deadline` - deadline until which this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `recipient` - Near Intents contract address (`intents.near`)
    * `callbackUrl`_(optional)_ - needed for some wallets
  * `signature` - signature of the payload
  * `public_key` - signer's public key

</details>

<details>

<summary>Parameters(ERC191)</summary>

* `quote_hashes` - quote response hashes from solverss
* `signed_data` - data containing user intents with a signature
  * `standard` - content signature standard (`erc191`)
  * `payload` - **STRINGIFIED** payload that was signed
    * `signer_id` - signer's account ID in Near Intents
    * `intents[]` - array of user intents
      * `intent` - intent type
      * other data depending on intent type
    * `deadline` - deadline until which this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `veryfying_contract` - Near Intents contract address (`intents.near`)
  * `signature` - signature of the payload

</details>

<details>

<summary>Parameters(RAW_ED25519)</summary>

* `quote_hashes` - quote response hashes from solvers
* `signed_data` - data containing user intents with a signature
  * `standard` - content signature standard (`raw_ed25519`)
  * `payload` - **STRINGIFIED** payload that was signed
    * `signer_id` - signer's account ID in Near Intents
    * `intents[]` - array of user intents
      * `intent` - intent type
      * other data depending on intent type
    * `deadline` - deadline until which this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `veryfying_contract` - Near Intents contract address (`intents.near`)
  * `signature` - signature of the payload
  * `public_key` - signer's public key

</details>

<details>

<summary>TokenDiff Intent</summary>

* `intent` - intent type (equals `token_diff`)
* `diff` - map where:
  * **Keys**: Represent `defuse_asset_identifiers` of tokens.
  * **Values**: Represent amounts for the tokens as strings.
    * Positive values indicate tokens to be received by the account.
    * Negative values (prefixed with "-") indicate tokens to be transferred from the account.

</details>

<details>

<summary>Response</summary>

* `status` - intent receiving status (`OK` or `FAILED`)
* `reason` - error reason (if the status is `FAILED`)
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
              "receiver_id": "ft1.near",
              "amount": "299",
              "memo": "WITHDRAW_TO:address_on_target_chain"
            }
          ]
        },
        "nonce": "bacFZfjWD8lm4mwAZ/TScL8HrrapeXlTSyAeD4i8Lfs=",
        "recipient": "intents.near",
        "signature": "ed25519:2yJ1ANYAL1yRoXk8uiDZygyH3TeRpVucwBMpUh1bsvcCLL3BBoJzqAojQNN4mxz9v5fSzbwqz7p9MFtZKNKW81Cg",
        "public_key": "ed25519:4vyWshm6BE4uoHk7fot2iij7tFXrjWp4wDnNEJx2W4sf"
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
        "status": "FAILED",
        "reason": "expired",
        "intent_hash": "00000000000000000000000000000000"
    }
}
```

</details>

<mark style="color:orange;">**3. Get intent status**</mark>

Check the status of intent execution.

<details>

<summary>Parameters</summary>

* `intent_hash` - intent identifier

</details>

<details>

<summary>Response</summary>

* `intent_hash` - intent identifier
* `status` - intent execution status
* `data` - additional data
  * `hash` - Near transaction hash (if available)

</details>

<details>

<summary>Statuses</summary>

* `PENDING` - the intent was successfully received by the Relay and is pending the execution
* `TX_BROADCASTED` - transaction for the intent has been successfully sent to the Near Intents contract
* `SETTLED` - the intent has been successfully settled on chain
* `NOT_FOUND_OR_NOT_VALID` - the intent wasn't received successfully, has expired, or there was an error when executing it on chain

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
            "hash": "8yFNEk7GmRcM3NMJihwCKXt8ZANLpL2koVFWWH1MEEj"
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
* `params[0]` - subscription name ("**quote"** or **"quote\_status"**)

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

<mark style="color:orange;">**2. Receive quote requests**</mark>

<details>

<summary>Event data</summary>

* `subscription` - subscription identifier
* `quote_id` - quote request identifier
* `defuse_asset_identifier_in` - asset to trade from
* `defuse_asset_identifier_out` - asset to trade to
* `exact_amount_in` - amount of _token\_in_ for exchange
* `exact_amount_out` - amount of _token\_out_ for exchange
  * **Condition**: Only one of `exact_amount_in` or`exact_amount_out` can will be specified in a request.
* `min_deadline_ms` - minimum validity time for an offer from solvers (in milliseconds). The shorter the time, the better the price solvers can offer.

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
        "min_deadline_ms": 60000
    }
}
</code></pre>

</details>

<mark style="color:orange;">**3. Respond to quote requests**</mark>

<details>

<summary>Parameters</summary>

* `quote_id` - quote request identifier
* `quote_output` - quote result
  * `amount_out` - proposed `amount`for `exact_amount_in` requests
  * `amount_in` - proposed `amount`for `exact_amount_out` requests
* `signed_data` - data containing solver intents with a signature
  * `standard` - content signature standard (`nep413`)
  * `payload` - payload that was signed
    * `message`
      * `signer_id` - solver's account ID in Near Intents
      * `intents[]` - array of intents
        * `intent` - intent type
        * other data depending on intent type
      * `deadline` - deadline until which this intent is valid, ISO-8601 string
    * `nonce` - unique nonce for operation
    * `recipient` - Near Intents contract address (`intents.near`)
  * `signature` - signature of the payload
  * `public_key` - signer's (solver's) public key
* `other_quote_hashes` - hashes of other quote results to fulfill the current intent (optional)

</details>

<details>

<summary>Response</summary>

* `result` - "OK" if the solver's proposal was successfully accepted

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
        "nonce": "BJwLqfS+yJSMgjzWAXOULFgSfHWdlK4Uso65/jeVUrA=",
        "recipient": "intents.near",
        "signature": "ed25519:5193Sx4eC6f3YdztiUfg2MSWJBuqi9Ykbuh3NEzw9b3nQQCCkT84YmJZoN4dpjc8g4rx1D5o2asesR5ECtGa5vFQ",
        "public_key": "ed25519:AgYQSY8hemfdL8ENQqwBrVoohB5d6j972qwgWgPgjgiZ"
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

* `quote_hash` - solver's intent hash used to identify the quote response
* `intent_hash` - user's intent hash (identifier)
* `tx_hash` - hash of the mined intent transaction on Near

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

* `method` - "unsubscribe"
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
