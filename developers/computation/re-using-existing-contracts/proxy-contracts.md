# Proxy Contracts

**Proxy contracts** act on behalf of one or more users, allowing controlled execution of transactions or actions under specific conditions. Instead of a user calling contracts directly, they can delegate that responsibility to a proxy, which then executes the desired operations, either automatically or with certain restrictions.

In the **CosmWasm** and **XION** ecosystem, proxy contracts are commonly used for:

* **Delegating transaction execution**
* **Granting limited permissions to third parties or apps**
* **Enabling programmable account logic**
* **Simulating multisig-like behavior without full governance**



## Proxy Contracts

The **CW1 standard** defines a generic proxy interface for executing messages on behalf of others. Two primary CW1-based contracts demonstrate its flexibility.

| Contract          | Access Control         | Allowance Tracking | Use Case                              |
| ----------------- | ---------------------- | ------------------ | ------------------------------------- |
| **cw1-whitelist** | Whitelisted addresses  | No                 | Admin execution, limited delegation   |
| **cw1-subkeys**   | Per-address allowances | Yes                | Spend limits, delegated token control |

#### 1. **cw1-whitelist**

A minimal, reference implementation of a CW1 proxy contract where only **whitelisted addresses** can execute messages through the proxy.\
**Features:**

* Owner controls the list of allowed executors
* Whitelisted users can call `Execute` on the proxy, forwarding messages
* Simple access control pattern for demonstration or lightweight usage

**Ideal for:**\
Use cases where only trusted addresses (like admins or bots) should trigger transactions on behalf of the contract\
📦 Repo: [`cw1-whitelist`](https://crates.io/crates/cw1-whitelist)

#### 2. **cw1-subkeys**

A practical CW1 proxy that allows setting **spending allowances** (especially for native tokens) per address.\
**Features:**

* Assigns per-address allowances for native tokens
* Tracks balance usage and enforces limits
* Enables more granular control without modifying the bank module
* Allows users to delegate spend permissions without sharing private keys

**Ideal for:**\
Gasless UX, spend limits, delegated spending by apps or services (e.g. subscription models, wallets)\
📦 Repo: [`cw1-subkeys`](https://crates.io/crates/cw1-subkeys)
