---
description: Abstraxion authentication.type signer — getSignerConfig, AA API, env vars, Turnkey registration pattern
vars:
  doc_type: reference
  primary_auth_mode: signer
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo"
  required_env_vars: "NEXT_PUBLIC_CHAIN_ID + network preset block (AA API + smart-account wasm) from demo-app .env.example"
  optional_env_vars: "Appendix — RPC/REST/gas overrides, treasury, indexer, Turnkey, etc."
---

# Abstraxion signer mode

**Signer mode** (`authentication.type: "signer"`) runs Meta Account flows **without** redirecting the user to the full-page XION dashboard.

## The essentials: `getSignerConfig` and required wiring

**1. Implement `getSignerConfig`**

This is the **only custom logic** you must write: an async function that returns a **`SignerConfig`** when your wallet or custody layer is ready. The SDK calls it when the user connects.

In **`demo-app`**, see **[`useTurnkeyViem.ts`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyViem.ts)** (or **`useTurnkeyRawAPI.ts`**), wired through **[`useTurnkeyForAbstraxion`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyForAbstraxion.ts)** and passed from **[`signer-mode/layout.tsx`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/app/signer-mode/layout.tsx)** into **`AbstraxionProvider`**. Replace that hook with your wallet (MetaMask, Privy, …); keep the same **`authentication`** shape.

**2. Fields on `AbstraxionProvider` `config` (what is “default” vs custom)**

Top level you always pass **`chainId`**. **`normalizeAbstraxionConfig`** already fills **`rpcUrl`**, **`restUrl`**, **`gasPrice`**, and **`feeGranter`** from **`@burnt-labs/constants`** when you omit them for a **known XION `chainId`**—same pattern as other Abstraxion modes.

Under **`authentication`** with **`type: "signer"`**:

| Field | You implement? | Default / preset |
| ----- | -------------- | ---------------- |
| **`getSignerConfig`** | **Yes** — this is your wallet/custody integration. | — |
| **`aaApiUrl`** | No — for **public XION networks** use the **canonical AA API URL** paired with that **`chainId`**. | Published in **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)** (`NEXT_PUBLIC_AA_API_URL` block). Override only if you point at a **non-standard** AA deployment. |
| **`smartAccountContract`** | No — for **public XION networks** use the **canonical wasm `codeId` / `checksum`** (and optional **`addressPrefix`**, commonly **`xion`**) shipped for that network + AA API. | Same file: **`NEXT_PUBLIC_CODE_ID`**, **`NEXT_PUBLIC_CHECKSUM`**, **`NEXT_PUBLIC_ADDRESS_PREFIX`**. **`codeId` / `checksum` must match what that AA API expects** (see comments in `.env.example`). |

So **`aaApiUrl`** and **`smartAccountContract`** are **not** open-ended choices: they are **network defaults** you copy from the template for your **`chainId`**, not values you invent—unless you run custom infrastructure.

The TypeScript **`SignerAuthentication`** type still expects **`aaApiUrl`** and **`smartAccountContract`** on the object you pass in; fill them from that preset block (or your override).

**3. Environment variables (minimal mental model)**

1. Set **`NEXT_PUBLIC_CHAIN_ID`** to your target public network (e.g. testnet value from `.env.example`).
2. Copy the **Account Abstraction API** + **Smart account contract** lines from **`.env.example`** for that network into your env. Those strings **are** the defaults for `aaApiUrl` and `smartAccountContract`; the demo’s **`layout.tsx`** maps them into `AbstraxionProvider` config.

If **`CODE_ID`** / **`CHECKSUM`** are missing in the browser, the demo **logs an error**—the preset block should always include them for real use.

**That is enough to understand the integration.** RPC overrides, treasury, indexers, Turnkey org id, and the exact env→field mapping for **`layout.tsx`** are in the [appendix](#appendix-optional-provider-fields-and-environment-variables).

## When to use signer mode

| Use signer mode when… | Prefer dashboard auth (`auto` / `popup` / `redirect`) when… |
| --------------------- | ----------------------------------------------------------- |
| You control the key material (Turnkey, Privy, custom HSM) or bridge a browser wallet as owner. | Standard Meta Account login via hosted dashboard is enough. |
| You must avoid a full navigation away from your SPA. | Popup or embedded dashboard UX is acceptable. |
| You are prototyping **wallet-led** abstract accounts (e.g. MetaMask in **`direct-signing-demo`**). | You want the fastest path with minimal AA API / contract metadata. |

Reference routes: [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) (Turnkey + gasless session signing) and [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) (wallet-led / `requireAuth` comparisons).

## Turnkey demo: registration pattern (short)

The **`signer-mode`** route pairs Turnkey UI with Abstraxion via **`registerAbstraxionLogin`**, **`registerAbstraxionLogout`**, **`registerCreateWallet`**, and **`registerWalletsGetter`** in **`providers.tsx`**. Same idea for other vendors: a small context + refs, and **`getSignerConfig`** reads the live signer. Details stay in the demo source; optional env for Turnkey is in the [appendix](#appendix-optional-provider-fields-and-environment-variables).

## Signing: session key vs direct

- **`useAbstraxionSigningClient()`** — default gasless / session-style path (typical dApp).
- **`useAbstraxionSigningClient({ requireAuth: true })`** — user-pays-gas style; often with explicit fee simulation instead of `"auto"` on `execute`.

See **`/direct-signing-demo`** in **`demo-app`** for side-by-side examples.

## Related

- [Web App Development — modes hub](README.md)
- [Custom UI and loading states](custom-ui-abstraxion-authentication.md)
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md)

---

## Appendix: Optional provider fields and environment variables

Use this when you need **gasless treasury grants**, **faster indexers**, or to **match `signer-mode/layout.tsx` line-for-line**. The canonical list with comments is **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)**.

### Network preset env → `signer-mode/layout.tsx` (AA + wasm defaults)

These are the **defaults for public chains**—not “extra” knobs. The demo reads:

| Variable | Maps to |
| -------- | ------- |
| `NEXT_PUBLIC_CHAIN_ID` | `config.chainId` |
| `NEXT_PUBLIC_AA_API_URL` | `authentication.aaApiUrl` |
| `NEXT_PUBLIC_CODE_ID` | `smartAccountContract.codeId` |
| `NEXT_PUBLIC_CHECKSUM` | `smartAccountContract.checksum` |
| `NEXT_PUBLIC_ADDRESS_PREFIX` | `smartAccountContract.addressPrefix` (demo falls back to **`xion`** if unset) |

### Optional fields on `config`

**Under `authentication`**

- **`indexer`** — Only if you set **`NEXT_PUBLIC_INDEXER_URL`**. The demo builds a Numia or Subquery object from **`NEXT_PUBLIC_INDEXER_TYPE`** and **`NEXT_PUBLIC_INDEXER_TOKEN`** (see `.env.example` commented blocks). If the URL is unset, the SDK falls back to RPC-based discovery.
- **`treasuryIndexer`** — **`{ url }`** when **`NEXT_PUBLIC_TREASURY_INDEXER_URL`** is set; otherwise omitted in the demo.

**Top-level `config`**

- **`rpcUrl`**, **`restUrl`**, **`gasPrice`** — **`NEXT_PUBLIC_RPC_URL`**, **`NEXT_PUBLIC_REST_URL`**, **`NEXT_PUBLIC_GAS_PRICE`**. Often set in `.env.example`; for known XION **`chainId`** values the SDK can fill RPC/REST/gas when omitted.
- **`treasury`** — **`NEXT_PUBLIC_TREASURY_ADDRESS`** (gasless grant UX).
- **`feeGranter`** — **`NEXT_PUBLIC_FEE_GRANTER_ADDRESS`**.

**Framework note (Next.js App Router)**

- The demo **`signer-mode/layout.tsx`** uses **`export const dynamic = "force-dynamic"`** so runtime **`process.env`** is respected.

### Optional and demo-only environment variables

| Variable | Role |
| -------- | ---- |
| `NEXT_PUBLIC_RPC_URL` | Tendermint RPC override. |
| `NEXT_PUBLIC_REST_URL` | REST endpoint override. |
| `NEXT_PUBLIC_GAS_PRICE` | e.g. `0.001uxion`. |
| `NEXT_PUBLIC_TREASURY_ADDRESS` | Treasury contract for gasless flows. |
| `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` | Fee granter for grant creation. |
| `NEXT_PUBLIC_ADDRESS_PREFIX` | Bech32 prefix for smart accounts (default `xion` in demo). |
| `NEXT_PUBLIC_INDEXER_URL` | Enables **`indexer`** when set. |
| `NEXT_PUBLIC_INDEXER_TYPE` | `subquery` vs Numia-style (see `.env.example`). |
| `NEXT_PUBLIC_INDEXER_TOKEN` | Numia auth token when using Numia. |
| `NEXT_PUBLIC_TREASURY_INDEXER_URL` | DaoDao-style treasury indexer URL. |
| `NEXT_PUBLIC_TURNKEY_ORG_ID` | Required for **Turnkey** in **`providers.tsx`**, not for Abstraxion itself. |
| `NEXT_PUBLIC_TURNKEY_API_BASE_URL` | Turnkey API base (see `.env.example`). |
