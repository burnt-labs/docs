# Custom UI and Abstraxion loading states

Build **hook-first** authentication: your own buttons, copy, and spinners while Abstraxion drives the **Meta Account** flow. This pattern matches the demo route **`/loading-states`** in [xion.js `apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states).

{% hint style="info" %}
**Document metadata (for humans and agents)**\
`doc_type`: guide\
`primary_auth_mode`: auto (recommended; flags apply across `auto` / `popup` / `redirect`)\
`sdk_packages`: `@burnt-labs/abstraxion`, `@burnt-labs/abstraxion-core`\
`demo_app_routes`: `/loading-states`\
`required_env_vars` (dashboard auth: `auto` / `popup` / `redirect`): `NEXT_PUBLIC_CHAIN_ID`, `NEXT_PUBLIC_RPC_URL`, `NEXT_PUBLIC_REST_URL`, `NEXT_PUBLIC_GAS_PRICE`, `NEXT_PUBLIC_TREASURY_ADDRESS`, `NEXT_PUBLIC_AUTH_APP_URL`
{% endhint %}

## Choose an authentication mode first

Hook-level flags are **independent of whether you render a modal**: they reflect session and navigation state. Pick **`authentication.type`** on `AbstraxionProvider` before polishing UI—see [Web App Development](README.md) (**`auto`** is the default recommendation).

- **`auto` / `popup`:** Auth often completes in a **popup**; the parent page stays mounted.
- **`redirect` (including omitting `authentication`):** After the auth app returns, the user may land with **`?granted=true`** on your URL; you may call `login()` once to finalize session restoration (see below).

**Signer** and **embedded** modes use different controllers; this guide focuses on **dashboard-style** flows. For **`type: "signer"`**, see [Abstraxion signer mode](abstraxion-signer-mode.md).

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

If your flow uses **full-page redirect** (mobile leg of **`auto`**, or explicit **`redirect`**), the app may reload with **`?granted=true`**. You can finalize the session by calling `login()` when that query param is present (the demo illustrates this pattern). With **popup**-based **`auto`**, this path is less prominent because the opener never unloads.

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
