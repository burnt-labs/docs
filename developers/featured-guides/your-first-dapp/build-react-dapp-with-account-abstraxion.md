# Build React dApp with Account Abstraxion

A working demo can be found here: [https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app)

## Goal

The intention here is to help you setup a basic dapp using the Abstraxion library showcasing both query and transaction submission.

## Requirements

1. [Node.js](https://nodejs.org/)
2. Nextjs

## Setup Project

For this example we will use [nextjs](https://nextjs.org/learn-pages-router/basics/create-nextjs-app) to scaffold the project

Run the following commands in your terminal:

```bash
# Generate project along with settings to avoid wizard.
npx create-next-app@latest nextjs-xion-abstraxion-example --use-npm --ts --eslint --tailwind --app  --src-dir --import-alias "@/*"

# Enter application directory
cd nextjs-xion-abstraxion-example
```

Add the Abstraxion library to the project:

```bash
npm i @burnt-labs/abstraxion
```

Start the project in developer mode:

```
npm run dev
```

Open `https://localhost:3000` in a web browser and you will see a fancy animated react logo.

{% hint style="warning" %}
**NOTE:**&#x20;

A package versioning mismatch can cause builds to break and you'll see an error on the lines of:\
\
`Module build failed: UnhandledSchemeError: Reading from "node:url" is not handled by plugins (Unhandled scheme).`

\
To fix this, update your `next.config.js` in the root of your app to this file: [https://gist.githubusercontent.com/probablyangg/4c520e376cdddf6991951e233d1f9bb6/raw/fa0ecaf1b2e52876610be9d36a8aeaaef53f0dd5/next.config.js](https://gist.githubusercontent.com/probablyangg/4c520e376cdddf6991951e233d1f9bb6/raw/fa0ecaf1b2e52876610be9d36a8aeaaef53f0dd5/next.config.js)
{% endhint %}

## Setup Abstraxion Library

**Before** we can get into setting up our abstraxion SDK in the app we'll be deploying a treasury contract here:&#x20;

1. Login to the Developer portal
2. Click on "New Treasury"&#x20;
3.  Select appropriate config - to get started here's a config that should work for most of the use cases:

    <figure><img src="../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
4. **NOTE:** the contract address in "Grant config" is the contract your users will be interacting with

{% hint style="info" %}
Read more about the treasury contracts [here](create-a-gas-less-user-experience/).
{% endhint %}

Replace the contents of `src/app/layout.tsx` with the following body:

{% code title="src/app/layout.tsx" %}
```typescript
"use client";
import { Inter } from 'next/font/google'
import './globals.css'
import {AbstraxionProvider} from "@burnt-labs/abstraxion";

import "@burnt-labs/abstraxion/dist/index.css";
import "@burnt-labs/ui/dist/index.css";

const inter = Inter({ subsets: ['latin'] })


const treasuryConfig = {
  treasury: "YOUR_TREASURY_CONTRACT_ADDRESS_HERE",
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <AbstraxionProvider
          config={treasuryConfig}>
          {children}
        </AbstraxionProvider>
      </body>
    </html>
  )
}

```
{% endcode %}

{% hint style="warning" %}
Without the "use client"; directive at the top, you will get a error
{% endhint %}



The `AbstraxionProvider` is required to provide context for the `useAbstraxionAccount` and \`useAbstraxionSigningClient\` hooks.

## Add Hooks to the homepage

Replace the contents of \`src/app/page.tsx\` with the following:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import {
  Abstraxion,
  useAbstraxionAccount,
  useModal
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import { useEffect } from "react";

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: { bech32Address }, isConnected, isConnecting } = useAbstraxionAccount();

  // General state hooks
  const [, setShow] = useModal();

  // watch isConnected and isConnecting
  // only added for testing
  useEffect(() => {
    console.log({ isConnected, isConnecting });
  }, [isConnected, isConnecting])

  return (
      <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
        <h1 className="text-2xl font-bold tracking-tighter text-black dark:text-white">
          Abstraxion
        </h1>
        <Button
            fullWidth
            onClick={() => { setShow(true) }}
            structure="base"
        >
          {bech32Address ? (
              <div className="flex items-center justify-center">VIEW ACCOUNT</div>
          ) : (
              "CONNECT"
          )}
        </Button>
        {
          bech32Address &&
            <div className="border-2 border-primary rounded-md p-4 flex flex-row gap-4">
              <div className="flex flex-row gap-6">
                <div>
                  address
                </div>
                <div>
                  {bech32Address}
                </div>
              </div>
            </div>
        }
        <Abstraxion onClose={() => setShow(false)} />
      </main>
  );
}
```
{% endcode %}

This will give a a button that initiates a meta account using social login. Click the \`CONNECT\` button and try it out!

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Transaction submission

Querying the chain wouldn't be of much use without a mechanism to alter chain state. Let's do that now.

Refresh the contents of `src/app/page.tsx` with the following:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import Link from "next/link";
import { useState } from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import "@burnt-labs/ui/dist/index.css";
import type { ExecuteResult } from "@cosmjs/cosmwasm-stargate";
import { seatContractAddress } from "./layout";

type ExecuteResultOrUndefined = ExecuteResult | undefined;
export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: account } = useAbstraxionAccount();
  const { client } = useAbstraxionSigningClient();

  // General state hooks
  const [isOpen, setIsOpen] = useState(false);
  const [loading, setLoading] = useState(false);
  const [executeResult, setExecuteResult] =
    useState<ExecuteResultOrUndefined>(undefined);

  const blockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/tx/${executeResult?.transactionHash}`;

  function getTimestampInSeconds(date: Date | null) {
    if (!date) return 0;
    const d = new Date(date);
    return Math.floor(d.getTime() / 1000);
  }

  const now = new Date();
  now.setSeconds(now.getSeconds() + 15);
  const oneYearFromNow = new Date();
  oneYearFromNow.setFullYear(oneYearFromNow.getFullYear() + 1);

  async function claimSeat() {
    setLoading(true);
    const msg = {
      sales: {
        claim_item: {
          token_id: String(getTimestampInSeconds(now)),
          owner: account.bech32Address,
          token_uri: "",
          extension: {},
        },
      },
    };

    try {
      const claimRes = await client?.execute(
        account.bech32Address,
        seatContractAddress,
        msg,
        {
          amount: [{ amount: "0.001", denom: "uxion" }],
          gas: "500000",
        },
        "", // memo
        [],
      );

      setExecuteResult(claimRes);
    } catch (error) {
      // eslint-disable-next-line no-console -- No UI exists yet to display errors
      console.log(error);
    } finally {
      setLoading(false);
    }
  }

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      <Button
        fullWidth
        onClick={() => {
          setIsOpen(true);
        }}
        structure="base"
      >
        {account.bech32Address ? (
          <div className="flex items-center justify-center">VIEW ACCOUNT</div>
        ) : (
          "CONNECT"
        )}
      </Button>
      {client ? (
        <Button
          disabled={loading}
          fullWidth
          onClick={() => {
            void claimSeat();
          }}
          structure="base"
        >
          {loading ? "LOADING..." : "CLAIM SEAT"}
        </Button>
      ) : null}
      <Abstraxion
        isOpen={isOpen}
        onClose={() => {
          setIsOpen(false);
        }}
      />
      {executeResult ? (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white">
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Transaction Hash</span>
            </p>
            <p className="text-sm">{executeResult.transactionHash}</p>
          </div>
          <div className="mt-2">
            <p className=" text-zinc-500">
              <span className="font-bold">Block Height:</span>
            </p>
            <p className="text-sm">{executeResult.height}</p>
          </div>
          <div className="mt-2">
            <Link
              className="text-black underline visited:text-purple-600 dark:text-white"
              href={blockExplorerUrl}
              target="_blank"
            >
              View in Block Explorer
            </Link>
          </div>
        </div>
      ) : null}
    </main>
  );
}


```
{% endcode %}

### Quick Note on Fee Config

Inside the `claimSeat` function above, the `.execute()` is being called as such:

{% code lineNumbers="true" %}
```typescript
const claimRes = await client?.execute(
  account.bech32Address,
  seatContractAddress,
  msg,
  {
    amount: [{ amount: "0.001", denom: "uxion" }],
    gas: "500000",
    // ...
  },
  "", // memo
  [],
);
```
{% endcode %}

The fourth parameter in the above function call represents the `fee` config - replacing the object with `auto` here will allow the SDK to handle fee configuration for you, eg.,

```typescript
const claimRes = await client?.execute(
  account.bech32Address,
  seatContractAddress,
  msg,
  "auto",
  "", // memo
  [],
);
```

If everything is successful you should see transaction results as shown above.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption><p>After clicking "Claim Seat" you should see the confirmation above.</p></figcaption></figure>

## Summary

We accomplished several things:

1. **Setup a Next.js Project** Start by generating a Next.js project using `npx create-next-app@latest`
2. **Setting up our first treasury contract**
3. **Adding the Abstraxion Library** Add the Abstraxion library (`@burnt-labs/abstraxion`) to the project via `npm`
4. **Setup AbstraxionProvider** In the layout file, setup the `AbstraxionProvider` to provide context for the `useAbstraxionAccount` and `useAbstraxionSigningClient` hooks
5. **Landing Page Setup** Modify the home page content to allow initiating a meta account through a social login with a button
6. **Submit a Transaction** Alter the chain's state by submitting a transaction

These basic components are the majority of what is needed to create and deploy a successful dapp! Feel free to reach out to us on discord or on our [Github](https://github.com/burnt-labs/xion.js).
