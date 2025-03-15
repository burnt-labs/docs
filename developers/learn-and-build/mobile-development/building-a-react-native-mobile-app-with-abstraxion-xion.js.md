# Building a React Native Mobile App with Abstraxion (Xion.js)

Mobile apps are an integral part of modern life, and therefore integrating blockchain technology into mobile applications is a crucial step toward mass adoption. This guide will walk you through creating a React Native app using the [**Abstraxion** authentication system](https://github.com/burnt-labs/xion.js), based on the [**Abstraxion Expo Demo**](https://github.com/burnt-labs/abstraxion-expo-demo). By the end of this guide, you'll have a functional app that leverages **Abstraxion** for blockchain authentication and transaction signing.



## Prerequisites

Before you begin, ensure you have the following installed:

* [**Node.js** (LTS version recommended) and **NPM**](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
* **Android Emulator** or **iOS Simulator** (or a physical device for testing)



## Project Setup

### Create a New Expo Project

Start by creating a new Expo project using the default template. This template is ideal for multi-screen apps.

```bash
npx create-expo-app@latest abstraxion-app --template default
```

You’ll be prompted to name your app. Once the setup is complete, navigate into the project directory:

```bash
cd abstraxion-app
```



### Install Required Dependencies

Install the necessary dependencies for **Abstraxion** and other required libraries:

```bash
npm install @burnt-labs/abstraxion-react-native @react-native-async-storage/async-storage@2.1.2 react-native-get-random-values react-native-libsodium react-native-quick-crypto expo-dev-client
```

These libraries enable blockchain authentication, cryptographic operations, and secure storage.



### Update Project Configuration

#### Modify `app.json`

Update the `app.json` file to configure your app’s package name and bundle identifier.

**For Android:**

Add the following under the `"android"` section:

```json
"package": "com.anonymous.abstraxionexpodemo"
```

**For iOS:**

Add the following under the `"ios"` section:

```json
"bundleIdentifier": "com.anonymous.abstraxionexpodemo"
```



### Update the Main Tab (`index.tsx`)

Replace the contents of the `index.tsx` file with the following code to integrate Abstraxion authentication and transaction signing:

```typescript
import { useState } from "react";
import { View, StyleSheet, Text, TouchableOpacity, Alert } from "react-native";
import {
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion-react-native";

export default function Index() {
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



### Update the Layout (`_layout.tsx`)

Replace the contents of `app/_layout.tsx` with the following code to set up the Abstraxion provider and theme:

```typescript
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

SplashScreen.preventAutoHideAsync();

const treasuryConfig = {
  treasury: "xion1r0tt64mdld2svywzeaf4pa7ezsg6agkyajk48ea398njywdl28rs3jhvry",
  gasPrice: "0.001uxion",
  rpcUrl: "https://rpc.xion-testnet-2.burnt.com:443",
  restUrl: "https://api.xion-testnet-2.burnt.com:443",
  callbackUrl: "abstraxion-expo-demo://",
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

#### 3. **New `treasuryConfig` Object**

* Contains configuration for interacting with the **XION blockchain**, including:
  * **Treasury address**: `"xion1r0tt64mdld2svywzeaf4pa7ezsg6agkyajk48ea398njywdl28rs3jhvry"`
  * **Gas price**: `"0.001uxion"`
  * **RPC & REST URLs**: Connecting to **XION testnet**
  * **Callback URL**: `"abstraxion-expo-demo://"` for handling deep linking.

#### 4. **Introduction of `AbstraxionProvider`**

* Wraps the application inside `<AbstraxionProvider config={treasuryConfig}>`, providing the app with **Abstraxion** functionalities.
* This allows interaction with **XION treasury**, as seen in the newly added **`treasuryConfig`**.



### Add Metro Configuration

Create a `metro.config.js` file in the root of your project to configure Metro:

```javascript
const { getDefaultConfig } = require("expo/metro-config");
const {
  withLibsodiumResolver,
} = require("@burnt-labs/abstraxion-react-native/metro.libsodium");

const config = getDefaultConfig(__dirname);
module.exports = withLibsodiumResolver(config);
```



## Run the Project

To build and run the app on your emulator or device, use the following commands:

**For Android:**

```bash
npx expo run:android
```

**For iOS:**

```bash
npx expo run:ios
```



## Video Demo

For a visual walkthrough of the app, check out this video demo&#x20;

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FB5Z5ijJgMx0GJO3l1Il9%2Fuploads%2F7t2NOuNYZaDnd1CaUcBt%2FAbstraxtion%20React%20Native%20Demo.mp4?alt=media&token=9a54fb1c-cfc2-467b-bbfd-23e04843f926" %}
