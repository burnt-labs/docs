---
hidden: true
---

# Copy of Building a React Native App with Abstraxion (Xion.js)

Mobile apps are a permanent staple in our everyday lives and therefore to increase Blockchain adoption developers need to start tapping into this resource that almost everyone uses. This guide walks through creating a React Native application using the Abstraxion authentication system, based on the [Abstraxion Expo Demo](https://github.com/burnt-labs/abstraxion-expo-demo/) app.



## Prerequisites

Before you start, make sure you have the following installed:

* [NPM (LTS) and Node.js](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* Android Emulator or iOS Simulator



## Project Setup

Let's start by creating a new Expo project:

```bash
npx create-expo-app@latest abstraxion-app --template default
```

The Default template is designed to build multi-screen apps and includes recommended tools such as Expo CLI, Expo Router library and TypeScript configuration enabled, making it suitable for most apps.

{% hint style="info" %}
You will be prompted to enter a **name** for your app.
{% endhint %}

Once the installation is completed execute the following command to change into the newly created project directory:

```bash
cd abstraxion-app
```

### Installing Required Dependencies

Install the necessary dependencies for the project:

```bash
npm install @burnt-labs/abstraxion-react-native @react-native-async-storage/async-storage@2.1.2 react-native-get-random-values react-native-libsodium react-native-quick-crypto expo-dev-client
```



## Update Project

There are a number of modifications that are required in order to get the app. to utilize the Abstraxion package for authentication.

### Update app.json

We need to make some minor additions to the `appl.json` file. If you will be building on Android you should add the following to the `"android"` section changing the value to whatever you would want to call your package:

```json
"package": "com.anonymous.abstraxionexpodemo"
```

For `ios` you would add the following to the `ios` element changing the value to what you would want it called:

```json
"bundleIdentifier": "com.anonymous.abstraxionexpodemo"
```

### Update Main Tab

This is where a lot of action will happen. If you've worked on any of the dapp tutorials before you will see that this will be similar in that we will be adding a lot of the Abstraxion code here to bring everything together. Replace the `index.tsx` file with the following content:

```javascript
import { useState } from "react";
import { View, StyleSheet, Text, TouchableOpacity, Alert } from "react-native";
import {
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion-react-native";

export default function Index() {
  // Abstraxion hooks
  const {
    data: account,
    logout,
    login,
    isConnected,
    isConnecting,
  } = useAbstraxionAccount();
  const { client, signArb } = useAbstraxionSigningClient();

  const [signArbResponse, setSignArbResponse] = useState("");
  const [txHash, setTxHash] = useState("");
  const [loadingInstantiate, setLoadingInstantiate] = useState(false);

  async function handleInstantiate() {
    setLoadingInstantiate(true);
    try {
      // Sample treasury contract instantiate msg
      const msg = {
        type_urls: ["/cosmwasm.wasm.v1.MsgInstantiateContract"],
        grant_configs: [
          {
            description: "Ability to instantiate contracts",
            optional: false,
            authorization: {
              type_url: "/cosmos.authz.v1beta1.GenericAuthorization",
              value: "CigvY29zbXdhc20ud2FzbS52MS5Nc2dJbnN0YW50aWF0ZUNvbnRyYWN0",
            },
          },
        ],
        fee_config: {
          description: "Sample fee config for testnet-2",
          allowance: {
            type_url: "/cosmos.feegrant.v1beta1.BasicAllowance",
            value: "Cg8KBXV4aW9uEgY1MDAwMDA=",
          },
        },
        admin: account.bech32Address,
      };

      const instantiateRes = await client?.instantiate(
        account.bech32Address,
        33,
        msg,
        "instantiate on expo demo",
        "auto"
      );

      console.log(instantiateRes);

      if (!instantiateRes) {
        throw new Error("Instantiate failed.");
      }

      setTxHash(instantiateRes.transactionHash);
    } catch (error) {
      Alert.alert("Error", (error as Error).message);
    } finally {
      setLoadingInstantiate(false);
    }
  }

  async function handleSign(): Promise<void> {
    if (client?.granteeAddress) {
      const response = await signArb?.(
        client.granteeAddress,
        "abstraxion challenge"
      );
      if (response) setSignArbResponse(response);
    }
  }

  function handleLogout() {
    logout();
    setSignArbResponse("");
    setTxHash("");
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Abstraxion React-Native Demo</Text>
      {isConnected ? (
        <>
          <TouchableOpacity
            onPress={handleInstantiate}
            style={styles.button}
            disabled={loadingInstantiate}
          >
            <Text style={styles.buttonText}>
              {loadingInstantiate ? "Loading..." : "Sample instantiate"}
            </Text>
          </TouchableOpacity>
          <TouchableOpacity onPress={handleSign} style={styles.button}>
            <Text style={styles.buttonText}>Sign Arb</Text>
          </TouchableOpacity>
        </>
      ) : null}
      {isConnected ? (
        <TouchableOpacity onPress={handleLogout} style={styles.button}>
          <Text style={styles.buttonText}>Logout</Text>
        </TouchableOpacity>
      ) : (
        <TouchableOpacity
          onPress={login}
          style={[styles.button, isConnecting && styles.disabledButton]}
          disabled={isConnecting}
        >
          <Text style={styles.buttonText}>
            {isConnecting ? "Connecting..." : "Login"}
          </Text>
        </TouchableOpacity>
      )}
      {signArbResponse || txHash ? (
        <View style={styles.card}>
          {signArbResponse ? (
            <Text style={styles.responseText}>{signArbResponse}</Text>
          ) : null}
          {txHash ? <Text style={styles.responseText}>{txHash}</Text> : null}
        </View>
      ) : null}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#f0f0f0",
    alignItems: "center",
    justifyContent: "center",
    padding: 20,
  },
  title: {
    fontSize: 24,
    fontWeight: "bold",
    marginBottom: 20,
    color: "#333",
  },
  button: {
    marginVertical: 10,
    padding: 15,
    borderRadius: 5,
    backgroundColor: "#2196F3",
    width: "80%",
    alignItems: "center",
  },
  buttonText: {
    color: "#fff",
    fontSize: 16,
  },
  card: {
    backgroundColor: "#fff",
    padding: 15,
    borderRadius: 10,
    marginTop: 20,
    width: "90%",
    shadowColor: "#000",
    shadowOffset: {
      width: 0,
      height: 2,
    },
    shadowOpacity: 0.2,
    shadowRadius: 4,
    elevation: 5,
  },
  responseText: {
    color: "#000",
    marginTop: 10,
    fontSize: 16,
  },
  disabledButton: {
    backgroundColor: "#B0BEC5",
  },
});
```

Here are the key changes in the updated file:

1. **Integration with Abstraxion** package
   * The new file now imports `useAbstraxionAccount` and `useAbstraxionSigningClient` from `@burnt-labs/abstraxion-react-native`, enabling authentication and transaction signing functionalities.
2. **State Management with `useState`**
   * Introduces state variables (`signArbResponse`, `txHash`, `loadingInstantiate`) to manage transaction status and responses.
3. **New Authentication Logic**
   * Includes authentication management (`login`, `logout`, `isConnected`, `isConnecting`) using `useAbstraxionAccount()`.
4. **Transaction Signing & Contract Instantiation**
   * Adds a function (`handleInstantiate`) to instantiate a smart contract on a blockchain using `MsgInstantiateContract`.

### Remove explore tab

We'll not be using the Explore tab and so let's go ahead and remove it from the project. Delete the following file "**app/(tabs)/explore.tsx**".



### Update Layout

Within the layout file we'll add the treasury configuration along with the AbstraxionProvider that will set up the authentication flow.

Replace the contents of the `app/_layout.tsx` file with the following content:

```javascript
import "react-native-reanimated";
import "react-native-get-random-values";
import * as SplashScreen from "expo-splash-screen";
import { useEffect } from "react";
import {
  DarkTheme,
  DefaultTheme,
  ThemeProvider,
} from "@react-navigation/native";
import { useFonts } from "expo-font";
import { Stack } from "expo-router";
import { StatusBar } from "expo-status-bar";

import { AbstraxionProvider } from "@burnt-labs/abstraxion-react-native";

import { useColorScheme } from "@/hooks/useColorScheme";

import { Buffer } from "buffer";
import crypto from "react-native-quick-crypto";
global.crypto = crypto;
global.Buffer = Buffer;

// Prevent the splash screen from auto-hiding before asset loading is complete.
SplashScreen.preventAutoHideAsync();

const treasuryConfig = {
  treasury: "xion1r0tt64mdld2svywzeaf4pa7ezsg6agkyajk48ea398njywdl28rs3jhvry", // Example XION treasury instance
  gasPrice: "0.001uxion", // If you feel the need to change the gasPrice when connecting to signer, set this value. Please stick to the string format seen in example
  rpcUrl: "https://rpc.xion-testnet-2.burnt.com:443",
  restUrl: "https://api.xion-testnet-2.burnt.com:443",
  callbackUrl: "abstraxion-expo-demo://", // this comes from app.json scheme
};

export default function RootLayout() {
  const colorScheme = useColorScheme();
  const [loaded] = useFonts({
    SpaceMono: require("../assets/fonts/SpaceMono-Regular.ttf"),
  });

  useEffect(() => {
    if (loaded) {
      SplashScreen.hideAsync();
    }
  }, [loaded]);

  if (!loaded) {
    return null;
  }

  return (
    <AbstraxionProvider config={treasuryConfig}>
      <ThemeProvider value={colorScheme === "dark" ? DarkTheme : DefaultTheme}>
        <Stack>
          <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
          <Stack.Screen name="+not-found" />
        </Stack>
        <StatusBar style="auto" />
      </ThemeProvider>
    </AbstraxionProvider>
  );
}
```

Here’s a breakdown of the key modifications:

#### 1. **New Imports**

* **`import "react-native-get-random-values";`**
  * This import ensures that cryptographic random values are properly generated in a React Native environment.
* **`import { AbstraxionProvider } from "@burnt-labs/abstraxion-react-native";`**
  * This adds support for the **Abstraxion** library
* **`import { Buffer } from "buffer";` and `import crypto from "react-native-quick-crypto";`**
  * These provide cryptographic and buffer functionalities, often needed for handling blockchain operations.

#### 2. **Global Object Modifications**

* **`global.crypto = crypto;`**
  * Assigns `react-native-quick-crypto` as the global crypto library, ensuring cryptographic functions work correctly.
* **`global.Buffer = Buffer;`**
  * Makes the `Buffer` object globally available, which is commonly used for handling binary data in blockchain transactions.

#### 3. **Introduction of `AbstraxionProvider`**

* Wraps the application inside `<AbstraxionProvider config={treasuryConfig}>`, providing the app with **Abstraxion** functionalities.
* This allows interaction with **XION treasury**, as seen in the newly added **`treasuryConfig`**.

#### 4. **New `treasuryConfig` Object**

* Contains configuration for interacting with the **XION blockchain**, including:
  * **Treasury address**: `"xion1r0tt64mdld2svywzeaf4pa7ezsg6agkyajk48ea398njywdl28rs3jhvry"`
  * **Gas price**: `"0.001uxion"`
  * **RPC & REST URLs**: Connecting to **XION testnet**
  * **Callback URL**: `"abstraxion-expo-demo://"` for handling deep linking.



### Addition of metro config

We will add this file in the root of the project. Create a new file `metro.config.js`. The **`metro.config.js`** file in an **Expo** project is used to configure **Metro**, the JavaScript bundler that processes and optimizes code for React Native apps. Add the following content to the file:

```javascript
// metro.config.js
const { getDefaultConfig } = require("expo/metro-config");
const {
  withLibsodiumResolver,
} = require("@burnt-labs/abstraxion-react-native/metro.libsodium");

const config = getDefaultConfig(__dirname);
module.exports = withLibsodiumResolver(config);
```



## Execute Project

We first need to build the app and install it on your emulator, simulator or device. Run the development build command for `Android`:

```bash
npx expo run:android
```

or for `ios`:

```bash
npx expo run:ios
```



## Video Demo

Here is a quick walkthrough of the app.

{% file src="../../../.gitbook/assets/422392444-42fcbde7-2ee2-4f08-a1c8-38ea55bf9602.mov" %}
