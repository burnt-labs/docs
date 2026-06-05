---
icon: wrench
---

# Tools & SDKs

Libraries and CLIs for building on XION—whether you ship a web or mobile app, automate from the terminal, or integrate native signing in Python, Kotlin, Swift, or Rust.

## xion.js

[**xion.js**](https://github.com/burnt-labs/xion.js) is XION’s JavaScript monorepo for Meta Accounts, gasless transactions, and Abstraxion. It powers React web apps and React Native mobile apps with shared packages (`@burnt-labs/abstraxion`, `@burnt-labs/abstraxion-core`, `@burnt-labs/abstraxion-react-native`) and a reference [`demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app).

If you are building in JavaScript or TypeScript, start here—then follow the platform guides below for end-to-end integration.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Web App Development</strong></td><td>React + Abstraxion: Meta Accounts, Treasury gasless flows, auth modes (<code>auto</code>, <code>signer</code>, <code>embedded</code>), and tutorials backed by <code>xion.js</code>.</td><td><a href="../accounts/web-app/">web-app</a></td></tr><tr><td><strong>Mobile App Development</strong></td><td>React Native + Expo with Abstraxion: environment setup, Meta Account auth, gasless UX, and common mobile fixes.</td><td><a href="../accounts/mobile-app/">mobile-app</a></td></tr></tbody></table>

## Mob

**Mob** is a multi-platform signing client for XION—Rust at the core, with native bindings via [UniFFI](https://github.com/mozilla/uniffi-rs) for Kotlin, Swift, Python, Ruby, and more. Use it when you are not in JavaScript: native iOS/Android, backend services, scripts, or automation. It supports session keys, treasury-paid gas, and meta-accounts—the native counterpart to xion.js.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Mob: multi-platform signing library</strong></td><td>Sign and send, query chain state, gasless transfers, authz session keys, and meta-account authenticators—same API surface across supported languages.</td><td><a href="mob-signing-client.md">mob-signing-client.md</a></td></tr></tbody></table>

## CLI tools

For direct chain interaction, scripting, validator workflows, or AI-agent automation, use the command-line tools below. They complement the SDKs above rather than replacing app integration paths.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Verona Agent Toolkit</strong></td><td>Gasless development with Meta Accounts, Treasury management, and OAuth2 client management. <strong>Beta</strong>—testnet (default) and mainnet (<code>--network mainnet</code>); faucet is testnet-only.</td><td><a href="verona-toolkit.md">verona-toolkit.md</a></td></tr><tr><td><strong>Daemon CLI (<code>xiond</code>)</strong></td><td>Traditional chain CLI for querying and transacting directly with XION endpoints.</td><td><a href="daemon-cli.md">daemon-cli.md</a></td></tr></tbody></table>
