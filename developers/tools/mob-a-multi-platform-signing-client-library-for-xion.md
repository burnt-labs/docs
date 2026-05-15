# Mob: a multi-platform signing client library for XION



**Mob** is a multi-platform signing client for the XION blockchain with first-class session-key support. It's written in Rust and exposed through Mozilla's [UniFFI](https://github.com/mozilla/uniffi-rs) and has native libraries for **Kotlin, Swift, Python, Ruby, and any other UniFFI target,** plus the Rust crate itself for server and CLI use. Mobile, backend, scripts, desktop, automation: same SDK, same surface, same code paths.

It's the native equivalent of [xion.js](https://github.com/burnt-labs/xion.js) which is our JavaScript signing client without being tied to JavaScript or a web view.



### Why we built it

For most of XION's history, building against the chain meant utilizing [xion.js](https://github.com/burnt-labs/xion.js). That's fine if you're shipping a web app or a React Native app. It's a problem if you're not.

* A **Python data team** wanting to script a treasury or batch payouts had to either shell out to `xiond` or hand-roll protobuf signing.
* A **Ruby backend** doing payment reconciliation against on-chain state had no SDK at all.
* A **Go or Rust service** had to import `cosmrs` and re-implement XION's account-abstraction wrappers from scratch.
* A **native Android or iOS team** had to drag in React Native + a JavaScript bundle + a web-view OAuth dance to get session keys and gasless UX.

Every team was solving the same problem, sign a XION transaction, talk to an RPC, optionally wrap the call in `MsgExec` for authz, in their own way, in their own language, with their own bugs.

**Mob** is one implementation of that problem, in Rust, with idiomatic bindings to whatever language you're already writing.



### What can you do with it

* **Sign and send.** BIP39 mnemonics → BIP44 derivation → secp256k1 → Bech32 addresses. Three lines from mnemonic to a broadcast transaction, in any supported language.
* **Query.** Block height, account info, balances, transactions by hash. CosmWasm smart queries against any contract via the LCD endpoint.
* **Send tokens gaslessly.** Pass a `feeGranter` and gas is paid by a treasury contract — the user (or the script, or the service) pays nothing.
* **Run authz session keys.** Pass a `granter` and your write call gets wrapped in a `MsgExec`, signed by the session key but executed against another account. This is the native equivalent of an abstraxion session, and it works the same whether you're a mobile app, a backend service signing on behalf of customers, or a CLI tool.
* **Use meta-accounts.** Full `AbstractAccount` support with `Secp256k1`, `WebAuthn`, `JWT`, and `MultiSig` authenticators.
* **Catch typed errors.** A single `MobError` enum with 13 variants covers everything from `InsufficientFunds` to `KeyDerivation` — no string-matching against RPC blobs.

The same `send()` call across four languages, same parameter names, same return type:

**Rust**

```rust
let tx = client.send(
    recipient,
    vec![Coin::new("uxion", "1000000")],
    Some("gasless transfer".into()),
).await?;
```

**Kotlin**

```kotlin
val tx = client.send(
    toAddress  = recipient,
    amount     = listOf(Coin(denom = "uxion", amount = "1000000")),
    granter    = state.metaAccountAddress,
    feeGranter = state.treasuryAddress,
    memo       = "gasless transfer"
)
```

**Swift**

```swift
let tx = try client.send(
    toAddress:  recipient,
    amount:     [Coin(denom: "uxion", amount: "1000000")],
    granter:    state.metaAccountAddress,
    feeGranter: state.treasuryAddress,
    memo:       "gasless transfer"
)
```

**Python**

```python
tx = client.send(
    to_address=recipient,
    amount=[Coin(denom="uxion", amount="1000000")],
    granter=state.meta_account_address,
    fee_granter=state.treasury_address,
    memo="gasless transfer",
)
```

That's the whole authz / fee-granting story. Set `granter` and `feeGranter` to non-nil and you're routing through a meta-account with treasury-paid gas. Set them to null and you're a standalone wallet. Same client, same code, both modes.



### Demo project

[**xion-brale-demo**](https://github.com/burnt-labs/xion-brale-demo) is Brale's reference stablecoin wallet, shipping mob in both apps:

* **iOS** — SwiftUI, Keychain-backed mnemonic storage, native `ASWebAuthenticationSession` for the abstraxion OAuth handshake, FFI calls dispatched on a serial queue so the main thread never blocks.
* **Android** — Jetpack Compose, `EncryptedSharedPreferences` for the mnemonic, Custom Tabs for OAuth, the same FFI surface running through JNA.

The full flow works end-to-end today: a user signs in with abstraxion, the app verifies the on-chain grant, and from then on every transfer is signed by a session key, executed against the user's meta-account, and gassed by a treasury contract. Same flow on both platforms, same SDK on both platforms.

The mobile case is the most visible, but it's the same library a Python script uses to disburse a payroll, a Ruby backend uses to reconcile on-chain state, or a Rust service uses to sign treasury operations.



### When to use mob

| You're building…                     | Use                                              |
| ------------------------------------ | ------------------------------------------------ |
| A web app                            | `xion.js` / `abstraxion`                         |
| A React Native app                   | `xion.js` /`@burnt-labs/abstraxion-react-native` |
| A native iOS or Android app          | **mob** (Swift / Kotlin)                         |
| A Python script, ETL job, or backend | **mob** (Python bindings)                        |
| A Ruby service                       | **mob** (Ruby bindings)                          |
| A Rust service, CLI, or daemon       | **mob** (Rust crate, no FFI)                     |
| Anything else with a UniFFI target   | **mob** (generate bindings yourself)             |

If you're already in JavaScript-land, `xion.js` is still the right choice. Everywhere else, mob is the SDK we wish we'd had.
