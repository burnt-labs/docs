---
description: Abstraxion authentication.type signer — getSignerConfig, AA API, env vars, Turnkey registration pattern
vars:
  doc_type: reference
  primary_auth_mode: signer
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo"
  required_env_vars: "chainId (+ getSignerConfig in app code); AA/wasm default with network id"
  optional_env_vars: "Appendix — env overrides, treasury, indexer, Turnkey, etc."
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

Concrete **demo routes** for signer auth + signing paths are in [Signing: session key vs direct](#signing-session-key-vs-direct) below.

## Signing: session key vs direct

**Signer mode** only changes **how the user authenticates** (`getSignerConfig`). **How transactions are signed** is only **two** choices below: **session (default hook)** vs **direct (`requireAuth`)**. Turnkey in **`/signer-mode`** is **example wiring** for the session demo (custody UI → `getSignerConfig`), not a third signing mode.

### Session signing (default `useAbstraxionSigningClient()`)

Call **`useAbstraxionSigningClient()`** with no options (or only options that do not set **`requireAuth: true`**).

- After connect, the SDK uses a **session-style** signing client (**`GranteeSignerClient`**) aligned with **Treasury / fee grants**: gas is covered for allowed messages when grants are configured.
- Typical **gasless dApp** flow: user approves grants once (when applicable), then submits through the session client.
- Fees on **`execute`**: you often use **`"auto"`** for gas estimation like the main [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md).

**Vendor example — Turnkey on [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode):** that route pairs Turnkey with Abstraxion using **`registerAbstraxionLogin`**, **`registerAbstraxionLogout`**, **`registerCreateWallet`**, and **`registerWalletsGetter`** in **`providers.tsx`** (“how does custody UI hand off before **`getSignerConfig`** runs?”). The same **context + refs** idea applies to other vendors. Turnkey env: [appendix](#appendix-signer-configuration-reference). For **MetaMask**-style wiring used to **diff** session vs direct signing, use **`/direct-signing-demo`** instead of Turnkey there.

### Direct signing (`requireAuth: true`)

Call **`useAbstraxionSigningClient({ requireAuth: true })`**.

- Each (or many) operations go through the **user’s authenticator** on the meta-account (**`AAClient`** path in signer mode), not the delegated session shortcut.
- **Gas** is usually **paid by the user** (or you model fees explicitly); **`"auto"`** on **`execute`** is less appropriate than **simulate + `calculateFee`** (or your own fee model)—see the demo for the split.
- Use when you need **per-transaction user approval**, **no session grants**, or to compare behavior next to the session path.

### Which demo to read

| Example | Route | What it highlights |
| ------- | ----- | ------------------ |
| **Session / gasless with signer auth** | **[`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode)** | **`signer`** + Turnkey → **`getSignerConfig`**; default **`useAbstraxionSigningClient()`**; gasless / Treasury. |
| **Direct vs session side by side** | **[`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo)** | Same **`signer`** auth; **default vs `requireAuth: true`**; MetaMask-style wallet; **simulate / fees** vs **`"auto"`**. |

Run **`demo-app`** locally and open those routes after reading the page components and hooks—they are the **authoritative** examples next to this page.

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
| No | **`aaApiUrl`** | Use the **preset URL** for your **`chainId`** from `.env.example` (or override). **Demo** `layout.tsx` reads **`NEXT_PUBLIC_AA_API_URL`**. |
| No | **`smartAccountContract`** | Use the **preset wasm** for that network + AA API from `.env.example` (or override). **`codeId` / `checksum`** must match the AA API you use. |
| No | **`indexer`** | Set when using **`NEXT_PUBLIC_INDEXER_URL`** (+ type/token per `.env.example`); else RPC discovery. |
| No | **`treasuryIndexer`** | **`{ url }`** when **`NEXT_PUBLIC_TREASURY_INDEXER_URL`** is set. |

If your **`SignerAuthentication`** type still lists **`aaApiUrl`** / **`smartAccountContract`**, populate them from the **same `chainId` preset** (not new values you invent)—equivalent to copying the block for that network from **[`.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)** into config.

### Top-level `config` & environment variables

**`chainId`** is required. **`normalizeAbstraxionConfig`** fills **`rpcUrl`**, **`restUrl`**, **`gasPrice`**, and **`feeGranter`** from **`@burnt-labs/constants`** when omitted for a known XION **`chainId`**.

**`aaApiUrl`** and **`smartAccountContract`** are **not** filled by that same helper in the public SDK today—treat them as **documented presets per network** (same values as the **`chainId` block** in **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)**): copy or merge them in app config. You override only for **custom** AA / wasm.

| Required? | Variable | Maps to |
| --------- | -------- | ------- |
| Yes | `NEXT_PUBLIC_CHAIN_ID` | `config.chainId` — required network id; enables RPC/REST/gas/fee-granter **defaults** via **`normalizeAbstraxionConfig`** when those env vars are unset. |
| No | `NEXT_PUBLIC_AA_API_URL` | `authentication.aaApiUrl` — **override**; demo reads from env. |
| No | `NEXT_PUBLIC_CODE_ID` | `authentication.smartAccountContract.codeId` — **override**; demo reads from env. |
| No | `NEXT_PUBLIC_CHECKSUM` | `authentication.smartAccountContract.checksum` — **override**; demo reads from env. |
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

**Next.js:** demo layout uses **`export const dynamic = "force-dynamic"`** for runtime **`process.env`**. The **demo** `layout.tsx` reads AA / wasm from env; if **`CODE_ID`** / **`CHECKSUM`** are missing in the browser, it **logs an error**. Your app can instead **hardcode or merge** the `.env.example` preset for your **`chainId`** and omit those env vars.
