# Verification Infrastructure

### The Truth Engine: Verify Anything on the Internet, Privately

XION's verification infrastructure, collectively called the Truth Engine, is what makes XION useful. It applies zero-knowledge proofs to three distinct data sources, enabling applications to verify any data on the internet without exposing the underlying information.

#### Why Verification Matters for Developers

Traditional applications face a fundamental limitation: they cannot verify external data without trusting a centralized authority or requiring users to hand over sensitive information. Want to verify a user's income? They upload bank statements. Want to confirm their reputation on another platform? They share login credentials. Want to know if they are a real human? You rely on CAPTCHAs that bots increasingly bypass.

Zero-knowledge verification changes this entirely. Your application can receive cryptographic proof that a claim is true without ever accessing the data behind it. The proof is mathematically guaranteed to be authentic, meaning the user cannot fabricate or tamper with it.

This creates an entirely new class of applications that were previously impossible to build:

* Loyalty programs that recognize competitor status without the competitor's cooperation
* Advertising platforms that verify real humans without invasive tracking
* Lending applications that confirm income ranges without seeing exact figures
* Marketplaces where reputation is portable and verifiable across platforms
* Data monetization systems where users control exactly what they share

#### How It Works (High Level)

The verification flow follows a consistent pattern across all three pillars of the Truth Engine:

1. **User generates proof.** The user's device creates a zero-knowledge proof from a data source (website, email, or mobile app). This happens client-side; the raw data never leaves the user's device.
2. **Proof is submitted on-chain.** The ZK proof is submitted to XION where it is verified by the protocol. If valid, the verified claim is recorded on-chain as an attestation.
3. **Application reads the attestation.** Your smart contract or application queries the on-chain attestation to confirm the claim. You receive a binary answer ("this user has a 4.8+ Uber rating") without any access to the underlying data ("their exact rating, trip history, or account details").
4. **Application takes action.** Based on the verified claim, your application triggers logic: unlock a reward, grant access, adjust pricing, issue credentials, or any other programmable response.

#### The Three Verification Modules

XION's Truth Engine provides three complementary verification technologies. Together, they allow applications to verify virtually any data on the internet privately.

| Module                    | Data Source | What It Verifies                                | Example                                                           |
| ------------------------- | ----------- | ----------------------------------------------- | ----------------------------------------------------------------- |
| **zkTLS**     | Any website | Data displayed in TLS-encrypted web sessions    | Uber rating, bank balance range, social media metrics             |
| **DKIM Module** (zkEmail) | Email inbox | Email contents authenticated by DKIM signatures | Purchase receipts, account confirmations, employment verification |
| **App Attestations**      | Mobile apps | In-app data and state                           | Fitness achievements, delivery history, streaming habits          |

See the dedicated pages for [zkTLS](./zktls.md), [zkEmail (DKIM)](./dkim-module-zkemail.md), and App Attestations.

#### Developer Integration Pattern

All three verification technologies follow the same high-level integration pattern:

```
// Pseudocode: Generic verification flow

// 1. Your frontend requests a proof from the user
const proofRequest = {
  module: "zk_tls" | "dkim" | "app_attestation",
  claim: "uber_rating_above_4.5",
  // Module-specific parameters
};

// 2. User generates proof client-side (raw data never leaves their device)
const proof = await userGenerateProof(proofRequest);

// 3. Proof is submitted and verified on-chain
const attestation = await submitProofOnChain(proof);

// 4. Your smart contract reads the attestation
// In your CosmWasm contract:
// query the attestation registry for the user's verified claim
// execute logic based on the result
```

Specific integration guides, SDKs, example contracts, and client libraries for each technology are available in the [Developer Documentation](../developers/).

#### Composability

Verification proofs are composable. A single user can generate multiple proofs from different sources and combine them into a rich, verified profile:

* Prove Uber rating (zkTLS) + Prove income range (zkEmail) + Prove fitness activity (App Attestation) = Verified, multi-dimensional user profile with zero sensitive data exposed.

Applications can require any combination of proofs before granting access or triggering logic. This composability is what enables complex use cases like privacy-preserving underwriting, multi-factor reputation systems, and cross-platform loyalty.
