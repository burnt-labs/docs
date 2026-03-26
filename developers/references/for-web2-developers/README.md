# For Web2 Developers

It can be a challenge coming from a pure Web2 background to fully understand how to build on Web3 infrastructure like XION. Building on Web3 requires a mindset shift. While the core principles of software development still apply, the architecture, tooling, and user interaction models are different. This guide bridges the gap.

{% hint style="info" %}
**The good news:** XION's abstraction layer means your users' experience will feel exactly like a Web2 app. Email login, credit card payments, no crypto terminology. The differences are primarily in how you structure your backend logic and data storage.
{% endhint %}

### **Key architectural differences**

You'll start by understanding the fundamental architectural differences between Web2 and Web3 applications:

* **Backend vs Smart Contracts:** In Web2, your backend logic lives in frameworks like Laravel, Express, or Django. On XION, core business logic is deployed on-chain via CosmWasm smart contracts (Rust). Your traditional backend can still exist for off-chain operations, API integrations, and caching.
* **Databases vs On-Chain State:** Instead of MySQL or MongoDB, persistent data lives within the smart contract's storage, which is public and immutable. For private data, you use off-chain storage with on-chain attestation hashes. XION's verification infrastructure means you can reference real-world data without storing it on-chain.
* **Centralized Auth vs Meta Accounts:** Authentication moves from traditional JWT/session systems to Meta Accounts. But because Meta Accounts support email and social login via the Abstraxion SDK, the user-facing experience is nearly identical to Web2 auth. The underlying mechanism is different (cryptographic signatures vs. password hashes), but your users will not notice.

### **What XION adds that Web2 cannot do**

* **Verification without data access.** Your app can confirm a user's real-world credentials (income, reputation, identity) without ever accessing the sensitive data. This is impossible in Web2 without trusted third parties.
* **Trustless automation.** Smart contracts execute automatically when conditions are met. No server downtime, no manual intervention, no single point of failure.
* **User data ownership.** Users own their accounts and data. They can take their verified reputation to any app on XION. This creates a network effect that benefits all applications in the ecosystem.

### In This Section

* [Web2 vs Web3 App Architecture: A Comparison](web2-vs-web3-app-architecture-a-comparison.md)
* [Misconceptions and Misapplied Web2 Patterns](misconceptions-and-misapplied-web2-patterns.md)
* [Recommended Architecture for Apps on XION](recommended-architecture-for-dapps-on-xion.md)
