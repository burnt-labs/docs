---
description: Web app integration with Abstraxion — guides, authentication modes, and xion.js demo references
icon: browser
vars:
  doc_type: hub
  primary_auth_mode: auto
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/, /loading-states, /abstraxion-ui, /signer-mode, /direct-signing-demo, /popup-demo, /embedded-inline, /embedded-dynamic"
  demo_repo: https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app
  required_env_vars_dashboard_auth: "NEXT_PUBLIC_CHAIN_ID, NEXT_PUBLIC_RPC_URL, NEXT_PUBLIC_REST_URL, NEXT_PUBLIC_GAS_PRICE, NEXT_PUBLIC_TREASURY_ADDRESS, NEXT_PUBLIC_AUTH_APP_URL"
  required_env_when: "auto, popup, or explicit redirect"
---

# Web App Development

XION **Meta Accounts** and **gasless** flows are integrated into web apps through **`@burnt-labs/abstraxion`** (React provider and hooks). Lower-level signing and account types are shared with **`@burnt-labs/abstraxion-core`**.

## Guides in this section

Pick a document by what you are building. For **how to choose** `authentication.type` (`auto`, `signer`, `embedded`, …), see [Authentication and integration details](#authentication-and-integration-details) below. For **why** teams combine auth modes with signing hooks (before you copy env tables), see [Abstraxion modes and opportunities](abstraxion-modes-opportunities.md).

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Account abstraction with gasless transactions</strong></td><td>End-to-end <strong>React (Vite)</strong> app: Treasury, Counter contract, <strong>auto</strong> auth, native <code>&lt;button&gt;</code>, hooks, and transactions.</td><td><a href="build-react-dapp-with-account-abstraxion.md">build-react-dapp-with-account-abstraxion</a></td></tr><tr><td><strong>Abstraxion modes and opportunities</strong></td><td>Explanation: start with <strong><code>auto</code> / dashboard</strong> auth; then <strong>signer + session</strong> vs <strong>signer + <code>requireAuth</code></strong>; multi-wallet UI as wiring only; passkey/backup as narrative last. Verify in <code>demo-app</code>.</td><td><a href="abstraxion-modes-opportunities.md">abstraxion-modes-opportunities</a></td></tr><tr><td><strong>Abstraxion signer mode</strong></td><td><strong>type: "signer"</strong>: implement <code>getSignerConfig</code>; session vs <code>requireAuth</code> signing; vendor wiring examples (e.g. Turnkey) and env from <code>demo-app/.env.example</code>.</td><td><a href="abstraxion-signer-mode.md">abstraxion-signer-mode</a></td></tr><tr><td><strong>Custom UI and Abstraxion loading states</strong></td><td>Hook-first UX: <code>login</code>, granular flags, session lifecycle (works across <code>auto</code> / <code>popup</code> / <code>redirect</code>).</td><td><a href="custom-ui-abstraxion-authentication.md">custom-ui-abstraxion-authentication</a></td></tr></tbody></table>

**Reference implementation:** [xion.js `apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app) (Next.js App Router—each route isolates one pattern). The **main tutorial** is **Vite + React**; use **`import.meta.env.VITE_*`** there vs **`NEXT_PUBLIC_*`** in the demo.

---

## Authentication and integration details

Use this section when you need **mode names**, **defaults**, **env vars**, or **signing** behavior. The guides above walk through concrete projects; this part is the compact map.

### Recommended default: `auto`

For most new apps, set:

```typescript
authentication: {
  type: "auto",
  authAppUrl: process.env.NEXT_PUBLIC_AUTH_APP_URL,
},
```

**Behavior:** During configuration normalization, `auto` becomes either **`popup`** (desktop-style auth window) or **`redirect`** (full-page navigation on mobile / PWA). The SDK then runs the same code paths as those explicit modes. End users get an experience similar to common OAuth flows: the dApp stays mounted when a popup is used.

**Backward compatibility:** If you omit `authentication` entirely, the SDK defaults to **`redirect`** for the dashboard flow. That remains supported, but **new integrations should set `auto` explicitly** so intent is clear and desktop users get popups where appropriate.

### Glossary (`AuthenticationConfig.type`)

These labels match the TypeScript union on `AbstraxionProvider` config (`@burnt-labs/abstraxion`).

| `type` | What it does |
| ------ | -------------- |
| **`auto`** | Resolved at runtime to **`popup`** or **`redirect`** (desktop vs mobile / standalone). **Recommended default** for dashboard auth. |
| **`popup`** | Auth UI opens in a separate window; parent dApp stays loaded. |
| **`redirect`** | Full-page navigation to the auth app and return (e.g. `?granted=true`). Used implicitly when `authentication` is omitted, or as the mobile leg of `auto`. |
| **`signer`** | No dashboard redirect: you supply a **`getSignerConfig`** (MetaMask, Turnkey, etc.) and AA API / contract metadata. See [Abstraxion signer mode](abstraxion-signer-mode.md). |
| **`embedded`** | Dashboard UI runs inside an **iframe** you host (`iframeUrl`). Use **`AbstraxionEmbed`** and `authentication.type: "embedded"`. See demo routes `/embedded-inline` and `/embedded-dynamic`. |

### When to use which mode

| Scenario | Prefer | Example in demo app | Key env / config |
| -------- | ------ | ------------------- | ---------------- |
| Standard Meta Account login (most dApps) | **`auto`** | [`/popup-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/popup-demo) | `NEXT_PUBLIC_AUTH_APP_URL` |
| You build all connection UI with hooks | **`auto`** (or `popup` / `redirect` if you pin one) | [`/loading-states`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states) | Same as above + treasury / chain |
| Legacy reference: pre-built modal + Burnt UI (not recommended for new apps) | **`auto`** or `redirect` | [`/abstraxion-ui`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/abstraxion-ui) | `@burnt-labs/ui`; prefer **`login()`** + your own UI ([tutorial](build-react-dapp-with-account-abstraxion.md), [`/loading-states`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states)) |
| Wallet-led or programmatic signer (no dashboard) | **`signer`** | [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) | Implement **`getSignerConfig`**; copy env from [`demo-app/.env.example`](https://github.com/burnt-labs/xion.js/blob/main/apps/demo-app/.env.example) (AA API, code id/checksum, optional indexer) |
| Session-key vs user-pays-gas signing (`useAbstraxionSigningClient` vs `{ requireAuth: true }`) | See demo (auth is **`signer`** in that route) | [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) | Same env family as signer mode; compare hooks and fee simulation in source |
| Inline or “button + modal” embedded dashboard | **`embedded`** | [`/embedded-inline`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/embedded-inline), [`/embedded-dynamic`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/embedded-dynamic) | `NEXT_PUBLIC_IFRAME_URL` (demo) |

### Signing model (brief)

- **Default gasless path:** `useAbstraxionSigningClient()` — session-style signing (Treasury / grantee flow) as in the main tutorials.
- **Direct / user-pays-gas:** `useAbstraxionSigningClient({ requireAuth: true })` — see the **direct-signing** demo for fee simulation vs `"auto"`.

For **combinations and product posture** (not just hook names), see [Abstraxion modes and opportunities](abstraxion-modes-opportunities.md). Details stay in the tutorial and the demo source so this hub stays stable for navigation.

### React vs Next.js

New walkthroughs use **React + Vite** with **`VITE_*`** env vars and a provider at **`src/main.tsx`**. **Next.js** remains valid: mirror the same config in a **client** layout with **`NEXT_PUBLIC_*`**—see **`demo-app`** and the optional Next.js section in [Account abstraction with gasless transactions](build-react-dapp-with-account-abstraxion.md).

Some **Next** demo layouts use `export const dynamic = "force-dynamic"` when **server layouts read `process.env` at runtime** (e.g. signer or embedded). Pure **Vite** client apps do not need that.
