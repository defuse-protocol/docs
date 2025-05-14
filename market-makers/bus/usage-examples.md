---
description: Example of signing a quote (creating a commitment).
---

# Market Makers

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
const generateNonce = async (): Promise<string> => {
    const randomArray = randomBytes(32);
    return randomArray.toString('base64');
    if (await this.isNonceUsed(nonceString)) { //this step can be skipped but if nonce is already used quote wont be taken into account
      return this.generateNonce();
    } else {
     return nonceString;
    }
}
const isNonceUsed = async (nonce: string) => {
    const account = getAccount(); //function that will return Account instance(from "near-api-js") of market makers' Near account
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
import { BorshSchema, borshSerialize } from 'borsher';

const standardNumber = {
    ["nep413"]: 413,
  };
const Nep413PayloadSchema = BorshSchema.Struct({
  message: BorshSchema.String,
  nonce: BorshSchema.Array(BorshSchema.u8, 32),
  recipient: BorshSchema.String,
  callback_url: BorshSchema.Option(BorshSchema.String),
});
const serializeIntent = (
  intentMessage: any,
  recipient: string,
  nonce: string,
  standard: string,
): Buffer => {
  const payload = {
    message: intentMessage,
    nonce: base64ToUint8Array(nonce),
    recipient,
  };
  const payloadSerialized = borshSerialize(Nep413PayloadSchema, payload);
  const baseInt = 2 ** 31 + standardNumber[standard];
  const baseIntSerialized = borshSerialize(BorshSchema.u32, baseInt);
  const combinedData = Buffer.concat([baseIntSerialized, payloadSerialized]);
  return crypto.createHash('sha256').update(combinedData).digest();
}

const base64ToUint8Array = (base64: string): Uint8Array => {
  const binaryString = atob(base64);
  const len = binaryString.length;
  const bytes = new Uint8Array(len);
  for (let i = 0; i < len; i++) {
    bytes[i] = binaryString.charCodeAt(i);
  }
  return bytes;
}
```

```typescript
const signMessage = async (message: Uint8Array) { //you can implement your own way to sign message with near wallet
  return (await keyStore.getKey(nearNetworkId, accountId)).sign(message); //keyStore is instance of KeyStore(from "near-api-js")
}
```

```typescript
import bs58 from 'bs58';

const amount = "1000" //calculated amount market maker want to propose
const standard = "nep413";
const message = {
  signer_id: "...", //account id of market maker's account that will be used for signing
  deadline: {
    timestamp: 10000, //timestamp deadline in seconds
  },
  intents: [
    {
      intent: 'token_diff',
      diff: {
        [params.defuse_asset_identifier_in]: !!params.exact_amount_in
          ? params.exact_amount_in
          : amount,
        [params.defuse_asset_identifier_out]: `-${
          !!params.exact_amount_out ? params.exact_amount_out : amount
        }`,
      },
    },
  ],
};
const messageStr = JSON.stringify(message);
const nonce = await generateNonce();
const recipient = defuseContract; //for example "intents.near"
const quoteHash = serializeIntent(messageStr, recipient, nonce, standard);
const signature = signMessage(quoteHash);

const resp: IQuoteObject = {
  quote_id,
  quote_output: {},
  signed_data: {
    standard,
    payload: {
      message: messageStr,
      nonce,
      recipient,
    },
    signature: `ed25519:${bs58.encode(signature.signature)}`,
    public_key: `ed25519:${bs58.encode(signature.publicKey.data)}`,
  },
};
if (!params.exact_amount_in) {
  resp.quote_output.amount_in = amount;
} else {
  resp.quote_output.amount_out = amount;
}
```



