---
description: Frequently Asked Questions
---

# FAQs

## Do intents not support native NEAR deposits?

Only  `ft_transfer_call` can be used to deposit NEP-141 tokens from Near to `intents.near` :\


```
<TOKEN_ACCOUNT_ID>::ft_transfer_call({
  "receiver_id": "intents.near",
  "amount": "1234",
  "msg": "{\"receiver_id\": \"<ACCOUNT_ID>\"}"
})
```

Here is an example [receipt](https://nearblocks.io/txns/EwmeXzZJStA6e5JB49vgxNYJDemqeYCFGvPH7zapP1Fw#execution#4tyaF4MnMcNQVqrg3kXzsH9277ErDeCXS9g3c2keV38G) for that.\
Parameter `msg` can also be empty, so that funds will be deposited to `sender_id` (i.e. caller of `ft_transfer_call`). Here is an example of such [tx](https://nearblocks.io/txns/HoWpAR8dF5azsUVaQWrBW5VsRve5X4dwr9GGiHWj3R1P#execution)\
