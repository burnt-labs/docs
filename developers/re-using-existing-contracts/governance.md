# Governance

**Governance** refers to the mechanisms by which decisions are made within a community, protocol, or decentralized organization. In Web3, governance moves from centralized authorities to **on-chain systems**, allowing token holders, stakeholders, and contributors to **propose, vote on, and execute decisions transparently**.

Good governance ensures:

* Fair participation
* Clear decision-making processes
* Accountability and traceability
* Long-term sustainability of decentralized projects



## DAO Contracts

| Contract              | Purpose                                                                                                  | Audit                                                                                       | Repo                                                          |
| --------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **dao-dao-contracts** | A modular system for building DAOs with support for proposals, voting, staking, delegation, and rewards. | Oak Security ([Files](https://github.com/oak-security/audit-reports/tree/master/DAO%20DAO)) | [dao-dao-contracts](https://github.com/DA0-DA0/dao-contracts) |

### dao-dao-contracts

A modular and extensible contract suite for building DAOs with customizable governance, proposal workflows, staking, voting, and more. These contracts allow DAOs to be assembled like building blocks, with each module handling a specific piece of the governance system.

**Features:**

* Core contract for initializing and managing a DAO
* Proposal and pre-propose modules for structured decision-making
* Voting modules that support token-based, NFT-based, or custom voting logic
* Staking modules for CW20-based staking with reward tracking
* Delegation modules to allow vote delegation among members
* Distribution modules for payouts and airdrops
* External utility contracts for enhanced DAO functionality (e.g., wrappers, integrations)
* Designed for use across chains like XION, Juno, Neutron, and others

**Ideal for:**\
Launching composable DAOs with fine-grained control over governance logic, voting mechanisms, and member incentives, from small groups to full-fledged protocol DAOs.

📦 Repo: [dao-dao-contracts](https://github.com/DA0-DA0/dao-contracts)
