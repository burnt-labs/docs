---
description: Key Terms and Concepts
---

# Architecture & Tech Glossary

### Product Terms

| Term      | Definition                                                                                                                                                                      |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Verona** | The intelligence-layer brand and documentation name for the network. Narrative and product surfaces (EarnOS, Burnt Verified, Ask & MCP, Data Backpack); on-chain identifiers remain **XION**. |
| **Truth Engine** | Verification infrastructure comprising zkTLS (web data), zkEmail (authenticated email), and App Attestations (mobile apps). Enables private, cryptographic verification of real-world data. |
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
| **Generalized Abstraction** | The Verona network's protocol-level abstraction of accounts, signatures, gas, payments, devices, and interoperability. Makes blockchain invisible to end users.                                               |
| **Meta Account**            | A CosmWasm smart contract that serves as a user's on-chain account. Supports modular authentication, programmable permissions, and account recovery. Replaces traditional wallets.              |
| **Authenticator**           | A module within a Meta Account that validates a specific authentication method (email JWT, WebAuthn/passkeys, cryptographic keys, etc.). Multiple authenticators can be registered per account. |
| **Session Key**             | A temporary, permission-scoped keypair that allows an application to execute specific operations on behalf of a user without requiring approval for each transaction.                           |
| **Treasury Contract**       | A smart contract deployed by an application developer that sponsors gas fees for users. The primary mechanism for gas abstraction on the Verona network.                                                      |
| **Parameterized Fee Layer** | The Verona network's gas abstraction system that allows fees to be paid in any token, sponsored by applications, or built into product pricing.                                                               |
| **Abstraxion SDK**          | The Verona network's developer SDK that wraps all abstraction layers (account, gas, signature, payment, device) into a simple integration for web and mobile applications.                                    |

### Infrastructure Terms

| Term                     | Definition                                                                                                                                                         |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **CosmWasm**             | The smart contract execution environment used by the Verona network. Contracts are written in Rust and compiled to WebAssembly (Wasm).                                           |
| **CometBFT**             | The Byzantine Fault Tolerant consensus engine underlying the Verona network (formerly Tendermint).                                                                               |
| **Cosmos SDK**           | The framework on which the Verona network is built. Provides modular blockchain application development.                                                                         |
| **IBC**                  | Inter-Blockchain Communication protocol. Enables native cross-chain messaging and asset transfers between the Verona network and other Cosmos ecosystem chains.                  |
| **Proof of Stake (PoS)** | The Verona network's consensus mechanism. Validators stake $VERONA (Previously $XION) to participate in block production and earn rewards. Inflation is calculated only against staked tokens. |
|                          |                                                                                                                                                                    |

### Economic Terms

| Term               | Definition                                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **$VERONA**        | The native token of the Verona network (Previously $XION). Used for staking, governance, and on-chain settlement (denom `uxion`). End users typically experience **gasless** flows when applications sponsor fees via Treasury contracts; see [Treasury contracts](../../developers/accounts/getting-started/treasury-contracts.md). |
| **Revenue Share**  | Agreements where application or verification revenue can flow back to the protocol. Specific figures should only be published after marketing and legal approval. |
| **Buyback & Burn** | The mechanism by which ecosystem revenue is used to purchase and permanently remove $VERONA from circulation, creating deflationary pressure.     |
