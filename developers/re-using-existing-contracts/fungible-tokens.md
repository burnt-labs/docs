# Fungible Tokens

The CW20 standard defines a fungible token interface for smart contracts in the CosmWasm ecosystem, similar to Ethereum’s ERC-20. On XION, you can deploy your own CW20 contracts or use the built-in [Token Factory module](https://docs.burnt.com/xion/developers/learn-and-build/token-factory) to create native tokens without writing custom logic.

This guide explains when and how to deploy CW20 token contracts using production-ready implementations from the [cw-plus](https://github.com/CosmWasm/cw-plus) and [cw-tokens](https://github.com/CosmWasm/cw-tokens) repositories.

{% hint style="success" %}
Reusing these contracts **without modification** may reduce or eliminate the need for a security audit before deploying to XION Mainnet.
{% endhint %}

## When to use CW20 Contracts vs Token Factory

| Use Case                                            | Use CW20 Contract                 | Use Token Factory |
| --------------------------------------------------- | --------------------------------- | ----------------- |
| Need custom logic (e.g., bonding, vesting)          | Yes                               | No                |
| Just need a **simple native token**                 | No                                | Yes               |
| Allows for transferring tokens across chain via IBC | No (Requires cw20-ics20 contract) | Yes               |

If you're just minting a governance or utility token with no special rules, use **Token Factory**. If you need full control and customization, go with CW20 contracts. See the following [Token Factory guide](../learn-and-build/token-factory/creating-minting-and-interacting-with-a-token-factory-token.md) for more details.

### Available CW20 Contract Variants

#### 1. `cw20-base`

Standard CW20 implementation with all basic functionality.

**Features:**

* Minting and burning
* Transfer and allowance logic
* Query total supply, balances

📦 Repo: [`cw20-base`](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw20-base)

**Ideal for:**

* Standalone utility tokens
* Basic reward and governance tokens

#### 2. `cw20-ics20`

Wraps a CW20 token to be compatible with **IBC (Interchain Standard 20)**.

**Features:**

* Builds on `cw20-base`
* Adds IBC packet handling for token transfers across Cosmos chains

📦 Repo: [`cw20-ics20`](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw20-ics20)

**Ideal for:**

* Interchain assets
* Apps that rely on token bridging

#### 3. `cw20-bonding`

CW20 token with **bonding curve logic** for dynamic token pricing.

**Features:**

* Automatic pricing based on supply
* Mint/burn tokens via bonding curve
* Customizable curve function

📦 Repo: [`cw20-bonding`](https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw20-bonding)

**Ideal for:**

* Fundraising apps
* Creator economy tokens
* Algorithmic supply control

#### 4. **cw20-atomic-swap**

Implements atomic swaps between native and CW20 tokens in a trust-minimized way.\
**Features:**

* Supports atomic swaps between native tokens and CW20 tokens
* Time-locked contracts ensure either party can claim or refund
* Hash-based secret verification (HTLC-style)

**Ideal for:**\
Cross-party token swaps without intermediaries, simple DEX-like functionality\
📦 Repo: [`cw20-atomic-swap`](https://github.com/CosmWasm/cw-tokens/tree/main/contracts/cw20-atomic-swap)&#x20;

#### 5. **cw20-staking**

Provides staking derivatives by staking native tokens and issuing CW20 representations.\
**Features:**

* Stakes native tokens on behalf of the user
* Issues CW20 tokens as liquid staking derivatives
* Integrates with the staking module for rewards
* Based on `cw20-base`&#x20;

**Ideal for:**\
Liquid staking solutions, DeFi integrations, enabling composability of staked assets\
📦 Repo: [`cw20-staking`](https://github.com/CosmWasm/cw-tokens/tree/main/contracts/cw20-staking)&#x20;

#### 6. **cw20-escrow**

A simple escrow contract compatible with both native and CW20 tokens.\
**Features:**

* Funds are held until an arbiter approves release or refund
* Supports multiple token types (CW20 and native)
* Basic access control for disbursement

**Ideal for:**\
Trust-minimized payments, freelance work, over-the-counter trades\
📦 Repo: [`cw20-escrow`](https://github.com/CosmWasm/cw-tokens/tree/main/contracts/cw20-escrow)&#x20;

#### 7. **cw20-merkle-airdrop**

Efficient CW20 token airdrop mechanism using Merkle proofs.\
**Features:**

* Airdrop claim eligibility via Merkle root verification
* Reduces gas cost for mass distribution
* Claim tracking to prevent double claims

**Ideal for:**\
Token launches, community rewards, incentivized campaigns\
📦 Repo: [`cw20-merkle-airdrop`](https://github.com/CosmWasm/cw-tokens/tree/main/contracts/cw20-merkle-airdrop)

