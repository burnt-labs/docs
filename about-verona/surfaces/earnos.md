---
description: Ero by EarnOS — verified participation on Verona
icon: coins
---

# Ero (EarnOS)

**Ero** (by EarnOS), built on Verona, is the **flagship consumer launch** for **user-owned, verified participation**. People earn for what they already do online: activity is cryptographically verified, and the data underneath is never exposed.

**EarnOS** is the product surface; **Ero** is the live consumer experience users open at launch. Together they demonstrate verification, ownership, reuse, and real economic activity on the same network validators secure and developers build on.

## What Ero shows

* Users connect everyday accounts (for example ride-share or delivery apps) and verify attributes with the [Truth Engine](../concepts/verification-infrastructure/README.md)
* Verified facts stay **user-owned** on the network and can be reused where the user consents
* Rewards and campaigns map to **proof of eligibility**, not self-reported claims
* [Meta Accounts](../concepts/meta-accounts.md) handle identity and consent boundaries without wallet friction

## For developers

Ero builds on the same primitives as any Verona application:

* **zkTLS, zkEmail, zkPassport, and app attestations** for eligibility without raw data exposure
* **Meta Accounts** for authentication and permission scopes
* **CosmWasm** and Treasury patterns for on-chain settlement and gasless UX (`xion-testnet-2` for development)

Typical integration patterns:

1. Verify a user action or attribute with the Truth Engine
2. Map the proof to your app's business rules (campaign, tier, payout, agent gate)
3. Settle or record outcomes on the Verona network

{% hint style="warning" %}
Do not describe unannounced token products, partner programs, or revenue figures as live in your own apps or docs. Follow legal and marketing review before publishing reward, revenue-share, or token numbers.
{% endhint %}

## Related docs

* [Surfaces](README.md)
* [Verification guides](../../developers/verification/README.md)
* [Meta Accounts](../concepts/meta-accounts.md)
