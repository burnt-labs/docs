# Building a React dApp to Interact with Token Factory Tokens

This guide walks through creating a React-based decentralized application (dApp) that interacts with a token created using the XION Token Factory. This dApp will:

* Authenticate users with a **Meta Account** that would have received a token created via the Token Factory. See the following [guide](creating-minting-and-interacting-with-a-token-factory-token.md) on how to create your token.
* Display the **token balance** for the logged-in user
* Enable the logged in user to **send tokens to another address**



## **Setting Up the Project**

### **Initialize a New Next.js Project**

Run the following command to create a new Next.js project configured with TypeScript, ESLint, and Tailwind CSS:

```sh
npx create-next-app@14.0.0 xion-token-dapp \
  --use-npm --ts --eslint --tailwind --app --src-dir --import-alias "@/*"
```

### **Navigate to the Project Directory**

```sh
cd xion-token-dapp
```

### **Install Dependencies**

Install the Abstraxion SDK:

```sh
npm install @burnt-labs/abstraxion
```

### **Start the Development Server**

```sh
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to view the default Next.js page.



## Deploying a Treasury Contract for Gasless Transactions

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** by executing **fee grants** on behalf of users.

### Steps to Deploy a Treasury Contract

1. Login to the [XION Developer Portal](https://dev.testnet.burnt.com/).
2. Click on **"New Treasury"** to create a new treasury contract.
3. **Select the appropriate configuration** based on your use case. The following "**Fee Grant**" and "**Grant Config**" images gives a recommended configuration that works for this particular use case:

#### Fee Grant

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Example of a general <strong>Fee Grant</strong> configuration</p></figcaption></figure>

1. Enter a **"Description"** in the description field. This will reflect the intended purpose of the request.
2. In the **"Allowance Type"** field, enter `"/cosmwasm.feegrant.v1beta1.BasicAllowance"`.
3. You can apply a **"Spend Limit"** if you wish, which will be applied to each user .
4. Click the **"Save"** button to apply the configuration.

#### **Grant Config**

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption><p>Example of additional <strong>Grant</strong> configuration</p></figcaption></figure>

1. For this example the **"Type URL"**  would be `"`/cosmos.bank.v1beta1.MsgSend`"` as this would allow for the dapp the send tokens on behalf of the user.
2. Enter a **"Description"** in the description field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
3. In the **"Authorization Type"** field, select `"`/cosmos.authz.v1beta1.GenericAuthorization`"`.
4. Then click the "**Save**" button which generates the "**Treasury Instance Preview**"

#### Treasury Instance Preview

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Once the preview is to your liking click the "**Create**" button to create the Treasury contract.

{% hint style="info" %}
Learn more about Treasury Contracts [here](../../featured-guides/your-first-dapp/create-a-gas-less-user-experience/).
{% endhint %}



## **Configuring Abstraxion for Authentication**

To enable account abstraction and gasless transactions, integrate the **Abstraxion provider** into your application.

### **Configure the Abstraxion Provider**

Replace the contents of `src/app/layout.tsx` with the following:

```tsx
"use client";
import { Inter } from 'next/font/google';
import './globals.css';
import { AbstraxionProvider } from "@burnt-labs/abstraxion";
import "@burnt-labs/abstraxion/dist/index.css";
import "@burnt-labs/ui/dist/index.css";

const inter = Inter({ subsets: ['latin'] });

const treasuryConfig = {
  treasury: "YOUR_TREASURY_CONTRACT_ADDRESS_HERE",
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <AbstraxionProvider config={treasuryConfig}>
          {children}
        </AbstraxionProvider>
      </body>
    </html>
  );
}
```

Replace `YOUR_TREASURY_CONTRACT_ADDRESS_HERE` with the actual treasury contract address.



### **Bringing it Together**

#### **Modify `src/app/page.tsx` to Use Components**

```tsx
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

const TOKEN_DENOM = "factory/xion1xyz.../mytoken";

type ExecuteResultOrUndefined = ExecuteResult | undefined;

export default function Page(): JSX.Element {
  // Abstraxion hooks
  const { data: account } = useAbstraxionAccount();
  const { client, signArb, logout } = useAbstraxionSigningClient();
  const { client: queryClient } = useAbstraxionClient();

  // State variables
  const [loading, setLoading] = useState(false);
  const [, setShowModal]: [boolean, React.Dispatch<React.SetStateAction<boolean>>] = useModal();
  const [balance, setBalance] = useState("0");
  const [recipient, setRecipient] = useState("");
  const [amount, setAmount] = useState("");
  const [exResultTransactionHash, setExResultTransactionHash] = useState(null);
  const [exResultHeight, setExResultHeight] = useState("");

  const blockExplorerUrl = `https://explorer.burnt.com/xion-testnet-1/tx/${exResultTransactionHash}`;

  // Fetch the token balance
  const getTokenBalance = async () => {
    if (account?.bech32Address) {
      setLoading(true);
      try {
        const response = await queryClient.getBalance(account?.bech32Address, TOKEN_DENOM);
        console.log("Response: ", response);
        setBalance(response ? response.amount : "0");
      } catch (error) {
        console.error("Error querying contract:", error);
      } finally {
        setLoading(false);
      }
    }
  };

  // Transfer token to receipient address
  const handleSend = async () => {
    setLoading(true);

    try {
      const result = await client?.sendTokens(
        account.bech32Address,
        recipient,
        [{ denom: TOKEN_DENOM, amount }],
        1.5
      );

      if(result.code == 0) {
        setExResultTransactionHash(result.transactionHash);
        setExResultHeight(result.height);
      }

      await getTokenBalance(); // Refresh token balance after successful transfer

      //setExResult(result);
      console.log("Transaction successful:", result);
      alert("Transaction successful!");
      setRecipient("");
      setAmount("");
    } catch (error) {
      console.error("Error sending tokens:", error);
      alert("Transaction failed!");
    } finally {
      setLoading(false);
    }
  };

  // Fetch token balance on page load
  useEffect(() => {
    if (queryClient) {
      getTokenBalance();
    }
  }, [queryClient]);

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white text-center">TokenFactory Token Transfer</h1>

      <div className="flex flex-col p-2 mb-7 text-center">
        <div className="mt-2">
          <p className="text-zinc-500"><span className="font-bold">My Address</span></p>
          <p className="text-sm">{account?.bech32Address}</p>
        </div>
      </div>

      <Button fullWidth onClick={() => setShowModal(true)} structure="base">
        {account?.bech32Address ? <div className="flex items-center justify-center">VIEW ACCOUNT</div> : "CONNECT"}
      </Button>

      {client && (
        <>
          <Button disabled={loading} fullWidth onClick={getTokenBalance} structure="base">
            {loading ? "LOADING..." : "Get Token Balance"}
          </Button>
        </>
      )}

      <Abstraxion onClose={() => setShowModal(false)} />

      {client && (
        <>
          <div className="w-full max-w-md p-4 bg-gray-800 rounded-lg shadow-md">
            <h2 className="text-xl font-semibold text-white mb-4">Token Balance</h2>
            <p className="text-white mb-14">{balance} MYTOKEN</p>

            <h2 className="text-xl font-semibold text-white mb-4">Send Tokens</h2>
            <div className="mb-4">
              <label htmlFor="recipient" className="block text-sm font-medium text-gray-300 mb-2">
                Recipient Address
              </label>
              <input
                type="text"
                id="recipient"
                className="w-full p-2.5 bg-gray-700 border border-gray-600 text-white rounded-lg focus:ring-blue-500 focus:border-blue-500"
                placeholder="Enter recipient address"
                value={recipient}
                onChange={(e) => setRecipient(e.target.value)}
              />
            </div>
            <div className="mb-4">
              <label htmlFor="amount" className="block text-sm font-medium text-gray-300 mb-2">
                Amount
              </label>
              <input
                type="number"
                id="amount"
                className="w-full p-2.5 bg-gray-700 border border-gray-600 text-white rounded-lg focus:ring-blue-500 focus:border-blue-500"
                placeholder="Enter amount to send"
                value={amount}
                onChange={(e) => setAmount(e.target.value)}
              />
            </div>
            <Button
              fullWidth
              onClick={handleSend}
              structure="base"
              disabled={loading}
              className="mt-4"
            >
              {loading ? "Processing..." : "Send Tokens"}
            </Button>
          </div>
        </>
      )}

      {client && (
        <>
          {logout && (
            <Button disabled={loading} fullWidth onClick={logout} structure="base">
              LOGOUT
            </Button>
          )}
        </>
      )}

      {exResultTransactionHash !== null && (
        <div className="flex flex-col rounded border-2 border-black p-2 dark:border-white mt-7">
          <div className="mt-2">
            <p className="text-zinc-500"><span className="font-bold">Transaction Hash</span></p>
            <p className="text-sm">{exResultTransactionHash}</p>
          </div>
          <div className="mt-2">
            <p className=" text-zinc-500"><span className="font-bold">Block Height:</span></p>
            <p className="text-sm">{exResultHeight}</p>
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

Replace TOKEN\_DENOM with the actual token denom.

You now have a functional React dApp that:&#x20;

* **Authenticates users via Meta Accounts**&#x20;
* **Fetches and displays Token Factory token balance**
* **Enables users to send tokens**

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
