# React Native Mobile Dapp on XION in 5 Minutes

Building a **Mobile Dapp** on XION typically involves three main components:

* **Smart contract** – Contains the core business logic of your Dapp.
* **Treasury contract** – Enables gasless transactions using **Fee Grants**, making onboarding smoother for users. It also handles **Authorization Grants**, which allow your frontend to execute transactions on behalf of the user, enabling a more traditional application experience through our OAuth2-style **Abstraxion** solution.
* **Mobile Frontend** – This is your Dapp’s interface, used to display data and trigger transactions via the smart contract.

As a beginner to XION development, getting a fully functional Dapp up and running should be simple and shouldn’t require any complex setup. With this in mind, we’ve created a [**quick launch frontend solution**](https://quickstart.dev.testnet.burnt.com) that handles the creation of both the **User Map** and **Treasury** contracts with the Treasury contract being fully configured. We've also created a fully functional [**React Native Mobile Dapp**](https://github.com/burnt-labs/abstraxion-expo-demo) for interacting with the **User Map** contract.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FB5Z5ijJgMx0GJO3l1Il9%2Fuploads%2FZLyzVAyDYHTYwlX8G3LJ%2F06%20-%20Mobile%20Dapp.mp4?alt=media&token=13818280-4cf4-4de7-b129-738cce911063" %}

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

To make this much easier, we’ve created a [**quick launch frontend solution**](https://quickstart.dev.testnet.burnt.com) for this **User Map** Dapp that takes care of all the heavy lifting, so you can skip the setup pain and focus on experimenting with a real, working Mobile dapp.

1. Go to [https://quickstart.dev.testnet.burnt.com](https://quickstart.dev.testnet.burnt.com) and log in using your **Meta Account**

<figure><img src="../../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

2. Click the **Launch User Map & Fund Treasury** button

<figure><img src="../../../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

Behind the scenes, this:

* Instantiates your **User Map** contract
* Deploys a **Treasury contract** with a configured **Fee Grant** to enable gasless transactions and also **Authorization Grants** for users to give the Mobile dapp approval to interact with the **User Map** contract on their behalf
* Funds the **Treasury contract** to pay transaction fees for users via the **Fee Grant**

These 4 environment variables will be generated which will you will need to add to the Mobile Dapp `.env.local` environment file:

```env
EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS="..."
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="..."
EXPO_PUBLIC_RPC_ENDPOINT="..."
EXPO_PUBLIC_REST_ENDPOINT="..."
```

## Set up the Mobile Dapp

The Mobile Dapp will allow users to log in using their Meta Account via multiple authenticators (Email, Social Login, Wallets, Passkeys). Once logged in, users can query contract data and execute transactions to update their User Map record, which is linked to their Meta Account address.

To run the mobile dapp in either the iOS Simulator or Android Emulator, execute the following commands in your terminal:

```bash
git clone https://github.com/burnt-labs/abstraxion-expo-demo.git
cd abstraxion-expo-demo
```

### Set up Environment Variables

In the **Quick Launch Frontend** click the "**Mobile**" template:

<figure><img src="../../../.gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

There are two options available for copying the variables into your project:

* **Option 1: One-liner (Recommended):** Run the bash command provided in your terminal from within your project directory to automatically download and save the environment file into your project with the variables set to the correct values.&#x20;

<figure><img src="../../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

* **Option 2: Manual Copy & Paste:** Copy and paste the actual variables into your `.env.local` file in the root of your project:

<figure><img src="../../../.gitbook/assets/image (77).png" alt=""><figcaption></figcaption></figure>

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
