# Backend Adaption Guides for Web2 Developers

This section contains guides for Web2 developers to adapt their existing backend to work with XION without any web3 interactive behavior in frontend.

## What problem does this document address?

When you need to integrate Web3 into your product, you may encounter the following challenges:

- You already have an existing user base and do not want them to change their usage habits.
- You have a mature Web2 frontend user experience and do not want to rewrite most of your frontend code.
- You have existing infrastructure and integrations that you wish to continue using.

Xion provides a variety of ways to integrate with Web2, allowing you to choose the approach that best fits your product for Web3 integration.

## The different types of XION integration

| Approach | With frontend | With backend | Using MetaAccount(Account Abstraction) + Gasless Transaction | Example Guide |
|----------|---------------|--------------|--------------------------------------------------------------|---------------|
| Direct Blockchain Interaction | No | Yes | No | [Interact with XION via your Backend Service](./xion-backend-using-cosmjs.md) |
| General Web3 Experience | Yes | No | Yes | [Account Abstraction with Gasless Transactions](../build-react-dapp-with-account-abstraxion.md) |
| Web2 Experience with XION Integration in backend | Yes | Yes | Yes | [Account Abstraction for RESTful API with Backend Session](./build-restapi-with-account-abstraction-and-backend-session.md) |
| Pure Web2 Experience with XION OAuth2 Fully-Managed API | Yes | Yes | Yes | Coming soon |
