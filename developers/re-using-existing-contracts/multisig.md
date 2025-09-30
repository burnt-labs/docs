# Multisig

**Multisigs** short for _multi-signature contracts_ are smart contracts that require **multiple parties to approve an action** before it can be executed. Instead of relying on a single private key, control is distributed among a group, increasing **security**, **transparency**, and **collaborative governance**.

In the **XION** ecosystem and broader **CosmWasm** world, multisigs are commonly used for:

* **Decentralized treasury management**
* **Protocol upgrades or governance decisions**
* **Secure deployment of smart contracts**
* **Joint ownership of high-value assets**



## Multisig Contracts

CW3 is the CosmWasm standard for multisig contracts. There are two main implementations: one for **static membership** and one for **dynamic membership**.

| Contract               | Voter Set Type      | On-Chain Proposal Flow | Dynamic Membership | Ideal For                                 |
| ---------------------- | ------------------- | ---------------------- | ------------------ | ----------------------------------------- |
| **cw3-fixed-multisig** | Fixed (K-of-N)      | Yes                    | No                 | Small, static groups                      |
| **cw3-flex-multisig**  | Linked to cw4-group | Yes                    | Yes                | DAOs, evolving teams, flexible governance |

#### 1. **cw3-fixed-multisig**

A straightforward multisig implementation with a fixed list of members defined at instantiation.\
**Features:**

* Fixed set of voters with configurable weights
* On-chain proposal and approval tracking
* Simple K-of-N signature logic
* No dependency on external contracts

**Ideal for:**\
Small teams or DAOs with a known and unchanging voter set\
📦 Repo: [`cw3-fixed-multisig`](https://crates.io/crates/cw3-fixed-multisig)

#### 2. **cw3-flex-multisig**

An advanced multisig contract backed by a `cw4-group` contract for flexible, updateable membership.\
**Features:**

* Dynamic voter set via linked `cw4-group` contract
* Adjustable weights and thresholds
* Allows adding/removing members without redeploying the multisig
* More modular and governance-ready

**Ideal for:**\
DAOs and organizations where the voting group may change over time\
📦 Repo: [`cw3-flex-multisig`](https://crates.io/crates/cw3-flex-multisig)

