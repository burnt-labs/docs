# Building a Per-User Data Storage Dapp

Let's build a simple yet powerful decentralized application (dApp) that allows users to store and retrieve their **JSON** data. For developers coming from **Web2**, this serves as an excellent introduction to **Web3** concepts by providing familiar functionality (user-specific data storage) implemented in a decentralized way.



## User Map Contract

The **User Map** contract is a lightweight and beginner friendly smart contract designed to help developers, especially those coming from Web2, understand how to manage per-user data in a decentralized environment. You can find the source code here: [github.com/burnt-labs/contracts/tree/main/contracts/user\_map](https://github.com/burnt-labs/contracts/tree/main/contracts/user_map).

This contract enables users to store their own JSON encoded data using their wallet address as a unique key. It functions similarly to a key-value store backend in Web2 applications, with each user's address acting as the key and their JSON object as the value.

Key capabilities:

* Allows any user to **store structured data** under their address (e.g., profile, settings, preferences).
* **Validates** that only proper JSON strings are stored.
* Makes all data **publicly queryable** (anyone can fetch stored data for any address).

### Key Components of the User Map Contract

Below is a breakdown of the key components of the contract.

#### **State Definition (`state.rs`)**

```rust
pub const USER_MAP: Map<Addr, String> = Map::new("user_map");
```

This defines a `Map` named `USER_MAP`, which stores stringified JSON values for each user, keyed by their wallet address (`Addr`).\
It acts like a key-value store:

* **Key**: `Addr` (user wallet address)
* **Value**: `String` (the JSON data)

#### **Messages (`msg.rs`)**

This file defines the messages that the contract can handle:

**`InstantiateMsg`**

```rust
pub struct InstantiateMsg {}
```

No parameters are required when initializing this contract.

**`ExecuteMsg`**

```rust
pub enum ExecuteMsg {
    Update { value: String }
}
```

* **Update**: Allows a user to store a JSON string under their own address.

**`QueryMsg`**

```rust
pub enum QueryMsg {
    GetUsers {},
    GetValueByUser { address: Addr },
    GetMap {}
}
```

Provides three ways to query stored data:

* `GetUsers`: Returns a list of all user addresses that have stored data.
* `GetValueByUser`: Retrieves the stored value for a specific address.
* `GetMap`: Returns the full map of all user addressed data pairs.

#### **Contract Logic (`contract.rs`)**

This file defines the behavior of the contract using various entry points.

**`instantiate`**

```rust
pub fn instantiate(...)
```

No configigurations done during this particular instantiation.

**`execute`**

```rust
pub fn execute(...)
```

Handles the `Update` message:

* Uses `serde_json::from_str` to ensure the input string is valid JSON.
* Stores the validated JSON string under the sender's address in `USER_MAP`.

**`query`**

```rust
pub fn query(...)
```

Implements all the `QueryMsg` variants:

* `GetValueByUser`: Loads and returns the stored string for a given user.
* `GetUsers`: Iterates over the keys in `USER_MAP` and returns all addresses.
* `GetMap`: Iterates over the full map and returns all `(address, value)` pairs.

### Common Use Cases

The following are some use cases for that this contract could cover.

1.  **User Profiles**

    ```json
    {
      "username": "alice123",
      "bio": "Web3 developer",
      "avatar": "ipfs://Qm..."
    }
    ```
2.  **Application Preferences**

    ```json
    {
      "theme": "dark",
      "notifications": true,
      "language": "en"
    }
    ```
3.  **Form Submissions**

    ```json
    {
      "survey": {
        "question1": "answer",
        "question2": 5
      }
    }
    ```



## Deploying User Map Contract On-Chain <a href="#deploying-a-contract-on-chain" id="deploying-a-contract-on-chain"></a>

If you're new to compiling, deploying, and instantiating smart contracts, we recommend following the [guide](../../featured-guides/your-first-contract/deploy-a-cosmwasm-smart-contract.md) to learn the process. Once deployed, the contract address will be referenced in your **Treasury contract configuration** and in the upcoming **code updates**. We'll also walk through the major steps  below.

### **Clone the Repository** <a href="#clone-the-repository" id="clone-the-repository"></a>

First, you need to download the contract code that will be compiled. Use the following commands to clone the repository and navigate into the project directory:

```shell
git clone https://github.com/burnt-labs/contracts
cd contracts
```

### **Optimize Contract** <a href="#optimize-contract" id="optimize-contract"></a>

Next, compile and optimize the smart contract using the [CosmWasm Optimizer](https://github.com/CosmWasm/optimizer). You need to have **Docker** running to execute the command below:

```sh
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/optimizer:0.16.1
```

The compiled contract will be located at `artifacts/user_map.wasm`.

### **Upload Optimized Contract On-chain** <a href="#upload-optimized-contract-on-chain" id="upload-optimized-contract-on-chain"></a>

To learn how to set up a wallet see the following section on [generating an account](https://docs.burnt.com/xion/developers/featured-guides/your-first-contract/deploy-a-cosmwasm-smart-contract#generate-an-account) using **xiond**. Next, set your **wallet address** or **key name** by executing the following in your terminal:

```sh
WALLET="your-wallet-address-or-key-name-here"
```

Now, upload the contract to the blockchain:

```sh
RES=$(xiond tx wasm store ./artifacts/user_map.wasm \
      --chain-id xion-testnet-2 \
      --gas-adjustment 1.3 \
      --gas-prices 0.001uxion \
      --gas auto \
      -y --output json \
      --node https://rpc.xion-testnet-2.burnt.com:443 \
      --from $WALLET)
```

After running the command, **extract the transaction hash** by executing:

```sh
echo $RES
```

Example output:

```json
{
  "height": "0",
  "txhash": "B557242F3BBF2E68D228EBF6A792C3C617C8C8C984440405A578FBBB8A385035",
  ...
}
```

Copy the **txhash** value for the next step.

### **Retrieve the Code ID** <a href="#retrieve-the-code-id" id="retrieve-the-code-id"></a>

The **Code ID** is required for creating an instance of your contract. Set your **transaction hash** you retrieved above by executing:

```sh
TXHASH="your-txhash-here"
```

Query the blockchain to get the **Code ID**:

```sh
CODE_ID=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --output json | jq -r '.events[-1].attributes[1].value')
```

### **Instantiate the Contract** <a href="#instantiate-the-contract" id="instantiate-the-contract"></a>

When you uploaded the **WASM bytecode**, you stored the **contract code** on the blockchain, but no contract instance has been created. The uploaded code can be used to create multiple contract instances, each with its own state.

Each contract instance requires a **unique initialization message** based on its expected parameters. In the case of the [**User Map** contract](https://github.com/burnt-labs/contracts/blob/main/contracts/user_map/src/msg.rs#L5), there are no required fields.

Set the contract's initialization message by executing:

```sh
MSG='{ }'
```

Instantiate the contract with the **Code ID** from the previous step:

```sh
xiond tx wasm instantiate $CODE_ID "$MSG" \
  --from $WALLET \
  --label "user-map" \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y --no-admin \
  --chain-id xion-testnet-2 \
  --node https://rpc.xion-testnet-2.burnt.com:443
```

Example output:

```
gas estimate: 217976
code: 0
txhash: 09D48FE11BE8D8BD4FCE11D236D80D180E7ED7707186B1659F5BADC4EC116F30
```

Copy the new transaction hash for the next step.

### **Retrieve the Contract Address** <a href="#retrieve-the-contract-address" id="retrieve-the-contract-address"></a>

Once a contract instance is created, it is assigned a **unique contract address**, which can be queried for state changes.

Set the new transaction hash from the previous contract instantiation step:

```sh
TXHASH="your-txhash-here"
```

Query the blockchain to get the **contract address**:

```sh
CONTRACT=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --output json | jq -r '.events[] | select(.type == "instantiate") | .attributes[] | select(.key == "_contract_address") | .value')
```

Display the contract address:

```sh
echo $CONTRACT
```

Example output:

```
xion1v6476wrjmw8fhsh20rl4h6jadeh5sdvlhrt8jyk2szrl3pdj4musyxj6gl
```



## Deploying a Treasury Contract <a href="#deploying-a-treasury-contract-for-gasless-transactions" id="deploying-a-treasury-contract-for-gasless-transactions"></a>

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** for your smart contract by handling **fee grants** on behalf of users as well as allowing users to grant authorization(s) to your dapp to execute certain account transactions on their behalf.

### Steps to Deploy a Treasury Contract <a href="#steps-to-deploy-a-treasury-contract" id="steps-to-deploy-a-treasury-contract"></a>

1. Login to the [XION Developer Portal](https://dev.testnet2.burnt.com/).
2. Click othen **"New Treasury"** button to create a new treasury contract.
3. **Select the appropriate configuration** based on your use case. The following "**Fee Grant**" and "**Grant Config**" sections gives a recommended configuration that works for most scenarios:

#### **Fee Grant**

<figure><img src="../../../.gitbook/assets/image (45).png" alt=""><figcaption><p>Example of a general <strong>Fee Grant</strong> configuration</p></figcaption></figure>

1. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request.&#x20;
2. In the **"Allowance Type"** field, enter `"/cosmwasm.feegrant.v1beta1.BasicAllowance"`.
3. In the **"Spend Limit"** field, enter **`1000uxion`**.
4. Click the **"Save"** button to apply the configuration.

#### **Grant Config**

<figure><img src="../../../.gitbook/assets/image (46).png" alt=""><figcaption><p>Example of additional <strong>Grant</strong> configuration</p></figcaption></figure>

1. For the **"Type URL"** field, select `"/cosmwasm.wasm.v1.MsgExecuteContract"`.
2. Enter a **"Description"** in the respective field. This will reflect the intended purpose of the request. This description will be displayed to users when they click **"Allow"** after connecting their account.
3. In the **"Authorization Type"** field, select `"/cosmwasm.wasm.v1.ContractExecutionAuthorization"`.
4. Enter the **contract address** in the **"Contract Address"** field — this should be the **User Map** smart contract created above.
5. You **must** select at least one of the following::
   * **"Max Call"** – Limits the number of times a user can execute a transaction under this fee grant.
   * **"Max Funds"** – Specifies the maximum amount of funds allocated for covering transaction fees.
   * **"Both"** – Allows you to set both options.
6. Click the **"Add Contract Grant"** button to apply the configuration.
7. Then click the "**Save**" button which generates the "**Treasury Instance Preview**"

#### **Treasury Instance Preview**

<figure><img src="../../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

Once the preview is to your liking click the "**Create**" button to create the Treasury contract.

{% hint style="info" %}
Learn more about Treasury Contracts [here](https://docs.burnt.com/xion/developers/featured-guides/your-first-dapp/create-a-gas-less-user-experience).
{% endhint %}



## Building the Frontend

We've created a frontend which is a [Next.js](https://nextjs.org/) application built to interact with the **User Map** smart contract that can be downloaded at [https://github.com/burnt-labs/xion-user-map-json-store-frontend](https://github.com/burnt-labs/xion-user-map-json-store-frontend). It showcases how users can connect their wallets, submit JSON data to update their record, and query the stored data.

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Manual Installation

You will first need to clone to repository using `git clone` [`https://github.com/burnt-labs/xion-user-map-json-store-frontend`](https://github.com/burnt-labs/xion-user-map-json-store-frontend). After doing so you will have to change into the newly created directory and then execute the steps below.



1. Install dependencies:

```bash
npm install
```

2. Copy the **`.env.example`** file and name it **`.env`** and set the values with the correct information:

```bash
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

```bash
npm run dev
```



### Frontend: Main Page Breakdown

The `src/app/page.tsx` file is the **core** of the frontend. It connects the wallet, submits JSON data to the User Map smart contract, and queries stored data, all using **Xion’s Abstraxion** toolkit.

#### **Environment Setup**

```tsx
const contractAddress = process.env.NEXT_PUBLIC_CONTRACT_ADDRESS!;
const rpc = process.env.NEXT_PUBLIC_RPC_URL!;
const rest = process.env.NEXT_PUBLIC_REST_URL!;
```

* Loads required information from `.env`.
* Needed to know **which contract to interact with** and **which blockchain node to connect to**.

#### **Wallet and Blockchain Client Setup**

```tsx
const { data: account } = useAbstraxionAccount();
const { client, signArb, logout } = useAbstraxionSigningClient();
const { client: queryClient } = useAbstraxionClient();
```

* `account`: Information about the **connected wallet** (if connected).
* `signArb`: **Signs and sends transactions** (e.g., sending a message to store data).
* `queryClient`: **Queries blockchain data** without needing the user's signature.

**This sets up the page to handle both signed actions (store data) and unsigned actions (read data).**

#### **Local State Management**

```tsx
const [jsonInput, setJsonInput] = useState("");
const [userAddressQuery, setUserAddressQuery] = useState("");
const [queryResult, setQueryResult] = useState("");
```

* `jsonInput`: What the user wants to save (stringified JSON).
* `userAddressQuery`: What user address to query.
* `queryResult`: Where to show the retrieved data after a query.

**Keeps track of user inputs and outputs without page reloads.**

#### **Handling Submitting JSON Data (`handleSubmit`)**

```tsx
async function handleSubmit() { ... }
```

* Takes the `jsonInput` the user typed in.
* Wraps it into the **`update`** smart contract message structure.
* Sends the transaction using `client`.

If successful, the blockchain stores the user's JSON string under their address.

**Example message sent to blockchain:**

```json
{
  "update": {
    "value": "{\"username\":\"alice\",\"theme\":\"dark\"}"
  }
}
```

**This mirrors a REST API `POST` request in Web2.**

#### **Handling Querying User Data (`handleQuery`)**

```tsx
async function handleQuery() { ... }
```

* Takes an entered wallet address (`userAddressQuery`).
* Sends a **smart query** to the blockchain to fetch the stored data.
* Displays the result in `queryResult`.

**Example smart query sent:**

```json
{
  "get_value": {
    "address": "xion1abc..."
  }
}
```

**This mirrors a REST API `GET` request in Web2.**

#### **Quick Frontend Walkthrough**

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FB5Z5ijJgMx0GJO3l1Il9%2Fuploads%2Fm3KYp3p4hRXf9ASk62UA%2FPer-User%20Data%20Storage%20Dapp.mp4?alt=media&token=8c0bf0fd-7a4c-47cf-aeac-5bec51b51579" %}
