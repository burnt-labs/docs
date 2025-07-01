# Abstraxion x Reclaim Integration

This guide explains how to integrate Abstraxion with Reclaim Protocol to enable zkTLS-based verification in your mobile applications. The integration allows users to prove real-world facts (like their Twitter follower count) without compromising privacy.

## Overview

The Abstraxion x Reclaim integration combines:
- **Abstraxion**: XION's account abstraction solution for seamless user authentication
- **Reclaim Protocol**: zkTLS technology for privacy-preserving verification of web2 data

This integration enables mobile apps to verify user data from web2 sources while maintaining privacy and security.

## Key Components

### Environment Configuration

The integration requires the following environment variables:

```bash
# Treasury contract for gasless transactions
EXPO_PUBLIC_TREASURY_CONTRACT_ADDRESS="your-treasury-contract-address"

# XION network endpoints
EXPO_PUBLIC_RPC_ENDPOINT="https://rpc.xion-testnet-2.burnt.com:443"
EXPO_PUBLIC_REST_ENDPOINT="https://api.xion-testnet-2.burnt.com"

# Reclaim Protocol credentials
EXPO_PUBLIC_RECLAIM_APP_ID="your-reclaim-app-id"
EXPO_PUBLIC_RECLAIM_APP_SECRET="your-reclaim-secret"
EXPO_PUBLIC_RECLAIM_PROVIDER_ID="your-reclaim-provider-id"

# RUM contract for storing verified proofs
EXPO_PUBLIC_RUM_CONTRACT_ADDRESS="your-rum-contract-address"
```

### Android Configuration

The integration includes Android-specific configuration for building and packaging:

1. **Build Configuration** (`android/app/build.gradle`):
   - Configured for React Native and Expo modules
   - Includes signing configurations for release builds
   - Optimized packaging options for APK size

2. **Ignore Files** (`android/.gitignore`):
   - Excludes build artifacts and local configuration files
   - Prevents sensitive signing keys from being committed

## Implementation Steps

### 1. Set Up Reclaim Application

1. Create an account at [Reclaim Protocol](https://dev.reclaimprotocol.org/explore)
2. Create a new application
3. Add the desired data provider (e.g., Twitter User Profile)
4. Save your Application ID, Secret, and Provider ID

### 2. Deploy Required Contracts

1. Deploy a Treasury contract for gasless transactions
2. Deploy a RUM (Reclaim User Map) contract for storing verified proofs
3. Configure the Treasury contract to grant permissions to the RUM contract

### 3. Configure Your Mobile App

1. Clone the repository and install dependencies
2. Create `.env.local` file with your configuration
3. Build the application for your target platform:

```bash
# For Android
npx expo run:android

# For iOS
npx expo run:ios
```

### 4. Integrate Abstraxion Authentication

The integration uses Abstraxion for seamless user authentication:

```javascript
import { useAbstraxionAccount, useAbstraxionSigningClient } from "@burnt-labs/abstraxion";

// Get authenticated account
const { data: account } = useAbstraxionAccount();
const { client } = useAbstraxionSigningClient();
```

### 5. Implement Reclaim Verification

Users can verify their data through Reclaim:

```javascript
// Initialize Reclaim SDK
const reclaimClient = new ReclaimClient(appId, appSecret);

// Request proof from user
const proof = await reclaimClient.requestProof(providerId);

// Store verified proof in RUM contract
await client.execute(
  account.bech32Address,
  rumContractAddress,
  { update: { value: proof } },
  "auto"
);
```

## Security Considerations

1. **Private Keys**: Never commit signing configurations or private keys
2. **Environment Variables**: Use `.env.local` for sensitive configuration
3. **Contract Permissions**: Ensure Treasury contract only grants necessary permissions
4. **Proof Validation**: Always verify proofs through the on-chain verification contract

## Next Steps

- Explore additional Reclaim providers for different data sources
- Implement custom verification logic in your smart contracts
- Add error handling and user feedback for better UX

For more detailed implementation examples, see the [complete demo repository](https://github.com/burnt-labs/abstraxion-reclaim-demo).