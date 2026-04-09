---
## icon: browser
---

# Web App Development

{% hint style="info" %}
**Document metadata (for humans and agents)**\
`doc_type`: hub\
`primary_auth_mode`: auto\
`sdk_packages`: `@burnt-labs/abstraxion`, `@burnt-labs/abstraxion-core`\
`demo_app_routes`: `/`, `/loading-states`, `/abstraxion-ui`, `/signer-mode`, `/direct-signing-demo`, `/popup-demo`, `/embedded-inline`, `/embedded-dynamic`\
`demo_repo`: [github.com/burnt-labs/xion.js — `apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app)\
`required_env_vars` (typical dashboard flows): `NEXT_PUBLIC_CHAIN_ID`, `NEXT_PUBLIC_RPC_URL`, `NEXT_PUBLIC_REST_URL`, `NEXT_PUBLIC_GAS_PRICE`, `NEXT_PUBLIC_TREASURY_ADDRESS`, `NEXT_PUBLIC_AUTH_APP_URL` (when using `auto` / `popup` / explicit `redirect`)
{% endhint %}

XION **Meta Accounts** and **gasless** flows are integrated into web apps through **`@burnt-labs/abstraxion`** (React provider and hooks). Lower-level signing and account types are shared with **`@burnt-labs/abstraxion-core`**.

**Start here:** pick an **authentication mode** (`authentication.type` on `AbstraxionProvider`), then follow the guides in the table below.

## Recommended default: `auto`

For most new apps, set:

```typescript
authentication: {
  type: "auto",
  authAppUrl: process.env.NEXT_PUBLIC_AUTH_APP_URL,
},
```

**Behavior:** During configuration normalization, `auto` becomes either **`popup`** (desktop-style auth window) or **`redirect`** (full-page navigation on mobile / PWA). The SDK then runs the same code paths as those explicit modes. End users get an experience similar to common OAuth flows: the dApp stays mounted when a popup is used.

**Backward compatibility:** If you omit `authentication` entirely, the SDK defaults to **`redirect`** for the dashboard flow. That remains supported, but **new integrations should set `auto` explicitly** so intent is clear and desktop users get popups where appropriate.

## Glossary (`AuthenticationConfig.type`)

These labels match the TypeScript union on `AbstraxionProvider` config (`@burnt-labs/abstraxion`).

| `type` | What it does |
| ------ | -------------- |
| **`auto`** | Resolved at runtime to **`popup`** or **`redirect`** (desktop vs mobile / standalone). **Recommended default** for dashboard auth. |
| **`popup`** | Auth UI opens in a separate window; parent dApp stays loaded. |
| **`redirect`** | Full-page navigation to the auth app and return (e.g. `?granted=true`). Used implicitly when `authentication` is omitted, or as the mobile leg of `auto`. |
| **`signer`** | No dashboard redirect: you supply a **`getSignerConfig`** (MetaMask, Turnkey, etc.) and AA API / contract metadata. See [Abstraxion signer mode](abstraxion-signer-mode.md). |
| **`embedded`** | Dashboard UI runs inside an **iframe** you host (`iframeUrl`). Use **`AbstraxionEmbed`** and `authentication.type: "embedded"`. See demo routes `/embedded-inline` and `/embedded-dynamic`. |

## When to use which mode

| Scenario | Prefer | Example in demo app | Key env / config |
| -------- | ------ | ------------------- | ---------------- |
| Standard Meta Account login (most dApps) | **`auto`** | [`/popup-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/popup-demo) | `NEXT_PUBLIC_AUTH_APP_URL` |
| You build all connection UI with hooks | **`auto`** (or `popup` / `redirect` if you pin one) | [`/loading-states`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states) | Same as above + treasury / chain |
| You want the pre-built modal from Burnt UI | **`auto`** or `redirect` + `@burnt-labs/ui` | [`/abstraxion-ui`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/abstraxion-ui) | `@burnt-labs/ui` package; `Abstraxion` component |
| Wallet-led or programmatic signer (no dashboard) | **`signer`** | [`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode) | `NEXT_PUBLIC_AA_API_URL`, `NEXT_PUBLIC_CODE_ID`, `NEXT_PUBLIC_CHECKSUM`, optional indexer URLs |
| Session-key vs user-pays-gas signing comparison | **`signer`** (demo uses MetaMask) | [`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo) | Same family as signer mode |
| Inline or “button + modal” embedded dashboard | **`embedded`** | [`/embedded-inline`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/embedded-inline), [`/embedded-dynamic`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/embedded-dynamic) | `NEXT_PUBLIC_IFRAME_URL` (demo) |

## Signing model (brief)

- **Default gasless path:** `useAbstraxionSigningClient()` — session-style signing (Treasury / grantee flow) as in the main tutorials.
- **Direct / user-pays-gas:** `useAbstraxionSigningClient({ requireAuth: true })` — see the **direct-signing** demo for fee simulation vs `"auto"`.

Details stay in the tutorial and the demo source so this hub stays stable for navigation.

## Next.js note

Some demo layouts use `export const dynamic = "force-dynamic"` when **server layouts read `process.env` at runtime** (e.g. signer or embedded). If your provider reads secrets or non-`NEXT_PUBLIC_*` values in a layout, apply the same pattern.

## Guides in this section

| Guide | Description |
| ----- | ----------- |
| [Account abstraction with gasless transactions](build-react-dapp-with-account-abstraxion.md) | End-to-end Next.js app: Treasury, Counter contract, **`auto`** auth, hooks, and transactions. |
| [Custom UI and Abstraxion loading states](custom-ui-abstraxion-authentication.md) | Hook-first UX: `login`, granular flags, session lifecycle (works across `auto` / `popup` / `redirect`). |
| [Abstraxion signer mode](abstraxion-signer-mode.md) | **`type: "signer"`**: external wallets, `getSignerConfig`, registration callbacks, indexers. |

Public reference implementation: **[xion.js `apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app)** (Next.js App Router; each route isolates one pattern).
