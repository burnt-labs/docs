---
description: Web app integration with Abstraxion — guides, authentication modes, and xion.js demo references
icon: browser
vars:
  doc_type: hub
  primary_auth_mode: auto
  sdk_packages: "@burnt-labs/abstraxion-react"
  demo_app_routes: "/auto, /embedded, /signer-mode, /saas"
  demo_repo: https://github.com/burnt-labs/xion.js/tree/main/demos/react
  required_env_vars_dashboard_auth: "NEXT_PUBLIC_CHAIN_ID, NEXT_PUBLIC_RPC_URL, NEXT_PUBLIC_REST_URL, NEXT_PUBLIC_GAS_PRICE, NEXT_PUBLIC_TREASURY_ADDRESS, NEXT_PUBLIC_AUTH_APP_URL"
  required_env_when: "auto, popup, or explicit redirect"
---

# Web App Development

Verona **Meta Accounts** and **gasless** flows are integrated into React web apps through **`@burnt-labs/abstraxion-react`** (provider, hooks, and `AbstraxionEmbed`). The package builds on **`@burnt-labs/abstraxion-js`**; use that runtime directly for non-React stacks.

## Live demos

{% hint style="success" %}
Run each auth mode in the browser—no clone required. Source for every route lives in [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) (Vite + React).
{% endhint %}

Each demo wires `AbstraxionProvider` with a different `authentication.type`. Open a route, then inspect its source for the exact config.

| Mode | Live demo | What it shows |
| ---- | --------- | ------------- |
| **Auto** (recommended) | [sdk-react.demos.burnt.com/auto](https://sdk-react.demos.burnt.com/auto) | Resolves to popup on desktop and redirect on mobile/PWA; state-machine debug panel, session-key and direct signing, manage-authenticators. |
| **Embedded** | [sdk-react.demos.burnt.com/embedded](https://sdk-react.demos.burnt.com/embedded) | Dashboard inside an iframe—toggle inline (always visible) vs dynamic (button + modal). |
| **Signer** | [sdk-react.demos.burnt.com/signer-mode](https://sdk-react.demos.burnt.com/signer-mode) | BYO keypair via Turnkey or MetaMask; smart account on top with session-key and direct signing, no dashboard redirects. |
| **SaaS** | [sdk-react.demos.burnt.com/saas](https://sdk-react.demos.burnt.com/saas) | End-to-end product flow: app login, then a Connect-with-XION-Pay style button that provisions the smart account; both sessions persist with expiry. |

## Guides in this section

Pick a document by what you are building. For **how to choose** `authentication.type` (`auto`, `signer`, `embedded`, …), see [Authentication and integration details](#authentication-and-integration-details) below. For **why** teams combine auth modes with signing hooks (before you copy env tables), see [Abstraxion modes and opportunities](abstraxion-modes-opportunities.md).

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>Account abstraction with gasless transactions</strong></td><td>End-to-end <strong>React (Vite)</strong> app: Treasury, Counter contract, <strong>auto</strong> auth, native <code>&lt;button&gt;</code>, hooks, and transactions.</td><td><a href="build-react-dapp-with-account-abstraxion.md">build-react-dapp-with-account-abstraxion</a></td></tr><tr><td><strong>Abstraxion modes and opportunities</strong></td><td>Explanation: start with <strong><code>auto</code> / dashboard</strong> auth; then <strong>signer + session</strong> vs <strong>signer + <code>requireAuth</code></strong>; multi-wallet UI as wiring only; passkey/backup as narrative last. Verify in the <a href="#live-demos">live demos</a> or <code>demos/react</code>.</td><td><a href="abstraxion-modes-opportunities.md">abstraxion-modes-opportunities</a></td></tr><tr><td><strong>Abstraxion signer mode</strong></td><td><strong>type: "signer"</strong>: implement <code>getSignerConfig</code>; session vs <code>requireAuth</code> signing; vendor wiring examples (e.g. Turnkey) and env from <code>demos/react/.env.example</code>.</td><td><a href="abstraxion-signer-mode.md">abstraxion-signer-mode</a></td></tr><tr><td><strong>Custom UI and Abstraxion loading states</strong></td><td>Hook-first UX: <code>login</code>, granular flags, session lifecycle (works across <code>auto</code> / <code>popup</code> / <code>redirect</code>).</td><td><a href="custom-ui-abstraxion-authentication.md">custom-ui-abstraxion-authentication</a></td></tr></tbody></table>

**Reference implementation:** [xion.js `demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) (Vite + React; one route per live demo above). The **main tutorial** uses **`import.meta.env.VITE_*`**. Legacy Next.js routes in [`apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app) remain for migration reference only.

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

These labels match the TypeScript union on `AbstraxionProvider` config (`@burnt-labs/abstraxion-react`).

| `type` | What it does |
| ------ | -------------- |
| **`auto`** | Resolved at runtime to **`popup`** or **`redirect`** (desktop vs mobile / standalone). **Recommended default** for dashboard auth. |
| **`popup`** | Auth UI opens in a separate window; parent dApp stays loaded. |
| **`redirect`** | Full-page navigation to the auth app and return (e.g. `?granted=true`). Used implicitly when `authentication` is omitted, or as the mobile leg of `auto`. |
| **`signer`** | No dashboard redirect: you supply a **`getSignerConfig`** (MetaMask, Turnkey, etc.) and AA API / contract metadata. See [Abstraxion signer mode](abstraxion-signer-mode.md). |
| **`embedded`** | Dashboard UI runs inside an **iframe** you host (`iframeUrl`). Use **`AbstraxionEmbed`** and `authentication.type: "embedded"`. See the [embedded live demo](https://sdk-react.demos.burnt.com/embedded) and embedded routes in [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react). |

### When to use which mode

| Scenario | Prefer | Live demo / source | Key env / config |
| -------- | ------ | ------------------ | ---------------- |
| Standard Meta Account login (most dApps) | **`auto`** | [Live `/auto`](https://sdk-react.demos.burnt.com/auto) · [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) | `VITE_AUTH_APP_URL` (Vite) or `NEXT_PUBLIC_AUTH_APP_URL` (Next.js) |
| You build all connection UI with hooks | **`auto`** (or `popup` / `redirect` if you pin one) | [Live `/auto`](https://sdk-react.demos.burnt.com/auto) · [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) | Same as above + treasury / chain |
| Legacy reference: pre-built modal + Burnt UI (not recommended for new apps) | **`auto`** or `redirect` | [`demos/react` source only](https://github.com/burnt-labs/xion.js/tree/main/demos/react) (no live deploy) | `@burnt-labs/ui` is deprecated; prefer **`login()`** + your own UI ([tutorial](build-react-dapp-with-account-abstraxion.md), [live `/auto`](https://sdk-react.demos.burnt.com/auto)) |
| Wallet-led or programmatic signer (no dashboard) | **`signer`** | [Live `/signer-mode`](https://sdk-react.demos.burnt.com/signer-mode) · [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) | Implement **`getSignerConfig`**; copy env from [`demos/react/.env.example`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) (AA API, code id/checksum, optional indexer) |
| Session-key vs user-pays-gas signing (`useAbstraxionSigningClient` vs `{ requireAuth: true }`) | See demo (auth is **`signer`** in that route) | [`demos/react` source only](https://github.com/burnt-labs/xion.js/tree/main/demos/react) (no live deploy) | Same env family as signer mode; compare hooks and fee simulation in source |
| Inline or “button + modal” embedded dashboard | **`embedded`** | [Live `/embedded`](https://sdk-react.demos.burnt.com/embedded) · [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) | `VITE_IFRAME_URL` or `NEXT_PUBLIC_IFRAME_URL` |
| SaaS / treasury-style product onboarding | **`auto`** + product shell | [Live `/saas`](https://sdk-react.demos.burnt.com/saas) · [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) | App login + Connect-style provisioning flow; see live demo for session persistence |

### Signing model (brief)

- **Default gasless path:** `useAbstraxionSigningClient()` — session-style signing (Treasury / grantee flow) as in the main tutorials.
- **Direct / user-pays-gas:** `useAbstraxionSigningClient({ requireAuth: true })` — see the **direct-signing** demo for fee simulation vs `"auto"`.

For **combinations and product posture** (not just hook names), see [Abstraxion modes and opportunities](abstraxion-modes-opportunities.md). Details stay in the tutorial and the demo source so this hub stays stable for navigation.

### React vs Next.js

New walkthroughs use **React + Vite** with **`VITE_*`** env vars and a provider at **`src/main.tsx`**. **Next.js** remains valid: mirror the same config in a **client** layout with **`NEXT_PUBLIC_*`**—see [`demos/react`](https://github.com/burnt-labs/xion.js/tree/main/demos/react) and the optional Next.js section in [Account abstraction with gasless transactions](build-react-dapp-with-account-abstraxion.md).

Some **Next** demo layouts use `export const dynamic = "force-dynamic"` when **server layouts read `process.env` at runtime** (e.g. signer or embedded). Pure **Vite** client apps do not need that.
