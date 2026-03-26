# Misconceptions and Misapplied Web2 Patterns

As **Web2** developers begin building in **Web3**, it's natural to lean on familiar tools, frameworks, and mental models. This however often lead to **misapplied patterns** that don’t align with the decentralized nature of blockchain development, especially when working with XION.

This guide outlines common misconceptions and design patterns brought over from Web2 that **don’t translate well to Web3**, and explains how to rethink your approach for building secure, performant, and user-friendly apps on XION.



### Misconception #1: "I need a backend server to run my app"

In Web2, your backend is the heart of your application, handling routing, business logic, database queries, and more. But in Web3, **your smart contracts replace the backend**. There is no centralized server between your frontend and the blockchain.

#### The Web3 mindset:

* **Business logic** lives in CosmWasm smart contracts.
* **State** is managed and stored on-chain.
* The **frontend communicates directly with the blockchain**, not a backend.



### Misconception #2: "I’ll store user data in a database like MySQL or Firebase"

Databases are standard in Web2 for storing everything from user profiles to app data. In Web3, data stored off-chain is not **trustless**, **transparent**, or **verifiable**.

#### The Web3 mindset:

* Use smart contract **on-chain storage** for important, verifiable state.
* If off-chain data is needed (e.g. large files), consider decentralized solutions like **IPFS**, **Arweave**, or **Jackal**.
* Indexing services like **SubQuery** or **Numia** can help you efficiently read blockchain data.

{% hint style="info" %}
Only use off-chain databases when the data is not critical to contract logic or on-chain interactions or you need do do complex data agregation.
{% endhint %}



### Misconception #3: "Users should register with email and password"

Web3 applications rely on **wallets**, not usernames and passwords. Wallets represent a user's identity, and every action is verified using cryptographic signatures and therefore there's no need to manage credentials.

#### The Web3 mindset:

* **Wallets = Identity**. A user’s address is their identity.
* Authentication is done through **message signing**.
* **XION simplifies this even further**: With the `abstraxion` library, developers can create a smooth, OAuth2-style login and registration experience directly in the frontend. Behind the scenes, every user that authenticates via Abstraxion is issued a Meta Account, a smart contract account on XION that abstracts away private key management that is linked to their authenticator of choice which includes:
  * **Email**
  * **Social login** (e.g., Google)
  * **External wallets** (e.g., Keplr, MetaMask)
  * **Passkey support** for biometric authentication (e.g., Face ID, fingerprint)



### Misconception #4: "I’ll create an API layer between the frontend and the blockchain"

This introduces unnecessary centralization. If your frontend talks to a custom backend that relays messages to the blockchain, you reintroduce a point of failure.

#### The Web3 mindset:

* Interact with smart contracts directly from the frontend using `xion.js` or `cosmjs` communicating with the blockchain through RPC, gRPC, REST APIs, or WebSockets via connected network nodes.
* If needed, rely on indexers, to expose aggregated and filtered data which is usually just used for displaying data on the frontend.



### Misconception #5: "I can update my backend logic anytime"

Smart contracts are **immutable by default** once deployed. You can't just push a quick update like you would on a backend server.

#### The Web3 mindset:

* Treat every deployment as a **versioned release**.
* Design your contracts to support **migrations** which also requires setting an **admin** for each instantiated contract.
* Implement upgradability only when necessary using patterns like contract proxies or admin-controlled updates (with care).

