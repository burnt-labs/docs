---
description: Zero-knowledge verification — verify once, reuse everywhere
icon: shield-halved
---

# Truth Engine

### The Truth Engine

The **Truth Engine** is Verona's verification stack. It turns internet data into **provable facts**, stores them on the decentralized network the user owns, and lets any authorized agent or service **reuse** them without ever holding the data underneath.

**Verify once · Reuse everywhere · Expose nothing.**

#### Why verification matters

Traditional applications cannot verify external data without trusting a centralized authority or requiring users to hand over sensitive information. Want income for a lease? Upload bank statements. Want reputation from another platform? Share login credentials. Every new app re-verifies; every broker stores another copy.

Zero-knowledge verification changes this. Your application receives cryptographic proof that a claim is true without accessing the data behind it. The user cannot fabricate or tamper with the proof. One verification becomes a **durable, user-owned attestation** reusable across the network.

This enables applications that were previously impossible or impractical:

* Loyalty and reputation portable across platforms without source cooperation
* Lending and underwriting on income ranges without exact figures
* Enterprise decisions on cryptographically verified records—non-custodial, no document storage
* Agent workflows that act on provable facts without becoming data custodians
* User-controlled sharing of verified attributes instead of raw exports

#### How it works (high level)

The flow is consistent across all Truth Engine modules:

1. **User generates proof.** The user's device creates a zero-knowledge proof from a data source (website, email, passport, or mobile app). Raw data stays on the device.
2. **Proof is verified on-chain.** The proof is submitted to the Verona network and verified by the protocol. Valid claims are recorded as attestations bound to the user's Meta Account.
3. **Application or agent reads the attestation.** Your contract, API, or agent runtime queries the attestation and receives a scoped answer—not the underlying record.
4. **Action and settlement.** Based on the verified claim, your logic unlocks access, triggers an agent action, adjusts pricing, issues credentials, or settles on chain.

#### The four verification surfaces

| Surface       | Module               | Data source                | Example claim                                   |
| ------------- | -------------------- | -------------------------- | ----------------------------------------------- |
| **Websites**  | **zkTLS**            | TLS-encrypted web sessions | Uber rating above 4.5, balance in range         |
| **Email**     | **zkEmail** (DKIM)   | DKIM-signed email          | Payroll notification, purchase receipt          |
| **Passports** | **zkPassport**       | Identity documents         | Age or citizenship without copying the document |
| **Apps**      | **App Attestations** | Mobile app state           | Fitness activity, delivery history              |

Developer integration guides today cover [zkTLS](zktls.md), [zkEmail (DKIM)](dkim-module-zkemail.md), and [App Attestations](app-attestations.md). zkPassport is part of the Truth Engine roadmap; contact the team for early access patterns.

#### Developer integration pattern

All modules follow the same high-level integration pattern:

```
// Pseudocode: Generic verification flow

// 1. Your frontend requests a proof from the user
const proofRequest = {
  module: "zk_tls" | "dkim" | "zk_passport" | "app_attestation",
  claim: "uber_rating_above_4.5",
  // Module-specific parameters
};

// 2. User generates proof client-side (raw data never leaves their device)
const proof = await userGenerateProof(proofRequest);

// 3. Proof is submitted and verified on-chain
const attestation = await submitProofOnChain(proof);

// 4. Your smart contract or agent reads the attestation
// query the attestation registry for the user's verified claim
// execute logic based on the result
```

Specific integration guides, SDKs, and example contracts: [Build on Verona — Verification](../../../developers/verification/).

#### Composability

Proofs compose. A user can combine attestations from different surfaces into a rich, verified profile—Uber rating (zkTLS) + income range (zkEmail) + identity attribute (zkPassport) + fitness activity (App Attestation)—with zero sensitive data exposed.

Applications require any combination of proofs before granting access or triggering agent logic. That composability powers privacy-preserving underwriting, multi-factor reputation, and cross-platform loyalty on one network.
