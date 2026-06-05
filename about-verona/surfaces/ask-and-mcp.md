---
description: Query and MCP rails for authorized agent context
icon: message-bot
---

# Ask & Ask MCP

Ask is Verona’s **query and agent interface** over authorized, verified context. **Ask MCP** exposes the same capabilities to AI agents through the [Model Context Protocol](https://modelcontextprotocol.io/)—so assistants can request facts the user has consented to share, grounded in proofs rather than hallucination.

## Mental model

```
User consent → Verified facts (Truth Engine / Backpack) → Ask / MCP → Agent action
```

Agents should not scrape or guess. They should call rails that return **only** data the user authorized, with verification metadata attached.

## For developers

**Today**

* Build verification and storage flows with existing [Verification](../../developers/verification/README.md) and [Computation](../../developers/computation/README.md) guides
* Use [Verona Agent Toolkit](../../developers/tools/verona-toolkit.md) for Meta Account auth, Treasury, and gasless agent workflows
* See [For AI Agents](../ai-agents.md) for one-line agent setup

**Coming in docs**

* Ask API reference and MCP server configuration
* Scopes, consent, and audit patterns for production agents

{% hint style="info" %}
MCP integration is a Month 1 narrative priority; server endpoints and tool schemas will be added to this page as they are finalized. Tooling names may still reference `xion-toolkit` in CLI until the toolkit release catches up with the Verona brand.
{% endhint %}

## Related docs

* [For AI Agents](../ai-agents.md)
* [Verona Agent Toolkit](../../developers/tools/verona-toolkit.md)
* [Data Backpack](data-backpack.md)
