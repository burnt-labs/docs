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
See [public-endpoints-and-resources.md](../../section-overview/public-endpoints-and-resources.md "mention")for`'<rpcEndpoint>'` values &#x20;
{% endhint %}

## Initialize the signing client with a mnemonic

```typescript
import { GasPrice, SigningStargateClient, StargateClient, coins } from '@cosmjs/stargate';
import { DirectSecp256k1Wallet } from '@cosmjs/proto-signing';
import { toBech32 } from '@cosmjs/encoding';
import { rawSecp256k1PubkeyToRawAddress } from '@cosmjs/amino'

// Mnemonic for the account you want to use
const key = process.env.PRIVATE_KEY!;
const address = ADD_XION_ADDRESS_HERE
const rpcEndpoint = 'https://rpc.xion-testnet-1.burnt.com:443';

//Tx info
const amount = coins('1', 'uxion');
const gasPrice = GasPrice.fromString("0.001uxion");
const txOptions = { gasPrice };

// The main function to create and use the SigningStargateClient
async function main() {
    // Creating a wallet instance from a given mnemonic
    const wallet = await DirectSecp256k1Wallet.fromKey(Buffer.from(key,'hex'), "xion");

    // Fetching account from the wallet
    const [account] = await wallet.getAccounts();
    const sender = toBech32("xion",rawSecp256k1PubkeyToRawAddress(account.pubkey))

    // Creating an instance of SigningStargateClient
    const client = await SigningStargateClient.connectWithSigner(rpcEndpoint, wallet, txOptions);

    // Defining recipient and coins to be transferred
    const recipient = address;

    // Broadcasting the transaction
    const result = await client.sendTokens(sender, recipient, amount, "auto", "sending a msg!");

    console.log(result);
}

main().catch(console.error);
```

To fetch the private key from `xiond` execute the following in your terminal

{% hint style="warning" %}
The private key will be returned in plaintext, do NOT use `--unarmored-hex --unsafe` to export keys in production environments.&#x20;
{% endhint %}

```
❯ xiond keys export ACCOUNT_NAME --unarmored-hex --unsafe
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



