# Marketplace

NFT **marketplaces** are smart contracts that allow users to **buy, sell, and list NFTs** in a decentralized way. They function as digital storefronts where NFTs can be exchanged using smart contract logic, without the need for a centralized intermediary.

In the context of the **XION ecosystem**, marketplaces are critical because:

* They **enable discovery and liquidity** for NFTs
* They **automate trustless transactions** using smart contracts
* They **support royalty enforcement**, helping creators earn on secondary sales
* They offer **customization and access control**, allowing developers to tailor the buying/selling experience

By deploying a marketplace contract, developers can either create a **permissionless open market** for many collections or a **curated shop** for a single NFT series.



## Contract Comparison

| Contract                                | Multi-Collection Support    | Permissioned Access              | Royalties | Ideal For                                                |
| --------------------------------------- | --------------------------- | -------------------------------- | --------- | -------------------------------------------------------- |
| **cw721-marketplace**                   | Yes                         | No (open to all collections)     | Yes       | General-purpose, open NFT marketplaces                   |
| **cw721-marketplace-permissioned**      | Yes                         | Yes (admin approves collections) | Yes       | Curated galleries, permissioned or verified marketplaces |
| **cw721-marketplace-single-collection** | No (single collection only) | No (open within one collection)  | Yes       | Dedicated storefronts for one collection                 |



These smart contracts provide marketplace functionality for CW721 NFTs on XION. Each is tailored for specific use cases, whether you're creating an open market, a curated gallery, or a single-collection storefront.

#### 1. **cw721-marketplace**

A full-featured, open NFT marketplace contract that supports multiple collections.\
**Features:**

* List, buy, and cancel NFTs across many CW721 collections
* Supports royalties (CW2981)
* Uses native or CW20 tokens for payment
* Collection-level and token-level approvals
* Designed for broad, permissionless markets

**Ideal for:**\
General-purpose NFT marketplaces with support for multiple projects\
📦 Repo: [`cw721-marketplace`](https://crates.io/crates/cw721-marketplace)

#### 2. **cw721-marketplace-permissioned**

A variation of the `cw721-marketplace` contract that adds access controls for curated listings.\
**Features:**

* All features of `cw721-marketplace`
* Admin approval required before collections can be listed
* Better suited for curated or private markets
* Roles and access permissions baked into marketplace logic

**Ideal for:**\
Art galleries, curated drops, or marketplaces with verified creators only\
📦 Repo: [`cw721-marketplace-permissioned`](https://crates.io/crates/cw721-marketplace-permissioned)

#### 3. **cw721-marketplace-single-collection**

A lightweight marketplace contract designed for a single NFT collection.\
**Features:**

* Fixed-price sales within one collection
* Simplified logic, no need to manage multiple collections
* Minimal setup and configuration
* Royalties and fees configurable per contract

**Ideal for:**\
Creators launching a dedicated storefront for one collection, or small-scale sales\
📦 Repo: [`cw721-marketplace-single-collection`](https://crates.io/crates/cw721-marketplace-single-collection)
