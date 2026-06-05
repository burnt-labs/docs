---
icon: user-robot
---

# Computation

The Computation layer covers CosmWasm smart contracts, business logic, and workflows on the **XION** network (`xiond`, `xion-testnet-2`).

### Contract development notes

WASM binaries built with the **latest stable Rust** may fail to upload to XION (`xiond tx wasm store`) because the chain does not support WebAssembly **bulk memory** instructions that newer Rust toolchains can emit.

If you run into store or validation errors after upgrading Rust, create `rust-toolchain.toml` at your **contract project root**:

```toml
[toolchain]
channel = "1.86"
targets = ["wasm32-unknown-unknown"]
```

**Why 1.86?** Rust **1.87+** stable releases may emit WebAssembly with **bulk memory** enabled for `wasm32-unknown-unknown`. XION’s `wasm store` and runtime do not support bulk memory today, so uploads can fail even when local builds succeed. **1.86** is the latest stable release known to produce upload-compatible WASM for XION at the time of writing (May 2026). When you upgrade Rust—or when XION enables bulk memory—re-test `xiond tx wasm store` and update the pinned channel if needed.

Rebuild the contract (and re-run the [CosmWasm optimizer](local-development/deploy-a-cosmwasm-smart-contract.md) if you use Docker) before uploading again. See also the [local environment setup](local-development/setting-up-env/installation-prerequisites-setup-local-environment.md#rust) guide.

### Overview

Smart contracts on XION are built using CosmWasm and enable:

* **Business Logic**: Automate workflows and processes
* **State Management**: Store and manage application state
* **Integration**: Connect with other XION features (accounts, payments, verification)

### Quick Start

Get started quickly with pre-built app templates:

* [zero-to-dapp-in-5-minutes](quick-start/zero-to-dapp-in-5-minutes/ "mention")

### Local Development

Learn how to set up your development environment and deploy your first contract:

* [setting-up-env](local-development/setting-up-env/ "mention")
* [daemon-cli.md](../tools/daemon-cli.md "mention")
* [deploy-a-cosmwasm-smart-contract.md](local-development/deploy-a-cosmwasm-smart-contract.md "mention")

### Contract Library

Browse pre-built, audited contracts you can use in your applications:

* [re-using-existing-contracts](re-using-existing-contracts/ "mention")

### Integrations

Learn how to integrate with XION features:

* [token-factory](integrations/token-factory/ "mention")
* [oracles](integrations/oracles/ "mention")
* [websockets](integrations/websockets/ "mention")
* [indexers](integrations/indexers/ "mention")

### Use Cases

Explore more example applications and use cases:

* [use-cases](use-cases/ "mention")
