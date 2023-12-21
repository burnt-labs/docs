# Meta Accounts Design

**Overview**

XION’s modular _meta accounts_ introduce a highly adaptable and secure account creation & management framework.

Built at the protocol-level, _meta accounts_ have a host of benefits over traditional crypto wallets and enable novel Web3 application use-cases. See [generalized-abstraction](../../../learn/learn-about-xion/generalized-abstraction/ "mention") for more information around use cases and features.

## Components

<details>

<summary>Identity Provider</summary>

In the case of social login a trusted Identity provider must be used to handle the confirmation of identity, in this case [Stytch](https://stytch.com/) is being used.

</details>

<details>

<summary>Account API</summary>

A set of services used to assist and sponsor new meta account creation.&#x20;

See the [repo](https://github.com/burnt-labs/account-abstraction-api)

</details>

<details>

<summary>Abstraxion Library</summary>

This graz-based frontend library aids in integration with your react front end.

See the [repo](https://github.com/burnt-labs/abstraxion)

</details>

<details>

<summary>Meta Account Smart Contract</summary>

This contract along with a custom XION module represent the core of the meta account functionality.

See the [repo](https://github.com/burnt-labs/contracts)

</details>

## Supported Authentication Methods

* Email login
* Keplr (or any wallet supporting [ADR-036](https://github.com/cosmos/cosmos-sdk/blob/main/docs/architecture/adr-036-arbitrary-signature.md))
* EIP-191 Signatures
* (MetaMask, biometrics, and many more coming soon)

## Workflows

### Account Create

```mermaid
sequenceDiagram
    participant User as User/Authentication Method
    participant Abstraxion as Abstraxion Library
    participant API as Account API
    participant Contract as Meta Account Smart Contract

    User->>Abstraxion: Initial Connect
    Abstraxion->>Abstraxion: Check if Meta Account Exists
    alt Meta Account Doesn't Exist
        Abstraxion->>User: Prompt to Create Meta Account
        User->>Abstraxion: Agree to Create Meta Account
        Abstraxion->>Abstraxion: Precompute Contract Address using Instantiate2
        Abstraxion->>User: Prompt for Authentication Method Signature
        User->>Abstraxion: Provide Signature
        Abstraxion->>API: Submit Details to Accounts API
        API->>Contract: Instantiate Contract and Add Authentication Method
        Contract->>Abstraxion: Confirm Contract Creation

        Abstraxion->>User: User is shown as connected
    end
```

### Transaction submission

```mermaid
sequenceDiagram
    participant User as User/Authentication Method
    participant Abstraxion as Abstraxion Library
    participant API as Transaction API
    participant Xion as Xion
    User->>Abstraxion: Intention to submit transaction
    Abstraxion->>Abstraxion: Precompute Transaction Hash using method from Signature 
    Abstraxion->>User: Prompt for Signature
    User->>Abstraxion: Provide Signature
    Note over Abstraxion,User: Social login autosigns
    Abstraxion->>API: Submit Transaction Details to Transaction API
    API->>Xion: Execute Transaction 
    Xion->>Abstraxion: Confirm Transaction Execution
    Abstraxion->>User: User is shown transaction status
```







