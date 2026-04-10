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

Everything else on **`authentication`** and env mapping is in the [appendix](#appendix-signer-configuration-reference).

## When to use signer mode

| Use signer mode when… | Prefer dashboard auth (`auto` / `popup` / `redirect`) when… |
| --------------------- | ----------------------------------------------------------- |
| You control the key material (Turnkey, Privy, custom HSM) or bridge a browser wallet as owner. | Standard Meta Account login via hosted dashboard is enough. |
| You must avoid a full navigation away from your SPA. | Popup or embedded dashboard UX is acceptable. |
| You are prototyping **wallet-led** abstract accounts (e.g. MetaMask in **`direct-signing-demo`**). | You want the fastest path with minimal AA API / contract metadata. |

Reference routes: [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) (Turnkey + gasless session signing) and [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) (wallet-led / `requireAuth` comparisons).

## Turnkey demo: registration pattern (short)

The **`signer-mode`** route pairs Turnkey UI with Abstraxion via **`registerAbstraxionLogin`**, **`registerAbstraxionLogout`**, **`registerCreateWallet`**, and **`registerWalletsGetter`** in **`providers.tsx`**. Same idea for other vendors: a small context + refs, and **`getSignerConfig`** reads the live signer. Details stay in the demo source; Turnkey env is in the [appendix](#appendix-signer-configuration-reference).

## Signing: session key vs direct

- **`useAbstraxionSigningClient()`** — default gasless / session-style path (typical dApp).
- **`useAbstraxionSigningClient({ requireAuth: true })`** — user-pays-gas style; often with explicit fee simulation instead of `"auto"` on `execute`.

See **`/direct-signing-demo`** in **`demo-app`** for side-by-side examples.

## Related

- [Web App Development — modes hub](README.md)
- [Custom UI and loading states](custom-ui-abstraxion-authentication.md)
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md)

---

## Appendix: Signer configuration reference

Match **`signer-mode/layout.tsx`** or extend it. Full commented env: **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)**.

### `authentication` when **`type: "signer"`**

| Required? | Field | Notes |
| --------- | ----- | ----- |
| Yes | **`type`** | Must be **`"signer"`**. |
| Yes | **`getSignerConfig`** | Your async factory → **`SignerConfig`**. |
| Yes | **`aaApiUrl`** | AA API base URL; for public XION chains use the value paired with your **`chainId`** in `.env.example`. |
| Yes | **`smartAccountContract`** | **`{ codeId, checksum, addressPrefix? }`**. **`codeId` / `checksum`** must match the AA API; **`addressPrefix`** optional (demo defaults **`xion`**). Use `.env.example` presets for standard networks. |
| No | **`indexer`** | Set when using **`NEXT_PUBLIC_INDEXER_URL`** (+ type/token per `.env.example`); else RPC discovery. |
| No | **`treasuryIndexer`** | **`{ url }`** when **`NEXT_PUBLIC_TREASURY_INDEXER_URL`** is set. |

### Top-level `config` & environment variables

**`chainId`** is required. **`normalizeAbstraxionConfig`** fills **`rpcUrl`**, **`restUrl`**, **`gasPrice`**, and **`feeGranter`** from **`@burnt-labs/constants`** when omitted for a known XION **`chainId`**.

| Required? | Variable | Maps to |
| --------- | -------- | ------- |
| Yes | `NEXT_PUBLIC_CHAIN_ID` | `config.chainId` |
| Yes | `NEXT_PUBLIC_AA_API_URL` | `authentication.aaApiUrl` |
| Yes | `NEXT_PUBLIC_CODE_ID` | `authentication.smartAccountContract.codeId` |
| Yes | `NEXT_PUBLIC_CHECKSUM` | `authentication.smartAccountContract.checksum` |
| No | `NEXT_PUBLIC_ADDRESS_PREFIX` | `authentication.smartAccountContract.addressPrefix` (demo → **`xion`** if unset) |
| No | `NEXT_PUBLIC_RPC_URL` | `config.rpcUrl` |
| No | `NEXT_PUBLIC_REST_URL` | `config.restUrl` |
| No | `NEXT_PUBLIC_GAS_PRICE` | `config.gasPrice` |
| No | `NEXT_PUBLIC_TREASURY_ADDRESS` | `config.treasury` (gasless UX) |
| No | `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` | `config.feeGranter` |
| No | `NEXT_PUBLIC_INDEXER_URL` | builds `authentication.indexer` (with **`INDEXER_TYPE`** / **`INDEXER_TOKEN`** as in `.env.example`) |
| No | `NEXT_PUBLIC_TREASURY_INDEXER_URL` | `authentication.treasuryIndexer.url` |
| No | `NEXT_PUBLIC_TURNKEY_ORG_ID` | Turnkey **`providers.tsx`** only |
| No | `NEXT_PUBLIC_TURNKEY_API_BASE_URL` | Turnkey **`providers.tsx`** only |

**Next.js:** demo layout uses **`export const dynamic = "force-dynamic"`** for runtime **`process.env`**. Missing **`CODE_ID`** / **`CHECKSUM`** in the browser: demo **logs an error**.
