# Build React dApp with Account Abstraxion

A working demo can be found [here](https://github.com/burnt-labs/xion.js/) in the \`apps/demo-app\` folder.

## Goal

The intention here is to help you setup a basic dapp using the Abstraxion library showcasing both query and transaction submission.&#x20;

## Requirements&#x20;

1. [Node.js](https://nodejs.org/)

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
In tsconfig.json "moduleResolution" should be set to "node". The default ("bundler") does not work with the package and is a [known issue.](https://github.com/burnt-labs/xion.js/issues/29)
{% endhint %}



## Setup Abstraxion Library

Replace the contents of `src/app/layout.tsx` with the following body:

{% code title="src/app/layout.tsx" %}
```typescript
"use client";
import { Inter } from 'next/font/google'
import './globals.css'
import {AbstraxionProvider} from "@burnt-labs/abstraxion";

import "@burnt-labs/abstraxion/styles.css";
import "@burnt-labs/ui/styles.css";

const inter = Inter({ subsets: ['latin'] })

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <AbstraxionProvider
          config={{
            contracts: ["xion1z70cvc08qv5764zeg3dykcyymj5z6nu4sqr7x8vl4zjef2gyp69s9mmdka"],
          }}
        >
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

The `AbstraxionProvider` is required to provide context for  the `useAbstraxionAccount` and \`useAbstraxionSigningClient\` hooks.

{% hint style="danger" %}
Be sure to provide the array of contract addresses your DAPP intends to interact with.
{% endhint %}

## Add Hooks to the homepage

Replace the contents of \`src/app/page.tsx\` with the following:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import { useEffect, useState } from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: account } = useAbstraxionAccount();

  // General state hooks
  const [isOpen, setIsOpen] = useState(false);

  return (
      <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
        <h1 className="text-2xl font-bold tracking-tighter text-black dark:text-white">
          ABSTRAXION
        </h1>
        <Button
            fullWidth
            onClick={() => {
              setIsOpen(true);
            }}
            structure="base"
            theme="secondary"
        >
          {account ? (
              <div className="flex items-center justify-center">VIEW ACCOUNT</div>
          ) : (
              "CONNECT"
          )}
        </Button>
        <Abstraxion
            isOpen={isOpen}
            onClose={() => {
              setIsOpen(false);
            }}
        />
      </main>
  );
}

```
{% endcode %}

This will give a a button that initiates a meta account using social login.  Click the \`CONNECT\` button and try it out!

## Transaction submission

Querying the chain wouldn't be of much use without a mechanism to alter chain state.  Let's do that now.

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
import "@burnt-labs/ui/styles.css";
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
          amount: [{ amount: "0", denom: "uxion" }],
          gas: "500000",
        },
        "",
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

If everything is successful you should see transaction results as shown above.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption><p>After clicking "Claim Seat" you should see the confirmation above.</p></figcaption></figure>

## Summary

We accomplished several things:

1. **Setup a Next.js Project** Start by generating a Next.js project using `npx create-next-app@latest`.
2. **Adding the Abstraxion Library** Add the Abstraxion library (`@burnt-labs/abstraxion`) to the project via `npm`.
3. **Setup AbstraxionProvider** In the layout file, setup the `AbstraxionProvider` to provide context for the `useAbstraxionAccount` and `useAbstraxionSigningClient` hooks.
4. **Landing Page Setup** Modify the home page content to allow initiating a meta account through a social login with a button.
5. **Submit a Transaction** Alter the chain's state by submitting a transaction.&#x20;

These basic components are the majority of what is needed to create and deploy a successful dapp! Feel free to reach out to us on discord or on our [Github](https://github.com/burnt-labs/xion.js).&#x20;







