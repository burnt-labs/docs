# Quick Start: Launch a User Map dapp on XION in 5 Minutes

Building a dapp on XION typically involves three main components:

* **A smart contract** – Contains the core business logic of your dapp.
* **A Treasury contract** – Enables gasless transactions using **Fee Grants**, making onboarding smoother for users. It also handles **Authorization Grants**, which allow your frontend to execute transactions on behalf of the user, enabling a more Web2-like experience through our OAuth2-style **Abstraxion** solution.
* **A frontend** – This is your dapp’s interface, used to display data and trigger transactions via the smart contract.

As a beginner to XION development, getting a fully functional dapp up and running should be simple and shouldn’t require any complex setup. With this in mind, we’ve created a [**one-click frontend solution**](https://usermap-launcher.burnt.com) for the **User Map** dapp that handles the creation of both the **User Map** and **Treasury** contracts, along with a fully built [**frontend**](https://github.com/burnt-labs/xion-user-map-json-store-frontend) for interacting with the User Map contract.

{% hint style="info" %}
You should have your demo dapp up and running in under **5 minutes**. Once you're familiar with the workflow, you can move on to the advanced [**User Map guide**](building-a-per-user-data-storage-dapp.md).
{% endhint %}



## What is the User Map dapp?

The **User Map** contract is a lightweight and beginner friendly smart contract designed to help developers, especially those coming from traditional web or application development, understand how to manage per-user data in a decentralized environment.

Think of it like a Web2 key-value store:

* **Key:** User's wallet address
* **Value:** A JSON object (e.g., user profile, preferences, settings)

You can find the source code here: [github.com/burnt-labs/contracts/tree/main/contracts/user\_map](https://github.com/burnt-labs/contracts/tree/main/contracts/user_map).



## Prerequisites

Before you begin, make sure you have the following installed:

* [Node.js](https://nodejs.org) (v18+)
* [Git](https://git-scm.com)



## Create Instance of User Map and Treasury Contracts

**Compiling** and **instantiating** smart contracts can feel like huge tasks, especially for new developers. You typically need to set up your development environment with tools like **Docker** and **xiond**, and go through the full process of compiling, deploying, and instantiating your contracts manually. On top of that, creating and configuring a Treasury contract via the [**Dev Portal**](https://dev.testnet.burnt.com) can be overwhelming if you're just getting started.

To make this much easier, we’ve created a [**one-click frontend solution**](https://usermap-launcher.burnt.com) for this **User Map** dapp that takes care of all the heavy lifting, so you can skip the setup pain and focus on experimenting with a real, working dapp.



1. Go to [https://usermap-launcher.burnt.com](https://usermap-launcher.burnt.com) and log in using your **Meta Account**

<figure><img src="../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

2. Click the **Launch User Map & Fund Treasury** button

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

Behind the scenes, this:

* Instantiates your **User Map** contract
* Deploys a **Treasury contract** with a configured **Fee Grant** to enable gasless transactions and also **Authorization Grants** for users to give the frontend approval to interact with the **User Map** contract on their behalf
* Funds the **Treasury contract** to pay transaction fees for users via the **Fee Grant**

These 4 environment variables will be generated which will you will need to add to the frontend environment:

```env
NEXT_PUBLIC_CONTRACT_ADDRESS=...
NEXT_PUBLIC_TREASURY_ADDRESS=...
NEXT_PUBLIC_RPC_URL=...
NEXT_PUBLIC_REST_URL=...
```

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>



## Set Up the Frontend

The frontend allows users to log into your dapp using their Meta Account via multiple authenticators (Email, Social Login, Wallets, Passkeys). Once logged in, users can query contract data and execute transactions to update their User Map record, which is linked to their Meta Account address.

To run the frontend locally, execute the following commands in your terminal:

```bash
git clone https://github.com/burnt-labs/xion-user-map-json-store-frontend
cd xion-user-map-json-store-frontend
cp .env.example .env
```

Open `.env` and paste the variables from the launcher:

```env
NEXT_PUBLIC_CONTRACT_ADDRESS="..."
NEXT_PUBLIC_TREASURY_ADDRESS="..."
NEXT_PUBLIC_RPC_URL="https://rpc.xion-testnet-2.burnt.com:443"
NEXT_PUBLIC_REST_URL="https://api.xion-testnet-2.burnt.com"
```

Install dependencies and start the local dev server:

```bash
npm install
npm run dev
```

Visit `http://localhost:3000` in your browser to open the app. You should see the following:

<figure><img src="../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

Once logged in, you will be presented with an interface where you can enter a JSON object. When you click "Submit JSON," a transaction will be executed to update the User Map with the provided JSON data. You can also select from the other menu options that allow you to retrieve data from the contract.

<figure><img src="../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

***

For a detailed breakdown of the contract’s design, execution messages, and frontend setup see [Full Guide – Building a Per-User Data Storage dApp](https://docs.burnt.com/xion/developers/learn-and-build/use-cases/building-a-per-user-data-storage-dapp).
