# Gasless UX & Smart Permissions

Onboarding is one of the biggest barriers in Blockchain adoption. For many users, the need to manage seed phrases, install wallets, and acquire tokens just to interact with a dapp is a dealbreaker. At XION, we believe that **streamlining this experience is critical to bringing the next wave of users into the ecosystem**.

XION **Meta Accounts** are designed to solve this. Unlike traditional externally owned accounts (EOAs), Meta Accounts are **smart contract accounts,** which allow users can sign up and log in using familiar authenticators like **email, social login, wallets, or passkeys**. This removes the complexity of key management and lets users focus on what really matters: using your dapp.

To make this experience seamless, we’ve introduced the **Treasury contract**, which plays a crucial dual role:

* **Gasless Transactions** – It manages **Fee Grants**, so users don’t need tokens to pay for gas. This enables fully gasless interactions from the very first click.
* **Smart Permissions** – It simplifies dapp configuration by issuing **Authorization Grants**, which allow your frontend (via session keys) to execute pre-approved smart contract calls on behalf of the user’s Meta Account.

Together with our **OAuth2-style authentication** and **Abstraxion library** for generating secure, temporary session keys, these tools enable frictionless, secure, and user-friendly dapp experiences, **without compromising on decentralization or control**.

The following guides will help you understand how the Treasury contract works and how to set it up and configure it to power your dapp’s transaction flow:

<table data-view="cards"><thead><tr><th></th></tr></thead><tbody><tr><td><a href="https://docs.burnt.com/xion/developers/featured-guides/your-first-dapp/treasury-contracts"><strong>Enabling Gasless Transactions with Treasury Contracts</strong></a><br>Deploying your Treasury Contract for Delegated Authorization and Gasless Transactions.</td></tr></tbody></table>
