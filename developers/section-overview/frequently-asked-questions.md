---
description: >-
  Covers common questions about Xion, including its ecosystem, development,
  security, governance, and user interactions.
---

# Frequently Asked Questions



<details>

<summary><strong>How can my smart contract communicate with a Web2 service?</strong></summary>

Due to the deterministic nature of blockchains, a smart contract on Xion cannot interact with the web directly. The most common way to enable this is via an Oracle. The Pyth Oracle was available on Testnet-1 and is being worked on for Testnet-2, though it is currently limited to pricing data. Alternatively, you can create a custom Oracle service where a Web2 backend fetches data from an external API and submits transactions to the smart contract to store the data on-chain which can then be accessed by your contracts.

</details>

<details>

<summary><strong>Can we get the user's email from the Abstraxion SDK?</strong></summary>

No, you cannot access the user’s email from the Abstraxion SDK, as the Abstraxion library does not have access to it either. However, you can request the user’s email as part of your new user onboarding process.

</details>

<details>

<summary>Is the testnet permissioned for smart contracts?</summary>

No, the testnet is fully permissionless, allowing anyone to deploy and instantiate contracts.

</details>

<details>

<summary>Is Mainnet permissioned for smart contract deployment?</summary>

Yes, Mainnet is permissioned, requiring governance approval for contract deployment. However, once a contract is deployed, anyone can create an instance of that contract if it permits. You can find a list of such contracts along with their respective CODE IDs [here](https://github.com/burnt-labs/contracts).

</details>

<details>

<summary>What’s the preferred way to create a token on Xion?</summary>

We recommend using our **Token Factory** module for creating tokens. You can find details on why it’s the preferred method, along with instructions on creating, managing, and using tokens within your dApps, [here](https://docs.burnt.com/xion/developers/learn-and-build/token-factory).

</details>
