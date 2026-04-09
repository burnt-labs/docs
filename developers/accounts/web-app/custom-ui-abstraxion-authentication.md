---
description: Hook-first Abstraxion auth — loading flags, login(), session lifecycle (demo /loading-states)
vars:
  doc_type: guide
  primary_auth_mode: auto
  primary_auth_note: "recommended default; hook flags apply across auto, popup, and redirect"
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/loading-states"
  required_env_vars: "NEXT_PUBLIC_CHAIN_ID, NEXT_PUBLIC_RPC_URL, NEXT_PUBLIC_REST_URL, NEXT_PUBLIC_GAS_PRICE, NEXT_PUBLIC_TREASURY_ADDRESS, NEXT_PUBLIC_AUTH_APP_URL"
  required_env_when: "dashboard auth (auto, popup, or redirect)"
---

# Custom UI and Abstraxion loading states

This guide is about **your own buttons, copy, and loading states**—using **`useAbstraxionAccount`** (and optionally **`useAbstraxionSigningClient`**) instead of the pre-built **`<Abstraxion />`** modal from the [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md). It does **not** walk through choosing or comparing **`authentication.type`**; that belongs in the section hub.

**Live reference:** [xion.js `apps/demo-app` — `/loading-states`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states).

**Prerequisite:** You have already wrapped the app with **`AbstraxionProvider`** and set **`authentication`** (for dashboard Meta Account login, **`auto`** is the usual default). See [Web App Development](README.md) for the mode glossary, env vars, and when to use **`signer`** or **embedded**—those topics are not repeated here. The **`login()`**, **`?granted=true`**, and flag patterns below apply to **dashboard-style** flows (`auto` / `popup` / `redirect`); for **`type: "signer"`**, start from [Abstraxion signer mode](abstraxion-signer-mode.md).

Hook-level flags are **independent of whether you render a modal**: they describe session and navigation state from the SDK, not a specific widget.

## `useAbstraxionAccount` flags

Destructure the hook and drive your layout from granular booleans (wording aligned with the demo tooltips):

| Flag | Meaning |
| ---- | ------- |
| **`isInitializing`** | Brief period after mount before the provider knows real connection state (avoid flashing “logged out” too early). |
| **`isConnecting`** | User initiated login; active connection attempt in progress. |
| **`isConnected`** | Context resolved: user has an active session **or** you can treat as “ready to show app shell.” |
| **`isDisconnected`** | User explicitly logged out; blocks auto re-connect until they start login again. |
| **`isLoading`** | Aggregated busy state: initializing, connecting, or transitional work—use for disabling primary actions / showing a single spinner. |
| **`isReturningFromAuth`** | User just came back from the auth app callback (e.g. redirect flow); optional dedicated “Finishing sign-in…” UI. |
| **`isLoggingIn`** | Narrow window right after `login()` is invoked while the SDK starts the flow. |

**Session lifecycle (simplified):** mount → **`isInitializing`** → restored session or disconnected → user taps connect → **`isLoggingIn`** / **`isConnecting`** → popup closes or redirect returns (**`isReturningFromAuth`**) → **`isConnected`** with `data.bech32Address`.

Use **`isLoading`** when you want one gate for “disable submit / show full-page loader”; combine finer flags when you want different copy for “opening auth” vs “restoring session”.

## `login()` without the `<Abstraxion />` modal

Call **`login()`** from `useAbstraxionAccount` to start authentication without importing the pre-built modal component.

```typescript
const { data: account, login, logout, isConnecting, isLoading } =
  useAbstraxionAccount();
```

You do **not** need `@burnt-labs/abstraxion/dist/index.css` for hook-only UIs (you still typically import **`@burnt-labs/ui`** styles if you use Burnt UI components).

## Optional: finishing login after redirect

With **`auto`** on desktop, auth often runs in a **popup** and the parent page stays mounted. With **full-page redirect** (typical mobile leg of **`auto`**, or explicit **`redirect`**), the user may return to your URL with **`?granted=true`**. Call **`login()`** once to finalize session restoration (the demo illustrates this). Popup-heavy flows hit this path less often.

```typescript
useEffect(() => {
  const searchParams = new URLSearchParams(window.location.search);
  if (searchParams.get("granted") === "true") {
    void login();
  }
}, [login]);
```

## Example: minimal custom UI

```typescript
"use client";
import { useAbstraxionAccount, useAbstraxionSigningClient } from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import "@burnt-labs/ui/dist/index.css";

export default function Page(): JSX.Element {
  const {
    data: account,
    login,
    logout,
    isLoading,
    isConnected,
  } = useAbstraxionAccount();
  const { client } = useAbstraxionSigningClient();

  if (isLoading && !isConnected) {
    return (
      <main className="flex min-h-screen items-center justify-center p-4">
        <p className="text-sm text-neutral-400">Preparing wallet session…</p>
      </main>
    );
  }

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold text-white">ABSTRAXION</h1>
      {account?.bech32Address ? (
        <>
          <p className="truncate text-xs text-neutral-400">{account.bech32Address}</p>
          {client ? <p className="text-xs text-green-400">Signing client ready</p> : null}
          <Button fullWidth structure="outlined" onClick={() => logout()}>
            LOGOUT
          </Button>
        </>
      ) : (
        <Button
          fullWidth
          structure="base"
          disabled={isLoading}
          onClick={() => {
            void login();
          }}
        >
          CONNECT
        </Button>
      )}
    </main>
  );
}
```

## Migrating from the legacy modal pattern

1. Remove **`<Abstraxion />`** and **`useModal`** if you no longer need the bundled modal.
2. Prefer **`login()`** (and **`logout()`**) for explicit control.
3. Replace a single “connecting” boolean with the **granular flags** above to avoid UI flicker and double-submits.

## Related

- [Web App Development — modes hub](README.md)
- [Account abstraction tutorial (pre-built modal)](build-react-dapp-with-account-abstraxion.md)
- [Abstraxion signer mode](abstraxion-signer-mode.md)
