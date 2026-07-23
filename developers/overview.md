---
icon: lightbulb
---

# Overview

Welcome to **Build on Verona**. If you're coming from Web2 development, this page explains the network, primitives, and how to ship consumer and agentic applications.

**Verona** is the intelligence layer for AI. In narrative, it is a **network and layer**—not “another chain.” The underlying network and CLI still use **XION** on-chain naming (`xion-testnet-2`, `xiond`, on-chain addresses). See [What is Verona?](../about-verona/concepts/overview.md).

### What you build on

Verona gives developers a network of **verified facts** that users own and permission to apps and agents. Read a proof, have an agent act on it, settle when it acts—proof, action, and settlement on one network. Account, verification, computation, and payment primitives make that flow usable without collecting or storing sensitive source data.

Think of the stack as infrastructure for applications that need:

* **User authentication** (like Auth0 or Firebase)
* **Reusable verification** (like Plaid or ID verification APIs, but user-owned and reusable)
* **Payment processing** (like Stripe or Square)
* **Serverless compute** (like AWS Lambda or Cloud Functions)

All running on verifiable infrastructure, with technical identifiers and CLI flows still using `xiond` and on-chain identifiers.

### Core builder primitives

These primitives work together to create consumer and agentic applications:

| Pillar           | Web2 Analogy                 | What It Does                                                                                                                                       |
| ---------------- | ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Accounts**     | Auth0 / Firebase Auth        | Users sign up with email/Google/passkeys. No wallets, no seed phrases. You get a user identity that can own things and take actions.               |
| **Computation**  | AWS Lambda / Cloud Functions | Deploy backend logic (smart contracts) that runs trustlessly. Automate workflows, manage state, integrate with oracles and external data.          |
| **Verification** | Plaid / ID verification APIs | Prove real-world facts (social media stats, email ownership, financial data) without exposing raw data. Uses zero-knowledge proofs under the hood. |
| **Payments**     | Stripe / Square              | Accept credit cards, stablecoins, cross-chain payments. Users pay in dollars, you receive funds.                                                   |

#### Accounts: Web2-Style Authentication

**What it is:** Meta Accounts let your users sign up with email, Google, passkeys, or other familiar methods. Behind the scenes, each user gets a programmable account that can own assets, authorize transactions, and interact with smart contracts.

**Key benefits:**

* No wallets, no seed phrases, no gas fees for users
* Multiple authentication methods (email, Google, Apple, passkeys)
* OAuth2 integration for familiar account connection flows
* Gasless transactions through Treasury Contracts

**When to use:** Every Verona app starts here. Accounts are the foundation for user identity, authorization, and consent.

#### Computation: Trustless Backend Logic

**What it is:** Smart contracts on the Verona network run like serverless functions — they execute trustlessly, manage state, and can integrate with external data sources via oracles.

**Key benefits:**

* Deploy CosmWasm smart contracts
* Trustless execution (no need to trust a central server)
* Integrate with oracles (Pyth), indexers (SubQuery), and WebSockets
* Token Factory for creating custom tokens

**When to use:** Build automated workflows, manage on-chain state, create programmable money, or implement complex business logic that needs to run trustlessly.

#### Verification: Prove Without Exposing

**What it is:** Verification lets users prove real-world facts without sharing sensitive data. For example, a user can prove they have 10,000 Twitter followers without giving you their Twitter password.

**Key benefits:**

* Zero-knowledge proofs protect user privacy
* Verify social media stats, email ownership, financial data
* Integrations with Reclaim (zkTLS) and Opacity
* Standard modules include zkTLS, zkEmail, and App Attestations, with implementation guides expanding as surfaces mature

**When to use:** Build apps that require verified identity, social proof, or reputation systems. Perfect for rewards programs, gated content, or trust-based marketplaces.

#### Payments: Accept Money, Any Way Users Want to Pay

**What it is:** The payment layer handles credit cards, stablecoins, and cross-network transfers. Users pay in their preferred method, and your application receives funds through the configured settlement flow.

**Key benefits:**

* Credit card payments via Crossmint integration
* Stablecoin support (USDC, USDT)
* Cross-chain payments via IBC
* No need for users to buy crypto first

**When to use:** Any app that needs to accept payments, subscriptions, or process transactions. Works seamlessly with Accounts and Computation layers.

### How These Pieces Fit Together

The real power comes from combining these primitives. Here are examples of how they compose:

#### Example 1: EarnOS-Style Rewards App (All 4 Pillars)

This example shows how you can build a social rewards platform that uses every pillar:

```
1. ACCOUNTS     → User signs up with Google, gets a Meta Account
2. VERIFICATION → User proves their Twitter follower count via zkTLS
                   (without sharing their password)
3. COMPUTATION  → Smart contract calculates reward based on verified data
4. PAYMENTS     → Reward is paid out in USDC, user can off-ramp to their bank
```

**The flow:**

* User authenticates with Google (Accounts)
* User connects Twitter and proves follower count privately (Verification)
* Your smart contract reads the verified data and calculates rewards (Computation)
* Rewards are automatically sent to the user's account (Payments)

**Why this works:** Each pillar handles a specific concern, and they work together seamlessly. The user never touches a wallet or pays gas fees.

#### Example 2: Subscription Content Platform (Accounts + Payments + Computation)

Build a Netflix-style platform with on-chain access control:

```
1. ACCOUNTS    → User signs in with email
2. PAYMENTS    → User pays $9.99/month via credit card (Crossmint)
3. COMPUTATION → Smart contract manages access: checks subscription
                  status, gates content, handles renewals
```

**The flow:**

* User creates account with email (Accounts)
* User subscribes and pays monthly via credit card (Payments)
* Smart contract stores subscription status and gates content access (Computation)

**Why this works:** The smart contract acts as your subscription management system, running trustlessly without a central server. Users pay with credit cards, and you get on-chain payments.

#### Example 3: Adding Verona to an Existing Web App (Accounts + Payments)

You don't need to rebuild everything. Add Verona features incrementally:

```
1. ACCOUNTS → Add a Verona account connection alongside existing auth
              (via OAuth2 — works like "Connect with Google")
2. PAYMENTS → Enable crypto payments alongside Stripe,
              users pay with credit card via Crossmint
```

**The flow:**

* Keep your existing authentication, add Verona as a new connectable service (Accounts)
* Keep Stripe, add Crossmint for crypto payments (Payments)

**Why this works:** Verona integrates with your existing stack. You can adopt it gradually, starting with payments or authentication, then adding verification and agent-ready context over time.

#### Example 4: Verified Creator Marketplace (Accounts + Verification + Payments + Computation)

Build a marketplace where creators prove their credentials:

```
1. ACCOUNTS     → Creators sign up with social login
2. VERIFICATION → Creators prove YouTube subscriber count, email ownership
3. COMPUTATION  → Smart contract manages listings, escrow, and payouts
4. PAYMENTS     → Buyers pay creators via credit card or stablecoins
```

**The flow:**

* Creators authenticate and verify their credentials (Accounts + Verification)
* Smart contract manages marketplace logic and escrow (Computation)
* Payments flow through the contract to creators (Payments)

**Why this works:** Verification builds trust, Computation ensures fair transactions, Payments make it easy for buyers, and Accounts make it seamless for everyone.

### Getting Started

Now that you understand the big picture, here's how to start building:

1. **Start with Accounts**: Set up user authentication using Meta Accounts
   * [Meta Accounts Overview](accounts/getting-started/)
   * [Web App Getting Started](accounts/web-app/)
   * [OAuth2 App Getting Started](accounts/oauth2-app.md)
   * [Mobile App Getting Started](accounts/mobile-app/)
2. **Smart Contracts**: Build your backend logic
   * [Computation Layer Overview](computation/)
   * [Quick Start Guide](computation/quick-start/zero-to-dapp-in-5-minutes/)
   * [Local Development Guide](computation/local-development/)
   * [Re-using Existing Contracts](computation/re-using-existing-contracts/)
   * [Integrations](computation/integrations/)
3. **Add Verification**: Verify user credentials
   * [Verification Layer Overview](verification/)
   * [Internet Verification(zkTLS)](verification/internet-verification/)
4. **Add Payments**: Enable credit card and crypto payments
   * [Credit / Debit Card Integration](payments/credit-debit-cards/)

### For AI / Code Agents

{% hint style="info" %}
**Source over prose:** When package names, auth modes, or demo routes conflict with this site, prefer the [xion.js README](https://github.com/burnt-labs/xion.js) and [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) reference implementation. For native or server signing outside JavaScript, prefer [mob](https://github.com/burnt-labs/mob).
{% endhint %}

Code Agents should install **[verona-dev-plugin](https://github.com/burnt-labs/verona-dev-plugin)** for Verona integration skills and stack decision rules — follow [INSTALL.md](https://github.com/burnt-labs/verona-dev-plugin/blob/main/INSTALL.md) in the plugin repo. Do not rely on legacy bare `@burnt-labs/abstraxion` or `@burnt-labs/ui` names that may still appear in older pages.

* **App SDK source:** [xion.js](https://github.com/burnt-labs/xion.js) (`@burnt-labs/abstraxion-react`, `@burnt-labs/abstraxion-js`, `@burnt-labs/abstraxion-react-native`)
* **Native signing:** [mob](https://github.com/burnt-labs/mob)
* **Agent skills home:** [verona-dev-plugin](https://github.com/burnt-labs/verona-dev-plugin) ([INSTALL.md](https://github.com/burnt-labs/verona-dev-plugin/blob/main/INSTALL.md))
* **CLI automation (Treasury, OAuth2 clients):** [`verona-toolkit`](tools/verona-toolkit.md) — complementary to the Dev Plugin, not a replacement for its skills

Human-oriented Dev Plugin install: [For AI Agents](../about-verona/ai-agents.md). CLI walkthrough: [verona-toolkit](tools/verona-toolkit.md).

### Stack selection

Pick a default package or tool from the table below. Full decision rules (DR-1–DR-8) live in the Dev Plugin only — do not duplicate them here:

**[verona-dev-plugin `skills/verona-dev/references/`](https://github.com/burnt-labs/verona-dev-plugin/tree/main/skills/verona-dev/references)**

| Need | Package / tool |
| ---- | -------------- |
| React web | `@burnt-labs/abstraxion-react` |
| Non-React JS (Svelte, Vue, vanilla) | `@burnt-labs/abstraxion-js` |
| React Native / Expo | `@burnt-labs/abstraxion-react-native` |
| Native / server signing | [mob](https://github.com/burnt-labs/mob) |
| Headless OAuth2 | oauth2 flows — see plugin `references/` |
| Agent CLI | `verona-toolkit` ([releases](https://github.com/burnt-labs/verona-agent-toolkit/releases)) |
| Chain node / validator ops | `xiond` ([daemon CLI](tools/daemon-cli.md)) |

`@burnt-labs/abstraxion-core` is the connector and iframe protocol layer — use for headless or custom UI wiring, not as the primary React install.

### Tools & SDKs

Libraries and CLIs for app and automation workflows:

* [Tools & SDKs overview](tools/)
* [xion.js](https://github.com/burnt-labs/xion.js) — Web App and Mobile App guides under [Accounts](accounts/)
* [Mob signing library](tools/mob-signing-client.md)
* [verona-dev-plugin](https://github.com/burnt-labs/verona-dev-plugin) — Code Agent skills ([INSTALL.md](https://github.com/burnt-labs/verona-dev-plugin/blob/main/INSTALL.md)); stack rules in plugin `references/`
* [For AI Agents](../about-verona/ai-agents.md) — human-oriented Verona Dev Plugin install
* [Verona Agent Toolkit](tools/verona-toolkit.md) — CLI for Treasury, OAuth2 clients, and gasless automation
* [Daemon CLI (`xiond`)](tools/daemon-cli.md)

### Key Concepts for Web2 Developers

If you're coming from Web2, here are some concepts that map to what you already know:

| Network Term          | Web2 Equivalent                   | What It Means                                                        |
| --------------------- | --------------------------------- | -------------------------------------------------------------------- |
| **Meta Account**      | User Account                      | A user identity that can own assets and authorize actions            |
| **Treasury Contract** | App Permissions & Billing Config  | Manages what your app can do on behalf of users and who pays for gas |
| **Fee Grant**         | Computing Points                  | Your app pays transaction fees so users don't have to                |
| **Authz Grant**       | Delegated Permissions             | Users grant your app permission to perform specific actions          |
| **Smart Contract**    | Backend API / Serverless Function | Trustless code that runs on the blockchain                           |
| **Abstraxion**        | Auth & Transaction SDK            | The JavaScript SDK for integrating Meta Accounts into your app       |

### Why Build on Verona?

The network is designed for developers building consumer and verification-heavy applications—not only DeFi protocols. Here's what makes it different:

* **Familiar UX**: Your users don't need to know they're using blockchain technology
* **No Gas Fees for Users**: You can sponsor transactions or use fee grants
* **Multiple Payment Methods**: Accept credit cards, stablecoins, or cross-chain assets
* **Privacy-Preserving Verification**: Verify user credentials without exposing sensitive data
* **Composable Architecture**: Mix and match pillars to build exactly what you need
* **Production Ready**: Battle-tested infrastructure with OAuth2, Treasury Contracts, and more

### Next Steps

* Explore the [Accounts](accounts/) section for platform-specific guides
* Check out [Computation](computation/) to learn about smart contracts
* Review [Verification](verification/) for identity verification options
* See [Payments](payments/) for payment integration guides

Ready to build? Start with [Meta Accounts](accounts/getting-started/) and add more features as you go!
