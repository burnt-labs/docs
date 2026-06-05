---
description: Key Terms and Concepts
---

# Architecture & Tech Glossary

### Verification Terms

| Term                           | Definition                                                                                                                                                           |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Truth Engine**               | XION's verification infrastructure comprising zkTLS (web data), zkEmail (authenticated email), and App Attestations (mobile apps). Enables private, cryptographic verification of real-world data from the internet. |
| **zkTLS**                      | Zero-knowledge verification for web data. Creates proofs over TLS-encrypted sessions from any website, enabling claims about ratings, balances, loyalty status, and more without exposing raw data.              |
| **DKIM Module (zkEmail)**      | Verification module that creates zero-knowledge proofs over email contents using DKIM signatures. Proves email-based claims without exposing the email.              |
| **App Attestation**            | Verification module that creates cryptographic proofs of mobile application data. Proves in-app state without exposing app credentials or full data.                 |
| **Attestation**                | An on-chain record of a verified claim. Created when a zero-knowledge proof is successfully verified by the protocol. Queryable by smart contracts.                  |
| **Zero-Knowledge Proof (ZKP)** | A cryptographic method that allows one party to prove a statement is true without revealing any information beyond the truth of the statement itself.                |
| **Selective Disclosure**       | The ability to prove specific claims from a data source while keeping all other data private. E.g., proving "income above $100K" without revealing exact income.     |
| **Composable Proofs**          | Multiple attestations from different verification modules combined to create a multi-dimensional verified profile for a single user.                                 |

### Abstraction Terms

| Term                        | Definition                                                                                                                                                                                      |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Generalized Abstraction** | XION's protocol-level abstraction of accounts, signatures, gas, payments, devices, and interoperability. Makes blockchain invisible to end users.                                               |
| **Meta Account**            | A CosmWasm smart contract that serves as a user's on-chain account. Supports modular authentication, programmable permissions, and account recovery. Replaces traditional wallets.              |
| **Authenticator**           | A module within a Meta Account that validates a specific authentication method (email JWT, WebAuthn/passkeys, cryptographic keys, etc.). Multiple authenticators can be registered per account. |
| **Session Key**             | A temporary, permission-scoped keypair that allows an application to execute specific operations on behalf of a user without requiring approval for each transaction.                           |
| **Treasury Contract**       | A smart contract deployed by an application developer that sponsors gas fees for users. The primary mechanism for gas abstraction on XION.                                                      |
| **Parameterized Fee Layer** | XION's gas abstraction system that allows fees to be paid in any token, sponsored by applications, or built into product pricing.                                                               |
| **Abstraxion SDK**          | XION's developer SDK that wraps all abstraction layers (account, gas, signature, payment, device) into a simple integration for web and mobile applications.                                    |

### Infrastructure Terms

| Term                     | Definition                                                                                                                                                         |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **CosmWasm**             | The smart contract execution environment used by XION. Contracts are written in Rust and compiled to WebAssembly (Wasm).                                           |
| **CometBFT**             | The Byzantine Fault Tolerant consensus engine underlying XION (formerly Tendermint).                                                                               |
| **Cosmos SDK**           | The framework on which XION is built. Provides modular blockchain application development.                                                                         |
| **IBC**                  | Inter-Blockchain Communication protocol. Enables native cross-chain messaging and asset transfers between XION and other Cosmos ecosystem chains.                  |
| **Proof of Stake (PoS)** | XION's consensus mechanism. Validators stake $XION tokens to participate in block production and earn rewards. Inflation is calculated only against staked tokens. |
|                          |                                                                                                                                                                    |

### Economic Terms

| Term               | Definition                                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **$XION**          | The native token of the XION network. Used for staking, governance, transaction fees, and as the settlement layer for ecosystem activity.              |
| **Revenue Share**  | Agreements between XION and ecosystem applications where application revenue flows back to the protocol. Currently 6+ agreements generating $35M+ ARR. |
| **Buyback & Burn** | The mechanism by which ecosystem revenue is used to purchase and permanently remove $XION tokens from circulation, creating deflationary pressure.     |
