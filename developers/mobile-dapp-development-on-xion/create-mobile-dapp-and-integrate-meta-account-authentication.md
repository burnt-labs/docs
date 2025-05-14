# Create Mobile Dapp and Integrate Meta Account Authentication

This guide walks you through how to integrate Meta Account authentication into your mobile dapp using the `@burnt-labs/abstraxion-react-native` package. With Meta Accounts, users can easily sign in using familiar login methods such as email or social accounts, eliminating the need for wallets or seed phrases. This simplifies the onboarding experience and lowers the barrier for mainstream adoption by providing a secure, traditional style authentication flow within a blockchain application.



## Prerequisites

Before getting started, ensure you have the following installed:

* [Node.js](https://nodejs.org/en/) (v16 or later)
* A code editor like [VS Code](https://code.visualstudio.com/)



## What Are Meta Accounts?

Meta Accounts are smart contract accounts created and managed by XION’s Abstraxion system. They abstract away traditional blockchain complexity and provide:

* **An oAuth2 style login mechanism** (no wallet required) that allows for login via email, social accounts, passkeys and crypto wallets
* **Instant execution of pre-approved smart contract calls**
* **Gasless transactions which allows for easy user onboarding**



## Create a New Expo Project

In your terminal, run the following commands which will create an Expo app with minimum required npm dependencies without configuring navigation due to the use of the `blank` template:

```bash
npx create-expo-app my-xion-app --template blank-typescript
cd my-xion-app
```

### Install Required Dependencies

Install the Abstraxion React Native package and storage utility:

```sh
npm install @burnt-labs/abstraxion-react-native @react-native-async-storage/async-storage@2.1.2 react-native-get-random-values react-native-libsodium react-native-quick-crypto expo-dev-client
```



## Set Up Environment Variables

Environment variables are key-value pairs configured outside your source code that allow your app to behave differently depending on the environment. The Expo CLI will automatically load environment variables with an `EXPO_PUBLIC_` prefix from `.env` files for use within your code.

To interact with your deployed contracts, you’ll set a few environment variables:

Create a `.env.local` file in the root of your project and add the following where we've added an example contract with a treasury contract that's configured to work along with it:

```env
EXPO_PUBLIC_USER_MAP_CONTRACT_ADDRESS="xion1xlsdw7en2a45v9z7stmfrml3f88zj6lrxcq5uk7vv4xy4wyhjdqqlgwqa8"
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="xion1hcuf8dv4n2h6gffez3e0y70gjm20g72mtmcl3qf7t4q0laz5ef0shfm50z"
EXPO_PUBLIC_RPC_ENDPOINT="https://rpc.xion-testnet-2.burnt.com:443"
EXPO_PUBLIC_REST_ENDPOINT="https://api.xion-testnet-2.burnt.com"
```

* **EXPO\_PUBLIC\_CONTRACT\_ADDRESS -** The address of the smart contract your app will interact with.
* **EXPO\_PUBLIC\_TREASURY\_CONTRACT\_ADDRESS -** The treasury contract associated with the above contract. It enables gasless transactions using fee grants and handles authorization permissions, allowing your dApp to execute transactions on behalf of the Meta Account holder—delivering a seamless user experience.
* **EXPO\_PUBLIC\_RPC\_ENDPOINT -** The RPC endpoint used to communicate with the XION blockchain.
* **EXPO\_PUBLIC\_REST\_ENDPOINT -** The REST endpoint for querying chain state via HTTP.

You can access these values in your code using `process.env.`. For example:

```ts
const contractAddress = process.env.EXPO_PUBLIC_CONTRACT_ADDRESS;
```



## Set Up "AbstraxionProvider"

The `AbstraxionProvider` gives your app access to authentication and wallet state globally.

### Update "app.json"

There are some important items here that have to be set for your app to run on either android and ios. Expo uses a `scheme` to know which app to redirect back to and this must be unique to your app. Also for **Android** a `package` name must be set and for **iOS** a `bundleIdentifier` must be set.

Update your `app.json` file with the following;

```json
{
  "expo": {
    "name": "my-xion-app",
    "slug": "my-xion-app",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "userInterfaceStyle": "light",
    "newArchEnabled": true,
    "scheme": "abstraxion-expo-demo",
    "splash": {
      "image": "./assets/splash-icon.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
    "ios": {
      "supportsTablet": true,
      "bundleIdentifier": "com.anonymous.myxionapp"
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#ffffff"
      },
      "edgeToEdgeEnabled": true,
      "package": "com.anonymous.myxionapp"
    },
    "web": {
      "favicon": "./assets/favicon.png"
    }
  }
}
```

### Update "App.tsx"

```tsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View } from 'react-native';
import { AbstraxionProvider } from "@burnt-labs/abstraxion-react-native";

const treasuryConfig = {
  treasury: EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS,
  gasPrice: "0.001uxion",
  rpcUrl: EXPO_PUBLIC_RPC_ENDPOINT,
  restUrl: EXPO_PUBLIC_REST_ENDPOINT,
  callbackUrl: "abstraxion-expo-demo://", // This comes from app.json and should be unique to your app
};

export default function App() {
  return (
    <AbstraxionProvider config={treasuryConfig}>
      <View style={styles.container}>
        <Text>Open up App.tsx to start working on your app!</Text>
        <StatusBar style="auto" />
      </View>
    </AbstraxionProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

This wraps your entire app and ensures all child components can access authentication state and methods.

### Authentication Flows

Once wrapped in the provider, you can use the `useAbstraxion()` hook to access login methods, wallet state, and logout. Let's implement login and logout buttons to see authentication in action. Let's update `App.tsx`  with the following:

```tsx
import { StatusBar } from 'expo-status-bar';
import { StyleSheet, Text, View, TouchableOpacity } from 'react-native';
import { useAbstraxionAccount, useAbstraxionSigningClient, AbstraxionProvider } from '@burnt-labs/abstraxion-react-native';
import { useState } from 'react';

const treasuryConfig = {
  treasury: process.env.EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS,
  gasPrice: "0.001uxion",
  rpcUrl: process.env.EXPO_PUBLIC_RPC_ENDPOINT,
  restUrl: process.env.EXPO_PUBLIC_REST_ENDPOINT,
  callbackUrl: "abstraxion-expo-demo://", // This comes from app.json and should be unique to your app
};

function AppContent() {
  const account = useAbstraxionAccount();
  const { client: signingClient } = useAbstraxionSigningClient();
  const [isConnecting, setIsConnecting] = useState(false);

  const handleLogin = async () => {
    try {
      setIsConnecting(true);
      await signingClient?.signIn();
    } catch (error) {
      console.error('Login error:', error);
    } finally {
      setIsConnecting(false);
    }
  };

  const handleLogout = async () => {
    try {
      await signingClient?.signOut();
    } catch (error) {
      console.error('Logout error:', error);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Xion Abstraxion Demo</Text>
      
      {account ? (
        <View style={styles.userInfo}>
          <Text style={styles.welcomeText}>Welcome!</Text>
          <Text style={styles.addressText}>Address: {account.publicKey}</Text>
          <TouchableOpacity 
            style={styles.button} 
            onPress={handleLogout}
            disabled={isConnecting}
          >
            <Text style={styles.buttonText}>
              {isConnecting ? 'Logging out...' : 'Logout'}
            </Text>
          </TouchableOpacity>
        </View>
      ) : (
        <TouchableOpacity 
          style={styles.button} 
          onPress={handleLogin}
          disabled={isConnecting}
        >
          <Text style={styles.buttonText}>
            {isConnecting ? 'Connecting...' : 'Login with Xion'}
          </Text>
        </TouchableOpacity>
      )}
      
      <StatusBar style="auto" />
    </View>
  );
}

export default function App() {
  return (
    <AbstraxionProvider config={treasuryConfig}>
      <AppContent />
    </AbstraxionProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 30,
  },
  userInfo: {
    alignItems: 'center',
    gap: 10,
  },
  welcomeText: {
    fontSize: 20,
    fontWeight: '600',
    marginBottom: 10,
  },
  addressText: {
    fontSize: 14,
    color: '#666',
    marginBottom: 20,
  },
  button: {
    backgroundColor: '#007AFF',
    paddingHorizontal: 20,
    paddingVertical: 12,
    borderRadius: 8,
    minWidth: 200,
    alignItems: 'center',
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: '600',
  },
});

```



Here's what has been added:

**Login Functionality**

* A **“Login with Xion”** button appears when the user is not logged in.
* Utilizes the `useAbstraxionSigningClient` hook to handle authentication.
* Displays a loading state while connecting.

**Logout Functionality**

* A **“Logout”** button appears when the user is logged in.
* Uses the `disconnect` method from the signing client to log out.
* Displays a loading state while logging out.

**User Information Display**

* The user’s address is shown once logged in.
* Clean, modern UI with appropriate styling.

**Error Handling**

* Proper error catching implemented for both login and logout operations.
* Loading states prevent repeated clicks and improve UX.

***

#### How to Test It

1. Run your app.

{% tabs %}
{% tab title="For Android" %}
`npx expo run:android`
{% endtab %}

{% tab title="For iOS" %}
`npx expo run:ios`
{% endtab %}
{% endtabs %}

1. Click the **“Login with Xion”** button.
2. Complete the Xion Abstraxion authentication flow.
3. Upon success, your address should appear along with a **“Logout”** button.
4. Click **“Logout”** to disconnect the session.



## What’s Next?

Now that your app is authenticated with Meta Accounts, you're ready to start interacting with the blockchain.

Depending on your goals, here are two paths you can take next:

### Launch a Mobile dapp in 5 Minutes

If you're looking to get something up and running **quickly**, follow our streamlined quick-start guide: [**Launch a Mobile dApp in 5 Minutes**](https://docs.burnt.com/xion/developers/xion-quick-start/zero-to-dapp-in-5-minutes/launch-a-user-map-dapp-on-xion-in-5-minutes)**.**

\
This guide walks you through:

* Using our one-click launcher to deploy a contract and frontend
* Setting up a mobile app using a prebuilt template
* Authenticating users and saving their data to the blockchain

It’s the fastest way to see the full dapp lifecycle in action.



### Deep Dive: How the Mobile dapp is Built

If you want to understand **how the mobile app works under the hood**, explore this step-by-step tutorial: [**Building a React Native Mobile App with Abstraxion (Xion.js)**](https://docs.burnt.com/xion/developers/learn-and-build/mobile-development/building-a-react-native-mobile-app-with-abstraxion-xion.js)**.**

\
This guide breaks down:

* Smart contract integration using the `Abstraxion` package
* Transaction execution with Meta Accounts
* Querying and updating on-chain data
* Structuring your app for production use

It's ideal if you're building custom flows, want to extend contract functionality, or are preparing for a real-world deployment.

