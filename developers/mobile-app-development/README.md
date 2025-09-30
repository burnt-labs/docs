---
description: >-
  Build native iOS and Android apps with blockchain functionalities using Dave,
  powered by XION.
icon: mobile
---

# Mobile App Development

{% hint style="success" %}
Get a mobile app up and running fast:\
[**Build a React Native Mobile app on XION in 5 Minutes**](https://docs.burnt.com/xion/developers/xion-quick-start/zero-to-dapp-in-5-minutes/react-native-mobile-dapp-on-xion-in-5-minutes)

This guide is designed to give developers instant look at a working app on XION, without needing to write or compile code, set up a local dev environment, or build out a frontend.
{% endhint %}

XION has developed Dave, the Premier Mobile Development Kit, enabling builders to meet users where they are.&#x20;

Mobile app development on XION refers to the process of creating blockchain-based applications specifically optimized for mobile platforms (iOS and Android), using familiar tools like **React Native** and **Expo**, while leveraging XION’s native features such as:

* **Meta Accounts**: Smart contract accounts abstracted behind familiar authentication methods like email or social login.
* **Treasury Contracts**: A powerful mechanism that lets your app sponsor transaction fees, enabling gasless usage for your users and enable requesting permissions from users to execute transactions on their behalf.
* **Abstraxion SDKs**: Tools that make building with XION as simple as possible, including packages tailored to mobile platforms.

The goal is to meet billions of users where they are, without requiring end-users to understand the complexities of the blockchain.



## Abstraxion React Native Package

The [`@burnt-labs/abstraxion-react-native`](https://www.npmjs.com/package/@burnt-labs/abstraxion-react-native) package is the core library for building mobile apps on XION. It brings the full power of the Abstraxion Meta Account system and simplifies authentication, signing, and fee grants in a native mobile environment.

#### Key Benefits:

* **Optimized for React Native & Expo** – Seamlessly integrates with mobile projects using JavaScript/TypeScript.
* **Handles Secure Auth Flows** – Support for email, social logins, wallet and passkey authentication.
* **Simplifies Blockchain Interactions** – Manages signing and gasless transaction flows using Meta Accounts and Treasury contracts.
* **Session Persistence** – Built-in support for persistent logins using `AsyncStorage`.
* **Abstraxion Hooks** – React Native optimized hooks with the exact same interfaces as `@burnt-labs/abstraxion`.

With this package, developers can quickly create friendly onboarding experiences and interact with smart contracts as easily as making a POST request in a traditional app.
