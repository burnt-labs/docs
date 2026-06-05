---
description: Portable, user-controlled verified facts
icon: backpack
---

# Data Backpack

Data Backpack is Verona’s direction for a **portable, user-controlled store of verified facts**—credentials, attributes, and proofs that follow the user across apps and agents.

## Why it matters for agents

LLMs lack durable, trustworthy memory about a specific user. Backpack-style storage aims to give agents **consented, verifiable context**:

* Facts are grounded in Truth Engine proofs, not free-text profiles
* Users control what is stored, shared, and revoked
* Apps and MCP tools request scoped access instead of copying raw data

## For developers

Until dedicated Backpack APIs are documented, compose the same building blocks:

1. **Verify** — [Verification guides](../../developers/verification/README.md)
2. **Persist policy** — CosmWasm contracts or app backends with explicit user consent
3. **Authorize access** — Meta Accounts, OAuth2, and session scoping ([Accounts](../../developers/accounts/README.md))

## Related docs

* [Meta Accounts](../concepts/meta-accounts.md)
* [Ask & Ask MCP](ask-and-mcp.md)
* [Burnt Verified](burnt-verified.md)
