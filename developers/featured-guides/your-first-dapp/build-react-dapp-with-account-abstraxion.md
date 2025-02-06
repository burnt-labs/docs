# Account Abstraction with Gasless Transactions

In this guide, we will walk through building a basic dApp using the [Abstraxion library](https://www.npmjs.com/package/@burnt-labs/abstraxion), demonstrating how to create an Abstraxion account which can be done via a social account like Google, browser wallets (Keplr, Metamask, OKX etc.), email address, passkey and other authentication options. We will also implement a gasless transaction experience for users by leveraging XION's fee grants through a Treasury contract.

To better understand Account Abstraction you can visit the[ Introduction to Account Abstraction](https://docs.burnt.com/xion/developers/learn/intro-to-account-abstraction) page.

A fully functional demo of this dApp is also available in the [Xion.js](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app) repository.



## Requirements

Before getting started, ensure you have the following installed:

* [**Node.js**](https://nodejs.org/) (LTS version recommended) – Required for running the development environment and installing dependencies.



## Setting up the Project

In this example, we will use [**Next.js**](https://nextjs.org) to scaffold the project and set up a development environment with TypeScript, ESLint and Tailwind CSS.

### Step 1: Create a New Next.js Project

Run the following command in your terminal to generate a Next.js project with the required settings:

```bash
npx create-next-app@14.0.0 nextjs-xion-abstraxion-example \
  --use-npm --ts --eslint --tailwind --app --src-dir --import-alias "@/*"
```

### Step 2: Navigate to the Project Directory

```
cd nextjs-xion-abstraxion-example
```

### Step 3: Install the Abstraxion Package

Add the **Abstraxion** package to the project:

```bash
npm i @burnt-labs/abstraxion
```

### Step 4: Start the Development Server

Run the following command to start the development server:

```
npm run dev
```

### Step 5: Open the Project in a Browser

Once the server is running, open [**http://localhost:3000**](http://localhost:3000) in a web browser. You should see a Next.js welcome page with an animated React logo.

{% hint style="warning" %}
**NOTE:**&#x20;

A mismatch in package versions can lead to build failures, resulting in an error similar to the following:\
\
`Module build failed: UnhandledSchemeError: Reading from "node:url" is not handled by plugins (Unhandled scheme).`

\
To resolve this issue, update your `next.config.js` file located at the root of your project with the configuration provided in this file: [https://gist.githubusercontent.com/probablyangg/4c520e376cdddf6991951e233d1f9bb6/raw/fa0ecaf1b2e52876610be9d36a8aeaaef53f0dd5/next.config.js](https://gist.githubusercontent.com/probablyangg/4c520e376cdddf6991951e233d1f9bb6/raw/fa0ecaf1b2e52876610be9d36a8aeaaef53f0dd5/next.config.js)
{% endhint %}



## Deploying a Contract On-Chain

To interact with our dApp, we need to deploy a **smart contract** on-chain. In this guide, we will deploy a **basic Counter contract**, which allows you to:

* **Set an initial counter value**
* **Increment or reset the counter**
* **Query the current counter value**

Follow the steps in the following [guide](https://github.com/burnt-labs/cw-counter/blob/main/README.md) to **compile, deploy, and instantiate** the contract on-chain. Once deployed, this contract will be referenced in the **Treasury contract** and the **code updates** that follow.



## Deploying a Treasury Contract for Gasless Transactions

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** for your smart contract by handling **fee grants** on behalf of users.

### Steps to Deploy a Treasury Contract

1. Login to the [XION Developer Portal](https://dev.testnet.burnt.com/).
2. Click on **"New Treasury"** to create a new treasury contract.
3. **Select the appropriate configuration** based on your use case. The following "**Fee Grant**" and "**Grant Config**" images gives a recommended configuration that works for most scenarios:

#### Fee Grant

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Example of a general <strong>Fee Grant</strong> configuration</p></figcaption></figure>

1. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
2. In the **"Allowance Type"** field, enter `"/cosmwasm.feegrant.v1beta1.BasicAllowance"`.
3. In the **"Spend Limit"** field, enter **`1000uxion`**.
4. Click the **"Save"** button to apply the configuration.

#### **Grant Config**

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Example of additional <strong>Grant</strong> configuration</p></figcaption></figure>

1. For the **"Type URL"** field, select `"/cosmwasm.wasm.v1.MsgExecuteContract"`.
2. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
3. In the **"Authorization Type"** field, select `"/cosmwasm.wasm.v1.ContractExecutionAuthorization"`.
4. Enter the **contract address** in the **"Contract Address"** field — this should be the NFT smart contract created above.
5. You **must** select at least one of the following::
   * **"Max Call"** – Limits the number of times a user can execute a transaction under this fee grant.
   * **"Max Funds"** – Specifies the maximum amount of funds allocated for covering transaction fees.
   * **"Both"** – Allows you to set both options.
6. Click the **"Add Contract Grant"** button to apply the configuration.
7. Then click the "**Save**" button which generates the "**Treasury Instance Preview**"

#### Treasury Instance Preview

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Once the preview is to your liking click the "**Create**" button to create the Treasury contract.

{% hint style="info" %}
Learn more about Treasury Contracts [here](create-a-gas-less-user-experience/).
{% endhint %}



## Integrating the Abstraxion Library

To set up the **Abstraxion Library**, replace the contents of **`src/app/layout.tsx`** with the following code:

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
Update "**YOUR\_TREASURY\_CONTRACT\_ADDRESS\_HERE**" with the the Treasury Contract Address you created above to enable gasless transactions for users.
{% endhint %}

* **`"use client";`**
  * This directive is required to ensure that the file is treated as a **client component** in Next.js.
  * Omitting this will result in an error.
* **`AbstraxionProvider`**
  * This component wraps the application and provides context for **Abstraxion hooks**, such as:
    * `useAbstraxionAccount`
    * `useAbstraxionSigningClient`
* **`treasuryConfig`**
  * Set the **Treasury Contract Address** to enable gasless transactions for users.



## Adding Hooks to the Homepage

Replace the contents of **`src/app/page.tsx`** with the following code:

<pre class="language-typescript" data-title="src/app/page.tsx"><code class="lang-typescript"><strong>"use client";
</strong>import {
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
      &#x3C;main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
        &#x3C;h1 className="text-2xl font-bold tracking-tighter text-black dark:text-white">
          Abstraxion
        &#x3C;/h1>
        &#x3C;Button
            fullWidth
            onClick={() => { setShow(true) }}
            structure="base"
        >
          {bech32Address ? (
              &#x3C;div className="flex items-center justify-center">VIEW ACCOUNT&#x3C;/div>
          ) : (
              "CONNECT"
          )}
        &#x3C;/Button>
        {
          bech32Address &#x26;&#x26;
            &#x3C;div className="border-2 border-primary rounded-md p-4 flex flex-row gap-4">
              &#x3C;div className="flex flex-row gap-6">
                &#x3C;div>
                  address
                &#x3C;/div>
                &#x3C;div>
                  {bech32Address}
                &#x3C;/div>
              &#x3C;/div>
            &#x3C;/div>
        }
        &#x3C;Abstraxion onClose={() => setShow(false)} />
      &#x3C;/main>
  );
}
</code></pre>

#### What does this do?

1. **Displays a "CONNECT" button**
   * Clicking the button initiates a **Meta Account** login.
   * If the user is connected, it shows **"VIEW ACCOUNT"** instead.
2. **Shows the user's blockchain address** when connected.
3. **Handles connection state** with `useAbstraxionAccount()`:
   * `bech32Address`: The user's address.
   * `isConnected`: Whether the user is connected.
   * `isConnecting`: Connection state status.

Now, click **CONNECT** and try it out!



<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Note:** If the treasury contract address is changed within the dApp, users who are already logged in must log out and log back in. This ensures that their account goes through the approval process again, allowing the fee grants to function properly.
{% endhint %}

## Querying the Contract

The contract provides a method to query the current `count` value. Let's fetch this value and display it in the dApp.

Replace the contents of `src/app/page.tsx` with the following code:

```javascript
"use client";
import Link from "next/link";
import { useState } from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
  useAbstraxionSigningClient,
  useAbstraxionClient,
  useModal,
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import "@burnt-labs/ui/dist/index.css";
import type { ExecuteResult } from "@cosmjs/cosmwasm-stargate";

const contractAddress =
  "YOUR_COUNTER_CONTRACT_ADDRESS_HERE";

type ExecuteResultOrUndefined = ExecuteResult | undefined;

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: account } = useAbstraxionAccount();
  const { client, signArb, logout } = useAbstraxionSigningClient();
  const { client: queryClient } = useAbstraxionClient();

  // Count
  const [count, setCount] = useState<string | null>(null);

  // General state hooks
  const [, setShowModal]: [
    boolean,
    React.Dispatch<React.SetStateAction<boolean>>,
  ] = useModal();
  const [loading, setLoading] = useState(false);
  const [executeResult, setExecuteResult] =
    useState<ExecuteResultOrUndefined>(undefined);

  const blockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/tx/${executeResult?.transactionHash}`;

  function getTimestampInSeconds(date: Date | null): number {
    if (!date) return 0;
    const d = new Date(date);
    return Math.floor(d.getTime() / 1000);
  }
  
  const getCount = async () => {
    setLoading(true);
    //setError(null);

    try {
      // Query the contract
      const response = await queryClient.queryContractSmart(contractAddress, {
        get_count: { },
      });
      
      setCount(response.count);

      console.log("Get Count:", response);
    } catch (error) {
        console.error('Error querying contract:', error);
        //setError('Failed to query the contract. Please try again.');
    } finally {
      setLoading(false);
    }
  };

  const now = new Date();
  now.setSeconds(now.getSeconds() + 15);
  const oneYearFromNow = new Date();
  oneYearFromNow.setFullYear(oneYearFromNow.getFullYear() + 1);

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      <Button
        fullWidth
        onClick={() => {
          setShowModal(true);
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
        <>
          <Button
            disabled={loading}
            fullWidth
            onClick={() => {
              void getCount();
            }}
            structure="base"
          >
            {loading ? "LOADING..." : "Get Count"}
          </Button>
          {logout ? (
            <Button
              disabled={loading}
              fullWidth
              onClick={() => {
                logout();
              }}
              structure="base"
            >
              LOGOUT
            </Button>
          ) : null}
        </>
      ) : null}
      <Abstraxion
        onClose={() => {
          setShowModal(false);
        }}
      />
      {count ? (
        <div className="border-2 border-primary rounded-md p-4 flex flex-row gap-4">
              <div className="flex flex-row gap-6">
                <div>
                  Count:
                </div>
                <div>
                  {count}
                </div>
              </div>
            </div>
      ) : null}
    </main>
  );
}
```

{% hint style="info" %}
Update "**YOUR\_COUNTER\_CONTRACT\_ADDRESS\_HERE**" with the the Counter Contract Address you created above.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

## Submitting Transactions

Querying the blockchain is useful, but to fully interact with it, we need a way to modify the chain state. This section will implement transaction submission using the **Abstraxion SDK**.

Replace the contents of **`src/app/page.tsx`** with the following code:

{% code title="src/app/page.tsx" %}
```typescript
"use client";
import Link from "next/link";
import { useState, useEffect } from "react";
import {
  Abstraxion,
  useAbstraxionAccount,
  useAbstraxionSigningClient,
  useAbstraxionClient,
  useModal,
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import "@burnt-labs/ui/dist/index.css";
import type { ExecuteResult } from "@cosmjs/cosmwasm-stargate";

const contractAddress = "YOUR_COUNTER_CONTRACT_ADDRESS_HERE";

type ExecuteResultOrUndefined = ExecuteResult | undefined;

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: account } = useAbstraxionAccount();
  const { client, signArb, logout } = useAbstraxionSigningClient();
  const { client: queryClient } = useAbstraxionClient();

  // State variables
  const [count, setCount] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const [executeResult, setExecuteResult] = useState<ExecuteResultOrUndefined>(undefined);
  const [, setShowModal]: [boolean, React.Dispatch<React.SetStateAction<boolean>>] = useModal();

  const blockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/tx/${executeResult?.transactionHash}`;

  // Fetch the count from the smart contract
  const getCount = async () => {
    setLoading(true);
    try {
      const response = await queryClient.queryContractSmart(contractAddress, { get_count: {} });
      setCount(response.count);
      console.log("Get Count:", response);
    } catch (error) {
      console.error("Error querying contract:", error);
    } finally {
      setLoading(false);
    }
  };

  // Increment the count in the smart contract
  const increment = async () => {
    setLoading(true);
    const msg = { increment: {} };

    try {
      const res = await client?.execute(account.bech32Address, contractAddress, msg, "auto");
      setExecuteResult(res);
      console.log("Transaction successful:", res);
      await getCount(); // Refresh count after successful increment
    } catch (error) {
      console.error("Error executing transaction:", error);
    } finally {
      setLoading(false);
    }
  };

  // Fetch count on page load
  useEffect(() => {
    if (queryClient) {
      getCount();
    }
  }, [queryClient]);

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">ABSTRAXION</h1>

      <Button fullWidth onClick={() => setShowModal(true)} structure="base">
        {account?.bech32Address ? <div className="flex items-center justify-center">VIEW ACCOUNT</div> : "CONNECT"}
      </Button>

      {client && (
        <>
          <Button disabled={loading} fullWidth onClick={getCount} structure="base">
            {loading ? "LOADING..." : "Get Count"}
          </Button>
          <Button disabled={loading} fullWidth onClick={increment} structure="base">
            {loading ? "LOADING..." : "INCREMENT"}
          </Button>
          {logout && (
            <Button disabled={loading} fullWidth onClick={logout} structure="base">
              LOGOUT
            </Button>
          )}
        </>
      )}

      <Abstraxion onClose={() => setShowModal(false)} />

      {count !== null && (
        <div className="border-2 border-primary rounded-md p-4 flex flex-row gap-4">
          <div className="flex flex-row gap-6">
            <div>Count:</div>
            <div>{count}</div>
          </div>
        </div>
      )}

      {executeResult && (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white">
          <div className="mt-2">
            <p className="text-zinc-500"><span className="font-bold">Transaction Hash</span></p>
            <p className="text-sm">{executeResult.transactionHash}</p>
          </div>
          <div className="mt-2">
            <p className=" text-zinc-500"><span className="font-bold">Block Height:</span></p>
            <p className="text-sm">{executeResult.height}</p>
          </div>
          <div className="mt-2">
            <Link className="text-black underline visited:text-purple-600 dark:text-white" href={blockExplorerUrl} target="_blank">
              View in Block Explorer
            </Link>
          </div>
        </div>
      )}
    </main>
  );
}
```
{% endcode %}



{% hint style="info" %}
Update "**YOUR\_COUNTER\_CONTRACT\_ADDRESS\_HERE**" with the the NFT Contract Address you created above.
{% endhint %}

#### What does this do?

1. **Implements transaction submission**
   * The **"INCREMENT"** button sends a transaction using **Abstraxion Signing Client**.
2. **Manages user connection state**
   * Uses `useAbstraxionAccount()` to check if the user is connected.
   * If the user isn’t connected, the **"CONNECT"** button appears instead.
3. **Handles transaction execution**
   * Defines a `increment()` function to interact with a **CosmWasm smart contract**.
   * Sends an **ExecuteContract** transaction using `client.execute()`.
4. **Displays transaction results**
   * After submitting a transaction, it shows the **Transaction Hash** and **Block Height**.
   * Provides a link to view the transaction in **XION’s Block Explorer**.



### Quick Note on Fee Configuration

In the `increment` function, the `.execute()` method is called as follows:

{% code lineNumbers="true" %}
```typescript
const res = await client?.execute(
  account.bech32Address,
  seatContractAddress,
  msg,
  "auto",
  "", // memo
  [],
);
```
{% endcode %}

The **fourth parameter** in the function call represents the **fee configuration**. Instead of manually specifying the gas and fee amount, we use `"auto"`, allowing the **SDK to automatically handle fee estimation**:

#### Why Use `"auto"`?

* **Simplifies fee management** by automatically estimating optimal fees.
* **Reduces errors** related to underestimating or overestimating gas.

If everything is configured correctly, you should see the transaction results displayed as shown in the previous section.



<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption><p>After clicking "Increment" you should see the confirmation above</p></figcaption></figure>



These core components form the foundation for building and deploying a successful dApp! If you have any questions or need support, feel free to reach out to us on **Discord** or [**GitHub**](https://github.com/burnt-labs/xion.js).
