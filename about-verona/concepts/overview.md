# Overview

### What is Verona?

Verona is the **intelligence layer for AI**. It turns user-owned, cryptographically verified facts into reusable context that authorized agents and applications can act on—without exposing raw private data.

The network behind Verona runs on Cosmos SDK, CometBFT, and CosmWasm infrastructure. It combines zero-knowledge verification (the **Truth Engine**) with **Generalized Abstraction** so verification is both powerful and accessible to mainstream users.

**Making AI Intelligent** captures the goal: agents need grounded facts, not unverified scrapes. Verona supplies verification rails, portable user context (Data Backpack direction), and product surfaces (EarnOS, Burnt Verified, Ask & MCP) on top of the same developer stack Meta Account and CosmWasm builders use today.

The internet has a trust crisis: bots, ad fraud, and platforms monetizing user data while users receive little in return. Traditional Web2 infrastructure cannot fix this at scale—verification usually requires sharing underlying data, central authorities can fail, and tampering is hard to detect.

Verona addresses this with Web3 verification. Zero-knowledge proofs let data be checked cryptographically without revealing it, without trusting a single intermediary, and without tampering. Receivers of a proof can trust the claim knowing the user could not have fabricated or altered the source.

Powerful verification is useless if people cannot reach it. That is why Generalized Abstraction is embedded at the protocol level: accounts, signatures, gas, payments, devices, and interoperability are abstracted. Users log in with email, pay with familiar methods, and avoid blockchain complexity.

The result is infrastructure that is **useful** (verification for real-world problems) and **usable** (invisible to end users)—now framed for developers building agentic and intelligent applications.

### Why Verona Exists

Two problems block mainstream intelligent applications:

**1. Intelligence is not grounded enough.** Models and agents lack durable, verifiable user context. Verona makes verification the core primitive—identity without doxxing, portable reputation, user-controlled data sharing, and fraud-resistant workflows.

**2. Web3 is still too visible.** Even improved UX often exposes wallets, gas, and chain concepts. Verona’s abstraction layer keeps the network as infrastructure while apps and agents consume verified facts through familiar interfaces.

### Architecture at a Glance

Verona’s architecture has two primary layers:

**Verification Layer (Truth Engine)**

The Truth Engine is verification infrastructure built into the protocol. It applies zero-knowledge proofs to three data sources:

* **zkTLS:** Verifies real-world data from websites using zero-knowledge proofs over TLS sessions. Prove ratings, balance ranges, loyalty tiers, or social metrics without exposing underlying information.
* **DKIM Module (zkEmail):** Verifies email contents using DKIM signatures email providers already attach. Prove receipts, notifications, or employment verification without revealing full messages.
* **App Attestations:** Verifies mobile application data through cryptographic attestation of in-app state. Prove fitness, delivery, or streaming habits from mobile apps.

**Abstraction Layer**

Generalized Abstraction embeds critical network functionality in the protocol:

* **Account Abstraction:** Meta Accounts replace traditional wallets. Users authenticate via email, FaceID, passkeys, or social login.
* **Signature Abstraction:** Supports multiple cryptographic curves. Applications request familiar actions ("confirm purchase"), not raw transaction signatures.
* **Gas Abstraction:** Apps sponsor transactions, accept fees in various tokens, or embed fees in pricing. Users rarely see gas.
* **Payment Abstraction:** Credit cards, Apple Pay, and fiat displays—with conversion and settlement handled invisibly.
* **Device Abstraction:** Seamless cross-device access without browser extensions or device-specific setup.
* **Interoperability Abstraction:** Cross-ecosystem operations without users managing bridges, wrapped tokens, or multiple accounts.

<figure><img src="../../.gitbook/assets/XION Infrastructure Graphic Chain Abstraction 2.png" alt=""><figcaption></figcaption></figure>

**Consensus & Execution**

The network runs on Cosmos SDK with CometBFT consensus and CosmWasm smart contract execution. It uses Proof of Stake with inflation calculated against staked tokens. IBC (Inter-Blockchain Communication) provides native interoperability. 

### Key Differentiators

* **Integrated verification infrastructure.** zkTLS, zkEmail, and app attestations are protocol-level modules—not bolt-on services.
* **Protocol-level abstraction.** Account, gas, signature, payment, and device abstraction are part of the network, not app-layer patches.
* **Real-world traction.** Production verification products and ecosystem applications already demonstrate Verona's verification-and-reuse model.
* **Intelligence-layer surfaces.** EarnOS, Burnt Verified, Ask & MCP, and Data Backpack package verification for users and agents.

See [Surfaces](../surfaces/README.md) and [Rebrand: XION → Verona](../rebrand-from-xion.md) for the product narrative.

### For Developers: What This Means

Building on Verona gives you:

1. **Verification primitives** to prove real-world data privately—credentials, data sources, and trust between parties without seeing sensitive underlying information.
2. **Abstraction primitives** for Web2-quality UX—Meta Accounts, gas sponsorship, familiar login and payment flows.
3. **Agent-ready tooling** via the [Verona Agent Toolkit](../../developers/tools/verona-toolkit.md) and [For AI Agents](../ai-agents.md).
4. **CosmWasm smart contracts** for on-chain logic with mature Rust tooling.
5. **An existing ecosystem** to launch into rather than starting from zero.

Continue to [Truth Engine](verification-infrastructure/README.md), [Meta Accounts](meta-accounts.md), and [Build on Verona](../../developers/overview.md).
