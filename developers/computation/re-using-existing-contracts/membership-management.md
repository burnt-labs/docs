# Membership Management

**On-chain group membership management** can be useful in governance systems, access control, and permissioned modules. These contracts track **who is in a group** and **how much voting weight** each member has, forming the backbone for more complex systems like multisigs (CW3), DAOs, and role-based permissions.



### Membership Contracts

| Contract      | Membership Type       | Voting Power Source | Admin Controlled | Staking Required | Use Case                                            |
| ------------- | --------------------- | ------------------- | ---------------- | ---------------- | --------------------------------------------------- |
| **cw4-group** | Appointed (manual)    | Configured weights  | Yes              | No               | Static groups, multisig backends, role-based access |
| **cw4-stake** | Dynamic (via staking) | Token stake         | No (stake-based) | Yes              | Token-based DAOs, governance by stake               |

#### 1. **cw4-group**

A core implementation of the CW4 specification for managing static or admin-controlled groups.\
**Features:**

* Manages a list of members and their voting weights
* Admin can add, remove, or update members
* Queryable by external contracts (like CW3 multisigs)
* Fully compatible with CW3 for governance use

**Ideal for:**\
DAOs, governance systems, and multisigs where membership is appointed by an admin or another smart contract\
📦 Repo: [`cw4-group`](https://crates.io/crates/cw4-group)

#### 2. **cw4-stake**

An alternative CW4 implementation that calculates group membership based on **staked tokens** instead of manual assignment.\
**Features:**

* Membership and weight derived from staked token amounts
* Staking and unstaking adjust voting power dynamically
* Fully implements the CW4 spec and integrates with CW3 contracts
* Allows DAO participation to be directly tied to token economics

**Ideal for:**\
Token-weighted governance, DAOs with dynamic membership, and voting systems where stake = influence\
📦 Repo: [`cw4-stake`](https://crates.io/crates/cw4-stake)
