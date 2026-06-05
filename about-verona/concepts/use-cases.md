# Use Cases

### Real-World Applications Enabled by Verification + Abstraction

**Verona** packages the XION network's Truth Engine and Generalized Abstraction for applications and agents that need **grounded, user-consented facts**. The use cases below show what becomes possible when zero-knowledge verification and complete abstraction work together—many were previously impossible or impractical on other networks.

#### For Consumer Applications

**Fraud-Proof Advertising**

The advertising industry wastes $80B+ annually on bot traffic. Applications built on Verona can use zkTLS to verify that ad viewers are real humans with verified platform accounts, eliminating fake engagement entirely. Combined with abstraction, users verify themselves once and never interact with network mechanics.

_Technical approach:_ Users generate zkTLS proofs from authenticated accounts (social media, email, banking). Your application's smart contract queries these attestations before counting engagement or distributing rewards. Treasury contracts cover all gas, so the user experience is identical to any standard app.

**Portable Reputation and Loyalty**

Users accumulate reputation across dozens of platforms but own none of it. Applications on Verona can let users prove their Uber rating to Lyft, their Delta status to United, or their Amazon purchase history to a competing retailer, all without the source platform's cooperation or knowledge.

_Technical approach:_ Users generate proofs from zkTLS (web data), zkEmail (loyalty emails), or App Attestations (mobile app data). Attestations are bound to their Meta Account, creating a portable, verified reputation profile that any application can query with user permission.

**Private Data Monetization**

Users' data generates billions for platforms while users receive nothing. Applications on Verona can let users monetize their data (browsing habits, purchase patterns, preferences) while maintaining full privacy through selective disclosure.

_Technical approach:_ Users generate attestations that prove specific attributes (e.g., "frequent online shopper" or "premium streaming subscriber") without revealing exact data. Brands query these attestations and compensate users for verified insights. All payments, attestation queries, and data exchange happen on-chain with full abstraction.

**Cross-Platform Identity Verification**

Proving credentials today requires uploading sensitive documents. Applications on Verona let users prove age, income, accreditation, employment, or any other attribute with a ZK proof instead.

_Technical approach:_ Combine DKIM Module (employment verification email from HR), zkTLS (banking website data), and App Attestations (government ID app) to create composite verified identities. Smart contracts enforce minimum proof requirements before granting access.

#### For Enterprise and B2B

**Verified User Acquisition**

Brands can target verified, high-value users from competitors without invasive tracking. A user proves they are a top-tier customer of a competing service, and the brand makes a targeted offer based on verified credentials.

_Technical approach:_ Enterprise integration via API. Brand defines required attestation types (e.g., "competitor loyalty tier above Gold"). Users who match generate proofs and opt in. Brand receives cryptographic verification without accessing any personal data. All interaction runs through abstracted infrastructure; the brand does not need blockchain expertise.

**Automated Compliance (KYC/KYB)**

Zero-knowledge proofs can automate Know Your Customer and Know Your Business workflows. Users prove identity attributes (citizenship, accreditation, sanctions-list absence) without uploading documents that create liability.

_Technical approach:_ Combine DKIM proofs (government emails, banking communications) with zkTLS proofs (government website data) to verify compliance requirements. Smart contracts automatically grant or deny access based on verified attestation sets.

**Insurance Underwriting**

Insurers can verify risk factors (driving record, health metrics, employment stability) instantly and privately, reducing underwriting from weeks to seconds.

_Technical approach:_ Users generate proofs from relevant sources: zkTLS for government/DMV website data, App Attestations for fitness data, DKIM Module for employment verification. Insurance application's smart contract evaluates proofs against underwriting criteria and issues instant quotes.

#### For DeFi and Financial Applications

**Undercollateralized Lending**

DeFi lending today requires overcollateralization because borrowers cannot prove creditworthiness. With verification, lending applications can assess real-world financial data privately.

_Technical approach:_ Borrowers generate DKIM proofs of income (payroll emails), zkTLS proofs of bank data, and App Attestation proofs of financial app history. Lending contracts evaluate these attestations to calculate risk and set loan terms, enabling lower collateral requirements.

**Verified DAO Governance**

DAOs can restrict voting or proposal rights to members who meet specific real-world criteria (industry expertise, geographic location, professional credentials) without doxxing members.

_Technical approach:_ DAO members generate proofs of relevant credentials. Governance contracts check attestations before allowing votes or proposals. Maintains pseudonymity while ensuring qualified participation.

#### For Gaming and Entertainment

**Verified Achievements and Reputation**

Gaming applications can let players prove achievements, rankings, and history from other games or platforms, creating portable gaming reputation.

_Technical approach:_ App Attestations verify in-game achievements and rankings. Players carry verified reputation across games built on Verona, unlocking rewards and status based on proven skill rather than starting from zero.

**Fair Play Verification**

Gaming and betting applications can use attestations to verify outcomes, prevent cheating, and ensure fair play without relying on centralized referees.

_Technical approach:_ Game state attestations provide cryptographic proof of outcomes. Smart contracts resolve bets and distribute winnings based on verified results.

#### For Agents and Intelligent Applications

These patterns extend to **authorized agents** that need grounded context without raw data access:

**Verified Context for AI Agents**

Agents can request scoped attestations (income range, loyalty tier, account standing) bound to a Meta Account instead of relying on unverified user input or scraped data.

_Technical approach:_ Users generate proofs via zkTLS, zkEmail, or App Attestations. Attestations are stored against the Meta Account and exposed to agents through application consent flows and emerging [Ask & MCP](../surfaces/ask-and-mcp.md) rails. See [Data Backpack](../surfaces/data-backpack.md) for the portable-storage direction.

**EarnOS-Style Verified Participation**

Reward and revenue-sharing flows can require cryptographic proof of eligibility before settlement on chain.

_Technical approach:_ Combine verification modules with Treasury-sponsored transactions so users never manage gas. See [EarnOS](../surfaces/earnos.md) and [Build on Verona — Verification](../../developers/verification/README.md).
