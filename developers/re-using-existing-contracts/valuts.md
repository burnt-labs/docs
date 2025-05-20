# Valuts

In Web3 and DeFi, **vaults** are smart contracts designed to **hold and manage assets** according to a predefined strategy or set of rules. They serve as **automated safes** for users’ funds, allowing for more complex behaviours such as yield generation, trading, hedging, time-locking, or role-based access.

Vaults can be:

* **Permissioned** – where only specific users or contracts can interact
* **Governed** – controlled by DAOs or multisigs for security and flexibility



## Vault Contracts

| **Contract**          | **Purpose**                                    | **Features**                                                            | **Ideal For**                                    | **Repo / Docs**                                                                 |
| --------------------- | ---------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------- |
| **CW Vault Standard** | Defines a shared interface for vault contracts | Standard API, extensibility, deposit/withdraw, ownership logic          | Creating custom vaults with consistent structure | [cw-vault-standard](https://docs.rs/cw-vault-standard/0.4.1/cw_vault_standard/) |
| **Vaultenator**       | Vault with advanced financial strategies       | Strategy execution, vault config, risk management, trading integrations | Options vaults, yield strategies, DeFi protocols | [vaultenator](https://github.com/margined-protocol/vaultenator)                 |

### 1. **CW Vault Standard**

A formalized specification for building interoperable vaults in CosmWasm. This standard defines a shared interface and behavior pattern for vault contracts.

**Features:**

* Standardized vault API across projects
* Easy integration with frontends and third-party protocols
* Extensible logic for withdrawals, deposits, hooks, and ownership
* Enables interoperability between vaults and external dApps

**Ideal for:**\
Developers creating custom vault contracts that want to follow a shared interface for tooling, composability, and ease of use.

📦 Repo / Docs: [cw-vault-standard](https://docs.rs/cw-vault-standard/0.4.1/cw_vault_standard/)

### 2. **Vaultenator**

**Vaultenator** is a vault implementation that adheres to the [CosmWasm Vault Standard (CW4626)](https://docs.rs/cw-vault-standard/0.4.1/cw_vault_standard/), utilizing the Tokenfactory module for managing vault share tokens.

**Features:**

* **Administration:** Handles opening and pausing of contracts.
* **Configuration:** Manages contract configuration settings.
* **Ownership:** Manages contract ownership details.
* **State Management:** Maintains and updates the contract state.
* **CW4626 Compliance:** Implements the standard interface for vaults in the CosmWasm ecosystem.
* **Tokenfactory Integration:** Utilizes Tokenfactory denoms as vault share tokens.

**Ideal for:**

Developers seeking a foundational vault contract that complies with the CW4626 standard and leverages Tokenfactory for share token management.

📦 Repo: [vaultenator](https://github.com/margined-protocol/vaultenator)
