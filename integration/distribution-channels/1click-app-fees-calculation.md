# App Fees Calculation

## 1Click Swap API — Fees

A concise guide to how setup 1Click fees during quoting and execution as a distribution channel.

### Parameters

* **recipient** — any NEAR‑supported address (named account ID like `alice.near`, implicit based account or EVM-like).
* **fee\_bps** — fee in basis points (bps). `100` → `1.00%`. The fee is charged **from the input token**.

> Conversion: p = fee\_bps / 10\_000.

### Constraints

* `0 ≤ fee_bps ≤ 10_000` (`10_000` = 100%).

### How fees are applied

#### EXACT\_IN

* `amount_in` is decreased based on fee during swap
* `net_in = amount_in * (1 - p)`
* The quote computes `amount_out` **from `net_in`**.
* `fee_amount = amount_in − net_in` (deducted in input token).

#### EXACT\_OUT

* `min_amount_in` is increased based on fee
* Apply the fee to the required input: `net_in = min_amount_in × (1 + p)`.
* `fee_amount = net_in − min_amount_in` (deducted in input token).
* If user deposit more than `min_amount_in` for `EXACT_OUT` fees will be deducted from `amount_in` field. `fee_amount = net_in − amount_in`

### Examples

**Example 1 — EXACT\_IN**

* Inputs: `amount_in = 1,000,000`, `fee_bps = 100` (`p = 0.01`)
* Computation: `net_in = 1,000,000 × (1 − 0.01) = 990,000`
* User deposits `1,000,000` and the quote calculates `amount_out` from `net_in = 990,000`
* Fee: `fee_amount = 10,000` (in input token)

**Example 2 — EXACT\_OUT**

* Inputs: `min_amount_in = 500,000`, `fee_bps = 100` (`p = 0.01`)
* Computation: `net_in = 500,000 × (1 + 0.01) = 505,000`
* User have to deposit `505,000` and the quote require at least `505,000` units of the input token.
* Fee: `fee_amount = 5,000` (in input token)
