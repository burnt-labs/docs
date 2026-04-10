---
description: Abstraxion authentication.type signer — getSignerConfig, AA API, env vars, Turnkey registration pattern
vars:
  doc_type: reference
  primary_auth_mode: signer
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo"
  required_env_vars: "NEXT_PUBLIC_CHAIN_ID, NEXT_PUBLIC_RPC_URL, NEXT_PUBLIC_REST_URL, NEXT_PUBLIC_GAS_PRICE, NEXT_PUBLIC_AA_API_URL, NEXT_PUBLIC_CODE_ID, NEXT_PUBLIC_CHECKSUM"
  optional_env_vars: "NEXT_PUBLIC_ADDRESS_PREFIX, NEXT_PUBLIC_TREASURY_ADDRESS, NEXT_PUBLIC_FEE_GRANTER_ADDRESS, NEXT_PUBLIC_INDEXER_URL, NEXT_PUBLIC_INDEXER_TOKEN, NEXT_PUBLIC_TREASURY_INDEXER_URL"
  indexer_subquery: "set NEXT_PUBLIC_INDEXER_TYPE=subquery when using Subquery (see demo layout)"
---

# Abstraxion signer mode

**Signer mode** (`authentication.type: "signer"`) runs Meta Account flows **without** redirecting the user to the full-page XION dashboard. The integration surface you own is primarily **`getSignerConfig`** — an async factory that returns a **`SignerConfig`** once your wallet or custody stack is ready. Abstraxion uses **`@burnt-labs/abstraxion-core`** under the hood for signing and account operations.

Reference implementation: [demo app `signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) and, for MetaMask + direct signing comparisons, [`direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo).

## When to use signer mode

| Use signer mode when… | Prefer dashboard auth (`auto` / `popup` / `redirect`) when… |
| --------------------- | ----------------------------------------------------------- |
| You control the key material (Turnkey, Privy, custom HSM) or bridge a browser wallet as owner. | Standard Meta Account login via hosted dashboard is enough. |
| You must avoid a full navigation away from your SPA. | Popup or embedded dashboard UX is acceptable. |
| You are prototyping **wallet-led** abstract accounts (e.g. MetaMask in **`direct-signing-demo`**). | You want the fastest path with minimal AA API / contract metadata. |

## Provider configuration shape

Signer authentication adds these fields on `AbstraxionProvider` **`config.authentication`** (alongside **`type: "signer"`**):

- **`getSignerConfig`** — `() => Promise<SignerConfig>`; **primary integration point** — return the active signer once your wallet or custody layer is ready.
- **`aaApiUrl`** — Account Abstraction API base URL for your environment.
- **`smartAccountContract`** — `{ codeId, checksum, addressPrefix? }` so new smart accounts can be created when none exist.
- **`indexer`** (optional) — Numia or Subquery-style indexer for faster account discovery.
- **`treasuryIndexer`** (optional) — DaoDao-style treasury indexer URL for grant configuration lookups.

Top-level **`treasury`** and **`feeGranter`** on `config` still apply for gasless paths, same as other modes.

## Environment variables (demo-aligned)

| Variable | Role |
| -------- | ---- |
| `NEXT_PUBLIC_CHAIN_ID` | Chain identifier |
| `NEXT_PUBLIC_RPC_URL` / `NEXT_PUBLIC_REST_URL` | Tendermint / REST endpoints |
| `NEXT_PUBLIC_GAS_PRICE` | Gas price string (e.g. `0.025uxion`) |
| `NEXT_PUBLIC_AA_API_URL` | AA API for signer mode |
| `NEXT_PUBLIC_CODE_ID` / `NEXT_PUBLIC_CHECKSUM` | Wasm code id and checksum for the smart account contract |
| `NEXT_PUBLIC_TREASURY_ADDRESS` | Treasury for fee grants (if using gasless) |
| `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` | Optional explicit fee granter |
| `NEXT_PUBLIC_INDEXER_URL` | Optional indexer base |
| `NEXT_PUBLIC_INDEXER_TOKEN` | Numia auth token when using Numia |
| `NEXT_PUBLIC_INDEXER_TYPE=subquery` | Switch indexer flavor; may require `codeId` in config |
| `NEXT_PUBLIC_TREASURY_INDEXER_URL` | Optional treasury indexer |

## Cross-library registration (Turnkey demo)

The Turnkey demo wires external UI into Abstraxion using **registrations** (see `providers.tsx` in the demo):

- **`registerAbstraxionLogin`** / **`registerAbstraxionLogout`** — connect Turnkey session start/end to Abstraxion’s login/logout.
- **`registerCreateWallet`** / **`registerWalletsGetter`** — expose wallet creation and enumeration to the SDK.

Use the same **pattern** for other vendors: one React context that holds refs, and `getSignerConfig` reads the active signer from that context.

## Signing: session key vs direct (`direct-signing-demo`)

Signer mode addresses **authentication**. **How** messages are signed (gasless session path vs user-pays-gas) is controlled by hooks:

- Default: **`useAbstraxionSigningClient()`** — session-style / Treasury-aligned signing for typical gasless dApps.
- **`useAbstraxionSigningClient({ requireAuth: true })`** — user-visible approval path; often paired with **explicit fee simulation** instead of `"auto"` on `execute`.

Study **`/direct-signing-demo`** in the monorepo for MetaMask registration, `requireAuth`, and fee handling side by side.

## Related

- [Web App Development — modes hub](README.md)
- [Custom UI and loading states](custom-ui-abstraxion-authentication.md)
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md)
