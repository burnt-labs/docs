---
icon: mobile
---

# Mobile dapp Development on XION

Mobile dapp development on XION refers to the process of creating decentralized applications specifically optimized for mobile platforms (iOS and Android), using familiar tools like **React Native** and **Expo**, while leveraging XION’s native features such as:

* **Meta Accounts**: Smart contract accounts abstracted behind familiar authentication methods like email or social login.
* **Treasury Contracts**: A powerful mechanism that lets your dapp sponsor transaction fees, enabling gasless usage for your users and enable requesting permissions from users to execute transactions on their behalf.
* **Abstraxion SDKs**: Tools that make building with XION as simple as possible, including packages tailored to mobile platforms.

The goal is to bring the user experience of traditional apps to the decentralized world, without requiring end-users to understand the complexities of the blockchain.



## Abstraxion React Native Package

The [`@burnt-labs/abstraxion-react-native`](https://www.npmjs.com/package/@burnt-labs/abstraxion-react-native) package is the core library for building mobile dapps on XION. It brings the full power of the Abstraxion Meta Account system and simplifies authentication, signing, and fee grants in a native mobile environment.

#### Key Benefits:

* **Optimized for React Native & Expo** – Seamlessly integrates with mobile projects using JavaScript/TypeScript.
* **Handles Secure Auth Flows** – Support for email, social logins, wallet and passkey authentication.
* **Simplifies Blockchain Interactions** – Manages signing and gasless transaction flows using Meta Accounts and Treasury contracts.
* **Session Persistence** – Built-in support for persistent logins using `AsyncStorage`.
* **Abstraxion Hooks** – React Native optimized hooks with the exact same interfaces as `@burnt-labs/abstraxion`.

With this package, developers can quickly create friendly onboarding experiences and interact with smart contracts as easily as making a POST request in a traditional app.
