---
description: Why XION became Verona and what developers should expect
icon: arrows-rotate
---

# Rebrand: XION → Verona

Verona is the next chapter of the same network you may know as **XION**. The rebrand is a deliberate move from the **chain-abstraction** narrative—which ran its course—into **crypto AI**, where verified information for agents is the larger opportunity.

This is a **brand and ticker rename**, not a new token or contract migration. On-chain identifiers (`uxion`, `xiond`, chain IDs), **validator set**, contract addresses, and balances stay as they are.

## The story (short)

1. **Proof as provocation** — Burnt Banksy burned an original Banksy and minted the moment as an NFT, showing that digital scarcity could be made real on a global stage.
2. **Burnt** — The team built products for everyone, not only crypto-natives, and went down the stack to remove friction at the root.
3. **XION** — That work became a network built to make crypto disappear: no seed phrases, no gas in the user path, abstraction at the protocol level.
4. **The turn** — Enterprise adopters kept asking for the same thing: verify something true about a person **without holding the sensitive data**. The real product was never abstraction alone—it was **proof**.
5. **Verona** — The intelligence layer for AI: facts proven once, owned by the user, reusable by any authorized agent, with the data underneath never exposed.

## What changed

| Area                    | Before (XION)                                    | Now (Verona)                                                |
| ----------------------- | ------------------------------------------------ | ----------------------------------------------------------- |
| **Positioning**         | Trust Layer for the Internet / chain abstraction | Intelligence layer for AI                                   |
| **Primary user story**  | Verify data, abstract blockchain UX              | User-owned verified facts reusable by authorized agents     |
| **Public framing**      | Often described as a chain                       | A **network and layer**—the chain is how it works           |
| **Documentation brand** | XION throughout                                  | Verona in narrative; XION where chain precision is required |
| **Native token symbol** | $XION                                            | $VERONA (on-chain denom `uxion` unchanged)                  |
| **Product surfaces**    | Infrastructure-first                             | Ero (EarnOS), Burnt Verified                                |
| **Agent toolkit**       | `xion-agent-toolkit` repo; `xion-toolkit` CLI    | [`verona-agent-toolkit`](https://github.com/burnt-labs/verona-agent-toolkit); `verona-toolkit` CLI |

## What did not change

Developers should assume continuity for:

* **Chain IDs and network names** — e.g. `xion-testnet-2`, IBC paths, and relayer docs
* **CLI and daemon** — `xiond` remains the node and transaction CLI
* **Meta Accounts, Abstraxion, CosmWasm** — same primitives, updated doc paths where renamed
* **Contract addresses and token balances** — existing holdings stay on the same chain; display name and ticker update to Verona / $VERONA
* **Validator set and staking** — no restaking, key rotation, or validator re-registration required for the rename; staking rewards and governance continue on the same chain with denom `uxion`

{% hint style="info" %}
If you see **Verona** in prose and **XION** in a command, config file, or address, that is intentional. Use XION naming anywhere the chain, CLI, or an on-chain identifier must match exactly.
{% endhint %}

## Why now

AI is everywhere but not **intelligent** until it can act on verified information. Verona packages the Truth Engine and abstraction layer so that:

1. Facts are **verified once** at the source (websites, email, passports, apps)
2. They are **owned and stored** on the network under user control
3. **Authorized agents and apps reuse** them without rechecking or holding raw data

The technical stack you build on today is the foundation for that intelligence layer.

## Token display vs. economics

**$VERONA** (Previously $XION) is the network's native token for **staking, governance, and settlement** (on-chain denom `uxion`). End-user applications typically sponsor gas via Treasury contracts, so value is not primarily a "gas token" story.

Network value capture is designed around **ecosystem revenue**—revenue share from apps on the network, enterprise verification revenue, and agent-driven on-chain activity—routing into **buyback and burn** over time. Specific buyback percentages and emission schedules are not final until Phase 2 messaging.

## Where to go next

* [What is Verona?](../about-verona/concepts/overview.md) — architecture and developer implications
* [Surfaces](../about-verona/surfaces/) — Ero and Burnt Verified
* [Build on Verona](../developers/overview.md) — accounts, verification, computation guides
* [For AI Agents](../about-verona/ai-agents.md) — Verona Agent Toolkit quick start
