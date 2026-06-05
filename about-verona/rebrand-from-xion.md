---
description: Why XION became Verona and what developers should expect
icon: arrows-rotate
---

# Rebrand: XION → Verona

Verona is the next chapter of the same network you may know as **XION**. The rebrand reflects a deliberate shift in narrative—from “trust layer for the internet” to **the intelligence layer for AI**—while preserving on-chain compatibility and developer tooling.

## What changed

| Area | Before (XION) | Now (Verona) |
|------|---------------|--------------|
| **Positioning** | Trust Layer for the Internet | Intelligence layer for AI |
| **Primary user story** | Verify data, abstract blockchain UX | User-owned verified facts reusable by authorized agents |
| **Documentation brand** | XION throughout | Verona in narrative; XION where chain precision is required |
| **Product surfaces** | Infrastructure-first | EarnOS, Burnt Verified, Ask & Ask MCP, Data Backpack |

## What did not change

Developers should assume continuity for:

* **Chain IDs and network names** — e.g. `xion-testnet-1`, IBC paths, and relayer docs
* **CLI and daemon** — `xiond` remains the node and transaction CLI
* **Meta Accounts, Abstraxion, CosmWasm** — same primitives, updated doc paths where renamed
* **Contract addresses and existing deployments** — no migration is implied by this documentation rebrand

{% hint style="info" %}
If you see **Verona** in prose and **XION** in a command, config file, or address, that is intentional. Use XION naming anywhere the chain, CLI, or an on-chain identifier must match exactly.
{% endhint %}

## Why now

AI agents need **grounded, authorized context**—not scraped, unverified text. Verona packages the network’s verification infrastructure (Truth Engine) and abstraction layer as rails for:

1. **Collecting** user-consented, cryptographically verified facts
2. **Storing and porting** them in user-controlled form (Data Backpack direction)
3. **Serving** them to agents via MCP and product surfaces (Ask, Burnt Verified, EarnOS)

The technical stack you build on today is the foundation for that intelligence layer.

## Where to go next

* [What is Verona?](concepts/overview.md) — architecture and developer implications
* [Surfaces](surfaces/README.md) — Phase 1 product entry points
* [Build on Verona](../../developers/overview.md) — accounts, verification, computation guides
* [For AI Agents](../ai-agents.md) — Verona Agent Toolkit quick start
