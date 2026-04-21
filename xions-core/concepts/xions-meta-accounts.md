---
description: Protocol-Level Account Abstraction
---

# XION's Meta Accounts

## Protocol-Level Account Abstraction

XION's modular Meta Accounts introduce a highly adaptable and secure account creation and management framework. Built at the protocol level, Meta Accounts have significant benefits over traditional crypto wallets and enable novel Web3 application use cases.

#### Core Capabilities

* **Familiar Authentication** Enables non-crypto-native users to log in with familiar methods such as email, social login, FaceID, or passkeys. No wallet downloads, no browser extensions, no seed phrases.
* **Cross-Device Access** Enables a user to interact securely and seamlessly from any device of their choice using the same account. A user can start on desktop, continue on mobile, and switch to tablet without any migration or key transfer.
* **Session Keys** Enables an account to maintain security by providing time-limited sessions, proactively reducing the risks associated with potential key compromise. Applications can request scoped permissions (e.g., "execute in-game actions for the next 30 minutes") without requiring approval for every transaction.
* **Account Recovery** Enables the ability to recover an account if one of the authentication methods is lost by the user. Unlike traditional wallets where a lost key means permanent loss, Meta Accounts support configurable recovery mechanisms.
* **Programmable Permissions** Enables custom security policies at the account level. Set spending limits, require multi-factor authentication for high-value transactions, delegate limited access to third-party applications, and define custom rules for how the account can be used.
* **Modular Architecture** The authentication module is separate from the account logic. New authentication methods (biometrics, hardware keys, institutional custody) can be added without modifying the account contract itself.

#### How Meta Accounts Interact with Verification

Meta Accounts serve as the identity anchor for XION's verification infrastructure. When a user generates a zero-knowledge proof (via zkTLS, zkEmail, or App Attestations), the resulting attestation is bound to their Meta Account. This creates a persistent, verified identity layer:

* Attestations accumulate over time, building a rich verified profile
* Applications can query multiple attestations for a single Meta Account
* Users control which attestations are shared with which applications
* Verified credentials are portable across all applications on XION

This connection between Meta Accounts and verification is what enables the "Trust Layer" functionality. The account is the container for verified trust, and abstraction is what makes it accessible to everyone.

#### Start Building

Follow the Account Abstraction guide in the [Developer](/broken/pages/bFL2eEMYjRSn0lbLFgv4) section to start building with [Meta Accounts](../../developers/accounts/getting-started/) today. The Abstraxion SDK provides a complete integration for web and mobile platforms.

_For technical deep-dive into Meta Account architecture, see_ [_Meta Accounts Design_](meta-accounts-design.md)_._
