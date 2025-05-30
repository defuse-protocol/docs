# Chain and Address Type Support

List of supported chains and their respective address types can be seen in the table below:

| Chain      | Address Types                                                                                             | Support Status       | Example Address                                    |
|------------|-----------------------------------------------------------------------------------------------------------|-----------------------|----------------------------------------------------|
| **NEAR**   | - Named (`something.near`) <br> - Implicit (64-char hex, SHA-256 of pubkey)                               | ✅ Supported           | `alice.near`, `ed25519:...`                        |
| **ZCash**  | - Transparent (`t1`, `t3`)                                    | ⚠️ Partially supported | `t1ZCashExample...`, `t3ZCashExample...`           |
| **Ethereum** | - EVM address (`0x`-prefixed, 42-char hex)                                                              | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Gnosis**   | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Bera**     | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Base**     | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Arbitrum** | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Aurora**   | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **BNB**      | - EVM address                                                                                           | ✅ Supported           | `0x85F17Cf997934a597031b2E18a9aB6ebD4B9f6a4`       |
| **Bitcoin**  | - Legacy (`1` prefix) <br> - P2SH (`3` prefix) <br> - Bech32 (`bc1`) <br> - Taproot (`bc1p`)            | ✅ Supported           | `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` (Legacy) <br> `bc1qw508d6qejxtdg4y5r3zarvary0c5xw7kygt080` (Bech32) <br> `bc1p5cyxnuxmeuwuvkwfem96llyr29s8l68p7z6zgt7zdkv3g7zv3qvqz6z8h7` (Taproot) |
| **Solana**   | - Base58-encoded Ed25519 public key (typically 44 chars)                                                | ✅ Supported           | `4Z9fE3tx...7AVkHb`                                |
| **Doge**     | - P2PKH/Legacy (starts with `D`) <br> - P2SH (starts with `A` or `9`)                                   | ✅ Supported           | `D9nssC5jR1viPZhWwFvDkjYpJZYJVydN8k`               |
| **XRP**      | - Classic (starts with `r`)   <br> - Classic + Tag <br> - X-Address (starts with `X`)                   | ✅ Supported           | `rDsbeomae4FXwgQTJp9Rs64Qg9vDiTCdBv`               |
| **Tron**     | - Base58Check-encoded (starts with `T`)                                                                 | ✅ Supported           | `TQ1shhBFTN2TwaRXyH1oLyCz3Yvfbzgmbk`               |

