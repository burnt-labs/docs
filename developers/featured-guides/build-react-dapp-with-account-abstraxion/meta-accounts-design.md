# Meta Accounts Design

**Overview**

XION’s modular _meta accounts_ introduce a highly adaptable and secure account creation & management framework.

Built at the protocol-level, _meta accounts_ have a host of benefits over traditional crypto wallets and enable novel Web3 application use-cases. See [generalized-chain-abstraction](../../learn-about-xion/generalized-chain-abstraction/ "mention") for more information around use cases and features.

## Components

<details>

<summary>Identity Provider</summary>

In the case of social login an identity provider is used to handle the confirmation of identity.

</details>

<details>

<summary>Account API</summary>

A set of services used to assist and sponsor new meta account creation.&#x20;

</details>

<details>

<summary>Abstraxion Library</summary>

This frontend library aids in integration with your react front end.

See the [repo](https://github.com/burnt-labs/xion.js)

</details>

<details>

<summary>Meta Account</summary>

This contract along with a custom XION module represent the core of the meta account functionality.

See the [repo](https://github.com/burnt-labs/contracts)

</details>

<details>

<summary>UserSessionKey</summary>

The temporary key generated on the DAPP side which is granted ContractExecutionAuthorization by the user's Meta Account on a temporary basis.

</details>

<details>

<summary>FeeGrantTreasury</summary>

A account a dapp may user to sponsor transactions submitted by the UserSessionKey.

</details>

## Supported Authentication Methods

* Email login
* (MetaMask, biometrics, and many more coming soon)

## Workflows



```mermaid
sequenceDiagram
    Actor User
    participant DAPP
    participant AbstraxionLibrary as Abstraxion Library
    participant AccountManagementDashboard as Account Management Dashboard
    participant XionChain as Xion Chain
    User->>DAPP: Load Dapp
    User->>DAPP: Clicks 'Mint NFT'
    DAPP->>AbstraxionLibrary: connect()
    AbstraxionLibrary->>AbstraxionLibrary: Check for Dapp UserSessionKey 
    AbstraxionLibrary->>AbstraxionLibrary: Check Dapp UserSessionKey Authz Expiry
    Note right of AbstraxionLibrary: Note: Authz grants are a "session"
    alt no active Dapp UserSessionKey
        AbstraxionLibrary->>AbstraxionLibrary: Generate one time private UserSessionKey
        AbstraxionLibrary->>AccountManagementDashboard: Redirect user along with permission scope 
        AccountManagementDashboard->>AccountManagementDashboard: Check for an active connection
        alt no active connection
            AccountManagementDashboard->>User: Initiate and complete connect flow
        end
        AccountManagementDashboard->>User: Show authz permission dialog
    end
    User->>AccountManagementDashboard: Accept permissions
    AccountManagementDashboard->>XionChain: Submit transaction with authz grants
    alt transaction successful
        XionChain->>DAPP: Notify transaction success
        DAPP->>User: Return user to dapp
    else transaction failed
        XionChain->>AccountManagementDashboard: Notify transaction failure
        AccountManagementDashboard->>User: Show error modal
    end
    User->>DAPP: Clicks 'Mint NFT'
    DAPP->>AbstraxionLibrary: Locally sourced transaction
    AbstraxionLibrary->>AbstraxionLibrary: Autosigned using Dapp UserSessionKey  
    AbstraxionLibrary->>XionChain: Submitted 
```



