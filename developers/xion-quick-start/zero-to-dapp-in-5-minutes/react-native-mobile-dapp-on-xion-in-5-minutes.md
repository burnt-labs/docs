# React Native Mobile App on XION in 5 Minutes

Building a **Mobile App** on XION typically involves three main components:

* **Smart contract** – Contains the core business logic of your App.
* **Treasury contract** – Enables gasless transactions using **Fee Grants**, making onboarding smoother for users. It also handles **Authorization Grants**, which allow your frontend to execute transactions on behalf of the user, enabling a more traditional application experience through our OAuth2-style **Abstraxion** solution.
* **Mobile Frontend** – This is your App’s interface, used to display data and trigger transactions via the smart contract.

As a beginner to XION development, getting a fully functional App up and running should be simple and shouldn’t require any complex setup. With this in mind, we’ve created a [**quick launch frontend solution**](https://quickstart.dev.testnet.burnt.com) that handles the creation of both the **User Map** and **Treasury** contracts with the Treasury contract being fully configured. We've also created a fully functional [**React Native Mobile App**](https://github.com/burnt-labs/abstraxion-expo-demo) for interacting with the **User Map** contract.



## What is the User Map App?

The **User Map** contract is a lightweight and beginner friendly smart contract designed to help developers, especially those coming from traditional web or application development, understand how to manage per-user data in a decentralized environment.

Think of it like a basic key-value store:

* **Key:** User's wallet address
* **Value:** A JSON object (e.g., user profile, preferences, settings)

You can find the source code here: [github.com/burnt-labs/contracts/tree/main/contracts/user\_map](https://github.com/burnt-labs/contracts/tree/main/contracts/user_map).



## Prerequisites

Before you begin, ensure you have the following installed and configured:

* [**Node.js** (LTS version recommended) and **npm**](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* An Android Emulator via [Android Studio](https://docs.expo.dev/get-started/set-up-your-environment?platform=android\&device=physical\&mode=development-build\&buildEnv=local#set-up-an-android-device-with-a-development-build), iOS Simulator via [Xcode](https://docs.expo.dev/get-started/set-up-your-environment?platform=ios\&device=physical\&mode=development-build\&buildEnv=local#set-up-an-ios-device-with-a-development-build), **or** a physical mobile device for testing

The following [guide](https://docs.expo.dev/guides/local-app-development) will help you set up your local development environment for Expo. We also have a guide in the docs on [Set up your XION Mobile Development Environment](../../mobile-app-development/set-up-your-xion-mobile-development-environment.md), which shows how to get your Android emulator and iOS simulator set up.



## Create Instance of User Map and Treasury Contracts

**Compiling** and **instantiating** smart contracts can feel like huge tasks, especially for new developers. You typically need to set up your development environment with tools like **Docker** and **xiond**, and go through the full process of compiling, deploying, and instantiating your contracts manually. On top of that, creating and configuring a Treasury contract via the [**Dev Portal**](https://dev.testnet.burnt.com) can be overwhelming if you're just getting started.

To make this much easier, we’ve created a [**quick launch frontend solution**](https://quickstart.dev.testnet.burnt.com) for this **User Map** App that takes care of all the heavy lifting, so you can skip the setup pain and focus on experimenting with a real, working Mobile app.

1. Go to [https://quickstart.dev.testnet.burnt.com](https://quickstart.dev.testnet.burnt.com) and log in using your **Meta Account**

<figure><img src="../../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

2. Click the **Launch User Map & Fund Treasury** button

<figure><img src="../../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

Behind the scenes, this:

* Instantiates your **User Map** contract
* Deploys a **Treasury contract** with a configured **Fee Grant** to enable gasless transactions and also **Authorization Grants** for users to give the Mobile app approval to interact with the **User Map** contract on their behalf
* Funds the **Treasury contract** to pay transaction fees for users via the **Fee Grant**

These 4 environment variables will be generated which will you will need to add to the Mobile App `.env.local` environment file:

```env
EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS="..."
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="..."
EXPO_PUBLIC_RPC_ENDPOINT="..."
EXPO_PUBLIC_REST_ENDPOINT="..."
```

## Set up the Mobile App

The Mobile App will allow users to log in using their Meta Account via multiple authenticators (Email, Social Login, Wallets, Passkeys). Once logged in, users can query contract data and execute transactions to update their User Map record, which is linked to their Meta Account address.

To run the mobile app in either the iOS Simulator or Android Emulator, execute the following commands in your terminal:

```bash
git clone https://github.com/burnt-labs/abstraxion-expo-demo.git
cd abstraxion-expo-demo
```

### Set up Environment Variables

In the **Quick Launch Frontend** click the "**Mobile**" template:

<figure><img src="../../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

There are two options available for copying the variables into your project:

* **Option 1: One-liner (Recommended):** Run the bash command provided in your terminal from within your project directory to automatically download and save the environment file into your project with the variables set to the correct values.

<figure><img src="../../../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>

* **Manual Setup:** Copy and paste the actual variables into your `.env.local` file in the root of your project:

<figure><img src="../../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>

Install the dependencies by executing the following:

```bash
npm install
```

To build and run the app, ensure your emulator, simulator, or physical device is running. Then, use one of the following commands based on your target platform to launch the App:

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
