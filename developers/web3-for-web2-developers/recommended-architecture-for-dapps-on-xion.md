# Recommended Architecture for Dapps on XION

Building dapps on XION opens the door to a decentralized and user-friendly development model, but to fully take advantage of it, you need to rethink how your app is structured. This guide outlines a **recommended architecture** for developing scalable, secure, and gasless dapps on XION using smart contracts, modern frontend tools, and built-in wallet functionality.



## Architectural Overview

<figure><img src="../../.gitbook/assets/Recommended-Architecture-for-Dapps-on-XION-flow-chart (3).png" alt=""><figcaption></figcaption></figure>

At a high level, a typical XION dapp consists of the following components:

* **Frontend**: A browser-based or mobile user interface that connects directly to the blockchain.
* **Wallet Layer**: Wallets are generated and managed on the frontend using the `abstraxion` library.
* **Smart Contracts**: Core business logic written in Rust using the CosmWasm framework and deployed to the XION blockchain.
* **Optional Indexer**: An off-chain service (e.g. SubQuery) that provides advanced filtering, aggregation, and data access from the blockchain for frontend consumption.
* **Optional Storage Layer**: For storing large or private files, decentralized storage solutions like IPFS or Jackal can be integrated.



## Key Components Explained

### **Frontend (React/Vue + xion.js)**

* Built using modern JavaScript frameworks like **React** or **Vue**, the frontend serves as the primary interface for users to interact with your dapp.
* Wallet creation, login, and transaction signing are handled via **`abstraxion`**, a sub-package of `xion.js`, which eliminates the need for external wallets or browser extensions.
* User onboarding is seamless, you can programmatically set up a **fee grant** via XION **Treasury Contracts**, allowing your dapp to cover the gas costs for users.
* XION’s treasury contracts go even further by enabling a **Web2-style authorization model**. Users can approve a Grantz (similar to OAuth2 scopes), authorizing your frontend to send signed transactions on their behalf, no wallet popups required.
* The frontend communicates **directly with deployed smart contracts and the XION blockchain** via RPC, gRPC, REST APIs, or WebSockets using `xion.js`.
* Since your frontend is entirely static, it can be deployed to platforms like **Vercel**, **Netlify**, or any CDN. The full dapp loads in the user's browser, there's no need for server-side rendering or backend infrastructure.



### **Smart Contracts (CosmWasm in Rust)**

* All core business logic is implemented as **CosmWasm smart contracts**, written in **Rust** and deployed to the XION blockchain.
* Contracts store critical application state using a key-value data model, secured and managed on-chain.
* Common use cases include:
  * Managing token balances
  * Storing profile metadata
  * Handling voting, staking, or other custom logic

{% hint style="info" %}
Smart contracts are deterministic, upgradeable (via migrations), and auditable, forming the trusted core of your dapp.
{% endhint %}

### **Optional Indexer (SubQuery, Numia indexer)**

* For apps that require **complex queries, analytics, filtering, or historical lookups**, an indexer is essential.
* Tools like **SubQuery** or **Numia** parse blockchain data and expose it via **GraphQL** or **REST APIs**, making it easy to power advanced frontend views without querying the chain directly.
* Improves performance, scalability, and responsiveness of your UI.



### **Optional Storage Layer (IPFS / Jackal)**

* On-chain storage is expensive and limited, use decentralized storage networks like **IPFS** or **Jackal** to store:
  * Large files (images, PDFs)
  * Encrypted or private user content
  * NFT metadata and media
* Smart contracts can reference these assets via **content hashes**, ensuring verifiability while offloading heavy data.

