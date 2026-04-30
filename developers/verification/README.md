---
icon: badge-check
---

# Verification

This section covers how to integrate XION's verification infrastructure (the Truth Engine) into your application. Whether you are building a consumer app that needs to verify user identity, an advertising platform that needs to confirm real humans, or a DeFi protocol that needs to assess creditworthiness, these guides walk you through the full integration.

### ZK Infrastructure

#### [Publishing ZK Verification Keys](publishing-zk-verification-keys.md)

Publish and manage verification keys (vkeys) for custom ZK circuits on XION. This is **infrastructure-level** guidance for advanced use cases requiring custom ZK proving systems (Groth16, Gnark, UltraHonk).

> **Note**: Most applications use pre-configured vkeys for standard verification features (zkTLS, zkEmail, App Attestations). This guide is for developers building custom ZK circuits.

### Explore the verification modules

#### [Internet Verification (ZK Module, zkTLS)](internet-verification/)

Verify data from any website using zero-knowledge proofs over TLS-encrypted sessions. Prove user credentials, financial data, and platform reputation without exposing sensitive information.

* **Reclaim Integration**: Verify social media stats, financial data, and platform activity
* **Opacity Integration**: Alternative zkTLS provider for internet verification

#### Email Verification (DKIM Module, ZK Email)

Verify email contents cryptographically using existing DKIM signatures. Prove purchase receipts, employment verification, and account ownership without accessing the user's inbox.&#x20;

_**(Guides coming soon)**_

#### Mobile Verification (App Attestations)

Verify mobile application data with cryptographic proofs. Prove rideshare ratings, fitness metrics, and app usage from mobile devices.

_**(Guides coming soon)**_

