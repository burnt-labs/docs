# zkTLS Integration using Reclaim in a Xion Mobile App

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



## Create a Reclaim Account

**Reclaim** is a decentralized identity protocol that allows users to prove facts about themselves, such as social media stats, financial data, or platform activity, using **zero-knowledge proofs (ZKPs)**. Rather than sharing raw data, Reclaim enables users to generate cryptographic proofs that can be verified on-chain without revealing the underlying information.

In this guide, we’ll use Reclaim to let users prove their **Twitter follower count** without exposing their full profile or login credentials. This is done through Reclaim’s integration with trusted data providers like Twitter (Now X.com), wrapped in a ZK-proof that can be verified by your frontend or smart contract.

To get started, you’ll need to:

1. Visit [Reclaim](https://dev.reclaimprotocol.org/explore) and sign in.
2. Create a new application.
3. Save your **Application ID** and **Application Secret**.
4. Add the "**Twitter User Profile**" provider to your application. You will need the "**httpProviderId**" later.



## Reclaim's Verification Contract

Reclaim provides a zkTLS verification smart contract that validates user proofs. You do not need to deploy your own instance unless you want to customize it. The verification contract address is as follows:

* Verification Contract Address: `xion1qf8jtznwf0tykpg7e65gwafwp47rwxl4x2g2kldvv357s6frcjlsh2m24e`



## Reclaim User Map (RUM) Contract

The **Reclaim User Map (RUM)** contract is a modified version of the [User Map contract](https://docs.burnt.com/xion/developers/learn-and-build/use-cases/building-a-per-user-data-storage-dapp#user-map-contract), designed to store per-user data ion JSON format.

Instead of storing arbitrary user data, this contract is purpose-built to:

* Accept verified proof data from Reclaim (e.g., Twitter follower count)
* Map the data to the user's address
* Enable your application to query and verify the stored proof

#### Example Document Schema

```javascript
  const claimInfo = {
    provider: verificationResult.proofs[0].claimData.provider,
    parameters: verificationResult.proofs[0].claimData.parameters,
    context: verificationResult.proofs[0].claimData.context,
  };

  const signedClaim = {
    claim: {
      identifier: verificationResult.proofs[0].claimData.identifier,
      owner: verificationResult.proofs[0].claimData.owner,
      epoch: verificationResult.proofs[0].claimData.epoch,
      timestampS: verificationResult.proofs[0].claimData.timestampS,
    },
    signatures: verificationResult.proofs[0].signatures,
  };

  // This would be the execute message to update the RUM contract state
  const executeMsg = {
    update: {
      value: {
        proof: {
          claimInfo: claimInfo,
          signedClaim: signedClaim,
        },
      },
    },
  };
```

You can find the RUM contract here [https://github.com/burnt-labs/contracts/pull/72](https://github.com/burnt-labs/contracts/pull/72). There are two parameters required when creating an instance of the contract:

* **Verification Address**: Which is the address of the verification contract that will be called to verify if the proof is valid.
* **Claim Key**: This is an element in the proof dataset that will be stored on chain. This comes from picking out the value from the `claimData.context` within the proof. For this guide we will be using the `followers_count` key.

We have deployed a version of the RUM contract on Testnet with `CODE_ID` being `1289`. If you are using the [provided verification contract instance](zktls-integration-using-reclaim-in-a-xion-mobile-app.md#reclaims-verification-contract), you only need to update the `claim_key` parameter.\
If you are using your own verification contract, you must update both the `claim_key` and `verification_addr` parameters.

```javascript
  const instantiateRUMContract = async () => {
    if (!account?.bech32Address || !client) {
      Alert.alert("Error", "Account or client not found");
      return;
    }

    try {
      const instantiateMsg = {
        verification_addr: "xion1qf8jtznwf0tykpg7e65gwafwp47rwxl4x2g2kldvv357s6frcjlsh2m24e", // this stays hardcoded to our verification contract
        claim_key: "followers_count", // Change this to whatever claim key you want to use
      };
      
      let CODE_ID = 1289;

      const instantiateResult = await client.instantiate(
        account?.bech32Address,
        CODE_ID,
        instantiateMsg,
        "test-init",
        "auto"
      );

      console.log("RUM contract instantiated:", instantiateResult);
      Alert.alert("Success", "RUM contract instantiated!");
      return instantiateResult.contractAddress;
    } catch (error) {
      console.log("Error instantiating RUM contract:", error);
      Alert.alert("Error", "Failed to instantiate RUM contract");
      throw error;
    }
  };
```



## Set up Mobile App

We've built a [demo app](https://github.com/burnt-labs/abstraxion-reclaim-demo) to showcase how everything works together. In this example, we retrieve a user's `followers_count` from **X.com** (formerly Twitter) and store that value in their account's user map value within the RUM contract datastore.

### Deploy Required Contracts

Go to [https://quickstart.dev.testnet.burnt.com](https://quickstart.dev.testnet.burnt.com/), log in with your wallet, and select the **RUM (Reclaim User Map)** contract type. Then click **Launch RUM & Fund Treasury** to deploy both contracts.

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

### Automated Installation <a href="#manual-installation" id="manual-installation"></a>

1. For the automated installer, click the "**One-liner (Recommended)**" tab under "**Step 3**" and copy the bash command and execute it in your terminal.

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

2. Follow the prompts in the terminal:

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

3. Once you've followed all the prompts, the installer will install all the dependencies. You will then need to change in to the app directory:

```
cd xion-rum-quickstart
```

4. Build and launch the application:

To build and run the app, ensure your **emulator**, **simulator**, or **physical device** is running. Then, use one of the following commands based on your target platform to launch the App:

{% tabs %}
{% tab title="For Android" %}
```
npx expo run:android
```
{% endtab %}

{% tab title="For iOS" %}
```
npx expo run:ios
```
{% endtab %}
{% endtabs %}

### Manual Installation <a href="#manual-installation" id="manual-installation"></a>

1. Click the "**Manual Setup**" tab under "**Step 3**" and copy the values for use later.

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

2. In your terminal clone the repository and change into the newly created project directory:

```bash
git clone https://github.com/burnt-labs/abstraxion-reclaim-demo.git
cd abstraxion-reclaim-demo
```

3. Install dependencies:

```bash
npm install
```

4. Create an **`.env.local`** file in root of the project folder and add the environment values from the quickstart portal in "**Step 2**" above:

```javascript
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="treasury-contract-address"
EXPO_PUBLIC_RPC_ENDPOINT="https://rpc.xion-testnet-2.burnt.com:443"
EXPO_PUBLIC_REST_ENDPOINT="https://api.xion-testnet-2.burnt.com"

EXPO_PUBLIC_RECLAIM_APP_ID="your-reclaim-app-id"
EXPO_PUBLIC_RECLAIM_APP_SECRET="your-reclaim-secret"
EXPO_PUBLIC_RECLAIM_PROVIDER_ID="your-reclaim-provider-id"

EXPO_PUBLIC_RUM_CONTRACT_ADDRESS="your-rum-contract-address"
```

| Variable                                  | Description                                                                                                                                                                                                                                                                                                                            |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EXPO\_PUBLIC\_TREASURY\_CONTRACT\_ADDRESS | <p>Treasury contract instance used for gasless transactions and grants authorization to execute transactions via the User Map smart contract instance on behalf of users.<br><br>You will also need to add your RUM contract address to the treasury contract permission to allow for executing transactions on the user's behalf.</p> |
| EXPO\_PUBLIC\_RPC\_ENDPOINT               | RPC endpoint for Xion (default: `https://rpc.xion-testnet-2.burnt.com:443`)                                                                                                                                                                                                                                                            |
| EXPO\_PUBLIC\_REST\_ENDPOINT              | REST endpoint for Xion (default: `https://api.xion-testnet-2.burnt.com`)                                                                                                                                                                                                                                                               |
| EXPO\_PUBLIC\_RUM\_CONTRACT\_ADDRESS      | Your RUM contract instance where the user's follower count will be added to their user map record with their logged in wallet address being the key                                                                                                                                                                                    |
| EXPO\_PUBLIC\_RECLAIM\_APP\_ID            | App ID for the Application created within the Reclaim platform.                                                                                                                                                                                                                                                                        |
| EXPO\_PUBLIC\_RECLAIM\_APP\_SECRET        | App Secret for the Application created within the Reclaim platform.                                                                                                                                                                                                                                                                    |
| EXPO\_PUBLIC\_RECLAIM\_PROVIDER\_ID       | Provider ID of the provider added to the Application within the Reclaim platform. This would be found in the **httpProviderId field** in **Twitter User Profile** provider.                                                                                                                                                            |

5. Build and launch the application:

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
