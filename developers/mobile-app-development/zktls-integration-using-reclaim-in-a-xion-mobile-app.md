# zkTLS Integration using Reclaim in a Xion Mobile App

The ability to verify real-world facts without compromising user privacy is critical. Traditional methods of user verification, centralized APIs, can introduce trust assumptions, privacy tradeoffs, and central points of failure.

**zkTLS** (Zero-Knowledge Transport Layer Security) bridges this gap by allowing clients to generate verifiable proofs of HTTPS interactions, without revealing the full response or needing to trust the server. This has massive implications for Apps:

* **Trustless verification** of real-world data from web2 sources (e.g., social platforms, email providers, SaaS tools)
* **Privacy-preserving:** users reveal only what's necessary, not the full data
* **On-chain utility:** proofs can be verified by smart contracts for gating access or triggering functionality



## Create a Reclaim Account

**Reclaim** is a decentralized identity protocol that allows users to prove facts about themselves, such as social media stats, financial data, or platform activity, using **zero-knowledge proofs (ZKPs)**. Rather than sharing raw data, Reclaim enables users to generate cryptographic proofs that can be verified on-chain without revealing the underlying information.

In this guide, we’ll use Reclaim to let users prove their **Twitter follower count** without exposing their full profile or login credentials. This is done through Reclaim’s integration with trusted data providers like Twitter, wrapped in a ZK-proof that can be verified by your frontend or smart contract.

To get started, you’ll need to:

1. Visit [Reclaim](https://dev.reclaimprotocol.org/explore) and sign in with your Google account.
2. Create a new application.
3. Save your **Application ID** and **Application Secret**
4. Add the "**Twitter User Profile**" provider to your application. You will need the "**httpProviderId**" later.



## Reclaim's Verification Contract

Reclaim provides a global zkTLS verification smart contract that validates user proofs. You do not need to deploy your own instance unless you want to customize it. The verification contract address is as follows:&#x20;

* Global Verification Contract Address: `xion1qf8jtznwf0tykpg7e65gwafwp47rwxl4x2g2kldvv357s6frcjlsh2m24e`&#x20;



## Reclaim User Map (RUM) Contract

The **Reclaim User Map (RUM)** contract is a modified version of the User Map contract, designed to store structured JSON information derived from verified proofs.

Instead of storing arbitrary user data, this contract is purpose-built to:

* Accept verified proof data from Reclaim (e.g., Twitter follower count)
* Associate it with the user's address on-chain
* Enable your application to query and verify the stored proof later

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

Here is an example function in Javascript to create an instance of the RUM contract where you must update the `claim_key` and `verification_addr` if your not using the provided verification contract instance. We have deployed a version of the RUM contract on Testnet with `CODE_ID` being `1289`.

```javascript
  const instantiateRUMContract = async () => {
    if (!account?.bech32Address || !client) {
      Alert.alert("Error", "Account or client not found");
      return;
    }

    try {
      const instantiateMsg = {
        verification_addr: "xion1qf8jtznwf0tykpg7e65gwafwp47rwxl4x2g2kldvv357s6frcjlsh2m24e", // this stays hardcoded to our verification contract
        claim_key: "add-claim-key", // Change this to whatever claim key you want to use
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

We've built a [demo app](https://github.com/burnt-labs/abstraxion-expo-demo/pull/6) to showcase how everything works together. In this example, we retrieve a user's `follower_count` from **X.com** (formerly Twitter) and store that value in their user map on-chain using the smart contract. This app is an extension of the User Map contract so a separate User Map contract instance is used for the original flow of the app but there is a new **zkTLS** page that uses the **RUM** contract instance.

### Manual Installation <a href="#manual-installation" id="manual-installation"></a>

1. You will first need to clone the repository:

```bash
git clone https://github.com/burnt-labs/abstraxion-expo-demo.git
```

2. Because the PR is not yet merged we need to fetch the PR and checkout out the branch to get the zkLTS updates:

```bash
git fetch origin pull/6/head:impl-reclaim
git checkout impl-reclaim
```

3. Install dependencies:

```bash
npm install
```

4. Create an **`.env.local`** file and set the following values below below:

```javascript
EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS="usermap-address"
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="treasury-contract-address"
EXPO_PUBLIC_RPC_ENDPOINT="https://rpc.xion-testnet-2.burnt.com:443"
EXPO_PUBLIC_REST_ENDPOINT="https://api.xion-testnet-2.burnt.com"

EXPO_PUBLIC_CODE_ID=1289
EXPO_PUBLIC_VERIFICATION_CONTRACT_ADDRESS=xion1qf8jtznwf0tykpg7e65gwafwp47rwxl4x2g2kldvv357s6frcjlsh2m24e
EXPO_PUBLIC_RUM_CONTRACT_ADDRESS="your-rum-contract-address"
EXPO_PUBLIC_RECLAIM_APP_ID="your-reclaim-app-id"
EXPO_PUBLIC_RECLAIM_APP_SECRET="your-reclaim-secret"
EXPO_PUBLIC_RECLAIM_PROVIDER_ID="your-reclaim-provider-id"
```

| Variable                                      | Description                                                                                                                                                                                                                                                                                                                            |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| EXPO\_PUBLIC\_USER\_MAP\_CONTRACT\_ADDRESS    | Address of the User Map smart contract instance.                                                                                                                                                                                                                                                                                       |
| EXPO\_PUBLIC\_TREASURY\_CONTRACT\_ADDRESS     | <p>Treasury contract instance used for gasless transactions and grantz authorization to execute transactions via the User Map smart contract instance on behalf of users.<br><br>You will also need to add your RUM contract address to the treasury contract permission to allow for executing transactions on the user's behalf.</p> |
| EXPO\_PUBLIC\_RPC\_ENDPOINT                   | RPC endpoint for Xion (default: `https://rpc.xion-testnet-2.burnt.com:443`)                                                                                                                                                                                                                                                            |
| EXPO\_PUBLIC\_REST\_ENDPOINT                  | REST endpoint for Xion (default: `https://api.xion-testnet-2.burnt.com`)                                                                                                                                                                                                                                                               |
| EXPO\_PUBLIC\_CODE\_ID                        | The Code ID of the deployed RUM contract.                                                                                                                                                                                                                                                                                              |
| EXPO\_PUBLIC\_VERIFICATION\_CONTRACT\_ADDRESS | Global verification contract address that allows for verifying proofs.                                                                                                                                                                                                                                                                 |
| EXPO\_PUBLIC\_RUM\_CONTRACT\_ADDRESS          | Your RUM contract instance where the user's follower count will be added to their user map record with their logged in wallet address being the                                                                                                                                                                                        |
| EXPO\_PUBLIC\_RECLAIM\_APP\_ID                | App ID for the Application created within the Reclaim platform.                                                                                                                                                                                                                                                                        |
| EXPO\_PUBLIC\_RECLAIM\_APP\_SECRET            | App Secret for the Application created within the Reclaim platform.                                                                                                                                                                                                                                                                    |
| EXPO\_PUBLIC\_RECLAIM\_PROVIDER\_ID           | Provider ID of the provider added to the Application within the Reclaim platform. This would be found in the **httpProviderId field** in **Twitter User Profile** provider.                                                                                                                                                            |



5. Build and launch the application:

To build and run the app, ensure your **emulator**, **simulator**, or **physical device** is running. Then, use one of the following commands based on your target platform to launch the App:

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
