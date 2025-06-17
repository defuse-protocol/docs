# Chain and Address Type Support

List of supported chains and their respective address types can be seen in the table below:

| Chain      | Address Types                                                                                             | Support Status       | Example Address                                    |
|------------|-----------------------------------------------------------------------------------------------------------|-----------------------|----------------------------------------------------|
| **Arbitrum** | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Aurora**   | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Base**     | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Bera**     | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **BNB**      | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Bitcoin**  | - Legacy (`1` prefix) <br> - P2SH (`3` prefix) <br> - Bech32 (`bc1`) <br> - Taproot (`bc1p`)            | ✅ Supported           | `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` (Legacy) <br> `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kygt080` (Bech32) <br> `bc1p5cyxnuxmeuwuvkwfem96llyr29s8l68p7z6zgt7zdkv3g7zv3qvqz6z8h7` (Taproot) |
| **Doge**     | - P2PKH/Legacy (starts with `D`) <br> - P2SH (starts with `A` or `9`)                                   | ✅ Supported           | `D9nssC5jR1viPZhWwFvDkjYpJZYJVydN8k`               |
| **Ethereum** | - EVM address (`0x`-prefixed, 42-char hex)                                                              | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Gnosis**   | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **NEAR**     | - Named (`something.near`) <br> - Implicit (64-char hex, SHA-256 of pubkey)                            | ✅ Supported           | `alice.near`, `ed25519:...`                        |
| **Polygon**      | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Solana**   | - Base58-encoded Ed25519 public key (typically 44 chars)                                                | ✅ Supported           | `4Z9fE3tx...7AVkHb`                                |
| **Tron**     | - Base58Check-encoded (starts with `T`)                                                                 | ✅ Supported           | `TQ1shhBFTN2TwaRXyH1oLyCz3Yvfbzgmbk`               |
| **XRP**      | - Classic (starts with `r`)   <br> - Classic + Tag <br> - X-Address (starts with `X`)                   | ✅ Supported           | `rDsbeomae4FXwgQTJp9Rs64Qg9vDiTCdBv`               |
| **ZCash**    | - Transparent (`t1`, `t3`)                                                                               | ⚠️ Partially supported | `t1ZCashExample...`, `t3ZCashExample...`           |


This table provides an overview of supported signature standards and corresponding wallet types. For technical details on how intent signing works, see [Signing Intents](https://docs.near-intents.org/near-intents/market-makers/verifier/signing-intents).

| Signing Standard      | Supported Wallets / Apps                                                | Implementation Status          |
|-----------------------|--------------------------------------------------------------------------|-------------------------------|
| **NEP‑413**           | NEAR wallets: MyNearWallet, Meteor, Sender, Ledger‑NEAR                 | ✅ Implemented                |
| **ERC‑191**           | All EVM wallets: MetaMask, Rabby, Rainbow, WalletConnect‑compatible     | ✅ Implemented                |
| **Raw Ed25519**       | Solana wallets: Phantom, Solflare, Slope                                | ✅ Implemented                |
| **Passkeys (WebAuthn)** | Browsers/OS platforms: Chrome, Safari, Edge, Firefox, native apps      | ✅ Implemented                |
| **TON Connect**       | TON wallets: Tonkeeper, EverWallet, other TonConnect 2.0 wallets        | ⚙️ In progress                |
| **SEP‑53**            | Stellar wallets: Freighter, Lumens Wallet, SatoshiPay (in draft)        | ⚙️ In progress                |
