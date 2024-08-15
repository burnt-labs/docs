---
description: Access XION from TypeScript.
---

# XION TypeScript

## Setup

Add stargate to your project.

```bash
npm install @cosmjs/stargate
```

{% hint style="info" %}
See [public-endpoints-and-resources.md](../public-endpoints-and-resources.md "mention")for`'<rpcEndpoint>'` values &#x20;
{% endhint %}

## Initialize the signing client with a mnemonic

```typescript
import { SigningStargateClient, coins } from '@cosmjs/stargate';
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";

// Mnemonic for the account you want to use
const mnemonic = '<your-mnemonic>';
const rpcEndpoint = '<rpc-endpoint>';

// The main function to create and use the SigningStargateClient
async function main() {
    // Creating a wallet instance from a given mnemonic
    const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic);

    // Fetching account from the wallet
    const [account] = await wallet.getAccounts();

    // Creating an instance of SigningStargateClient
    const client = await SigningStargateClient.connectWithSigner(rpcEndpoint, wallet);

    // Defining recipient and coins to be transferred
    const recipient = '<recipient-address>';
    const sendingAmount = coins(1000, 'uxion');

    // Broadcasting the transaction
    const result = await client.sendTokens(account.address, recipient, sendingAmount);
    console.log(result);
}

main().catch(console.error);
```

The \`mnemonic\` must be generated generated.

### Initialize signing client with a private key

```typescript
import { DirectSecp256k1Wallet, SigningStargateClient, Secp256k1, makeCosmoshubPath, BroadcastTxResponse, coins } from '@cosmjs/stargate';
import { fromBase64 } from '@cosmjs/encoding';

// Private key for the account you want to use
// Make sure to have it in base64 format
const privkey = '<Your private key in base64>';  
const rpcEndpoint = '<rpc-endpoint>';

async function main() {
    // Converting base64 private key into Uint8Array
    const key = fromBase64(privkey);

    // Making a wallet instance from the DirectSecp256k1Wallet
    const wallet = await DirectSecp256k1Wallet.fromKey(key);

    // Fetching account from the wallet
    const [account] = await wallet.getAccounts();

    // Creating an instance of SigningStargateClient
    const client = await SigningStargateClient.connectWithSigner(rpcEndpoint, wallet);

    // Defining recipient and coins to be transferred
    const recipient = '<recipient-address>';
    const sendingAmount = coins(1000, 'uxion');

    // Broadcasting the transaction
    const result: BroadcastTxResponse = await client.sendTokens(account.address, recipient, sendingAmount);
    console.log(result);
}

main().catch(console.error);
```

If you need to generate a quickly the following run the following in a terminal:

```bash
openssl rand -hex 32
```

## Queries

### Query for the balance of a contract

```typescript
import { QueryClient } from '@cosmjs/stargate';
import { QueryAllBalancesResponse } from '@cosmjs/stargate/build/codec/cosmos/bank/v1beta1/query';

// Change this to your node endpoint
const rpcEndpoint = '<rpc-endpoint>';

// Address to query
const addressToQuery = '<address-to-query>';

async function main() {
  const client = QueryClient.withExtensions({rpcEndpoint});

  const balances: QueryAllBalancesResponse = await client.bank.allBalances(addressToQuery);

  console.log('Balances:', balances);

  client.disconnect();
}

main().catch(console.error);
```



