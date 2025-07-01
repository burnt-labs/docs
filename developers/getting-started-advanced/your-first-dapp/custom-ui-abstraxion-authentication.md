# Custom UI Abstraxion Authentication

This guide explains how to implement Abstraxion authentication without using the legacy modal UI, giving you complete control over the user experience during the authentication flow.

## Overview

Starting with version `@burnt-labs/abstraxion@2.0.0-alpha.49` and later, you can implement a custom UI for the Abstraxion authentication process. This allows you to:

- Display your own loading states and branding during authentication
- Create a seamless authentication experience that matches your app's design
- Handle the redirect flow programmatically

## Key Changes

### 1. No Legacy Modal UI Import Required

The Abstraxion CSS import is no longer needed when using custom UI authentication:

```diff
- import "@burnt-labs/abstraxion/dist/index.css";
```

### 2. Direct Login Method

The `useAbstraxionAccount` hook now provides a `login` method that handles authentication without showing the legacy modal:

```typescript
const { data: account, login, logout, isConnecting } = useAbstraxionAccount();
```

### 3. Custom Loading States

You can implement your own loading UI while authentication is in progress:

```typescript
const [isLoggingIn, setIsLoggingIn] = useState(false);

const handleLogin = async () => {
  try {
    setIsLoggingIn(true);
    await login();
  } catch (error) {
    console.error("There's been an error logging in");
  } finally {
    setIsLoggingIn(false);
  }
};
```

## Implementation Example

Here's a complete example showing how to implement custom UI authentication:

```typescript
"use client";
import { useState, useEffect } from "react";
import {
  useAbstraxionAccount,
  useAbstraxionSigningClient,
} from "@burnt-labs/abstraxion";
import { Button } from "@burnt-labs/ui";
import "@burnt-labs/ui/dist/index.css";

export default function Page(): JSX.Element {
  const { data: account, login, logout, isConnecting } = useAbstraxionAccount();
  const { client } = useAbstraxionSigningClient();
  const [isLoggingIn, setIsLoggingIn] = useState(false);

  // Handle login with custom loading state
  const handleLogin = async () => {
    try {
      setIsLoggingIn(true);
      await login();
    } catch (error) {
      console.error("There's been an error logging in");
    } finally {
      setIsLoggingIn(false);
    }
  };

  // Auto-login after redirect
  useEffect(() => {
    const searchParams = new URLSearchParams(window.location.search);
    if (searchParams.get("granted") === "true") {
      handleLogin();
    }
  }, []);

  // Custom loading UI
  if (isLoggingIn) {
    return (
      <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
        <div className="rounded border border-white/20 p-6 text-center">
          <p className="font-bold">You are being redirected...</p>
          <p className="text-sm">
            Use custom UI to render loading state with your own branding
          </p>
        </div>
      </main>
    );
  }

  return (
    <main className="m-auto flex min-h-screen max-w-xs flex-col items-center justify-center gap-4 p-4">
      <h1 className="text-2xl font-bold tracking-tighter text-white">
        ABSTRAXION
      </h1>
      
      <Button
        fullWidth
        onClick={handleLogin}
        structure="base"
        disabled={isConnecting}
      >
        {account.bech32Address ? (
          <div className="flex items-center justify-center">VIEW ACCOUNT</div>
        ) : isConnecting ? (
          "LOADING..."
        ) : (
          "CONNECT"
        )}
      </Button>

      {account.bech32Address && (
        <Button onClick={logout}>LOGOUT</Button>
      )}
    </main>
  );
}
```

## Important Changes from Legacy Modal Authentication

### 1. Remove Abstraxion Component

The `<Abstraxion />` component is no longer needed:

```diff
- import { Abstraxion, useModal } from "@burnt-labs/abstraxion";
+ import { useAbstraxionAccount } from "@burnt-labs/abstraxion";

// Remove legacy modal state management
- const [, setShowModal] = useModal();

// Remove Abstraxion component
- <Abstraxion onClose={() => setShowModal(false)} />
```

### 2. Updated AbstraxionContext

The `AbstraxionContext` now includes the `login` method and handles redirect authentication automatically:

```typescript
// Inside AbstraxionContextProvider
async function login() {
  try {
    setIsConnecting(true);
    await abstraxionAuth.login();
  } catch (error) {
    console.log(error);
    throw error;
  } finally {
    setIsConnecting(false);
  }
}

// Auto-login on redirect
useEffect(() => {
  const searchParams = new URLSearchParams(window.location.search);
  if (searchParams.get("granted") === "true") {
    login().catch((error) => {
      console.error("Failed to finish login:", error);
    });
  }
}, []);
```

### 3. Simplified Loading Component

The built-in Loading component has been simplified to show only a spinner, allowing you to implement your own loading UI:

```typescript
// Old Loading component with built-in text
- <h1>Connecting...</h1>
- <h2>Signing you in... Don't close the window.</h2>

// New simplified Loading component
+ <Spinner />
```

## Migration Guide

To migrate from legacy modal to custom UI authentication:

1. **Remove CSS import**: Delete the Abstraxion CSS import
2. **Remove legacy modal components**: Remove `Abstraxion` component and `useModal` hook
3. **Use login method**: Replace legacy modal trigger with the `login()` method
4. **Add custom loading UI**: Implement your own loading state
5. **Handle redirects**: The redirect handling is automatic with the new implementation

## Benefits

- **Better UX control**: Full control over the authentication flow UI
- **Consistent branding**: Loading states match your app's design
- **Cleaner code**: No need to manage legacy modal state
- **Improved performance**: Less CSS to load

## Demo

A complete working example is available in the [xion.js demo app](https://github.com/burnt-labs/xion.js/tree/main/apps/demo-app).