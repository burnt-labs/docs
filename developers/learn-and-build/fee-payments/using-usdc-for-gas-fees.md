# Using USDC for Gas Fees

This guide demonstrates how to use USDC to pay for transaction fees on XION, leveraging the network's Parameterized Fee Layer to eliminate the need for holding native XION tokens.

## Overview

XION's Parameterized Fee Layer allows the network to accept USDC (and other tokens) as payment for transaction fees. This feature enables:

- **Simplified User Experience**: Users only need to hold USDC
- **Predictable Costs**: Stable fee pricing in USD terms
- **Seamless Integration**: Works with existing wallets and applications
- **No Token Swaps**: Direct fee payment without intermediate conversions

## Prerequisites

Before you begin, ensure you have:

1. **USDC Balance**: USDC tokens on XION (via IBC transfer or native issuance)
2. **Development Environment**: Node.js and npm/yarn installed
3. **XION SDK**: Latest version of @burnt-labs/abstraxion or cosmjs
4. **Network Access**: Connection to XION testnet or mainnet

## How It Works

The Parameterized Fee Layer operates by:

1. **Fee Calculation**: Transaction fees are calculated in the base denomination
2. **Token Acceptance**: The network accepts USDC as fee payment
3. **Automatic Conversion**: Fee market handles conversion and distribution
4. **Validator Settlement**: Validators receive their share after conversion

## Implementation Guide

### Step 1: Set Up Your Environment

First, install the necessary dependencies:

```bash
npm install @burnt-labs/abstraxion @cosmjs/stargate @cosmjs/proto-signing
```

### Step 2: Configure USDC as Fee Token

Create a configuration for using USDC as the fee token:

```typescript
import { SigningStargateClient } from "@cosmjs/stargate";
import { coins } from "@cosmjs/proto-signing";

// USDC configuration
const USDC_DENOM = "ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349"; // Mainnet USDC
// For testnet use: "ibc/6490A7EAB61059BFC1CDDEB05917DD70BDF3A611654162A1A47DB930D40D8AF4"

// Fee configuration
const DEFAULT_GAS_PRICE = "0.025"; // USDC per gas unit
const DEFAULT_GAS_LIMIT = "200000";

// Calculate fees in USDC
function calculateUSDCFee(gasLimit: string = DEFAULT_GAS_LIMIT): StdFee {
  const amount = Math.ceil(Number(gasLimit) * Number(DEFAULT_GAS_PRICE)).toString();
  
  return {
    amount: coins(amount, USDC_DENOM),
    gas: gasLimit,
  };
}
```

### Step 3: Initialize Client with USDC Fee Support

Set up your signing client to use USDC for fees:

```typescript
import { DirectSecp256k1HdWallet } from "@cosmjs/proto-signing";
import { GasPrice } from "@cosmjs/stargate";

async function initializeClient(mnemonic: string, rpcEndpoint: string) {
  // Create wallet
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, {
    prefix: "xion",
  });
  
  // Get signer
  const [account] = await wallet.getAccounts();
  
  // Create client with USDC gas price
  const client = await SigningStargateClient.connectWithSigner(
    rpcEndpoint,
    wallet,
    {
      gasPrice: GasPrice.fromString(`${DEFAULT_GAS_PRICE}${USDC_DENOM}`),
    }
  );
  
  return { client, address: account.address };
}
```

### Step 4: Send Transactions with USDC Fees

Example of sending a transaction using USDC for fees:

```typescript
async function sendTransactionWithUSDCFee(
  client: SigningStargateClient,
  senderAddress: string,
  recipientAddress: string,
  amount: string
) {
  try {
    // Estimate gas for the transaction
    const gasEstimate = await client.simulate(
      senderAddress,
      [
        {
          typeUrl: "/cosmos.bank.v1beta1.MsgSend",
          value: {
            fromAddress: senderAddress,
            toAddress: recipientAddress,
            amount: coins(amount, USDC_DENOM),
          },
        },
      ],
      ""
    );
    
    // Add 20% buffer to gas estimate
    const gasLimit = Math.ceil(gasEstimate * 1.2).toString();
    
    // Calculate fee in USDC
    const fee = calculateUSDCFee(gasLimit);
    
    // Send transaction
    const result = await client.sendTokens(
      senderAddress,
      recipientAddress,
      coins(amount, USDC_DENOM),
      fee,
      "Sending USDC with USDC fee"
    );
    
    console.log("Transaction successful:", result.transactionHash);
    return result;
  } catch (error) {
    console.error("Transaction failed:", error);
    throw error;
  }
}
```

### Step 5: Advanced Usage with Custom Messages

For more complex transactions, you can specify USDC fees explicitly:

```typescript
async function executeContractWithUSDCFee(
  client: SigningStargateClient,
  senderAddress: string,
  contractAddress: string,
  msg: any
) {
  // Prepare execute message
  const executeMsg = {
    typeUrl: "/cosmwasm.wasm.v1.MsgExecuteContract",
    value: {
      sender: senderAddress,
      contract: contractAddress,
      msg: Buffer.from(JSON.stringify(msg)),
      funds: [],
    },
  };
  
  // Estimate gas
  const gasEstimate = await client.simulate(senderAddress, [executeMsg], "");
  const gasLimit = Math.ceil(gasEstimate * 1.2).toString();
  
  // Calculate USDC fee
  const fee = calculateUSDCFee(gasLimit);
  
  // Sign and broadcast
  const result = await client.signAndBroadcast(
    senderAddress,
    [executeMsg],
    fee,
    "Execute contract with USDC fee"
  );
  
  return result;
}
```

## Integration with Abstraxion

When using Abstraxion for wallet abstraction, USDC fees are handled automatically:

```typescript
import { Abstraxion } from "@burnt-labs/abstraxion";

// Configure Abstraxion with USDC fee preference
const abstraxion = new Abstraxion({
  chainId: "xion-mainnet-1",
  rpcUrl: "https://rpc.xion-mainnet-1.xion.burnt.com",
  feeToken: {
    denom: USDC_DENOM,
    gasPrice: DEFAULT_GAS_PRICE,
  },
});

// Transactions will automatically use USDC for fees
const tx = await abstraxion.execute({
  contractAddress: "xion1...",
  msg: { /* your message */ },
  // Fee will be paid in USDC automatically
});
```

## Fee Estimation Best Practices

### 1. Dynamic Gas Estimation

Always estimate gas before sending transactions:

```typescript
async function estimateUSDCFee(
  client: SigningStargateClient,
  messages: EncodeObject[],
  signer: string
): Promise<StdFee> {
  const gasEstimate = await client.simulate(signer, messages, "");
  const gasLimit = Math.ceil(gasEstimate * 1.3).toString(); // 30% buffer
  return calculateUSDCFee(gasLimit);
}
```

### 2. Fee Fallback Mechanism

Implement fallback logic for fee payment:

```typescript
async function sendWithFeeFallback(
  client: SigningStargateClient,
  signer: string,
  messages: EncodeObject[]
) {
  try {
    // Try with USDC fee first
    const usdcFee = await estimateUSDCFee(client, messages, signer);
    return await client.signAndBroadcast(signer, messages, usdcFee);
  } catch (error) {
    if (error.message.includes("insufficient fee")) {
      // Fallback to higher fee or different token
      const higherFee = {
        ...usdcFee,
        amount: coins(
          Math.ceil(Number(usdcFee.amount[0].amount) * 1.5).toString(),
          USDC_DENOM
        ),
      };
      return await client.signAndBroadcast(signer, messages, higherFee);
    }
    throw error;
  }
}
```

## Common Patterns

### Pattern 1: USDC-Only Wallet

For applications where users only hold USDC:

```typescript
class USDCOnlyWallet {
  private client: SigningStargateClient;
  private address: string;
  
  constructor(client: SigningStargateClient, address: string) {
    this.client = client;
    this.address = address;
  }
  
  async getBalance(): Promise<string> {
    const balance = await this.client.getBalance(this.address, USDC_DENOM);
    return balance.amount;
  }
  
  async send(recipient: string, amount: string): Promise<DeliverTxResponse> {
    const fee = calculateUSDCFee();
    return await this.client.sendTokens(
      this.address,
      recipient,
      coins(amount, USDC_DENOM),
      fee
    );
  }
}
```

### Pattern 2: Multi-Transaction Batching

Optimize fees by batching multiple operations:

```typescript
async function batchTransactionsWithUSDCFee(
  client: SigningStargateClient,
  signer: string,
  operations: Array<{ recipient: string; amount: string }>
) {
  const messages = operations.map(op => ({
    typeUrl: "/cosmos.bank.v1beta1.MsgSend",
    value: {
      fromAddress: signer,
      toAddress: op.recipient,
      amount: coins(op.amount, USDC_DENOM),
    },
  }));
  
  const fee = await estimateUSDCFee(client, messages, signer);
  return await client.signAndBroadcast(signer, messages, fee);
}
```

## Troubleshooting

### Common Issues and Solutions

1. **"Insufficient fee" Error**
   - Ensure your USDC balance covers both the transaction amount and fees
   - Increase gas price or gas limit
   - Check current network congestion

2. **"Invalid fee token" Error**
   - Verify you're using the correct USDC denom for your network
   - Ensure the Parameterized Fee Layer is enabled on the chain

3. **Transaction Timeout**
   - Increase gas limit for complex transactions
   - Check RPC endpoint connectivity
   - Verify network status

### Debug Logging

Enable detailed logging for fee debugging:

```typescript
function logFeeDetails(fee: StdFee, gasEstimate: number) {
  console.log("Fee Details:", {
    token: fee.amount[0].denom,
    amount: fee.amount[0].amount,
    gasLimit: fee.gas,
    gasEstimate: gasEstimate,
    gasPrice: DEFAULT_GAS_PRICE,
    totalCostUSDC: (Number(fee.amount[0].amount) / 1_000_000).toFixed(6),
  });
}
```

## Security Considerations

1. **Fee Validation**: Always validate fee amounts before signing
2. **Slippage Protection**: Implement maximum fee limits
3. **Balance Checks**: Verify sufficient USDC balance before transactions
4. **Rate Limiting**: Implement transaction rate limits to prevent fee drain

## Next Steps

- Learn about [Fee Grants](../gasless-ux-and-permission-grants/) for sponsoring user transactions
- Explore [Custom Fee Tokens](./custom-fee-tokens.md) for accepting other tokens
- Read about [Fee Market Parameters](../../core-concepts/fee-layer.md) for advanced configuration

## Resources

- [XION Testnet Faucet](https://testnet.xion.burnt.com/faucet) - Get test USDC
- [IBC Transfer Guide](../ibc-transfers/) - Transfer USDC to XION
- [Abstraxion Documentation](../abstraxion/) - Wallet abstraction with fee handling