# ACH & Bank Transfers

ACH and bank transfer integrations let Verona apps move users between traditional bank accounts and on-chain stablecoins. These flows are useful for fintech apps, wallets, marketplaces, payroll tools, savings products, and consumer apps that need a familiar bank-based funding or cash-out experience.

With Verona, developers can combine bank transfer providers with Meta Accounts and Abstraxion to create a smoother onboarding flow for users who may not already hold crypto. Users can connect with familiar authentication methods, link a bank account, fund their wallet, and cash out stablecoins without needing to understand the underlying blockchain mechanics.

### What You Can Build

ACH and bank transfer integrations can support flows such as:

* Bank account to stablecoin on-ramp
* Stablecoin to bank account off-ramp
* Wallet funding for consumer apps
* Payouts to linked bank accounts
* Stablecoin-backed balances for marketplace or fintech products
* Treasury or session-key based transaction flows for smoother repeat usage

### Production Considerations

When building ACH or bank transfer flows, make sure to:

* Keep provider API credentials server-side.
* Verify webhook signatures before updating local state.
* Show clear pending, completed, and failed transfer states.
* Use testnet credentials and test transfer types before moving to production.
* Understand the provider's compliance, KYC, KYB, settlement, and geographic requirements.
* Decide whether wallet transactions should use direct signing, Abstraxion session keys, or a hybrid flow.
