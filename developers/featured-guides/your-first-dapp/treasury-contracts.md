---
description: >-
  Deploying your Treasury Contract for Delegated Authorization and Gasless
  Transactions
---

# Enabling Gasless Transactions with Treasury Contracts

Treasury contracts introduce an OAuth-like authentication flow fully on-chain, allowing developers to create intricate allowance systems that enable delegated authorization. This allows an account (the granter — the end user) to grant permission to another account (the grantee — the treasury contract) to execute specific types of transactions on their behalf. These contracts also enable a gasless experience for end users via fee grants. This guide walks you through using the developer portal to deploy your dApp’s first treasury contract.

{% hint style="info" %}
Each treasury contract deployment is designed for a single dApp. Think of it like setting up OAuth for your application, where your dApp requests access to data from the XION chain on behalf of the user — without accessing any private information.
{% endhint %}



## **Understanding Fee Grants and Authorizations**

Treasury contracts leverage two key modules to provide their core functionality: **Fee Grants** and **Authz (Authorization)**. These modules enable **gasless transactions** and **delegated authorization**, enhancing the user experience.



### **Fee Grants**

The **Fee Grant module** allows an account (**granter**) to cover gas fees for another account (**grantee**), making transactions **gasless for the end user**. This feature is particularly useful for **onboarding users**, as it removes the need for them to **hold native tokens** to pay for transaction fees.

#### **How Fee Grants Work?**

1. The **treasury contract holds a balance of XION tokens** to cover gas fees.
2. The **contract grants fee sponsorship** for specific transactions.
3. The **user (grantee) submits a transaction**, and the **treasury contract pays the gas fee automatically**.

#### **Key Benefits of Fee Grants**

* **Frictionless onboarding** – Users can interact with the dApp immediately without needing XION tokens.
* **Better user experience** – Reduces complexity and improves adoption for non-crypto-native users.
* **Controlled sponsorship** – dApps can define limits on gas fee coverage.



### **Authorizations (Authz)**

The **Authz module** enables **delegated authorization**, allowing one account (**granter**) to grant another account (**grantee**) permission to execute specific transactions on its behalf.

#### **How Authz Works in Treasury Contracts?**

* **The user acts as the granter**, granting permission to the **treasury contract (grantee)** to execute transactions on their behalf.
* **The treasury contract defines allowed actions**, for exmple token transfers.
* **Users approve the treasury contract** to execute these actions on their behalf.
* **The dApp allows users to trigger transactions**, which the **treasury contract then executes on their behalf**.

#### **Key Benefits of Authz**

* **Security and control** – Users define exactly which actions the treasury contract can perform.
* **Automation** – Transactions can be executed without requiring constant user approval.
* **Efficiency** – Reduces manual signing and streamlines dApp interactions.

By combining **Fee Grants** and **Authz**, **Treasury Contracts** offer **a seamless and user-friendly blockchain experience**, eliminating gas fees while maintaining security and control over transaction execution.



## Developer Portal

We have developed a **Developer Portal** to provide resources that developers can leverage within their dApps. The first feature available is the **creation and management of Treasury Contracts**. You can access the Developer Portal using the following links:

* **Testnet**: [https://dev.testnet2.burnt.com](https://dev.testnet2.burnt.com)
* **Mainnet**: [https://dev.burnt.com](https://dev.burnt.com)

To perform any actions, you must **log in to the portal**. After logging in, you will be automatically redirected to the **Dashboard**, where you can create and manage your deployed Treasury Contracts.



### The Dashboard

The **Dashboard** displays all your deployed **Treasury Contracts**. Once you create a contract, it will appear in your list of active deployments.

#### **Important Note:**

* Contract details are **stored in your browser's local storage**.
* Switching **browsers or devices will not retain your deployed contract list**.

<figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## Creating a Treasury Contract

To create a new **Treasury Contract**, follow these steps:

1. Click the **"New treasury"** button located at the **top right corner** of the Dashboard.
2. A setup screen will appear, prompting you to configure the contract settings.

<figure><img src="../../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

You must complete the required fields for each module:

* **Fee Grant:** At least **one fee grant** is required to enable gasless transactions.
* **Grant Config:** At least **one authorization grant** must be set up to define transaction permissions.

In the next sections, we'll go through each module in more detail.



### Fee Grant

The **Fee Grant** section allows you to set up a mechanism to cover gas fees for user interactions within your dApp. There are three types of fee allowances available:

**1. Basic Allowance (`/cosmos.feegrant.v1beta1.BasicAllowance`)**

* Defines a **fixed spend limit per transaction**.
* Ideal for simple fee grants where users have a predefined gas budget.

**2. Periodic Allowance (`/cosmos.feegrant.v1beta1.PeriodicAllowance`)**

* Allows users to access the grant **at set intervals**.
* You can specify:
  * **Period duration** (time users must wait before reusing the grant).
  * **Spend limit per period** to control gas consumption.

**3. Allowed Message Allowance (`/cosmos.feegrant.v1beta1.AllowedMsgAllowance`)**

* Restricts the fee grant to cover **only specific transaction types**.
* You can apply either a **Basic Allowance** or **Periodic Allowance** to the specified transaction type.

#### **Configuring the Fee Grant**

1. **Enter a description** explaining what the grant will cover.
2. **Select the allowance type** (Basic, Periodic, or Allowed Message Allowance).
3. **Fill in the required fields** based on the selected allowance type.
4. Click **"Save"** to finalize and store the Fee Grant configuration.

Once saved, the Fee Grant settings will be applied, which you can see in the "T**reasury Instance Preview**":

<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

### Grant Configuration

This section allows you to define **transaction permissions (allowances)** that users must agree to before your dApp can execute actions on their behalf.

#### **Setting Up Authorization**

1. **Enter a Description**
   * Provide a **clear and concise description** explaining the purpose of the authorization.
   * This description will be shown to users when they click **"Allow"** after connecting their account.
2. **Select a Type URL**
   * The **Type URL** determines the kind of transactions your dApp will be authorized to perform.
   * **Example**: Selecting `"/cosmwasm.wasm.v1.MsgExecuteContract"` allows your dApp to execute transactions on a smart contract on behalf of the user.
     * **Define Contract Execution Authorization**
       * **Select the authorization type** (currently, `"/cosmwasm.wasm.v1.ContractExecutionAuthorization"` is the only option).
       * **Enter the contract address** that your dApp will interact with on behalf of the user.
     * **Set Transaction Limits (Optional)**
       * You can impose restrictions on:
         * **Total number of allowed contract calls**.
         * **Maximum fund spending limit**.
         * **Both call count and fund spending limits** for added security.
     * **Configure Message Filters**
       * **Allow all messages** – Grants permission to execute any message within the contract.
       * **Accepted message keys** – Specify which message types can be executed.
       * **Accepted raw messages** – Define precise message structures that can be processed.

#### **Customization and Editing**

* All configurations generated here can be edited and customized as needed.
* Ensure that **keys are always in snake\_case** (e.g., `max_spend_limit`) and not camelCase (`maxSpendLimit`).

Once configured, these settings define how your Treasury Contract interacts with users and enforces controlled transaction execution.

<figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Once you've entered all the required information, click the **"Add contract grant"** button.

**Important Notes:**

* You can add **multiple Grant Configs**, allowing you to define different permissions for various  interactions.
* After adding all the necessary grant configurations, click the **"Create"** button to finalize and deploy the Treasury Contract.

<figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

Once created, the contract will appear on the **Dashboard**.

{% hint style="success" %}
**Fund the Treasury Contract**: Transfer **XION tokens** to the contract to ensure it can cover gas fees for fee grants.
{% endhint %}



## Editing a Treasury Contract

Once a Treasury contract is created, it will appear in the **Dashboard**. To edit it, simply click on the contract from the list, which will open a management screen.

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

### Withdraw Funds

To withdraw any funds sent to the Treasury contract:

1. Click the **"Withdraw Funds"** button.
2. A withdrawal screen will appear.
3. Enter the amount to withdraw which transfers funds from the Treasury contract back to the admin account.

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

### Update Params <a href="#radix-r14" id="radix-r14"></a>

To modify the parameters:

1. Click the **"Update Params"** button.
2. A configuration screen will load, allowing you to adjust relevant params.

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

### Update Fee Config

To edit the fee config:

1. Click the **"Edit"** button under the **Fee Config** section.
2. This will open a screen where you can adjust the fee parameters.

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

### Update and Add Grant Configs

To modify existing grant configurations or add new ones, click the **arrow** next to the listed permission.

<figure><img src="../../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

A screen will open where you can edit the configuration:

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

If creating a new grant:

* Select a new **Type URL**.
* Enter the required field data.
* Click **"Save"** to create a new Grant Config.

Each Grant Config will be displayed under the **Permissions** section in the Dashboard:

<figure><img src="../../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>



## Client-Side Implementation (React App)

To integrate gasless transactions and delegated authorizations using a Treasury contract in your dApp, follow this [**full guide**](build-react-dapp-with-account-abstraxion.md).

You’ll need to configure the `AbstraxionProvider` by setting the Treasury contract in the config:

```typescript
const treasuryConfig = {
	treasury: `CONTRACT_ADDRESS`,
	// optional (only for mainnet)
	// rpcUrl: ``,
	// restUrl: ``
}


export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <AbstraxionProvider
          config={treasuryConfig}>
          {children}
        </AbstraxionProvider>
      </body>
    </html>
  )
}
```

This configuration also determines the redirect URL users see after clicking **Connect** and before clicking **Allow** (as shown in the screenshot below).

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

While connecting their accounts, users will see a list of **Allowances** — this message is set during the Treasury contract’s creation by the developer while configuring the grant settings.

Once the user clicks **Allow**, the dApp:

1. **Creates Grant permissions**
2. **Deploys a Fee Grant** on behalf of the user.

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Whenever your app executes a transaction using `.execute`, make sure the **granter** in the fee configuration (4th parameter) is set to the Treasury contract’s address:

{% hint style="warning" %}
If you modify the app to use Treasury contracts, ensure that all users **log out and reconnect** to apply the new settings.
{% endhint %}

```typescript
return client.execute(
    sender,
    contract,
    msg,
    {
      amount: [{ amount: "1", denom: "uxion" }],
      gas: "500000",
      granter: TREASURY.treasury
    },
    "",
    []
  );
```

{% hint style="success" %}
If you have any questions or encounter issues, feel free to ask in our [**Telegram Developer Group**](https://t.me/+SC47NRhVz9Q2MDg0) or on [**Discord**](https://discord.com/invite/burnt) in our [**Dev Chat channel**](https://discord.com/channels/823953904512401469/1308977704586186762).
{% endhint %}
