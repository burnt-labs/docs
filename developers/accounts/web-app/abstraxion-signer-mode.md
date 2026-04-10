---
description: Abstraxion authentication.type signer — getSignerConfig, AA API, env vars, Turnkey registration pattern
vars:
  doc_type: reference
  primary_auth_mode: signer
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo"
  required_env_vars: "See appendix — chainId + presets from demo-app .env.example"
  optional_env_vars: "Appendix — overrides, treasury, indexer, Turnkey, etc."
---

# Abstraxion signer mode

**Signer mode** (`authentication.type: "signer"`) runs Meta Account flows **without** redirecting the user to the full-page XION dashboard.

## The essentials: `getSignerConfig`

**What you implement**

Write **`getSignerConfig`**: an async function that returns a **`SignerConfig`** when your wallet or custody layer is ready. The SDK calls it when the user connects.

In **`demo-app`**, that logic lives in **[`useTurnkeyViem.ts`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyViem.ts)** (or **`useTurnkeyRawAPI.ts`**), selected by **[`useTurnkeyForAbstraxion`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyForAbstraxion.ts)** and passed from **[`signer-mode/layout.tsx`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/app/signer-mode/layout.tsx)** into **`AbstraxionProvider`**. Replace that hook with your stack (MetaMask, Privy, …).

**What you wire**

Put **`getSignerConfig`** on **`authentication`** next to **`type: "signer"`**, and set **`chainId`** (and anything else) the same way as the demo layout. **Do not re-derive** the boilerplate from scratch—start from **`layout.tsx`** + **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)**.

Everything else on **`authentication`** (preset URLs, wasm metadata, indexers, …) and env→config mapping is in the [appendix](#appendix-full-signer-config--environment-variables).

## When to use signer mode

| Use signer mode when… | Prefer dashboard auth (`auto` / `popup` / `redirect`) when… |
| --------------------- | ----------------------------------------------------------- |
| You control the key material (Turnkey, Privy, custom HSM) or bridge a browser wallet as owner. | Standard Meta Account login via hosted dashboard is enough. |
| You must avoid a full navigation away from your SPA. | Popup or embedded dashboard UX is acceptable. |
| You are prototyping **wallet-led** abstract accounts (e.g. MetaMask in **`direct-signing-demo`**). | You want the fastest path with minimal AA API / contract metadata. |

Reference routes: [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) (Turnkey + gasless session signing) and [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) (wallet-led / `requireAuth` comparisons).

## Turnkey demo: registration pattern (short)

The **`signer-mode`** route pairs Turnkey UI with Abstraxion via **`registerAbstraxionLogin`**, **`registerAbstraxionLogout`**, **`registerCreateWallet`**, and **`registerWalletsGetter`** in **`providers.tsx`**. Same idea for other vendors: a small context + refs, and **`getSignerConfig`** reads the live signer. Details stay in the demo source; Turnkey env is in the [appendix](#appendix-full-signer-config--environment-variables).

## Signing: session key vs direct

- **`useAbstraxionSigningClient()`** — default gasless / session-style path (typical dApp).
- **`useAbstraxionSigningClient({ requireAuth: true })`** — user-pays-gas style; often with explicit fee simulation instead of `"auto"` on `execute`.

See **`/direct-signing-demo`** in **`demo-app`** for side-by-side examples.

## Related

- [Web App Development — modes hub](README.md)
- [Custom UI and loading states](custom-ui-abstraxion-authentication.md)
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md)

---

## Appendix: Full signer `authentication` + environment variables

Use this to **match `signer-mode/layout.tsx`**, override presets, or add indexers / treasury. The canonical commented list is **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)**.

### `authentication` when **`type: "signer"`**

| Field | Role |
| ----- | ---- |
| **`type`** | `"signer"` |
| **`getSignerConfig`** | Your async factory (the only custom integration). |
| **`aaApiUrl`** | AA API base URL. For **public XION networks**, use the **canonical URL** for your **`chainId`** (see `.env.example` **`NEXT_PUBLIC_AA_API_URL`**). Override only for a **non-standard** AA deployment. |
| **`smartAccountContract`** | **`{ codeId, checksum, addressPrefix? }`**. For public networks, use the **preset wasm metadata** for that chain + AA API (`.env.example`: **`NEXT_PUBLIC_CODE_ID`**, **`NEXT_PUBLIC_CHECKSUM`**, **`NEXT_PUBLIC_ADDRESS_PREFIX`**). **`codeId` / `checksum` must match the AA API** (comments in `.env.example`). |
| **`indexer`** | Optional; see [Optional fields on `config`](#optional-fields-on-config) below. |
| **`treasuryIndexer`** | Optional; **`{ url }`** when using a treasury indexer URL. |

The TypeScript **`SignerAuthentication`** type expects **`aaApiUrl`** and **`smartAccountContract`** on the object you pass in; for standard deployments, **copy the preset values** from `.env.example` for your **`chainId`**.

### Top-level `config` (defaults from `chainId`)

**`normalizeAbstraxionConfig`** fills **`rpcUrl`**, **`restUrl`**, **`gasPrice`**, and **`feeGranter`** from **`@burnt-labs/constants`** when omitted for a **known XION `chainId`**.

You still pass **`chainId`** explicitly. **`treasury`** and **`feeGranter`** (and other top-level fields) are covered below and in `.env.example`.

### Preset env → `signer-mode/layout.tsx`

| Variable | Maps to |
| -------- | ------- |
| `NEXT_PUBLIC_CHAIN_ID` | `config.chainId` |
| `NEXT_PUBLIC_AA_API_URL` | `authentication.aaApiUrl` |
| `NEXT_PUBLIC_CODE_ID` | `smartAccountContract.codeId` |
| `NEXT_PUBLIC_CHECKSUM` | `smartAccountContract.checksum` |
| `NEXT_PUBLIC_ADDRESS_PREFIX` | `smartAccountContract.addressPrefix` (demo falls back to **`xion`** if unset) |

**Minimal env flow:** set **`NEXT_PUBLIC_CHAIN_ID`**, then copy the **Account Abstraction API** + **Smart account contract** block from `.env.example` for that network. If **`CODE_ID`** / **`CHECKSUM`** are missing in the browser, the demo **logs an error**.

### Optional fields on `config`

**Under `authentication`**

- **`indexer`** — Only if **`NEXT_PUBLIC_INDEXER_URL`** is set. The demo builds Numia or Subquery from **`NEXT_PUBLIC_INDEXER_TYPE`** / **`NEXT_PUBLIC_INDEXER_TOKEN`** (see `.env.example`). If unset, RPC-based discovery.
- **`treasuryIndexer`** — **`{ url }`** when **`NEXT_PUBLIC_TREASURY_INDEXER_URL`** is set.

**Top-level**

- **`rpcUrl`**, **`restUrl`**, **`gasPrice`** — **`NEXT_PUBLIC_RPC_URL`**, **`NEXT_PUBLIC_REST_URL`**, **`NEXT_PUBLIC_GAS_PRICE`**; optional when **`chainId`** is a known XION network.
- **`treasury`** — **`NEXT_PUBLIC_TREASURY_ADDRESS`**.
- **`feeGranter`** — **`NEXT_PUBLIC_FEE_GRANTER_ADDRESS`**.

**Framework (Next.js App Router)**

- **`signer-mode/layout.tsx`** uses **`export const dynamic = "force-dynamic"`** so runtime **`process.env`** is respected.

### Other environment variables (overrides & demo-only)

| Variable | Role |
| -------- | ---- |
| `NEXT_PUBLIC_RPC_URL` | Tendermint RPC override. |
| `NEXT_PUBLIC_REST_URL` | REST endpoint override. |
| `NEXT_PUBLIC_GAS_PRICE` | e.g. `0.001uxion`. |
| `NEXT_PUBLIC_TREASURY_ADDRESS` | Treasury for gasless flows. |
| `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` | Fee granter for grant creation. |
| `NEXT_PUBLIC_ADDRESS_PREFIX` | Bech32 prefix (default `xion` in demo). |
| `NEXT_PUBLIC_INDEXER_URL` | Enables **`indexer`**. |
| `NEXT_PUBLIC_INDEXER_TYPE` | `subquery` vs Numia (see `.env.example`). |
| `NEXT_PUBLIC_INDEXER_TOKEN` | Numia token when applicable. |
| `NEXT_PUBLIC_TREASURY_INDEXER_URL` | DaoDao-style treasury indexer. |
| `NEXT_PUBLIC_TURNKEY_ORG_ID` | **Turnkey** in **`providers.tsx`**, not Abstraxion core. |
| `NEXT_PUBLIC_TURNKEY_API_BASE_URL` | Turnkey API base (`.env.example`). |
