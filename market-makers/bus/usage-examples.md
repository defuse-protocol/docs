---
description: Example of signing a quote (creating a commitment).
---

# Usage

{% hint style="info" %}
There is no SDK for Message Bus API yet. But intents-sdk can be used to create intents
{% endhint %}

Example of how to create response for quote for market makers using TypeScript\
\
Here `params`  has same type as what you receive from relay in **"quote"** event

```typescript
params: {
  defuse_asset_identifier_in: string;
  defuse_asset_identifier_out: string;
  exact_amount_in: string | undefined;
  exact_amount_out: string | undefined;
  min_deadline_ms: number;
},
```

```typescript
import {VersionedNonceBuilder} from "@defuse-protocol/intents-sdk";

const generateNonce = async (deadline: Date): Promise<string> => {
  const account = await getAccount(); //function that will return Account instance(from "near-api-js") of market makers' Near account

  const salt_hex = await account.viewFunction({
    contractId: "intents.near",
    methodName: "current_salt",
  });

  let salt_bytes = Uint8Array.from(Buffer.from(salt_hex, "hex"));
  let versionedNonce = VersionedNonceBuilder.encodeNonce(salt_bytes, deadline);

  if (await isNonceUsed(versionedNonce)) {
    // this step can be skipped, but if the nonce is already used the quote won't be taken into account
    return generateNonce(deadline);
  } else {
    return versionedNonce;
  }
}

const isNonceUsed = async (nonce: string) => {
    const account = await getAccount();

    return await account.viewFunction({
      contractId: defuseContract,
      methodName: 'is_nonce_used',
      args: {
        account_id: account.accountId,
        nonce,
      },
    });
}
```

```typescript
const getIntentPayload = async (
  sdk: IntentsSDK,
  deadline: Date,
  params: {
    defuse_asset_identifier_in: string;
    defuse_asset_identifier_out: string;
    exact_amount_in: string | undefined;
    exact_amount_out: string | undefined;
    min_deadline_ms: number;
  },
  amount: string
) => {
  let nonce = await generateNonce(deadline);

  return await sdk
    .intentBuilder()
    .setDeadline(deadline)
    .setNonce(nonce)
    .addIntent({
      intent: "token_diff",
      diff: {
        [params.defuse_asset_identifier_in]: !!params.exact_amount_in
          ? params.exact_amount_in
          : amount,
        [params.defuse_asset_identifier_out]: `-${
          !!params.exact_amount_out ? params.exact_amount_out : amount
        }`,
      },
    })
    .build();
};
```

```typescript
import bs58 from 'bs58';

const amount = "1000" //calculated amount market maker want to propose
const deadline = new Date(Date.now() + 5 * 60 * 1000)

const intentSigner = createIntentSignerNearKeyPair({
  signer,
  accountId,
});

const sdk = new IntentsSDK({
  env: "production",
  referral: "your_referral",
  intentSigner: intentSigner,
});

const payload = await getIntentPayload(
  sdk,
  deadline,
  params
);

let signedPayload = await intentSigner.signIntent(payload);

const resp: IQuoteObject = {
  quote_id,
  quote_output: {},
  signed_data: signedPayload
};
if (!params.exact_amount_in) {
  resp.quote_output.amount_in = amount;
} else {
  resp.quote_output.amount_out = amount;
}
```



