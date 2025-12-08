---
icon: computer-mouse
---

# Aggregate fees from any token into desired asset

**1Click Swap — ANY\_INPUT**

**Purpose:** Aggregate fees from _any_ supported token into one `destinationAsset`, then auto-withdraw once the pool hits **$1,000 USD**.

### Wire-up (fees flow)

1. Create the ANY\_INPUT quote → grab **`depositAddress`**.
2. Put that address into **`appFees`** for user quotes.
3. System auto-swaps incoming tokens to `destinationAsset` and **withdraws at ≥$1,000 USD**.

### How it works

* Use `originAsset: "1cs:any"` with `amount: "0"`.
* Take the **`depositAddress`** from the quote response and put it into **`appFees`** in user quotes (that’s where fees should land).
* Quote **deadline** is checked only at **creation**; after that the quote is **valid indefinitely**.
* A background job **continuously polls**, swaps all deposits to `destinationAsset`, and **withdraws** when the **$1k** threshold is reached.
* **No refunds.** If a swap attempt fails, it retries **every 5 minutes** (no funds returned). However it’s still suggested to add the address you control to `refundTo`

**Auth:** You must call the Quote API with `Authorization: Bearer <JWT>` to receive the quote (and `depositAddress`).

**Tip:** `quoteWaitingTimeMs` ≈ **5000–10000** (3–4s often OK).

***

### Create ANY\_INPUT quote (example request)

```
{
  "dry": false,
  "swapType": "ANY_INPUT",
  "slippageTolerance": 0,
  "originAsset": "1cs:any",
  "depositType": "INTENTS",

  // Token you want to collect fees in:
  "destinationAsset": "nep141:eth-0xdac17f958d2ee523a2206206994597c13d831ec7.omft.near",

  "amount": "0",

  // Suggested to put the address you control
  "refundTo": "something.near",
  "refundType": "INTENTS",

  // Where converted fees will be withdrawn to:
  "recipient": "0x1ddA60d784483FBB54304c68830d42A706327C6d",
  "recipientType": "DESTINATION_CHAIN", // or "INTENTS"

  // Used only at creation; quote itself is unlimited:
  "deadline": "2025-09-22T20:00:00Z",

  "referral": "YOUR_REFERRAL",

  // Prefer 5–10s
  "quoteWaitingTimeMs": 10000
}

```

***

## ANY\_INPUT Withdrawals

#### **Endpoint**

`GET /any-input/withdrawals`

#### **Description**

Use this endpoint to retrieve withdrawal records. Withdrawals are identified and filtered by the **`depositAddress`** parameter.

Each response contains up to **50 records**, ordered by **timestamp** by default.

#### **Features**

* **Filtering:** Records are retrieved by `depositAddress`.
* **Pagination:** Supports pagination through standard query parameters.
* **Sorting:** Results are sorted by `timestamp` (descending by default).
* **Limit:** Maximum of 50 records per request.

### ANY\_INPUT Withdrawals response

```json
{
    "recipient": "0x88b9da55b59a59751424b357cf8aea239770944c",
    "affiliateRecipient": "2fd5aba4dc4729dcaec040242fc3b0b1faf8c81f74831c7c7843c6a96ad28add",
    "asset": "nep141:base-0x833589fcd6edb6e08f4c7c32d4f71b54bda02913.omft.near",
    "withdrawals": [
        {
            "status": "SUCCESS",
            "amountOut": "140735",
            "amountOutFormatted": "0.140735",
            "amountOutUsd": "0.140693483175",
            "withdrawFee": "2400",
            "withdrawFeeFormatted": "0.0024",
            "withdrawFeeUsd": "0.0023992919999999995",
            "timestamp": "2025-10-07T10:16:19.702Z",
            "hash": "0xcc005d3e3340c61b1240905c4e383d8b8ecb114a827bef3e0394293997406621"
        }
    ]
}
```
