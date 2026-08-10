---
description: Meta Accounts, auth, and gasless transactions on Verona
icon: screwdriver-wrench
---

# Accounts

Welcome to **Build on Verona — Accounts**.

This section covers Meta Accounts, authentication methods, gasless transactions, and building web, mobile, backend, or agent-driven applications on the **Verona** network.

### What are Meta Accounts?

Meta Accounts are the foundation of the Verona network experience. They provide:

* **Multiple Authentication Methods**: Email, social login, and more
* **Gasless Transactions**: Users can interact with apps without holding native tokens
* **Treasury Contracts**: Enable delegated authorization and fee grants
* **Seamless Integration**: Works with web, mobile, and backend applications

### Getting Started Paths

Choose your path based on what you want to build:

#### Overview

Learn about Meta Account settings, features, and authentication methods.

* [Meta Accounts Overview](getting-started/) - Learn about Meta Account features, authentication methods, and capabilities
* [Treasury Contracts for Gasless UX](getting-started/treasury-contracts.md) - Deploy and configure Treasury Contracts for gasless transactions and permission grants

#### Web App Development

Build React applications with account abstraction and gasless transactions.

* [Web App Development — integration hub](web-app/) - Authentication modes (`auto`, `signer`, `embedded`), env vars, and links to the xion.js demo app
* [Account Abstraction with Gasless Transactions](web-app/build-react-dapp-with-account-abstraxion.md) - Build a React dApp using the Abstraxion library with gasless transactions
* [Abstraxion modes and opportunities](web-app/abstraxion-modes-opportunities.md) - Dashboard-first defaults, then signer + session vs signer + `requireAuth`; verify in `demo-app`
* [Abstraxion signer mode](web-app/abstraxion-signer-mode.md) - `type: "signer"`, `getSignerConfig`, and wallet-led integrations
* [Custom UI and Abstraxion loading states](web-app/custom-ui-abstraxion-authentication.md) - Hook-first UX, loading flags, and session lifecycle

#### Mobile App Development

Create mobile applications with React Native and Meta Account authentication.

* [Set up your Mobile Development Environment](mobile-app/set-up-mobile-development-environment.md) - Configure your development environment for mobile app development
* [Building a React Native Mobile App with Abstraxion](mobile-app/building-a-react-native-mobile-app-with-abstraxion.md) - Build a React Native mobile app with Meta Account login and gasless UX
* [Create Mobile App and Integrate Meta Account Authentication](mobile-app/create-mobile-app-and-integrate-meta-account-authentication.md) - Integrate Meta Account authentication into your mobile app
* [Mobile Development – Common Errors & Fixes](mobile-app/mobile-development-common-errors-and-fixes.md) - Troubleshoot common issues in mobile development

#### **OAuth2 App Development**

* ​[Build a Verona OAuth2 Application](https://app.gitbook.com/o/-MYHVr_KzNLwJObSYyGU/s/B5Z5ijJgMx0GJO3l1Il9/~/diff/~/revisions/IKVpY4b27hWnM9vm8zy9/build-on-verona/accounts/oauth2-app) - Build an application with a Web2 experience using Verona OAuth2 Fully-Managed API

#### Backend App Development

Integrate Verona with your backend services using RESTful APIs or CosmJS.

* [Interact with Verona via your Backend Service ](backend-app/backend-using-cosmjs.md)- Learn how to interact with the Verona network from a backend environment using CosmJS
* [Account Abstraction for RESTful API with Backend Session](backend-app/build-restapi-with-account-abstraction-and-backend-session.md) - Build an application with a Web2 experience using Verona Integration in backend

#### AI Agent and CLI Path

Use this path if your team works from terminal-first workflows or coding agents:

* [AI Agent Quick Start](../../about-verona/ai-agents.md) - One-step copy instruction for Verona Agent Toolkit skills
* [Verona Agent Toolkit Tutorial](../tools/verona-toolkit.md) - Guided setup for auth, Treasury management, and OAuth2 client management
