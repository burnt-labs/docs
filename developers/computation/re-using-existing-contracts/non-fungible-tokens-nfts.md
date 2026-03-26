# Non-Fungible Tokens (NFTs)

NFTs are unique digital assets, and similar to the ERC-721 standard on Ethereum, the CosmWasm ecosystem uses the CW721 standard. XION supports this NFT standard, with several ready-to-use contracts available in the [**cw-nfts**](https://github.com/public-awesome/cw-nfts) repository. These contracts enable teams to deploy NFT collections with minimal customization while benefiting from enhanced security and reliability.

{% hint style="success" %}
Reusing these contracts **without modification** may reduce or eliminate the need for a security audit before deploying to XION Mainnet.
{% endhint %}

## Available CW721 Contract Variants

| Contract                   | Transferable | On-Chain Metadata      | Updatable Metadata   | Use Case Examples                                     |
| -------------------------- | ------------ | ---------------------- | -------------------- | ----------------------------------------------------- |
| **cw721-base**             | Yes          | No                     | No                   | Basic NFT collections with off-chain metadata         |
| **cw721-updatable**        | Yes          | No                     | Yes                  | Dynamic NFTs (e.g., avatars that evolve, gaming NFTs) |
| **cw721-metadata-onchain** | Yes          | Yes                    | No                   | Fully on-chain generative art, digital collectibles   |
| **cw721-soulbound**        | No           | No                     | No                   | Identity badges, certificates, proof-of-participation |
| **cw721-expiration**       | Yes          | No                     | Yes (via expiration) | Subscription NFTs, time-limited memberships           |
| **cw721-fixed-price**      | Yes          | No (usually off-chain) | Yes (price config)   | Simple marketplaces with fixed price sales            |
| **cw721-non-transferable** | No           | No                     | No                   | Achievements, diplomas, SBTs (Soulbound Tokens)       |
| **cw721-receiver-tester**  | Yes          | No                     | No                   | Utility contract used for testing receive hooks       |
| **cw2981-royalties**       | Yes          | Yes                    | No                   | Royalty-enabled NFTs for artists and creators         |



#### 1. **cw721-base**

The standard implementation of the CW721 specification.\
**Features:**

* Minting, transferring, burning
* Token-level ownership and metadata
* Approved operators

**Ideal for:**\
Basic NFT collections with off-chain metadata

📦 **Repo:** [`cw721-base`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-base)&#x20;

#### 2. **cw721-updatable**

An extension of `cw721-base` that allows metadata to be updated after minting.\
**Features:**

* All `cw721-base` features
* Supports updating token metadata

**Ideal for:**\
Dynamic NFTs such as evolving avatars, game characters, or NFTs that change over time

📦 **Repo:** [`cw721-updatable`](https://crates.io/crates/cw721-updatable)

#### 3. **cw721-metadata-onchain**

A variant of `cw721-base` where full metadata is stored directly on-chain.\
**Features:**

* All `cw721-base` features
* Stores metadata (name, description, image) directly in state

**Ideal for:**\
NFTs where immutability and decentralization of metadata are critical (e.g., generative art, collectibles)

📦 **Repo:** [`cw721-metadata-onchain`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-metadata-onchain)&#x20;

#### 4. **cw721-soulbound**

Implements non-transferable NFTs, often referred to as Soulbound Tokens (SBTs).\
**Features:**

* Minting only
* Disables transfer and approval functionality

**Ideal for:**\
Credentials, identity, certifications, badges, or non-transferable memberships

📦 **Repo:** [`cw721-soulbound`](https://crates.io/crates/cw721-soulbound)&#x20;

#### 5. **cw721-expiration**

Extends `cw721-base` with support for setting expiration times on NFTs.\
**Features:**

* All `cw721-base` features
* Optional expiration timestamp per token
* Hooks for expiration-based behavior

**Ideal for:**\
Time-based NFTs such as subscriptions, memberships, or event passes

📦 **Repo:** [`cw721-expiration`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-expiration)&#x20;

#### 6. **cw721-fixed-price**

A contract combining NFT minting with a built-in fixed-price marketplace.\
**Features:**

* Minting and transferring
* Price configuration per token
* Direct buy functionality

**Ideal for:**\
Launching NFT collections with fixed-price sales without needing a separate marketplace contract

📦 **Repo:** [`cw721-fixed-price`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-fixed-price)&#x20;

#### 7. **cw721-non-transferable**

A stricter soulbound implementation with enforced immutability.\
**Features:**

* No transferring or burning
* Tokens are permanent and non-movable once minted

**Ideal for:**\
Highly permanent identity artifacts such as diplomas, licenses, or irreversible credentials

📦 **Repo:** [`cw721-non-transferable`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-non-transferable)&#x20;

#### 8. **cw721-receiver-tester**

A testing utility contract for verifying the `cw721` `Receive` hook functionality.\
**Features:**

* Implements `cw721::Receiver` interface
* Logs or validates token reception

**Ideal for:**\
Testing and verifying behavior of contracts that send or receive NFTs

📦 **Repo:** [`cw721-receiver-tester`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-receiver-tester)&#x20;

#### 9. **cw2981-royalties**

Implements the CW2981 royalty standard for NFT creators.\
**Features:**

* Attaches royalty info to CW721 tokens
* Royalty enforcement during sales (via marketplaces)

**Ideal for:**\
NFT collections where artists or creators want automatic royalty support\
📦 Repo: [`cw2981-royalties`](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw2981-royalties)
