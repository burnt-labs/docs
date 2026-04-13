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

**Signer mode** only changes **how the user authenticates** (`getSignerConfig`). **How transactions are signed** is a separate choice: default **session / grantee** signing vs **direct** signing from the meta-account.

### Session key path (default hook)

Call **`useAbstraxionSigningClient()`** with no options (or only options that do not set **`requireAuth: true`**).

- After connect, the SDK uses a **session-style** signing client (**`GranteeSignerClient`**) aligned with **Treasury / fee grants**: gas is covered for allowed messages when grants are configured.
- Typical **gasless dApp** flow: user approves grants once (when applicable), then submits through the session client.
- Fees on **`execute`**: you often use **`"auto"`** for gas estimation like the main [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md).

### Turnkey registration (`/signer-mode` only)

The **[`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode)** example pairs Turnkey UI with Abstraxion using **`registerAbstraxionLogin`**, **`registerAbstraxionLogout`**, **`registerCreateWallet`**, and **`registerWalletsGetter`** in **`providers.tsx`**. That pattern answers “**how does custody UI hand off to `getSignerConfig`?**”—it is **not** part of the **direct vs session** choice; it belongs with the **session-path / gasless** story above.

**[`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo)** uses a **different** wallet registration flow (e.g. MetaMask) to **compare signing hooks**—do not look there for Turnkey registrations.

Same vendor pattern for non-Turnkey stacks: a small React context + refs, and **`getSignerConfig`** reads the live signer. Turnkey-specific env: [appendix](#appendix-signer-configuration-reference).

### Direct signing path (`requireAuth`)

Call **`useAbstraxionSigningClient({ requireAuth: true })`**.

- Each (or many) operations go through the **user’s authenticator** on the meta-account (**`AAClient`** path in signer mode), not the delegated session shortcut.
- **Gas** is usually **paid by the user** (or you model fees explicitly); **`"auto"`** on **`execute`** is less appropriate than **simulate + `calculateFee`** (or your own fee model)—see the demo for the split.
- Use when you need **per-transaction user approval**, **no session grants**, or to compare behavior next to the session path.

### Which demo to read

| Example | Route | What it highlights |
| ------- | ----- | ------------------ |
| **Session / gasless with signer auth** | **[`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode)** | **`type: "signer"`** + Turnkey **`getSignerConfig`**, registrations, **`useAbstraxionSigningClient()`** (default) for Treasury-style gasless flows. **Start here** if your product matches “external custody + gasless”. |
| **Direct vs session side by side** | **[`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo)** | Same **`signer`** auth; compares **default** signing client vs **`{ requireAuth: true }`**, **MetaMask**-style registration, **fee simulation** vs **`"auto"`**, and related UI. **Start here** if you need **direct signing** or to **diff** the two paths in one place. |

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
| No | **`aaApiUrl`** | **Defaults with `chainId`** for supported XION networks (same idea as RPC/REST). Only set in config to **override** or when your bundler supplies it via env (see **demo** `layout.tsx`). |
| No | **`smartAccountContract`** | **`{ codeId, checksum, addressPrefix? }`** — **defaults with `chainId`** for standard networks. Override for custom AA / wasm; **`codeId` / `checksum`** must stay consistent with the AA API you use. |
| No | **`indexer`** | Set when using **`NEXT_PUBLIC_INDEXER_URL`** (+ type/token per `.env.example`); else RPC discovery. |
| No | **`treasuryIndexer`** | **`{ url }`** when **`NEXT_PUBLIC_TREASURY_INDEXER_URL`** is set. |

If your **`SignerAuthentication`** type still lists **`aaApiUrl`** / **`smartAccountContract`**, populate them from the **same `chainId` preset** (not new values you invent)—equivalent to copying the block for that network from **[`.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)** into config.

### Top-level `config` & environment variables

**`chainId`** is required. **`normalizeAbstraxionConfig`** fills **`rpcUrl`**, **`restUrl`**, **`gasPrice`**, and **`feeGranter`** from **`@burnt-labs/constants`** when omitted for a known XION **`chainId`**. **`aaApiUrl`** and **`smartAccountContract`** follow the same pattern: **preset for the network id**—you do **not** need to treat them as extra required knobs unless you override or use a **custom** AA deployment.

| Required? | Variable | Maps to |
| --------- | -------- | ------- |
| Yes | `NEXT_PUBLIC_CHAIN_ID` | `config.chainId` — picks network defaults (RPC/REST/gas/fee granter, and the AA / wasm preset for that id). |
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

**Next.js:** demo layout uses **`export const dynamic = "force-dynamic"`** for runtime **`process.env`**. The **demo** `layout.tsx` still **reads** AA / wasm from env; if those vars are empty there, it **logs a warning**—your own app can rely on **chainId-only defaults** instead and omit those env vars when your code supplies the preset.
