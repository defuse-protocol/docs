# Chain Support

List of supported chains and their respective address types can be seen in the table below:

| Chain      | Address Types                                                                                             | Support Status       | Example Address                                    |
|------------|-----------------------------------------------------------------------------------------------------------|-----------------------|----------------------------------------------------|
| **EVM Chains (Arbitrum, Avalanche, ADI, Aurora, Base, Bera, BNB, Ethereum, Gnosis, Optimism, Plasma, Polygon, XLayer, Monad)** | - EVM address (`0x`-prefixed, 42-char hex)  | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Cardano**    | - Shelley Base <br>- Enterprise                                                                             | ⚠️ Partially supported | `addr1v8wfpcg4qfhmnzprzysj6j9c53u5j56j8rvhyjp08s53s6g07rfjm`           |
| **Bitcoin**  | - Legacy (`1` prefix) <br> - P2SH (`3` prefix) <br> - Bech32 (`bc1`) <br> - Taproot (`bc1p`)            | ✅ Supported           | `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` (Legacy) <br> `3FZbgi29cpjq2GjdwV8eyHuJJnkLtktZc5` (P2SH) <br> `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kygt080` (Bech32) <br> `bc1p5cyxnuxmeuwuvkwfem96llyr29s8l68p7z6zgt7zdkv3g7zv3qvqz6z8h7` (Taproot) |
| **Bitcoin Cash**  | - All Types           | ✅ Supported           | N/A |
| **Doge**     | - P2PKH/Legacy (starts with `D`) <br> - P2SH (starts with `A` or `9`)                                   | ✅ Supported           | `D9nssC5jR1viPZhWwFvDkjYpJZYJVydN8k`               |
| **Litecoin**  | - Legacy (`L` prefix) <br> - P2SH (`M` prefix) <br> - Bech32 (`ltc1q`) <br> - Taproot (`ltc1p`)        | ✅ Supported           | `LZ3v1o8qK4b7sJ9mH2f5xQp8Pd1cR6TuVa` (Legacy) <br> `MDf8y1Kq9Tm5sR3aP7uW4cXn2Lb6vZjQhE` (P2SH) <br> `ltc1q9k3p7u5n0s4y8v2h3w6j5c9r2t0m4k7f5p0d2` (Bech32) <br> `ltc1p4h7q9m3x5u2n8k6t4v9y3r5c2w7z0l3p5u8y6f0k2d` (Taproot) |
| **NEAR**     | - Named (`something.near`) <br> - Implicit (64-char hex, SHA-256 of pubkey)                             | ✅ Supported           | `alice.near`, `ed25519:...`                        |
| **Sui**      | - Sui Address (32-char hex)                                                                             | ✅ Supported           | `0xcc64b79a3adf4d3c21ad25a97e3ecbe83e659e68964f62e6a1da8a037346a4ce`    |
| **Stellar**  | - 56-character base32 string                                                                            | ✅ Supported           | `GBD7QFQVR4QWNEJSHP4VN7RAAUKXTMZ4EJ4EBMCR7CP3HMF7RXEASTD7`              |
| **Solana**   | - Base58-encoded Ed25519 public key (typically 44 chars)                                                | ✅ Supported           | `BYPsjxa3YuZESQz1dKuBw1QSFCSpecsm8nCQhY5xbU1Z`                          |
| **TON**      | - TON Addresses                                                                                         | ✅ Supported           | `EQAWzEKcdnykvXfUNouqdS62tvrp32bCxuKS6eQrS6ISgcLo` |
| **Tron**     | - Base58Check-encoded (starts with `T`)                                                                 | ✅ Supported           | `TQ1shhBFTN2TwaRXyH1oLyCz3Yvfbzgmbk`               |
| **XRP**      | - Classic (starts with `r`)   <br> - Classic + Tag <br> - X-Address (starts with `X`)                   | ✅ Supported           | `rDsbeomae4FXwgQTJp9Rs64Qg9vDiTCdBv`               |
| **ZCash**    | - Transparent (`t1`, `t3`) <br> - Unified (`u1`)                                                        | ⚠️ Partially supported | `t1ZCashExample...`, `t3ZCashExample...`, `u1ZCashExample...`           |


This table provides an overview of supported signature standards and corresponding wallet types. For technical details on how intent signing works, see [Signing Intents](https://docs.near-intents.org/near-intents/market-makers/verifier/signing-intents).

| Signing Standard      | Supported Wallets / Apps                                                 | Implementation Status         |
|-----------------------|--------------------------------------------------------------------------|-------------------------------|
| **NEP‑413**           | NEAR wallets: MyNearWallet, Meteor, Sender, Ledger‑NEAR                  | ✅ Implemented                |
| **ERC‑191**           | All EVM wallets: MetaMask, Rabby, Rainbow, WalletConnect‑compatible      | ✅ Implemented                |
| **Raw Ed25519**       | Solana wallets: Phantom, Solflare, Slope                                 | ✅ Implemented                |
| **Passkeys (WebAuthn)** | Browsers/OS platforms: Chrome, Safari, Edge, Firefox, native apps      | ✅ Implemented                |
| **SEP‑53**            | Stellar wallets: Freighter, Lumens Wallet, SatoshiPay                    | ✅ Implemented                |
| **TIP‑191**           | TRON wallets: TronLink, Klever, Trust Wallet                             | ✅ Implemented                |
| **TON Connect**       | TON wallets: Tonkeeper, EverWallet, HOT Wallet and other TonConnect 2.0 wallets         | ✅ Implemented                |
| **BIP‑322**           | Bitcoin wallets: Sparrow, Bitcoin Knots, Bitcoin Core                    | ⚙️ In progress                |
