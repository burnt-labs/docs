---
description: End-to-end React (Vite) tutorial — Treasury, Counter contract, auto auth, Abstraxion hooks
vars:
  doc_type: tutorial
  primary_auth_mode: auto
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/, /loading-states"
  demo_app_routes_note: "/ is monorepo demo hub; /loading-states shows hook-first auth without legacy modal"
  required_env_vars: "VITE_CHAIN_ID, VITE_RPC_URL, VITE_REST_URL, VITE_GAS_PRICE, VITE_TREASURY_ADDRESS, VITE_AUTH_APP_URL"
---

# Account Abstraction with Gasless Transactions

In this guide, we will walk through building a basic app using the [Abstraxion library](https://www.npmjs.com/package/@burnt-labs/abstraxion), demonstrating how to create an Abstraxion account which can be done via a social account like Google, browser wallets (Keplr, Metamask, OKX etc.), email address, passkey and other authentication options. We will also implement a gasless transaction experience for users by leveraging XION's fee grants through a Treasury contract.

The **`AbstraxionProvider`** examples below use **`authentication.type: "auto"`** (recommended for new apps). For other modes and terminology, see the section hub [Web App Development](README.md).

To better understand Account Abstraction you can visit the [Introduction to Account Abstraction](https://docs.burnt.com/xion/developers/learn/intro-to-account-abstraction) page.

The [xion.js `demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app) is a **Next.js** reference; **this tutorial uses Vite + React** so you can follow the same patterns without a framework lock-in.

For **authentication modes** (`auto`, `signer`, `embedded`, etc.), see the section hub: [Web App Development](README.md).

## Requirements

Before getting started, ensure you have the following installed:

- **[Node.js](https://nodejs.org/)** (LTS version recommended) – Required for running the development environment and installing dependencies.

## Setting up the project

This guide targets **React with [Vite](https://vitejs.dev/)** and **TypeScript**. Abstraxion is framework-agnostic; if you prefer **Next.js**, use the same provider config and component code in a client layout/page—see [xion.js `demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app).

### Step 1: Create a Vite + React + TypeScript app

```bash
npm create vite@latest xion-abstraxion-example -- --template react-ts
cd xion-abstraxion-example
npm install
```

### Step 2: Add Tailwind CSS (optional but matches the snippets below)

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Point Tailwind at your sources (see [Tailwind v3 + Vite](https://v3.tailwindcss.com/docs/guides/vite)): set `content` in `tailwind.config.js` to `["./index.html", "./src/**/*.{js,ts,jsx,tsx}"]`, add the `@tailwind` directives to `src/index.css`, and ensure **`src/main.tsx`** imports **`./index.css`**.

### Step 3: Install Abstraxion

```bash
npm i @burnt-labs/abstraxion
```

`@burnt-labs/abstraxion` depends on **`@burnt-labs/abstraxion-core`** for signing and session primitives. You normally do not install `abstraxion-core` separately; keep both on compatible versions when upgrading.

### Step 4: Start the dev server

```bash
npm run dev
```

### Step 5: Open the app

Open the URL Vite prints (usually **[http://localhost:5173](http://localhost:5173)**). You should see the default Vite + React welcome screen until you replace **`src/App.tsx`** in the sections below.

## Deploying a Contract On-Chain

We need to deploy a smart contract on-chain for our app to interact with, so we'll deploy a basic **Counter** contract, which will allow you to:

- **Set an initial counter value**
- **Increment or reset the counter**
- **Query the current counter value**

Follow the steps in the following [guide](../../computation/local-development/deploy-a-cosmwasm-smart-contract.md) to **compile, deploy, and instantiate** the contract on-chain. Once deployed, this contract will be referenced in the **Treasury contract** and the **code updates** that follow.

## Deploying a Treasury Contract for Gasless Transactions

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** for your smart contract by handling **fee grants** on behalf of users.

### Steps to Deploy a Treasury Contract

1. Login to the [XION Developer Portal](https://dev.testnet2.burnt.com).
2. Click on **"New Treasury"** to create a new treasury contract.
3. **Select the appropriate configuration** based on your use case. The following "**Fee Grant**" and "**Grant Config**" images gives a recommended configuration that works for most scenarios:

#### Fee Grant

<figure><img src="../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption><p>Example of a general <strong>Fee Grant</strong> configuration</p></figcaption></figure>

1. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
2. In the **"Allowance Type"** field, enter `"/cosmwasm.feegrant.v1beta1.BasicAllowance"`.
3. In the **"Spend Limit"** field, enter **`1000uxion`**.
4. Click the **"Save"** button to apply the configuration.

#### **Grant Config**

<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption><p>Example of additional <strong>Grant</strong> configuration</p></figcaption></figure>

1. For the **"Type URL"** field, select `"/cosmwasm.wasm.v1.MsgExecuteContract"`.
2. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
3. In the **"Authorization Type"** field, select `"/cosmwasm.wasm.v1.ContractExecutionAuthorization"`.
4. Enter the **contract address** in the **"Contract Address"** field — this should be the **Counter** smart contract created above.
5. You **must** select at least one of the following::
  - **"Max Call"** – Limits the number of times a user can execute a transaction under this fee grant.
  - **"Max Funds"** – Specifies the maximum amount of funds allocated for covering transaction fees.
  - **"Both"** – Allows you to set both options.
6. Click the **"Add Contract Grant"** button to apply the configuration.
7. Then click the "**Save**" button which generates the "**Treasury Instance Preview**"

#### Treasury Instance Preview

<figure><img src="../../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

Once the preview is to your liking click the "**Create**" button to create the Treasury contract.

{% hint style="info" %}
Learn more about Treasury Contracts [here](../../../others/archived/create-a-gas-less-user-experience/).
{% endhint %}

## Integrating the Abstraxion library

Wrap your app with **`AbstraxionProvider`**. With Vite, the usual place is **`src/main.tsx`** (the React root).

{% code title="src/main.tsx" %}

```typescript
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App";
import { AbstraxionProvider } from "@burnt-labs/abstraxion";

const abstraxionConfig = {
  chainId: import.meta.env.VITE_CHAIN_ID!,
  treasury: import.meta.env.VITE_TREASURY_ADDRESS,
  rpcUrl: import.meta.env.VITE_RPC_URL!,
  restUrl: import.meta.env.VITE_REST_URL!,
  gasPrice: import.meta.env.VITE_GAS_PRICE!,
  authentication: {
    type: "auto" as const,
    authAppUrl: import.meta.env.VITE_AUTH_APP_URL,
  },
};

createRoot(document.getElementById("root")!).render(
  <StrictMode>
    <AbstraxionProvider config={abstraxionConfig}>
      <App />
    </AbstraxionProvider>
  </StrictMode>,
);
```

{% endcode %}

{% hint style="warning" %}
Create a **`.env`** file in the project root (Vite only exposes variables prefixed with **`VITE_`**). Set **`VITE_CHAIN_ID`**, **`VITE_RPC_URL`**, **`VITE_REST_URL`**, **`VITE_GAS_PRICE`** (e.g. `0.025uxion`), **`VITE_TREASURY_ADDRESS`**, and **`VITE_AUTH_APP_URL`** for the XION auth app. Restart the dev server after changing env files. Wrong or missing values will break login or transactions.
{% endhint %}

- **`AbstraxionProvider`** — Supplies **Abstraxion hooks** (`useAbstraxionAccount`, `useAbstraxionSigningClient`, etc.) to everything under this root.
- **`authentication.type: "auto"`** — **Recommended default**: desktop users get a **popup** auth window; mobile / PWA typically falls back to **full-page redirect**. At runtime the SDK normalizes `auto` to `popup` or `redirect` internally.

### Optional: backward-compatible default (`redirect`)

If you **omit** the entire `authentication` field, the SDK defaults to the legacy **dashboard redirect** flow (same as `type: "redirect"`). That remains supported for older apps, but **new projects should set `auto` explicitly** so behavior matches current product guidance. See [Web App Development](README.md).

### Optional: Next.js instead of Vite

Use the same **`abstraxionConfig`** shape in a **client** root layout, reading **`process.env.NEXT_PUBLIC_*`** instead of **`import.meta.env.VITE_*`**. The official [demo app](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app) is Next-based; if you hit bundler errors such as `UnhandledSchemeError` for `node:url`, apply the [Next.js config workaround](https://gist.githubusercontent.com/probablyangg/4c520e376cdddf6991951e233d1f9bb6/raw/fa0ecaf1b2e52876610be9d36a8aeaaef53f0dd5/next.config.js) from the earlier version of this guide.

## Adding hooks to the main screen

Replace the contents of **`src/App.tsx`** with the following code. It uses **`login()`** from **`useAbstraxionAccount`** and **native `<button type="button">` elements**—**not** the legacy **`<Abstraxion />`** modal (deprecated for new apps).

```typescript
import { useEffect } from "react";
import { useAbstraxionAccount } from "@burnt-labs/abstraxion";

export default function App(): JSX.Element {
  const { data: account, login, logout, isLoading } = useAbstraxionAccount();

  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    if (params.get("granted") === "true") void login();
  }, [login]);

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      {account?.bech32Address ? (
        <>
          <p className="truncate text-xs text-zinc-400">{account.bech32Address}</p>
          <button
            type="button"
            className="w-full rounded-md border border-zinc-600 bg-zinc-900 px-4 py-2 text-white"
            onClick={() => logout()}
          >
            LOGOUT
          </button>
        </>
      ) : (
        <button
          type="button"
          className="w-full rounded-md border border-zinc-600 bg-zinc-900 px-4 py-2 text-white disabled:opacity-50"
          disabled={isLoading}
          onClick={() => void login()}
        >
          {isLoading ? "CONNECTING…" : "CONNECT"}
        </button>
      )}
    </main>
  );
}
```

#### What does this do?

1. **`login()`** starts Meta Account authentication (popup or redirect, depending on your **`auto`** / **`redirect`** config).
2. **`useAbstraxionAccount`** exposes `account`, **`isLoading`**, and **`logout`**.
3. After a **full-page redirect** return, **`?granted=true`** triggers **`login()`** once to finish restoring the session.

For **granular loading flags** (`isInitializing`, `isReturningFromAuth`, …), see [Custom UI and Abstraxion loading states](custom-ui-abstraxion-authentication.md).

Now, click **CONNECT** and try it out.

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Note:** If the treasury contract address is changed within the app, users who are already logged in must log out and log back in. This ensures that their account goes through the approval process again, allowing the fee grants to function properly.
{% endhint %}

## Querying the Contract

The contract provides a method to query the current `count` value. Let's fetch this value and display it in the app.

Replace the contents of **`src/App.tsx`** with the following code:

```typescript
import { useState, useEffect } from "react";
import {
  useAbstraxionAccount,
  useAbstraxionSigningClient,
  useAbstraxionClient,
} from "@burnt-labs/abstraxion";

const contractAddress = "YOUR_COUNTER_CONTRACT_ADDRESS_HERE";

const btnClass =
  "w-full rounded-md border border-zinc-600 bg-zinc-900 px-4 py-2 text-white disabled:opacity-50";

export default function App(): JSX.Element {
  const { data: account, login, isLoading: isAuthBusy } =
    useAbstraxionAccount();
  const { client, logout } = useAbstraxionSigningClient();
  const { client: queryClient } = useAbstraxionClient();

  const [count, setCount] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    if (params.get("granted") === "true") void login();
  }, [login]);

  const getCount = async () => {
    setLoading(true);
    try {
      const response = await queryClient.queryContractSmart(contractAddress, {
        get_count: {},
      });
      setCount(response.count);
      console.log("Get Count:", response);
    } catch (error) {
      console.error("Error querying contract:", error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      {!account?.bech32Address ? (
        <button
          type="button"
          className={btnClass}
          disabled={isAuthBusy}
          onClick={() => void login()}
        >
          {isAuthBusy ? "CONNECTING…" : "CONNECT"}
        </button>
      ) : null}
      {client ? (
        <>
          <button
            type="button"
            className={btnClass}
            disabled={loading}
            onClick={() => void getCount()}
          >
            {loading ? "LOADING..." : "Get Count"}
          </button>
          {logout ? (
            <button
              type="button"
              className={btnClass}
              disabled={loading}
              onClick={() => logout()}
            >
              LOGOUT
            </button>
          ) : null}
        </>
      ) : null}
      {count ? (
        <div className="flex flex-row gap-4 rounded-md border-2 border-blue-500 p-4">
          <div className="flex flex-row gap-6">
            <div>Count:</div>
            <div>{count}</div>
          </div>
        </div>
      ) : null}
    </main>
  );
}
```

{% hint style="info" %}
Update **`YOUR_COUNTER_CONTRACT_ADDRESS_HERE`** with the Counter contract address you created above.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

## Submitting Transactions

Querying the blockchain is useful, but to fully interact with it, we need a way to modify the chain state. This section will implement transaction submission using the **Abstraxion SDK**.

Replace the contents of **`src/App.tsx`** with the following code.

{% code title="src/App.tsx" %}

```typescript
import { useState, useEffect } from "react";
import {
  useAbstraxionAccount,
  useAbstraxionSigningClient,
  useAbstraxionClient,
} from "@burnt-labs/abstraxion";
import type { ExecuteResult } from "@cosmjs/cosmwasm-stargate";

const contractAddress = "YOUR_COUNTER_CONTRACT_ADDRESS_HERE";

type ExecuteResultOrUndefined = ExecuteResult | undefined;

const btnClass =
  "w-full rounded-md border border-zinc-600 bg-zinc-900 px-4 py-2 text-white disabled:opacity-50";

export default function App(): JSX.Element {
  const { data: account, login, isLoading: isAuthBusy } =
    useAbstraxionAccount();
  const { client, logout } = useAbstraxionSigningClient();
  const { client: queryClient } = useAbstraxionClient();

  const [count, setCount] = useState<string | null>(null);
  const [loading, setLoading] = useState(false);
  const [executeResult, setExecuteResult] =
    useState<ExecuteResultOrUndefined>(undefined);

  useEffect(() => {
    const params = new URLSearchParams(window.location.search);
    if (params.get("granted") === "true") void login();
  }, [login]);

  const getCount = async () => {
    setLoading(true);
    try {
      const response = await queryClient.queryContractSmart(contractAddress, {
        get_count: {},
      });
      setCount(response.count);
      console.log("Get Count:", response);
    } catch (error) {
      console.error("Error querying contract:", error);
    } finally {
      setLoading(false);
    }
  };

  const increment = async () => {
    if (!client || !account?.bech32Address) return;
    setLoading(true);
    const msg = { increment: {} };
    try {
      const res = await client.execute(
        account.bech32Address,
        contractAddress,
        msg,
        "auto",
      );
      setExecuteResult(res);
      console.log("Transaction successful:", res);
      await getCount();
    } catch (error) {
      console.error("Error executing transaction:", error);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    if (queryClient) void getCount();
  }, [queryClient]);

  const blockExplorerUrl = `https://www.mintscan.io/xion-testnet/tx/${executeResult?.transactionHash}`;

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      {!account?.bech32Address ? (
        <button
          type="button"
          className={btnClass}
          disabled={isAuthBusy}
          onClick={() => void login()}
        >
          {isAuthBusy ? "CONNECTING…" : "CONNECT"}
        </button>
      ) : null}
      {client ? (
        <>
          <button
            type="button"
            className={btnClass}
            disabled={loading}
            onClick={() => void getCount()}
          >
            {loading ? "LOADING..." : "Get Count"}
          </button>
          <button
            type="button"
            className={btnClass}
            disabled={loading}
            onClick={() => void increment()}
          >
            {loading ? "LOADING..." : "INCREMENT"}
          </button>
          {logout ? (
            <button
              type="button"
              className={btnClass}
              disabled={loading}
              onClick={() => logout()}
            >
              LOGOUT
            </button>
          ) : null}
        </>
      ) : null}
      {count !== null ? (
        <div className="flex flex-row gap-4 rounded-md border-2 border-blue-500 p-4">
          <div className="flex flex-row gap-6">
            <div>Count:</div>
            <div>{count}</div>
          </div>
        </div>
      ) : null}
      {executeResult ? (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white">
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Transaction Hash</span>
            </p>
            <p className="text-sm">{executeResult.transactionHash}</p>
          </div>
          <div className="mt-2">
            <p className="text-zinc-500">
              <span className="font-bold">Block Height:</span>
            </p>
            <p className="text-sm">{executeResult.height}</p>
          </div>
          <div className="mt-2">
            <a
              className="text-black underline visited:text-purple-600 dark:text-white"
              href={blockExplorerUrl}
              target="_blank"
              rel="noopener noreferrer"
            >
              View in Block Explorer
            </a>
          </div>
        </div>
      ) : null}
    </main>
  );
}
```

{% endcode %}

{% hint style="info" %}
Update **`YOUR_COUNTER_CONTRACT_ADDRESS_HERE`** with the Counter contract address you created above.
{% endhint %}

#### What does this do?

1. **Implements transaction submission** — **INCREMENT** sends an **ExecuteContract** message through **`useAbstraxionSigningClient()`** (default gasless / session path).
2. **Manages connection with hooks** — **`login()`** / **`?granted=true`** (same as earlier steps); no **`<Abstraxion />`** modal or **`@burnt-labs/ui`**.
3. **Queries on load** — When **`queryClient`** is ready, **`getCount()`** runs once so the counter is visible after refresh.
4. **Shows results** — After execute, displays **transaction hash**, **block height**, and a **Mintscan** link.

### Quick Note on Fee Configuration

In the `increment` function, the `.execute()` method is called as follows:

{% code lineNumbers="true" %}

```typescript
const res = await client?.execute(
  account.bech32Address,
  contractAddress,
  msg,
  "auto",
  "", // memo
  [],
);
```

{% endcode %}

The **fourth parameter** in the function call represents the **fee configuration**. Instead of manually specifying the gas and fee amount, we use `"auto"`, allowing the **SDK to automatically handle fee estimation**:

#### Why Use `"auto"`?

- **Simplifies fee management** by automatically estimating optimal fees.
- **Reduces errors** related to underestimating or overestimating gas.

By default, the `gas-adjustment` applied to the `auto` value is typically **1.3**. If transactions are still failing due to low fees, it may indicate that the gas adjustment, acting as a multiplier for the automatically calculated fee, is too low. In such cases, you may need to increase this value for specific transactions.

To update the gas adjustment in the **execute** call, replace `auto` with your desired adjustment value. The transaction will still use automatic gas estimation but will apply the specified gas adjustment instead of the default.

**Session key vs direct signing:** This tutorial uses the default **`useAbstraxionSigningClient()`** path (gasless / session-style signing with Treasury). For **user-pays-gas** flows, `useAbstraxionSigningClient({ requireAuth: true })`, and **explicit fee simulation** (`simulate`, `calculateFee`) instead of `"auto"`, see the demo route **`/direct-signing-demo`** in [xion.js `apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo).

If everything is configured correctly, you should see the transaction results displayed as shown in the previous section.

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption><p>After clicking "Increment" you should see the confirmation above</p></figcaption></figure>

These core components form the foundation for building and deploying a successful app! If you have any questions or need support, feel free to reach out to us on **Discord** or **[GitHub](https://github.com/burnt-labs/xion.js)**.