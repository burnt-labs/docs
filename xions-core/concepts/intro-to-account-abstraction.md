---
description: XION's protocol-level account abstraction solution
---

# Intro to Account Abstraction

### Types of Wallets in Blockchain

In general, blockchains have two types of accounts:

1. externally owned accounts (EOAs)
2. smart contract accounts (SCAs)

#### Externally-Owned Account (EOA)

Traditionally users interact with blockchains through externally owned accounts (EOAs) which involve the use of asymmetric cryptography. These accounts have a public/private key pair, whereby the public key is stored on the blockchain, and the private key is stored off-chain by the user. The private key, known only to the user, is used to sign transactions, while the public key is used to verify the signature’s authenticity. EOAs, however, have many drawbacks: they lack the ability to implement additional authentication mechanisms, they aren’t able to perform autonomous operations or smart contract executions, and a private/public keypair can not be changed. As a result, EOAs are a central point of failure for users. If a user loses access to their private key, they lose access to their account entirely. Similarly, if a user’s private key is compromised, their account is irreversibly compromised.&#x20;

#### Smart Contract Account (SCA)

The second type of account, smart contract accounts (SCAs), are governed by code on the blockchain. Their creation involves being deployed to the blockchain through a transaction initiated by an EOA. Once deployed, these smart contracts reside at a specific address on the blockchain and their code dictates the rules and conditions under which they operate. These rules are executed autonomously when certain conditions are met or when they are triggered by transactions or other smart contracts. Traditionally, they are unable to initiate transactions due to an absence of private keys.&#x20;

### Replacing Wallets with Programmable Accounts

Account abstraction is the foundation of XION's user experience. Instead of requiring users to create cryptocurrency wallets, manage private keys, and secure seed phrases, XION provides Meta Accounts: protocol-level smart contract accounts that behave like familiar Web2 accounts while retaining the full power of Web3.

#### The Problem with Traditional Wallets

Traditional blockchain wallets are the single largest barrier to mainstream adoption:

* **Key management is risky.** Losing a seed phrase means permanently losing access to all assets. There is no "forgot password" recovery.
* **Onboarding is confusing.** New users must understand wallets, seed phrases, gas tokens, and transaction signing before they can do anything.
* **Device lock-in.** Most wallets are tied to a specific device or browser extension. Switching devices requires manual key transfer.
* **No programmability.** Traditional wallets are static. They cannot enforce spending limits, require multi-factor authentication for large transactions, or delegate limited access to applications.

#### How XION Solves This

XION's account abstraction moves account logic from the application layer to the protocol layer. Every account on XION is a smart contract (a "Meta Account") with programmable authentication, permissions, and recovery.

**For users, this means:**

* Log in with email, FaceID, passkeys, Google, or Apple login
* No seed phrases to memorize or secure
* Access the same account from any device
* Recover access if an authentication method is lost
* Set custom security rules (spending limits, session timeouts, multi-sig for high-value transactions)

**For developers, this means:**

* No wallet integration complexity
* No "connect wallet" flow to build and maintain
* Users authenticate through the Abstraxion SDK with one function call
* Gas sponsorship through Treasury contracts (users never pay fees)
* Session keys for background operations without repeated user approval
* Programmable account permissions for your application's specific needs

#### Key Concepts

**Authentication Methods:** Meta Accounts support multiple, swappable authentication methods. A user might log in with email on desktop and FaceID on mobile, both accessing the same account. New methods can be added and old ones revoked without creating a new account.

**Session Keys:** Applications can request time-limited, permission-scoped session keys. This allows operations like in-game actions or automated transactions without requiring the user to approve every single interaction. Session keys expire automatically and can be revoked at any time.

**Treasury Contracts:** XION's mechanism for gas abstraction. Your application deploys a Treasury contract that sponsors transaction fees for your users. You configure which operations to sponsor, set spending limits, and fund the Treasury. Your users never see gas fees.

**Account Recovery:** Unlike traditional wallets, Meta Accounts support configurable recovery. If a user loses their authentication device, they can recover access through pre-configured recovery methods (additional email, social login, trusted contacts, time-locked recovery).

Detailed implementation guides are in the [Developer](/broken/pages/bFL2eEMYjRSn0lbLFgv4) section.
