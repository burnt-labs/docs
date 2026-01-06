# zkTLS Integration using Opacity in a Xion Mobile App

The ability to verify real-world facts without compromising user privacy is critical. Traditional methods of user verification can introduce trust assumptions, privacy tradeoffs, and central points of failure.

**zkTLS** (Zero-Knowledge Transport Layer Security) bridges this gap by allowing clients to generate verifiable proofs of HTTPS interactions, without revealing the full response or needing to trust the server. This has massive implications for Apps:

* **Trustless verification** of real-world data from web2 sources (e.g., social platforms, email providers, SaaS tools)
* **Privacy-preserving:** users reveal only what's necessary, not the full data
* **On-chain utility:** proofs can be verified by smart contracts for gating access or triggering functionality



## Prerequisites

Before you begin, ensure you have the following installed and configured:

* [**Node.js** (LTS version recommended) and **npm**](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* An Android Emulator via [Android Studio](https://docs.expo.dev/get-started/set-up-your-environment?platform=android\&device=physical\&mode=development-build\&buildEnv=local#set-up-an-android-device-with-a-development-build), iOS Simulator via [Xcode](https://docs.expo.dev/get-started/set-up-your-environment?platform=ios\&device=physical\&mode=development-build\&buildEnv=local#set-up-an-ios-device-with-a-development-build), **or** a physical mobile device for testing

The following [guide](https://docs.expo.dev/guides/local-app-development) will help you set up your local development environment for Expo. We also have a guide to help [Set up your XION Mobile Development Environment](set-up-your-xion-mobile-development-environment.md), which shows how to get your Android emulator and iOS simulator set up.



## Create an Opacity Account

**Opacity** is a decentralized identity protocol that allows users to prove facts about themselves, such as social media stats, financial data, or platform activity, using **zero-knowledge proofs (ZKPs)**. Rather than sharing raw data, Opacity enables users to generate cryptographic proofs that can be verified on-chain without revealing the underlying information.

In this guide, we’ll use Opacity to let users prove their **Github follower count** without exposing their full profile or login credentials. This is done through Opacity’s integration with trusted data providers like Github, wrapped in a ZK-proof that can be verified by your frontend or smart contract.

To get started, you’ll need to:

1. Visit [Opacity](https://www.opacity.network/) and sign up for a developer account.
2. Navigate to the "**Billing**" menu to purchase credits.
3. Select "**API Keys**" from the menu.
4. Click "**New key**" add a label, and select "**Create secret key**"
5. Copy and store the generated key as you'll need it for the demo app.



## Opacity's Verification Contract

Opacity provides a verifier contract deployed on XION testnet that validates zkTLS proof signatures on-chain. This ensures the GitHub data hasn't been tampered with.

**Contract Address (Testnet):**

```
xion1n5hj97fw96ef40t6h7e46lqhxsyr6fml02sjfrhk382cdl2z736qnq0yd0
```

**Query Message Format:**

```json
{
  "verify": {
    "signature": "0x...",
    "message": "<data_string_from_opacity>"
  }
}
```

The contract returns `true` if the signature is valid, confirming the data came from Opacity and wasn't modified.



## Deploy Required Contracts

Go to [https://quickstart.dev.testnet.burnt.com](https://quickstart.dev.testnet.burnt.com/), log in with your wallet, and select the **User Map** contract type. Then click **Launch User Map & Fund Treasury** to deploy both contracts.

1. For the automated installer, click the "**One-liner (Recommended)**" tab under "**Step 4**" and copy the bash command and execute it in your terminal.

<figure><img src="../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

2. Follow the prompts in the terminal:

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

3. We only need the **User Map** (EXPO\_PUBLIC\_USER\_MAP\_CONTRACT\_ADDRESS) and **Treasury** (EXPO\_PUBLIC\_TREASURY\_CONTRACT\_ADDRESS) contract addresses generated for the demo app. Go into the  `xion-mobile-quickstart` directory and copy these values from the `.env.local` file.



### User Map Contract

The User Map contract stores arbitrary JSON data per user address. We use it to persist GitHub verification results on-chain.

**Execute Message (Store Data):**

```json
{
  "update": {
    "value": "{\"github\":{\"followers\":7,\"isFamous\":true,...}}"
  }
}
```

**Query Message (Read Data):**

```json
{
  "get_value_by_user": {
    "address": "xion1..."
  }
}
```

**Data Schema:**

```typescript
interface VerificationData {
  followers: number;        // GitHub follower count
  isFamous: boolean;        // true if followers > following
  verifiedAt: string;       // ISO timestamp of verification
  verificationValid: boolean; // On-chain signature verification result
}
```



## Set up Mobile App

We've built a [demo app](https://github.com/emperorjm/opacity-expo-demo) to showcase how everything works together. In this example, we retrieve a user's followers count from **github.com** and store that value in the **User Map** contract datastore.



1. In your terminal clone the repository and change into the newly created project directory:

```bash
git clone https://github.com/emperorjm/opacity-expo-demo
cd opacity-expo-demo
```

2. Install dependencies:

```bash
npm install
```

3. Copy the `.env.example` and rename it **`.env.local`** and add the User Map address value generated above to `EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS` and add the generated Treasury contract address to `EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS`.

```javascript
EXPO_PUBLIC_RPC_ENDPOINT="https://rpc.xion-testnet-2.burnt.com:443"
EXPO_PUBLIC_REST_ENDPOINT="https://api.xion-testnet-2.burnt.com"

EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="treasury-contract-address"

EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS="your-user-map-contract-address"

EXPO_PUBLIC_OPACITY_API_KEY="your-opacity-app-key"
EXPO_PUBLIC_OPACITY_VERIFIER_CONTRACT="opacity-verifier-contract"
```

| Variable                                   | Description                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EXPO\_PUBLIC\_TREASURY\_CONTRACT\_ADDRESS  | <p>Treasury contract instance used for gasless transactions and grants authorization to execute transactions via the User Map smart contract instance on behalf of users.<br><br>You will also need to add your RUM contract address to the treasury contract permission to allow for executing transactions on the user's behalf.</p> |
| EXPO\_PUBLIC\_RPC\_ENDPOINT                | RPC endpoint for Xion (default: `https://rpc.xion-testnet-2.burnt.com:443`)                                                                                                                                                                                                                                                            |
| EXPO\_PUBLIC\_REST\_ENDPOINT               | REST endpoint for Xion (default: `https://api.xion-testnet-2.burnt.com`)                                                                                                                                                                                                                                                               |
| EXPO\_PUBLIC\_USER\_MAP\_CONTRACT\_ADDRESS | Your User Map contract instance where the user's follower count will be added to their user map record with their logged in wallet address being the key                                                                                                                                                                               |
| EXPO\_PUBLIC\_OPACITY\_API\_KEY            | API key for interacting with the Opacity platform.                                                                                                                                                                                                                                                                                     |
| EXPO\_PUBLIC\_OPACITY\_VERIFIER\_CONTRACT  | Validates that the zkTLS proofs were legitimately signed by authorized notary nodes and haven't been tampered with.                                                                                                                                                                                                                    |

4. Build and launch the application:

To build and run the app, ensure your **emulator**, **simulator**, or **physical device** is running. Then, use one of the following commands based on your target platform to launch the App:

{% tabs %}
{% tab title="For Android" %}
```
npx expo run:android
```
{% endtab %}

{% tab title="For iOS" %}
```sh
npx expo run:ios
```
{% endtab %}
{% endtabs %}

<figure><img src="../../.gitbook/assets/2026-01-06 10.09.11.jpg" alt="" width="303"><figcaption></figcaption></figure>
