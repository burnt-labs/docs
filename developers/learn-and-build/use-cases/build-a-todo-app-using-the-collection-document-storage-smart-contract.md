# Build a TODO App using the Collection-Document Storage Smart Contract

To reduce the need for custom contract development and accelerate Web2 to Web3 migrations, we've created a contract that provides a **collection/document based storage model**, inspired by Firebase’s Firestore. It supports structured JSON data storage using a flexible key-value format and an advanced permission model.

This solution is a major upgrade from the original `user_map` contract by enabling:

* **Namespaced storage**: Data is organized into logical **collections** and **documents**.
* **Permissioned access**: Fine-grained control over who can **`create`**, **`update`**, `delete`, and `read` documents.
* **Role-based security**: Support for roles like "creator", "admin", and any custom roles.
* **Batch operations**: Write multiple documents in a single transaction.



## Why This Pattern Works for Developers

This architecture is designed to simplify App development by offering a ready-made, flexible backend for storing user-specific and structured application data. Instead of writing custom storage logic for every project, developers can leverage a familiar model that handles collections, documents, and permissions out of the box. By adopting this approach, developers can:

* **Accelerates development**: Skip the need to write, test, or audit custom storage contracts from scratch.
* **Speeds up time-to-launch**: Teams can prototype quickly and launch with just a frontend and this reusable backend.
* **Reduces smart contract complexity**: Consolidates repetitive storage logic into a single, modular contract.
* **Eases the Web2-to-Web3 transition**: Adopts a Firebase-like model using collections and documents, making it intuitive for Web2 developers.

### Possible Use Cases

This pattern is ideal for applications that need to store dynamic, user-specific, and structured content on-chain. Whether you're building tools for creators, learners, shoppers, or communities, this storage model provides a reliable and scalable foundation.

Use cases include:

* **Social platforms** – Posts, messages, comments, and user timelines
* **Educational apps** – Quizzes, answers, grades, and course submissions
* **eCommerce platforms** – Product listings, shopping carts, user reviews, and order history
* **Creative tools** – Galleries, stories, media libraries, and asset management

Because of its modular design and flexible permissions, the same backend contract can support multiple use cases across a single app or ecosystem.



## Storage Model

This model is inspired by Firebase’s document-based NoSQL structure, allowing apps to store structured, hierarchical data in a flexible and scalable way.

At its core, the storage is based on a two-level key-value mapping:

```rust
Map<(String, String), Document>
```

Each entry is organized by:

* **Collection name** (e.g., "users", "quizzes", "settings")
* **Document ID** (e.g., "xion1abc...", "quiz\_001", "user\_quiz1")



Each document includes metadata:

```rust
pub struct Document {
    pub data: String,         // The actual data as JSON or encoded string
    pub owner: Addr,          // Owner address (user or admin)
    pub created_at: Timestamp,
    pub updated_at: Timestamp,
}
```

This structure supports two main types of storage:

### 1. User-Owned Documents

These are records that individual users can create and manage themselves, such as profile data or quiz submissions. Each user’s access is limited to their own documents, while admins retain the ability to override or update any record if needed.

Examples:

* `user_profiles/xion1abc...`
* `quiz_submissions/user1_quiz1`

### 2. Admin-Controlled Collections

Reserved for system-level or shared data, only authorized addresses (like the contract admin or a configured role) can write to these collections. They are ideal for managing global or shared state across the app.

Examples:

* `quizzes/quiz_001`
* `settings/platform_config`

This model provides a clean and extensible foundation for building multi-user, multi-role apps with fine-grained control over data access, storage structure, and updates.



## Permission System

The permission system provides fine-grained access control over data stored in the contract, allowing developers to define who can perform specific actions on each collection. Permissions are defined at the collection level, ensuring consistent rules for all documents within that namespace.

### Collection-Level Permissions

Each collection can specify separate rules for the following actions:

```rust
pub struct CollectionPermissions {
    pub create: PermissionLevel,
    pub update: PermissionLevel,
    pub delete: PermissionLevel,
    pub read: PermissionLevel,
}
```

This means you can independently control who can **create**, **update**, **delete**, or **read** documents in any given collection.

### Permission Levels

The following permission levels are supported:

* **Anyone**: Open access to all users.
* **AdminOnly**: Only the admin can perform the action.
* **AllowList(Vec)**: Only specific addresses are allowed.
* **DenyList(Vec)**: Everyone except the listed addresses are allowed.
* **RequireRole(String)**: Only users with a specific role (e.g., "moderator") can perform the action.

These flexible permission levels allow each collection to enforce tailored access rules based on user roles or specific address whitelisting/blacklisting.

### Role System

The role system complements permissions by allowing role-based access control (RBAC). Roles are stored in a simple mapping:

```rust
Map<Addr, Vec<String>>
```

This allows each address to have one or more assigned roles.

* **Admin role**: Granted full access across all collections and operations.
* **Custom roles**: Developers can define and assign roles such as "**verified\_tutor**", "**moderator**", or any application-specific designation.

By combining the permission and role systems, dapps can implement sophisticated access models to manage both user-generated and system-controlled content in a secure and scalable way.

***

## Create an Instance of the DocuStore Contract

There's no need to deploy your own version of the DocuStore contract on-chain as we've already done so for Testnet.&#x20;

> The **Code ID** for the latest deplosyment is **1,228**

Each contract instance requires a **unique initialization message** based on its expected parameters. In the case of the **DocuStore** contract, an **admin** parameter which stores a Xion address is required.

Set the contract's initialization message by executing the following in your terminal, swapping the address to a Xion address you own and would like to be the admin of the contract:

```bash
MSG='{"admin":"xion1..."}'
```

Execute the following command updating the value for `--from` to your wallet address that will be executing the transaction:

```bash
xiond tx wasm instantiate $CODE_ID "$MSG" \
  --from "your-address-here" \
  --label "todo app" \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y --no-admin \
  --chain-id xion-testnet-2 \
  --node https://rpc.xion-testnet-2.burnt.com:443
```

Example output:

```bash
gas estimate: 217976
code: 0
txhash: 235FFA25254D8E22BF24E6CCF4F0F3055D5B56D4529BC968B1FF36AB9EA7509D
```

Copy the `txhash` value for the next step.

### **Retrieve the Contract Address** <a href="#retrieve-the-contract-address" id="retrieve-the-contract-address"></a>

Once a contract instance is created, it is assigned a **unique contract address**.

Set the new transaction hash from the previous contract instantiation step:

```bash
TXHASH="your-txhash-here"
```

Query the blockchain to get the **contract address**:

```bash
CONTRACT=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --output json | jq -r '.events[] | select(.type == "instantiate") | .attributes[] | select(.key == "_contract_address") | .value')
```

Display the contract address:

```bash
echo $CONTRACT
```

Example output:

```bash
xion1svpts9q2ml4ahgc4tuu95w8cqzv988s6mf5mupt5kt56gvdnklks9hzar4
```



## Deploy a Treasury Contract <a href="#deploying-a-treasury-contract-for-gasless-transactions" id="deploying-a-treasury-contract-for-gasless-transactions"></a>

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** for your smart contract by handling **fee grants** on behalf of users as well as allowing users to grant authorization(s) to your app to execute certain  transactions on their behalf.

### Steps to Deploy a Treasury Contract <a href="#steps-to-deploy-a-treasury-contract" id="steps-to-deploy-a-treasury-contract"></a>

1. Login to the [XION Developer Portal](https://dev.testnet.burnt.com/).
2. Click the **"New Treasury"** button to create a new treasury contract instance.
3. **Select the appropriate configurations**. The following "**Fee Grant (Allowance)**" and "**Grant Config (Permission)**" sections gives a recommended configuration that works for the DocuStore app:

#### **Allowance**

<figure><img src="../../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

1. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request.
2. For the **"Allowance Type"** select "**Basic Allowance**".
3. In the **"Spend Limit"** field, enter **`0.1`** and select `XION` as the token.
4. Click the **"Save"** button to apply the configuration.

#### **Permission**

<figure><img src="../../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

1. Enter a description in the **"Description"** field. This will reflect the intended purpose of the permission request. This description will be displayed to users when they click **"Allow"** after connecting their account.
2. For the **"Permission Type"** field, select `Execute on a smart contract`.
3. In the **"Authorization Type"** field, select `Contract Execution Authorization`.
4. Enter the **contract address** in the **"Contract Address"** field — this should be the **DocuStore** smart contract created above.
5. You **must** select at least one of the following::
   * **"Max Call"** – Limits the number of times a user can execute a transaction under this fee grant.
   * **"Max Funds"** – Specifies the maximum amount of funds allocated for covering transaction fees.
   * **"Both"** – Allows you to set both options.
6. Click the **"Add Contract Grant"** button to apply the configuration.
7. Then click the "**Save**" button which generates the "**Treasury Instance Preview**"

#### **Treasury Params**

<figure><img src="../../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

You will need to enter a `Redirect URL` along with an `ICON URL`. After those fields are set click the "**Create**" button to finally create the treasury contract instance. You will be taken to the dahsboard where you will see the newly created Treasury Contract instance. You can click on an instance to get the treasury contract address which will be required for setting up the frontend.

{% hint style="info" %}
Learn more about Treasury Contracts [here](https://docs.burnt.com/xion/developers/featured-guides/your-first-dapp/create-a-gas-less-user-experience).
{% endhint %}



## Todo App Data Structure

This todo app interacts with the DocuStore smart contract by organizing data into three main collections. Each collection stores structured documents with user-specific data.

### Todos Collection

```rust
struct Document {
    owner: String,
    title: String,
    content: String,
    created_at: u64,
    updated_at: u64,
    metadata: Option<Metadata>,
}
```

Each document in the `todos` collection represents an individual task. It includes basic task details, ownership, timestamps, and optional metadata (e.g., tags, priority, due date).

### Profiles Collection

```rust
struct Profile {
    display_name: String,
    bio: String,
    avatar: String,
    social_links: Vec<SocialLink>,
}
```

The `profile` collection stores public facing user profile data such as name, bio, and social media links, useful for personalization or social features in the app.

### Settings Collection

```rust
struct Settings {
    dark_mode: bool,
    notifications: bool,
    language: String,
    timezone: String,
}
```

The `settings` collection holds user preferences for customizing the application experience, including UI theme, notification toggles, and localization options.



## Building the Frontend <a href="#building-the-frontend" id="building-the-frontend"></a>

We've created a frontend which is a [Next.js](https://nextjs.org/) application built to interact with the **DocuStore** smart contract that can be downloaded at [https://github.com/burnt-labs/todo-app-frontend](https://github.com/burnt-labs/todo-app-frontend). It showcases how users can connect their wallets, create todo records, update their profile, app settings and query their data.

<figure><img src="../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

### Manual Installation <a href="#manual-installation" id="manual-installation"></a>

You will first need to clone the repository:

```bash
git clone https://github.com/burnt-labs/todo-app-frontend
```

After doing so you will have to change into the newly created directory and then execute the steps below.

1. Install dependencies:

```bash
cd todo-app-frontend
npm install
```

2. Copy the **`.env.example`** file and name it **`.env.local`** and set the values with the correct information:

```javascript
NEXT_PUBLIC_TREASURY_ADDRESS=your_treasury_address
NEXT_PUBLIC_CONTRACT_ADDRESS=your_contract_address
NEXT_PUBLIC_RPC_URL="https://rpc.xion-testnet-2.burnt.com:443"
NEXT_PUBLIC_REST_URL="https://api.xion-testnet-2.burnt.com"
```

| Variable                        | Description                                                                 |
| ------------------------------- | --------------------------------------------------------------------------- |
| NEXT\_PUBLIC\_TREASURY\_ADDRESS | Treasury address used for gasless transactions and grantz authorization     |
| NEXT\_PUBLIC\_CONTRACT\_ADDRESS | Address of your deployed User Map smart contract                            |
| NEXT\_PUBLIC\_RPC\_URL          | RPC endpoint for Xion (default: `https://rpc.xion-testnet-2.burnt.com:443`) |
| NEXT\_PUBLIC\_REST\_URL         | REST endpoint for Xion (default: `https://api.xion-testnet-2.burnt.com`)    |



3. Build and start the application:

```
npm run dev
```

You can now access the app at [http://localhost:3000](http://localhost:3000/) in your browser.
