# Your First DApp

Decentralized applications (apps) rely on both **frontend** and **backend** components to provide smooth user experiences. The **frontend** interacts with users via web or mobile interfaces, while the **backend**, powered by smart contracts, executes transactions and business logic on the blockchain.

XION enhances this by enabling **meta accounts** with **gasless transactions**, ensuring users can interact with apps just like traditional web apps, without having to pay gas fees. Developers can build **intuitive frontends** that abstract blockchain complexities, improving usability and adoption.

<table data-view="cards">
  <thead><tr><th></th><th></th></tr></thead>
  <tbody>
    <tr>
      <td><strong>Account Abstraction with Gasless Transactions</strong></td>
      <td>Walk through building a basic app using the Abstraxion library.</td>
    </tr>
    <tr>
      <td><strong>Custom UI Abstraxion Authentication</strong></td>
      <td>Learn how to implement a custom UI for the Abstraxion authentication process.</td>
    </tr>
    <tr>
      <td><strong>Interact with XION via a Backend Service</strong></td>
      <td>Learn how to interact with the XION blockchain from a backend environment using <strong>CosmJS</strong>.</td>
    </tr>
    <tr>
      <td><strong>Backend adaption with Web2 experience</strong></td>
      <td>Learn how to adapt your existing backend to work with XION without any web3 interactive behavior in frontend.</td>
    </tr>
  </tbody>
</table>

## The different types of XION integration for developers

When you need to integrate Web3 into your product, you may encounter the following challenges:

- You already have an existing user base and do not want them to change their usage habits.
- You have a mature Web2 frontend user experience and do not want to rewrite most of your frontend code.
- You have existing infrastructure and integrations that you wish to continue using.

Xion provides a variety of ways to integrate with Web2, allowing you to choose the approach that best fits your product for Web3 integration.

| Approach | With frontend | With backend | Using MetaAccount(Account Abstraction) + Gasless Transaction | Example Guide |
|----------|---------------|--------------|--------------------------------------------------------------|---------------|
| Direct Blockchain Interaction | No | Yes | No | [Interact with XION via your Backend Service](./xion-backend-using-cosmjs.md) |
| General Web3 Experience | Yes | No | Yes | [Account Abstraction with Gasless Transactions](../build-react-dapp-with-account-abstraxion.md) |
| Web2 Experience with XION Integration in backend | Yes | Yes | Yes | [Account Abstraction for RESTful API with Backend Session](./build-restapi-with-account-abstraction-and-backend-session.md) |
| Pure Web2 Experience with XION OAuth2 Fully-Managed API | Yes | Yes | Yes | Coming soon |
