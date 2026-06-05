---
description: User-owned data and verified participation
icon: coins
---

# EarnOS

EarnOS is Verona’s surface for **user-owned data and verified participation**. It connects verified actions on and off chain to rewards and revenue-sharing models that users control.

## For developers

EarnOS builds on verification primitives you already use on the network:

* **zkTLS, zkEmail, and app attestations** to prove eligibility without exposing raw data
* **Meta Accounts** for identity and consent boundaries
* **CosmWasm** for on-chain settlement, attribution, and policy

Typical integration patterns:

1. Verify a user action or attribute with the [Truth Engine](../concepts/verification-infrastructure/README.md)
2. Map the proof to your app’s business rules (campaign, tier, payout)
3. Settle or record outcomes on XION (`xion-testnet-2` for development)

{% hint style="warning" %}
Do not describe unannounced token products or partner programs as live. Follow legal and marketing review before publishing yield, interest, or revenue figures in your own apps or docs.
{% endhint %}

## Related docs

* [Verification guides](../../developers/verification/README.md)
* [Meta Accounts](../concepts/meta-accounts.md)
* [Token & Revenue (Phase 1)](../token-and-revenue.md)
