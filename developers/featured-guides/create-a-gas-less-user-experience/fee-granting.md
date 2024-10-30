# Fee Granting

#### Demonstrating Fee Allowance

This guide outlines how to grant a fee allowance, so that you can enable a gas-less experience for your end users. This guide also demonstrates how to validate its functionality by sending tokens back using AAClient. The process involves:

1. **Initial Setup:** Creating client instances for a funding account and a recipient account.
2. **Granting Fee Allowance:** Allowing the recipient account to use the funding account's resources for transaction fees.
3. **Validating Fee Allowance:** Sending a token from the recipient account to the funding account to demonstrate the successful fee grant.

**Initial Setup**

Begin by initializing client instances for both accounts using user-provided private keys:

```typescript
import { AAClient } from "@burnt-labs/signers";

// These are used in the following code examples.
import { MsgGrantAllowance } from "cosmjs-types/cosmos/feegrant/v1beta1/tx";
import { BasicAllowance } from "cosmjs-types/cosmos/feegrant/v1beta1/feegrant";


export async function buildClient(
  key: string
): Promise<[AAClient, AccountData]> {
  const signer: OfflineDirectSigner = await DirectSecp256k1Wallet.fromKey(
    fromHex(key),
    burntChainInfo.bech32Config.bech32PrefixAccAddr
  );

  const [accountData] = await signer.getAccounts();
  const client = await AAClient.connectWithSigner(
    // This can be any RPC endpoint.
    "https://rpc.xion-testnet-1.burnt.com:443",
    signer
  );

  return [client, accountData];
}

const [fundingAccountClient, fundingAccount] = await buildClient("<private_key_of_funding_account>");
const [recipientAccountClient, recipientAccount] = await buildClient("<private_key_of_recipient_account>");

```

**Granting Fee Allowance**

Grant a fee allowance from the funding account to the recipient account:

```typescript
  // Construct the grant allowance message
  const msgGrantAllowance = {
    typeUrl: "/cosmos.feegrant.v1beta1.MsgGrantAllowance",
    value: MsgGrantAllowance.fromPartial({
      granter: fundingAccount.address,
      grantee: recipientAccount.address,
      allowance: {
        typeUrl: "/cosmos.feegrant.v1beta1.BasicAllowance",
        value: BasicAllowance.encode(
          BasicAllowance.fromPartial({
            spendLimit: [
              {
                denom: "uxion",
                amount: "<amount of token to grant>",
              },
            ],
            expiration: {
              seconds: Math.floor((Date.now() + <Number of seconds until grant expires>) / 1000),
            },
          })
        ).finish(),
      },
    }),
  };

  // Send the grant allowance transaction
  const grantResponse = await fundingAccountClient.signAndBroadcast(
    fundingAccount.address,
    [msgGrantAllowance],
    defaultFee,
    "Granting fee allowance to recipient account"
  );
```

**Validating Fee Allowance**

To confirm the fee allowance, send a token back from the recipient account to the funding account:

```typescript
  // Construct the send token message
  const msgSend = {
    typeUrl: "/cosmos.bank.v1beta1.MsgSend",
    value: {
      fromAddress: recipientAccount.address,
      toAddress: fundingAccount.address,
      amount: [
        {
          denom: "uxion",
          amount: "1",
        },
      ],
    },
  };

  // Send the token transaction
  const sendResponse = await recipientAccountClient.signAndBroadcast(
    recipientAccount.address,
    [msgSend],
    {
      ...defaultFee,
      granter: fundingAccount.address,
    },
    "Returning token to funding account"
  );

  console.log("sendResponse", sendResponse);
```

This complete example shows how to set up a fee allowance and verify its success through a token transaction.
