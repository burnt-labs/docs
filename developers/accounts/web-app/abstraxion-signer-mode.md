---
description: Abstraxion authentication.type signer — getSignerConfig, AA API, env vars, Turnkey registration pattern
vars:
  doc_type: reference
  primary_auth_mode: signer
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo"
  required_env_vars: "NEXT_PUBLIC_CHAIN_ID, NEXT_PUBLIC_AA_API_URL, NEXT_PUBLIC_CODE_ID, NEXT_PUBLIC_CHECKSUM"
  optional_env_vars: "See demo-app .env.example (RPC/REST/gas, treasury, indexer, Turnkey org)"
---

# Abstraxion signer mode

**Signer mode** (`authentication.type: "signer"`) runs Meta Account flows **without** redirecting the user to the full-page XION dashboard.

## What you actually implement

The **only app-specific piece** is **`getSignerConfig`**: an async function that returns a **`SignerConfig`** when your wallet or custody layer is ready. Everything else is **wiring** the same `AbstraxionProvider` fields the demo uses (mostly from env).

In **`demo-app`**, that function is built in **[`useTurnkeyViem.ts`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyViem.ts)** (or **`useTurnkeyRawAPI.ts`** for the raw Turnkey API path), selected via **[`useTurnkeyForAbstraxion`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/hooks/useTurnkeyForAbstraxion.ts)**. **[`signer-mode/layout.tsx`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/src/app/signer-mode/layout.tsx)** does **`const { getSignerConfig } = useTurnkeyForAbstraxion(...)`** and passes **`getSignerConfig`** into **`authentication`**. Swap Turnkey for MetaMask, Privy, etc. by replacing **that hook/module**—keep the layout’s env-backed config shape.

Reference routes: [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) (Turnkey + session signing) and [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) (wallet-led signing comparisons).

## When to use signer mode

| Use signer mode when… | Prefer dashboard auth (`auto` / `popup` / `redirect`) when… |
| --------------------- | ----------------------------------------------------------- |
| You control the key material (Turnkey, Privy, custom HSM) or bridge a browser wallet as owner. | Standard Meta Account login via hosted dashboard is enough. |
| You must avoid a full navigation away from your SPA. | Popup or embedded dashboard UX is acceptable. |
| You are prototyping **wallet-led** abstract accounts (e.g. MetaMask in **`direct-signing-demo`**). | You want the fastest path with minimal AA API / contract metadata. |

## Provider configuration (matches `signer-mode/layout.tsx`)

`AbstraxionProvider` **`config`** should include:

**`authentication` (type `"signer"`)**

- **`getSignerConfig`** — your implementation (see above).
- **`aaApiUrl`** — AA API base URL (`NEXT_PUBLIC_AA_API_URL` in the demo).
- **`smartAccountContract`** — `{ codeId, checksum, addressPrefix? }` from env (demo: `NEXT_PUBLIC_CODE_ID`, `NEXT_PUBLIC_CHECKSUM`, optional `NEXT_PUBLIC_ADDRESS_PREFIX`).
- **`indexer`** — optional; built in the demo only when `NEXT_PUBLIC_INDEXER_URL` is set (Numia vs Subquery via `NEXT_PUBLIC_INDEXER_TYPE` / token—same logic as layout).
- **`treasuryIndexer`** — optional; demo passes `{ url }` when `NEXT_PUBLIC_TREASURY_INDEXER_URL` is set.

**Top-level (same as other modes)**

- **`chainId`**, **`rpcUrl`**, **`restUrl`**, **`gasPrice`** — demo reads the `NEXT_PUBLIC_*` chain vars (optional overrides; SDK can fill known networks from `chainId` when omitted).
- **`treasury`**, **`feeGranter`** — gasless / grant flows (`NEXT_PUBLIC_TREASURY_ADDRESS`, `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` in `.env.example`).

Next.js **App Router**: the demo layout uses **`export const dynamic = "force-dynamic"`** because env is read at runtime.

## Environment variables

Do **not** treat the following as a second source of truth—copy **[`apps/demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example)** and edit values there. It already groups **chain**, **AA API**, **smart account wasm** (`CODE_ID` / `CHECKSUM` must match the AA API), **treasury**, **optional indexer**, **optional treasury indexer**, and **Turnkey** vars for this route.

**Tight list (what `signer-mode/layout.tsx` actually consumes):**

| Variable | Notes |
| -------- | ----- |
| `NEXT_PUBLIC_CHAIN_ID` | Passed to `config.chainId`. |
| `NEXT_PUBLIC_AA_API_URL` | Passed to `authentication.aaApiUrl`. |
| `NEXT_PUBLIC_CODE_ID` / `NEXT_PUBLIC_CHECKSUM` | `smartAccountContract`; layout logs if missing in the browser. |
| `NEXT_PUBLIC_RPC_URL`, `NEXT_PUBLIC_REST_URL`, `NEXT_PUBLIC_GAS_PRICE` | Optional overrides on `config` (same as rest of demo-app). |
| `NEXT_PUBLIC_TREASURY_ADDRESS`, `NEXT_PUBLIC_FEE_GRANTER_ADDRESS` | Top-level `treasury` / `feeGranter`. |
| `NEXT_PUBLIC_ADDRESS_PREFIX` | Smart-account prefix; demo defaults to `xion` if unset. |
| `NEXT_PUBLIC_INDEXER_URL` | If unset, **`indexer`** is omitted (RPC path). If set, see `.env.example` for Numia vs Subquery (`NEXT_PUBLIC_INDEXER_TYPE`, `NEXT_PUBLIC_INDEXER_TOKEN`). |
| `NEXT_PUBLIC_TREASURY_INDEXER_URL` | If set, passed as `treasuryIndexer.url`. |

**Turnkey embed only** (`signer-mode/providers.tsx`, not Abstraxion itself): **`NEXT_PUBLIC_TURNKEY_ORG_ID`**, and **`NEXT_PUBLIC_TURNKEY_API_BASE_URL`** as in `.env.example`.

We no longer enumerate every indexer variant in a long table—follow the commented blocks in **`.env.example`** so your config stays aligned with the demo.

## Cross-library registration (Turnkey demo)

The Turnkey demo wires external UI into Abstraxion using **registrations** (see **`providers.tsx`**):

- **`registerAbstraxionLogin`** / **`registerAbstraxionLogout`** — connect Turnkey session start/end to Abstraxion’s login/logout.
- **`registerCreateWallet`** / **`registerWalletsGetter`** — expose wallet creation and enumeration to the SDK.

Use the same **pattern** for other vendors: one React context that holds refs, and **`getSignerConfig`** reads the active signer from that context.

## Signing: session key vs direct (`direct-signing-demo`)

Signer mode addresses **authentication**. **How** messages are signed (gasless session path vs user-pays-gas) is controlled by hooks:

- Default: **`useAbstraxionSigningClient()`** — session-style / Treasury-aligned signing for typical gasless dApps.
- **`useAbstraxionSigningClient({ requireAuth: true })`** — user-visible approval path; often paired with **explicit fee simulation** instead of `"auto"` on `execute`.

Study **`/direct-signing-demo`** in the monorepo for MetaMask registration, `requireAuth`, and fee handling side by side.

## Related

- [Web App Development — modes hub](README.md)
- [Custom UI and loading states](custom-ui-abstraxion-authentication.md)
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md)
