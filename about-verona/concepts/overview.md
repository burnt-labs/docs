---
description: Intelligence layer overview — Making AI Intelligent
icon: eye
---

# What is Verona?

### What is Verona?

**Making AI Intelligent.** Verona is the **intelligence layer for AI**—a network where facts about the real world are proven once, owned by the user, and reusable by any agent they authorize, with the data underneath never exposed.

Verona makes user-verified data **portable, private, and programmable**, so agents can transact on information the user actually owns. In public language, Verona is a **network and a layer**, not “another chain.” The chain is how it works; the layer is what it is.

**Verify once · Reuse everywhere · Expose nothing.**

### The gap

Today's AI is capable—until it counts. Agents need **provable information** to act and cannot store sensitive data. Scraped text, self-reported forms, and one-off KYC checks do not scale: every new app makes the user prove the same thing again, and every broker stores another copy.

Verona treats a verification as a **durable, user-owned asset** on the network—not a disposable check that dies on use.

### The unlock

**One proof, and the whole network trusts it.** Verify a fact once, at its source. You own it; any agent you authorize acts on it; the data underneath never shows.

The **Truth Engine** generates zero-knowledge proofs across four surfaces:

| Surface       | Module           | What it proves                                             |
| ------------- | ---------------- | ---------------------------------------------------------- |
| **Websites**  | zkTLS            | What a site shows about you—without handing over the login |
| **Email**     | zkEmail (DKIM)   | What landed in your inbox—without opening it up            |
| **Passports** | zkPassport       | Who you are—without copying the document                   |
| **Apps**      | App Attestations | What you did in an app—without exposing the account        |

Verified claims are recorded on the network, bound to the user's **Meta Account**, and reusable by any application or agent the user permits.

### Why abstraction still matters

Powerful verification is useless if people cannot reach it. **Generalized Abstraction** is embedded at the protocol level: accounts, signatures, gas, payments, devices, and interoperability are abstracted so users log in with email, pay with familiar methods, and rarely see blockchain mechanics.

The network runs on Cosmos SDK, CometBFT, and CosmWasm. Builders still use chain-precise identifiers where required (`xion-testnet-2`, `xiond`, on-chain addresses).

### Who Verona is for

**Humans** — Turn everyday proof into assets you own. [Ero](../surfaces/earnos.md) (by EarnOS) is the live consumer surface: verified activity without exposing raw data.

**Enterprises** — Make decisions on records cryptographically verified at the source. No one touches the data underneath; you never carry the liability. See [Burnt Verified](../surfaces/burnt-verified.md).

**Agents** — Act on what the user has already verified, without rechecking or holding sensitive data. Prescription refills, flight check-in, loan workflows, and similar actions become possible when context is provable and permissioned.

**Developers** — Build on a network of verified facts. Intelligence owned by users, permissioned to your app. Read a proof, have an agent act on it, settle when it acts—proof, action, and settlement in one flow. No collecting data, no storing it, no carrying the liability.

### Architecture at a glance

**Verification layer (Truth Engine)**

Protocol-level zero-knowledge verification across websites, email, passports, and apps. See [Truth Engine](verification-infrastructure/).

**Abstraction layer**

Generalized Abstraction embeds account, signature, gas, payment, device, and interoperability abstraction in the protocol. See [Meta Accounts](meta-accounts.md) and [Intro to Account Abstraction](intro-to-account-abstraction.md).

<figure><img src="../../.gitbook/assets/XION Infrastructure Graphic Chain Abstraction 2.png" alt=""><figcaption></figcaption></figure>

**Consensus and execution**

Cosmos SDK with CometBFT consensus and CosmWasm smart contract execution. Proof of Stake with inflation calculated against staked tokens. IBC provides native interoperability.

### Surfaces into one network

Product **surfaces** are doors into the same Truth Engine—not separate products:

* [**Ero**](../surfaces/earnos.md) (EarnOS) — Consumer proof: verified participation and user-owned data flows.
* [**Burnt Verified**](../surfaces/burnt-verified.md) — Enterprise proof: income, employment, and credential verification without document custody.

See [Surfaces](../surfaces/) for Phase 1 entry points.

### For developers: what this means

Building on Verona gives you:

1. **Verification primitives** — Prove real-world data privately; trust between parties without seeing sensitive underlying information.
2. **Reusable attestations** — One verification, many authorized consumers—agents and apps query proofs instead of re-running KYC.
3. **Abstraction primitives** — Meta Accounts, gas sponsorship, familiar login and payment flows.
4. **Agent-ready tooling** — [Verona Agent Toolkit](../../developers/tools/verona-toolkit.md) and [For AI Agents](../ai-agents.md).
5. **CosmWasm smart contracts** — On-chain logic with mature Rust tooling.
6. **An existing ecosystem** — Production verification usage across brands including Uber, Amazon, and Nike, and millions of users on the engine.

### Network economics (high level)

$VERONA secures the network through Proof of Stake. Because end-user flows are often gas-sponsored, token value is designed to track **network revenue and usage**—revenue share from ecosystem apps, enterprise verification, and agent activity—rather than raw transaction gas alone. Stakers and validators participate in that growth through staking rewards and governance. See [Architecture & Glossary](architecture-and-tech-glossary.md) and [Rebrand: XION → Verona](../../others/rebrand-from-xion.md) for rename and Phase 2 boundaries.

Continue to [Truth Engine](verification-infrastructure/), [Meta Accounts](meta-accounts.md), and [Build on Verona](../../developers/overview.md).
