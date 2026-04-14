---
description: Explanation — how Abstraxion authentication types and signing choices combine into product-level UX patterns; demo-app as the verification surface
vars:
  doc_type: explanation
  primary_auth_mode: mixed
  sdk_packages: "@burnt-labs/abstraxion, @burnt-labs/abstraxion-core"
  demo_app_routes: "/signer-mode, /direct-signing-demo, /popup-demo, /loading-states"
  verification_note: "Patterns are architectural directions; confirm behavior in xion.js demo-app for your SDK version."
---

# Abstraxion modes and opportunities

This page is **understanding-oriented**. It sketches **what kinds of experiences** you can build by combining Abstraxion **authentication** configuration with **signing** behavior. It does **not** replace the step-by-step tutorials or the signer configuration reference.

For wiring details, use:

- [Web App Development — modes hub](README.md) — glossary of `authentication.type`, env map, and demo route index
- [Abstraxion signer mode](abstraxion-signer-mode.md) — `getSignerConfig`, session vs direct signing, appendix tables
- [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md) — end-to-end gasless app with dashboard auth

**Verification rule:** Treat each numbered section (and the multi-wallet integration note) as a **typical architecture direction**. Validate flows, fees, and authenticator support against **[`apps/demo-app`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app)** and the Abstraxion release you depend on.

---

## Two axes: authenticate vs sign

Abstraxion separates concerns that are easy to merge mentally:

1. **How the user reaches a Meta Account session** — controlled mainly by **`authentication.type`** (`auto`, `popup`, `redirect`, `signer`, `embedded`, …). Dashboard modes navigate or embed hosted UI; **signer** mode keeps the user on your SPA and calls **`getSignerConfig`** when your custody or browser wallet layer is ready.
2. **How individual transactions are authorized on-chain** — controlled by signing client choice (for example default **`useAbstraxionSigningClient()`** vs **`useAbstraxionSigningClient({ requireAuth: true })`** in signer contexts). Session-style paths align with Treasury / fee grants when you configure them; **`requireAuth`** pushes approval through paths closer to the user’s authenticator and often shifts fee responsibility.

Neither axis alone defines the product story: **pairings** matter. The same Meta Account can feel like a gasless consumer app or like a self-custodied wallet experience depending on authenticators, grants, and signing hooks.

---

## 1 — Recommended baseline: dashboard auth (`auto` / `popup` / `redirect`)

**Direction:** For most new apps, **`authentication.type: "auto"`** (or an explicit **`popup`** / **`redirect`**) is the **default we recommend**: users complete Meta Account login through the hosted dashboard experience, then **`useAbstraxionSigningClient()`** without **`requireAuth`** gives the usual **session-style** signing with Treasury / fee grants when you configure them.

**Why teams choose it:** Fastest path, least custom signer code, clearest alignment with tutorials and hosted recovery flows.

**Where to verify:** [Account abstraction tutorial](build-react-dapp-with-account-abstraxion.md), **[`/popup-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/popup-demo)**, and **[`/loading-states`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/loading-states)**.

---

## 2 — Signer mode + **session** signing (Meta Account execution; wallet or custody only drives **connect**)

**Direction:** Set **`authentication.type: "signer"`** and implement **`getSignerConfig`** so a **browser extension wallet, hardware signer, or custody SDK** establishes the session **without** a full-page dashboard redirect. Then use the default **`useAbstraxionSigningClient()`** (no **`requireAuth: true`**).

**What this feels like:** After connect, the app still behaves like a **Meta Account product**: session-style submission, gasless paths when grants and Treasury are set up, and the same hook shape as dashboard-first apps—only the **authentication surface** moved in-app.

**Contrast with section 3:** Users are **not** prompted to approve **every** transaction through the extension like a classic self-custody wallet; approvals follow the session / grantee model unless you change signing options.

**Where to verify:** **[`/signer-mode`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/signer-mode)** (Turnkey there is **example custody wiring** for **`getSignerConfig`**; swap in your signer stack). Wiring tables: [Abstraxion signer mode](abstraxion-signer-mode.md).

---

## 3 — Signer mode + **direct** signing (`requireAuth: true`) (wallet-like per transaction on an abstract account)

**Direction:** Keep **`type: "signer"`** and your **`getSignerConfig`**, but call **`useAbstraxionSigningClient({ requireAuth: true })`**.

**What this feels like:** The **signing rhythm** matches a traditional browser-wallet flow: users see and approve work closer to each operation, **gas is typically on them** (or you model fees explicitly), and you lean less on long-lived session shortcuts. You still keep **abstract account** capabilities (multiple authenticators, policy, recovery options from the Meta Account model)—the change is **how each tx is authorized**, not “whether Meta Accounts exist.”

**Contrast with section 2:** Same “wallet or custody proves identity for signer auth” idea; **different signing hook**. Section 2 = Meta Account–style execution after connect; section 3 = **per-tx** authenticator involvement for submits.

**Where to verify:** **[`/direct-signing-demo`](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app/src/app/direct-signing-demo)** compares default vs **`requireAuth`** side by side. See [Signing: session key vs direct](abstraxion-signer-mode.md#signing-session-key-vs-direct).

---

## Multi-wallet “connect” UI (integration shape, not a separate product mode)

Some apps already ship a **wallet list or connection modal** (sometimes reused from another chain’s stack). That layer usually feeds **which signer** your **`getSignerConfig`** factory returns—it does **not** replace the split between **section 2** (session signing after connect) and **section 3** (**`requireAuth`**). As one **illustration** only: a codebase might already depend on a common open-source **multi-wallet connection module** for non-XION chains; you can explore mapping its signer objects into **`SignerConfig`**, then **prove** the result in **`demo-app`** and your own tests—not a third runtime mode in Abstraxion.

---

## Strong authenticator as primary, others as backup (user-journey narrative)

**Direction:** Teams often want a **primary** authenticator for day-to-day signing (for example passkey-style WebAuthn in product designs) and **additional** methods (email, OTP, another device) as **backup** if the primary is lost. That story sits mostly at the **Meta Account / dashboard / authenticator policy** layer; **signer** mode can reduce how often users see hosted UI, but the exact catalog, recovery paths, and in-app vs dashboard boundaries **change with product and SDK releases**.

**Code in `demo-app`:** There is **no** single route that demonstrates every authenticator combination end-to-end. Treat this section as a **planning and UX narrative**; validate your target mix with product docs, manager APIs, and security review, then use signer + session or signer + **`requireAuth`** demos for the **Abstraxion wiring** slice.

---

## Keeping narrative aligned with implementation

Product and SDK discussions sometimes distinguish **“how the SDK calls are spelled”** from **“what product posture those calls enable.”** This document stays in the second bucket. When feedback emphasizes **pairings**—dashboard-first (**section 1**) vs signer-backed Meta Account execution (**section 2**) vs signer-backed **per-tx** signing (**section 3**), plus passkey-first journeys—map them to the sections above, then **lock behavior** with demo routes and release notes—not with prose alone.

---

## Related

- [Web App Development](README.md)
- [Abstraxion signer mode](abstraxion-signer-mode.md)
- [Custom UI and Abstraxion loading states](custom-ui-abstraxion-authentication.md)
