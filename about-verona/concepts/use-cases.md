# Use Cases

Verona combines the [Truth Engine](verification-infrastructure/README.md) and [Generalized Abstraction](generalized-abstraction.md) so apps and agents can act on **verified once, reused everywhere** facts—without collecting or storing sensitive source data.

The patterns below map to the four audiences on [What is Verona?](overview.md). For integration detail, see [Build on Verona — Verification](../../developers/verification/README.md) and [Surfaces](../surfaces/README.md).

## Humans

| Pattern | What becomes possible |
| ------- | --------------------- |
| **Verified participation** | Users prove everyday activity and earn from what they already do—raw records stay private. See [Ero (EarnOS)](../surfaces/earnos.md). |
| **Portable reputation** | Prove a loyalty tier, rating, or account standing from one platform to another without the source app's cooperation. |
| **Controlled sharing** | Disclose only the attribute an app needs (for example "premium subscriber") while keeping underlying data private. |

## Enterprises

| Pattern | What becomes possible |
| ------- | --------------------- |
| **Income and employment** | Cryptographic proof from payroll or HR sources in seconds—no document uploads, no custodial liability. See [Burnt Verified](../surfaces/burnt-verified.md). |
| **Verified acquisition** | Target offers to users who cryptographically prove competitor loyalty or spend tiers—without invasive tracking. |
| **Compliance and underwriting** | KYC/KYB, insurance, and lending workflows on scoped proofs instead of document archives. |

## Agents

| Pattern | What becomes possible |
| ------- | --------------------- |
| **Authorized context** | Agents read attestations bound to a [Meta Account](meta-accounts.md)—income range, identity attributes, account standing—instead of scraped or self-reported input. |
| **Action without custody** | Refills, travel check-in, loan steps, and similar flows run on proofs the user already verified; the agent never holds bank statements or passports. |

See [For AI Agents](../ai-agents.md) for toolkit setup.

## Developers

| Pattern | What becomes possible |
| ------- | --------------------- |
| **Proof → action → settlement** | Read an attestation, trigger app or agent logic, settle on chain in one flow—with [Treasury](../../developers/accounts/getting-started/treasury-contracts.md) gas sponsorship for end users. |
| **Composable verification** | Combine zkTLS, zkEmail, zkPassport, and app attestations into multi-factor gates for access, pricing, or rewards. |
| **On-chain policy** | CosmWasm contracts query attestations before executing transfers, governance, or game outcomes. |
