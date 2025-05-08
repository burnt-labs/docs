# React Native Mobile Dapp on XION in 5 Minutes

Building a **Mobile Dapp** on XION typically involves three main components:

* **Smart contract** – Contains the core business logic of your Dapp.
* **Treasury contract** – Enables gasless transactions using **Fee Grants**, making onboarding smoother for users. It also handles **Authorization Grants**, which allow your frontend to execute transactions on behalf of the user, enabling a more Web2-like experience through our OAuth2-style **Abstraxion** solution.
* **Mobile Frontend** – This is your Dapp’s interface, used to display data and trigger transactions via the smart contract.

As a beginner to XION development, getting a fully functional Dapp up and running should be simple and shouldn’t require any complex setup. With this in mind, we’ve created a [**one-click frontend solution**](https://usermap-launcher.burnt.com)  that handles the creation of both the **User Map** and **Treasury** contracts with the Treasury contract being fully configured. We've also created a fully functional [**React Native Mobile Dapp**](https://github.com/burnt-labs/abstraxion-expo-demo/tree/integrate-user-map) for interacting with the **User Map** contract.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FB5Z5ijJgMx0GJO3l1Il9%2Fuploads%2FAqm7ts8pt04Ep39VbEqT%2F03%20-%20User%20Map%20Mobile%20Dapp.mp4?alt=media&token=91bd828c-3d31-4960-8516-c34d22322d6b" %}

## What is the User Map Dapp?

The **User Map** contract is a lightweight and beginner friendly smart contract designed to help developers, especially those coming from traditional web or application development, understand how to manage per-user data in a decentralized environment.

Think of it like a basic key-value store:

* **Key:** User's wallet address
* **Value:** A JSON object (e.g., user profile, preferences, settings)

You can find the source code here: [github.com/burnt-labs/contracts/tree/main/contracts/user\_map](https://github.com/burnt-labs/contracts/tree/main/contracts/user_map).



## Prerequisites

Before you begin, ensure you have the following installed:

* [**Node.js** (LTS version recommended) and **NPM**](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* **Android Emulator,** **iOS Simulator** or a physical device for testing



## Create Instance of User Map and Treasury Contracts

**Compiling** and **instantiating** smart contracts can feel like huge tasks, especially for new developers. You typically need to set up your development environment with tools like **Docker** and **xiond**, and go through the full process of compiling, deploying, and instantiating your contracts manually. On top of that, creating and configuring a Treasury contract via the [**Dev Portal**](https://dev.testnet.burnt.com) can be overwhelming if you're just getting started.

To make this much easier, we’ve created a [**one-click frontend solution**](https://usermap-launcher.burnt.com) for this **User Map** Dapp that takes care of all the heavy lifting, so you can skip the setup pain and focus on experimenting with a real, working Mobile dapp.



1. Go to [https://usermap-launcher.burnt.com](https://usermap-launcher.burnt.com) and log in using your **Meta Account**

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

2. Click the **Launch User Map & Fund Treasury** button

<figure><img src="../../../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

Behind the scenes, this:

* Instantiates your **User Map** contract
* Deploys a **Treasury contract** with a configured **Fee Grant** to enable gasless transactions and also **Authorization Grants** for users to give the Mobile dapp approval to interact with the **User Map** contract on their behalf
* Funds the **Treasury contract** to pay transaction fees for users via the **Fee Grant**

These 4 environment variables will be generated which will you will need to add to the Mobile Dapp config constants:

```env
NEXT_PUBLIC_CONTRACT_ADDRESS=...
NEXT_PUBLIC_TREASURY_ADDRESS=...
NEXT_PUBLIC_RPC_URL=...
NEXT_PUBLIC_REST_URL=...
```

<figure><img src="../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>



## Set up the Mobile Dapp

The Mobile Dapp will allow users to log in using their Meta Account via multiple authenticators (Email, Social Login, Wallets, Passkeys). Once logged in, users can query contract data and execute transactions to update their User Map record, which is linked to their Meta Account address.

To run the mobile dapp in either the iOS Simulator or Android Emulator, execute the following commands in your terminal:

```bash
git clone https://github.com/burnt-labs/abstraxion-expo-demo.git
cd abstraxion-expo-demo
git fetch
git checkout integrate-user-map
```

Open `constants/Config.ts` and replace the placeholder values with the corresponding environment variables from your launcher. Use the comments as a guide to match each variable correctly:

```ts
export const USER_MAP_CONTRACT_ADDRESS = "..." // Replace with value from NEXT_PUBLIC_CONTRACT_ADDRESS
export const TREASURY_CONTRACT_ADDRESS = "..." // Replace with value from NEXT_PUBLIC_TREASURY_ADDRESS
export const RPC_ENDPOINT = "..."              // Replace with value from NEXT_PUBLIC_RPC_URL
export const REST_ENDPOINT = "..."             // Replace with value from NEXT_PUBLIC_REST_URL
```

Make sure each field is updated with the correct value to ensure proper contract interaction.

Install the dependencies by executing the following:

```bash
npm install
```

To build and run the dapp, ensure your emulator, simulator, or physical device is running. Then, use one of the following commands based on your target platform to launch the Dapp:

{% tabs %}
{% tab title="For Android" %}
```sh
npx expo run:android
```
{% endtab %}

{% tab title="For iOS" %}
```sh
npx expo run:ios
```
{% endtab %}
{% endtabs %}

You should see something like the following:

<figure><img src="../../../.gitbook/assets/02 - User Map Demo App.png" alt="" width="375"><figcaption></figcaption></figure>

Once logged in you should see something like the following:

<figure><img src="../../../.gitbook/assets/01 - User Map Demo App.png" alt="" width="375"><figcaption></figcaption></figure>
