# Build a Brale ACH On/Off-Ramp App on Verona

The **Brale** ACH Ramp Demo shows how to add bank-based on-ramp and off-ramp flows to a Verona application. Users connect with their Verona Meta Account, link a bank account through **Brale** and **Plaid**, buy stablecoins with ACH, and cash out stablecoins back to a bank account.

This guide walks through running the demo locally and explains the pieces you can copy into another Verona app.

The demo app source is available at: https://github.com/burnt-labs/brale-ach-demo

### How the Demo Works

The app has three main parts:

* A frontend built with Next.js and Abstraxion for wallet connection and transaction signing.
* A small backend API that stores users, KYC details, linked bank addresses, transfer records, and webhook events in SQLite.
* Brale API calls for bank linking, ACH debit, ACH credit, transfer creation, and transfer status reconciliation.

The on-ramp and off-ramp flows are intentionally separate:

* On-ramp: bank USD to wallet SBC. The user does not need to sign an on-chain transaction.
* Off-ramp: wallet SBC to bank USD. The user sends SBC to Brale's custodial Verona address before Brale initiates the ACH credit.

### Prerequisites

Before you begin, make sure you have:

* Node.js 26 or newer
* pnpm
* Git
* A Verona testnet wallet with `uxion` for gas
* Brale API credentials
* A Brale account ID
* ngrok or another public tunnel for local webhook testing

You will also need access to Brale's Verona testnet transfer type, which is configured in this demo as `xion_testnet`.

### Clone the Demo App

Start by cloning the repository:

```bash
git clone https://github.com/burnt-labs/brale-ach-demo.git
cd brale-ach-demo
```

Install dependencies:

```bash
pnpm install
```

### Set Up Environment Variables

Create your local environment file:

```bash
cp .env.example .env
```

Generate an app session secret:

```bash
openssl rand -base64 32
```

Add the value to `.env`:

```bash
AUTH_SECRET=your-generated-secret
```

Keep SQLite on the default path unless you want to store the local database somewhere else:

```bash
SQLITE_PATH=./data/brale-demo.sqlite
```

The app creates the SQLite database and applies `sql/schema.sql` automatically on startup.

### Configure Verona Testnet

The demo defaults to Verona testnet:

```bash
NEXT_PUBLIC_XION_CHAIN_ID=xion-testnet-2
NEXT_PUBLIC_XION_RPC=https://rpc.xion-testnet-2.burnt.com
NEXT_PUBLIC_XION_REST=https://api.xion-testnet-2.burnt.com
```

If you already have a Treasury contract or bank spend grant configured for your app, you can add it:

```bash
NEXT_PUBLIC_TREASURY_ADDR=
NEXT_PUBLIC_ABSTRAXION_BANK_GRANT_AMOUNT=
```

If no Treasury or bank grant is configured, cash-out falls back to direct wallet approval.

### Configure Brale

Add your Brale credentials:

```bash
BRALE_CLIENT_ID=
BRALE_CLIENT_SECRET=
BRALE_ACCOUNT_ID=
```

If you need to find your `BRALE_ACCOUNT_ID`, request a Brale access token and list accounts:

```bash
TOKEN=$(curl -s -X POST "https://auth.brale.xyz/oauth2/token" \
  -H "Authorization: Basic $(echo -n 'CLIENT_ID:CLIENT_SECRET' | base64)" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" | jq -r '.access_token')

curl -s "https://api.brale.xyz/accounts" \
  -H "Authorization: Bearer $TOKEN"
```

For local testnet, keep these Brale transfer settings:

```bash
BRALE_API_URL=https://api.brale.xyz
BRALE_AUTH_URL=https://auth.brale.xyz
BRALE_ALLOWED_TRANSFER_TYPES=xion_testnet,ach_debit,ach_credit,same_day_ach_credit,rtp_credit
BRALE_SBC_TRANSFER_TYPE=xion_testnet
BRALE_SBC_VALUE_TYPE=SBC
BRALE_ACH_DEBIT_TYPE=ach_debit
BRALE_ACH_CREDIT_TYPE=same_day_ach_credit
NEXT_PUBLIC_BRALE_SBC_DENOM=factory/xion17grq736740r70awldugfs3mls3stu9haewctv2/sbc
```

The allowlist helps prevent accidentally using the wrong transfer type while testing.

### Run the App Locally

Start the dev server:

```bash
pnpm dev --hostname 127.0.0.1
```

Open the app:

```
http://127.0.0.1:3000
```

If port `3000` is busy, use another port:

```bash
pnpm dev --hostname 127.0.0.1 --port 3001
```

### Connect with Abstraxion

When the app opens:

1. Connect your Verona wallet.
2. Approve the login proof transaction.
3. The backend verifies the transaction memo through Verona REST.
4. The app creates a signed HTTP-only session cookie for the connected wallet.

The login proof is a small 1 `uxion` self-transfer with a nonce memo. The server checks the sender, memo, and transaction hash before creating the session.

### Save KYC Details

Before a user can link a bank account, the app stores basic KYC details:

* Legal name
* Email address
* Phone number in E.164 format
* Optional date of birth

These details are stored locally in `brale_users` and sent to Brale when creating the Plaid Link token.

### Link a Bank Account

Click **Link bank** to open Plaid Link through Brale.

Behind the scenes:

1. The frontend calls `POST /api/brale/plaid/link-token`.
2. The backend sends the user's KYC details to Brale.
3. Brale returns a Plaid Link token.
4. The frontend opens Plaid Link.
5. After Plaid succeeds, the frontend calls `POST /api/brale/plaid/register`.
6. The backend exchanges the Plaid public token for a reusable Brale bank `address_id`.

The app saves that bank `address_id` as `brale_users.plaid_bank_address_id`.

### Start an ACH On-Ramp

ACH on-ramp moves USD from the user's linked bank account to SBC on their Verona wallet.

In the demo:

1. The user enters a USD amount and clicks **Buy with ACH**.
2.  The frontend calls `POST /api/brale/transfers` with:

    ```json
    {
      "kind": "onramp",
      "amountUsd": "5.00"
    }
    ```
3. The backend verifies the user session, saved KYC, and linked bank.
4. The backend registers the user's Verona wallet as a Brale external address if needed.
5. The backend creates a local `brale_transfers` row.
6. The local transfer UUID is used as Brale's `Idempotency-Key`.
7.  The backend creates a Brale transfer:

    ```json
    {
      "amount": { "value": "5.00", "currency": "USD" },
      "source": {
        "address_id": "bank-address-id",
        "value_type": "USD",
        "transfer_type": "ach_debit"
      },
      "destination": {
        "address_id": "wallet-address-id",
        "value_type": "SBC",
        "transfer_type": "xion_testnet"
      }
    }
    ```

The user does not need to sign an on-chain transaction for on-ramp because the source is the linked bank account.

### Start an ACH Off-Ramp

ACH off-ramp moves SBC from the user's Verona wallet to USD in the linked bank account.

In the demo:

1. The user enters an amount and clicks **Cash out to bank**.
2.  The frontend calls `POST /api/brale/transfers` with:

    ```json
    {
      "kind": "offramp",
      "amountUsd": "5.00"
    }
    ```
3.  The backend creates a local transfer row and returns:

    ```json
    {
      "id": "local-transfer-id",
      "custodialBech32": "xion1...",
      "memo": "local-transfer-id",
      "sbcDenom": "factory/...",
      "microAmount": "5000000"
    }
    ```
4. The frontend signs and broadcasts `sendTokens(wallet -> Brale custodial address)` with the memo.
5. The frontend PATCHes `/api/brale/transfers/{id}` with the on-chain transaction hash.
6.  The backend creates a Brale transfer from custodial SBC to bank USD:

    ```json
    {
      "amount": { "value": "5.00", "currency": "USD" },
      "source": {
        "address_id": "brale-custodial-address-id",
        "value_type": "SBC",
        "transfer_type": "xion_testnet"
      },
      "destination": {
        "address_id": "bank-address-id",
        "value_type": "USD",
        "transfer_type": "same_day_ach_credit"
      }
    }
    ```

If a Treasury or bank spend grant is configured, the app tries Abstraxion session-key signing first. Otherwise, it reconnects the wallet signer and uses direct wallet approval.

### Configure Brale Webhooks

Brale transfer creation returns a status such as `pending`. To know when a transfer is fully completed, configure Brale webhooks.

Expose this endpoint to Brale:

```
POST https://your-domain.example.com/api/brale/webhook
```

For local testing, start a tunnel:

```bash
ngrok http 3000
```

Use the tunnel URL plus `/api/brale/webhook` when creating the Brale webhook subscription.

Subscribe to:

```
transfer.completed
```

Save the returned webhook shared secret in `.env`:

```bash
BRALE_WEBHOOK_SHARED_SECRET=
```

Restart the dev server after adding the secret.

The webhook route:

1. Reads the raw request body.
2. Verifies `x-request-signature-sha-256` with HMAC-SHA256.
3. Stores the Brale event in `brale_webhook_events` for deduplication.
4. Updates the matching local transfer where `brale_transfer_id = event.data.id`.
5. Sets `status = complete` and fills `completed_at`.

The dashboard also reconciles open transfers against Brale during refresh. This is useful for local development when webhooks are not yet reachable.

### Check Transfer Status in the Dashboard

The **Recent transfers** table shows:

* Transfer kind: on-ramp or off-ramp
* USD amount
* Current Brale status
* Completion timestamp when available
* Error messages if a transfer fails

Pending transfers automatically refresh while the app is open.

### Copying the Flow into Another Verona App

To add this flow to another Next.js app, copy the same boundaries rather than the whole UI:

* `sql/schema.sql` tables for users, linked bank addresses, transfers, and webhook events.
* `src/lib/brale-client.ts` for Brale authentication, request helpers, and transfer-type validation.
* `src/lib/brale-account.ts` for KYC, bank address, wallet address, and custodial address helpers.
* `src/lib/brale-webhook.ts` for webhook signature verification and event processing.
* `src/app/api/brale/**` routes for KYC, Plaid, transfers, and webhooks.
* `src/lib/session.ts` and `src/lib/xion.ts` if your app needs wallet-only login.
* The dashboard components as a reference for user-facing states.

If your app already has authentication, replace `getSessionUser()` with your own user lookup. The Brale routes only need a stable user ID and wallet address.

### Production Notes

Before using this in production:

* Keep Brale client credentials server-only.
* Use a narrow `BRALE_ALLOWED_TRANSFER_TYPES` allowlist.
* Persist the local transfer row before calling Brale so retries reuse the same idempotency key.
* Store Brale webhook events for deduplication.
* Verify webhook signatures against the raw body.
* Use Brale webhooks for final completion and status polling as a fallback.
* Use mainnet Brale credentials and mainnet transfer types only when you are ready to move real funds.
* Decide whether cash-out should use Abstraxion session-key grants, direct wallet signing, or both.

### Additional Resources

* Verona Docs: [Welcome to Verona](../../../README.md)
* Developer Portal: https://dev.testnet.burnt.com
* Brale API Docs: https://docs.brale.xyz
* Brale Webhook Events: https://docs.brale.xyz/webhooks/webhook-events
* Brale Quick Start: https://docs.brale.xyz/overview/quick-start/
* Demo Repository: https://github.com/burnt-labs/brale-ach-demo
