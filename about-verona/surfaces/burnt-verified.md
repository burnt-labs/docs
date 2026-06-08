---
description: Enterprise verification without document custody
icon: badge-check
---

# Burnt Verified

**Burnt Verified** is Verona's enterprise surface for **income, employment, and credential verification**—cryptographic proofs from the source, with **no documents stored** and no custodial liability for the verifier.

Where Ero proves the consumer story, Burnt Verified proves the **real-company** story: paying customers, enterprise workflows, and verification revenue on the same Truth Engine that powers agents and apps.

## What it enables

* **Income and employment verification** — Users authenticate to payroll or HR sources; verifiers receive a cryptographic proof in seconds, not a document upload
* **Real-estate, lending, and insurance workflows** — Decisions on records verified at the source; the data underneath never transits through your systems
* **Non-custodial assurance** — Gate features, payouts, compliance, or agent tools on attestations instead of PII archives
* **Composable with agents** — Authorized agents query scoped proofs (for example income above a threshold) without holding bank statements

Burnt (the company) sells verification to landlords, lenders, insurers, and other buyers who today pay traditional data brokers. Burnt's wins are **revenue**; Verona's wins are **category and network effects**—the surfaces reinforce each other.

## For developers

Start with the verification stack, then layer product logic:

| Source | Module | Developer entry |
|--------|--------|-----------------|
| Websites / APIs | zkTLS | [Internet Verification](../../developers/verification/internet-verification/README.md) |
| Email | zkEmail | [Email Verification](../../developers/verification/email-verification.md) |
| Identity | zkPassport | Truth Engine surface; contact the team for integration paths |
| Mobile apps | App Attestations | [Mobile Verification](../../developers/verification/app-attestations.md) |

Publishing custom verification keys: [Publishing ZK Verification Keys](../../developers/verification/publishing-zk-verification-keys.md).

Burnt Verified product APIs and issuer flows will be documented here as they ship; the modules above are the current integration path.

{% hint style="info" %}
Do not name unannounced data-broker or operator partnerships in public docs until cleared by marketing and legal.
{% endhint %}

## Related docs

* [Truth Engine](../concepts/verification-infrastructure/README.md)
* [Use Cases](../concepts/use-cases.md)
* [Ero (EarnOS)](earnos.md)
