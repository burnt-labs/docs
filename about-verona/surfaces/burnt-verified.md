---
description: Verified credentials and attestations
icon: badge-check
---

# Burnt Verified

Burnt Verified is Verona’s surface for **credentials and attestations**—packaging Truth Engine proofs into forms apps and agents can trust.

## What it enables

* Prove facts from the web (zkTLS), email (zkEmail), or mobile apps (attestations)
* Issue or consume **verified claims** without holding sensitive source data
* Gate features, payouts, or agent tools on cryptographic assurance rather than self-reported input

## For developers

Start with the verification stack, then layer your product logic:

| Source | Module | Developer entry |
|--------|--------|-----------------|
| Websites / APIs | zkTLS | [Internet Verification](../../developers/verification/internet-verification/README.md) |
| Email | zkEmail | [Email Verification](../../developers/verification/email-verification.md) |
| Mobile apps | App Attestations | [Mobile Verification](../../developers/verification/app-attestations.md) |

Publishing custom verification keys: [Publishing ZK Verification Keys](../../developers/verification/publishing-zk-verification-keys.md).

Burnt Verified product APIs and issuer flows will be documented here as they ship; the modules above are the current integration path.

## Related docs

* [Truth Engine](../concepts/verification-infrastructure/README.md)
* [Use Cases](../concepts/use-cases.md)
