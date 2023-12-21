# Build React dApp with Account Abstraxion

{% hint style="info" %}
A working demo can be found [here](https://github.com/burnt-labs/xion.js/) in the \`apps/demo-app\` folder.
{% endhint %}

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
        <AbstraxionProvider>{children}</AbstraxionProvider>
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

{% hint style="info" %}
If you are working on a domain other than `localhost` or `*.vercel.app, we may need to whitelist your domain. This limitation is only temporary and is being addressed`[`here`](https://github.com/burnt-labs/xion.js/issues/11)
{% endhint %}



## Query account info

Let's take your new account for a spin by adding a simple account info section after login. This should look familiar to you if you have worked with Stargate before. The `useAbstraxionSigningClient` will expose a client preconfigured to access testnet.



Refresh `src/app/page.tsx`with the following code:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import {useEffect, useState} from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion";
import {Button} from "@burnt-labs/ui";
import Link from "next/link";
import {Account} from "@cosmjs/stargate";

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const {data: account} = useAbstraxionAccount();
  const {client} = useAbstraxionSigningClient();

  // General state hooks
  const [isOpen, setIsOpen] = useState(false);

  const [accountInfo, setAccountInfo] = useState<Account | undefined>(undefined);

  const getAccountInfo = async () => {
    if (client && account?.bech32Address) {
      const accountInfo = await client.getAccount(account?.bech32Address || "");
      setAccountInfo(accountInfo);
    } else {
      setAccountInfo(undefined);
    }
  };

  // useEffect to get account info on load
  useEffect(() => {
    void getAccountInfo();
  }, [client]);

  const accountBlockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/account/${accountInfo?.address}`;

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
      {accountInfo ? (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white">
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Account Address:</span>
            </p>
            <p className="text-sm">{accountInfo.address}</p>
          </div>
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Account Number:</span>
            </p>
            <p className="text-sm">{accountInfo?.accountNumber}</p>
          </div>
          <div className="mt-2">
            <Link
              className="text-black underline visited:text-purple-600 dark:text-white"
              href={accountBlockExplorerUrl}
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

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption><p>The resulting page should look like the above.</p></figcaption></figure>

Well done! Click block explorer link to see your account info. As you can see the client provided by the `useAbstraxionSigningClient` hook unlocks a versatile set of possibilities such as query balances, contract information, etc.&#x20;

## Transaction submission

Querying the chain wouldn't be of much use without a mechanism to alter chain state.  Let's do that now.

Refresh the contents of `src/app/page.tsx` with the following:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import {useEffect, useState} from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion";
import {Button} from "@burnt-labs/ui";
import Link from "next/link";
import {Account} from "@cosmjs/stargate";
import {InstantiateResult} from "@cosmjs/cosmwasm-stargate/build/signingcosmwasmclient";

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const {data: account} = useAbstraxionAccount();
  const {client} = useAbstraxionSigningClient();

  // General state hooks
  const [isOpen, setIsOpen] = useState(false);
  const [isSubmittingTransaction, setSubmittingTransaction] = useState(false);

  const [accountInfo, setAccountInfo] = useState<Account | undefined>(undefined);

  // useState to store txResult
  const [txResults, setTxResults] = useState<InstantiateResult[]>([]);

  const getAccountInfo = async () => {
    if (client && account?.bech32Address) {
      const accountInfo = await client.getAccount(account?.bech32Address || "");
      setAccountInfo(accountInfo);
    } else {
      setAccountInfo(undefined);
    }
  };

  // useEffect to get the balance on load
  useEffect(() => {
    void getAccountInfo();
  }, [client]);

  const accountBlockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/account/${accountInfo?.address}`;

  const submitTransaction = async (): Promise<void> => {
    setSubmittingTransaction(true);
    try {
      if (!client) {
        setIsOpen(true);
        return;
      }
      const initMsg = {
        metadata: {
          metadata: {
            name: "name",
            hub_url: "url",
            description: "description",
            tags: [],
            social_links: [],
            creator: account?.bech32Address,
            thumbnail_image_url: "https://fakeimg.pl/200/",
            banner_image_url: "https://fakeimg.pl/500/",
          },
        },
        ownable: {
          owner: account?.bech32Address,
        },
      };

      const txResult = await client.instantiate(
        account?.bech32Address || "",
        1,
        initMsg,
        "my-hub",
        {
          amount: [{amount: "0", denom: "uxion"}],
          gas: "500000",
        },
      );

      setTxResults([...txResults, txResult]);
    } catch (error) {
      // eslint-disable-next-line no-console -- No UI exists yet to display errors
      console.log(error);
    } finally {
      setSubmittingTransaction(false);
    }
  };


  return (
    <main className="m-auto flex min-h-screen max-w-lg flex-col items-center justify-center gap-4 p-4">
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
      {client ? (
        <Button
          disabled={isSubmittingTransaction}
          fullWidth
          onClick={() => {
            void submitTransaction();
          }}
          structure="base"
          theme="secondary"
        >
          {isSubmittingTransaction ? "SUBMITTING..." : "SUBMIT TEST TRANSACTION"}
        </Button>
      ) : null}
      {accountInfo ? (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white">
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Account Address:</span>
            </p>
            <p className="text-sm">{accountInfo.address}</p>
          </div>
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Account Number:</span>
            </p>
            <p className="text-sm">{accountInfo?.accountNumber}</p>
          </div>
          <div className="mt-2">
            <Link
              className="text-black underline visited:text-purple-600 dark:text-white"
              href={accountBlockExplorerUrl}
              target="_blank"
            >
              View in Block Explorer
            </Link>
          </div>
        </div>
      ) : null}

      <div className="w-full space-y-4">
        <h2 className="text-2xl font-bold mb-4 dark:text-white">Transaction Results</h2>

        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
          {txResults.map((txResult, index) => {
            const transactionBlockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/tx/${txResult.transactionHash}`;

            return (
              <div
                key={index}
                className="flex flex-col rounded border-2 dark:border-white p-2 space-y-2 bg-white dark:bg-gray-800 shadow dark:shadow-white"
              >
                <div>
                  <p className="font-bold dark:text-white">Transaction Hash:</p>
                  <p className="text-sm overflow-ellipsis overflow-hidden dark:text-gray-300">{txResult.transactionHash}</p>
                </div>
                <div>
                  <p className="font-bold dark:text-white">Block Height:</p>
                  <p className="text-sm dark:text-gray-300">{txResult.height}</p>
                </div>
                <div>
                  <Link
                    className="text-black underline visited:text-purple-600 dark:text-white"
                    href={transactionBlockExplorerUrl}
                    target="_blank"
                  >
                    View in Block Explorer
                  </Link>
                </div>
              </div>
            );
          })}
        </div>
      </div>
    </main>
  );
}

```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>After submitting a few transactions you should several results</p></figcaption></figure>

If everything is successful you should see transaction results as shown above.



## Summary

We accomplished several things:

1. **Setup a Next.js Project** Start by generating a Next.js project using `npx create-next-app@latest`.
2. **Adding the Abstraxion Library** Add the Abstraxion library (`@burnt-labs/abstraxion`) to the project via `npm`.
3. **Setup AbstraxionProvider** In the layout file, setup the `AbstraxionProvider` to provide context for the `useAbstraxionAccount` and `useAbstraxionSigningClient` hooks.
4. **Landing Page Setup** Modify the home page content to allow initiating a meta account through a social login with a button.
5. **Query Account Information** Use the `useAbstraxionSigningClient` hook to query account information by adding a simple account info section post-login.
6. **Submit a Transaction** Alter the chain's state by submitting a transaction.&#x20;

These basic components are the majority of what is needed to create and deploy a successful dapp! Feel free to reach out to us on discord or on our [Github](https://github.com/burnt-labs/xion.js).&#x20;







