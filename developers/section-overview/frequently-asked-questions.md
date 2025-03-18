---
description: >-
  Covers common questions about Xion, including its ecosystem, development,
  security, governance, and user interactions.
---

# Frequently Asked Questions



<details>

<summary>Is Testnet permissioned for smart contract deployment?</summary>

No, our Testnet is fully permissionless, allowing anyone to deploy and instantiate contracts.

</details>

<details>

<summary>Is Mainnet permissioned for smart contract deployment?</summary>

Yes, Mainnet is permissioned, requiring governance approval for contract deployment. However, once a contract is deployed, anyone can create an instance of that contract if it permits. You can find a list of such contracts along with their respective CODE IDs [here](https://github.com/burnt-labs/contracts).

</details>

<details>

<summary>What’s the preferred way to create a token on Xion?</summary>

We recommend using our **Token Factory** module for creating tokens. You can find details on why it’s the preferred method, along with instructions on creating, managing, and using tokens within your dApps, [here](https://docs.burnt.com/xion/developers/learn-and-build/token-factory).

</details>

<details>

<summary>Can we get the user's email from the Abstraxion Library?</summary>

No, you cannot access the user’s email from the Abstraxion library, as the library does not have access to it either. You can however request the user’s email as part of your new user onboarding process.

</details>

<details>

<summary>How can my smart contract communicate with a Web2 service?</summary>

Due to the deterministic nature of blockchains, a smart contract on Xion cannot interact with the web directly. The most common way to enable this is via an Oracle. The Pyth Oracle should be available on Testnet. Alternatively, you can create a custom Oracle service where a Web2 backend fetches data from an external API and submits transactions to a smart contract to store the data on-chain which can then be accessed by your contracts.

</details>
