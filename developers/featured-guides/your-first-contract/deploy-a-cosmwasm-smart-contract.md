# Deploying Your First Smart Contract on XION

This guide will walk you through the process of deploying a smart contract on the **XION** network. The deployment process involves **compiling** an **optimized** version of your contract code, **uploading** this optimized code to the blockchain, and then creating an **instance** of the contract. Once instantiated, the contract will have its own **unique address** and **data store**, allowing you to interact with it through transactions and queries.



## Prerequisites

Before deploying your smart contract on-chain, ensure you have completed the following setup steps:

* **Set up your local environment**: Follow the [installation and setup guide](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment) to configure your development environment.
* **Install the XION daemon**: Set up the XION CLI by following the [installation instructions](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/interact-with-xion-chain-setup-xion-daemon) to interact with the blockchain.
* Make sure you have [Docker](https://www.docker.com/get-started) installed and running, as it is required to compile your contract.



## Generate an account

To execute transactions on-chain, you need at least one funded account. This section will guide you through the process of creating and funding an account using `xiond`.

### Generate an Account Key Pair

To interact with the XION blockchain, you need a cryptographic key pair, consisting of a **public key** and a **private key**. The public key is used to derive your address, while the private key is required to sign transactions.

#### **Generate a New Key Pair**

Use the following command to create a new key pair and add it to your local key store:

```sh
xiond keys add <keyname>
```

Replace `<keyname>` with a name of your choice for easy reference.

#### **Retrieve Public Key**

If you have already generated a key pair and need to retrieve its public key, use the following command:

```sh
xiond keys show <keyname>
```

Replace `<keyname>` with the name of your key to display its public key.



### Fund Your Account

Your account is not fully registered on-chain until it is involved in a transaction. The easiest way to achieve this is by **funding your account**, which allows it to interact with the network.

#### **Request Testnet Tokens**

You can obtain testnet tokens through one of the following methods:

* **Discord Faucet**: Request tokens by using the faucet bot in the **XION Discord**.
* **Faucet Web Page**: Visit the [XION Faucet](https://faucet.xion.burnt.com/) and follow the instructions to receive testnet tokens.

For more details on accessing testnet tokens, see our [Faucet Page](https://docs.burnt.com/xion/developers/section-overview/xion-testnet-1).

#### **Mainnet Tokens**

If you’re deploying contracts on **XION Mainnet**, you can acquire XION tokens through various **decentralized** and **centralized exchanges**.



## **Compile and Optimize the Contract**

To demonstrate the deployment process, we’ll use a simple [**Counter**](https://github.com/burnt-labs/cw-counter) smart contract. This contract provides an example of state management on the XION blockchain. It allows you to:

* Set an initial counter value
* Increment or reset the counter
* Query the current counter value

### **Clone the Repository**

First, you need to download the contract code that will be compiled. Use the following commands to clone the repository and navigate into the project directory:

```
git clone https://github.com/burnt-labs/cw-counter
cd cw-counter
```

### **Optimize Contract**

Next, compile and optimize the smart contract using the [CosmWasm Optimizing Compiler](https://github.com/CosmWasm/optimizer). You need to have **Docker** running to execute the command below:

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/optimizer:0.16.0
```

#### **Why Optimization is Important**

Optimization ensures that the contract is compiled into a compact **WebAssembly (WASM) binary**, reducing on-chain storage costs and improving execution performance.

Once compiled, the optimized contract will be available at

```
./artifacts/cw_counter.wasm
```



## **Upload Optimized Contract On-chain**

First, set your wallet address or key name by executing the following in your terminal:

```
WALLET="your-wallet-address-or-key-name-here"
```

Now, upload the contract to the blockchain:

```
RES=$(xiond tx wasm store ./artifacts/cw_counter.wasm \
      --chain-id xion-testnet-2 \
      --gas-adjustment 1.3 \
      --gas-prices 0.1uxion \
      --gas auto \
      -y --output json \
      --node https://rpc.xion-testnet-2.burnt.com:443 \
      --from $WALLET)
```

After running the command, **extract the transaction hash** by executing:

```
echo $RES
```

Example output:

```
{
  "height": "0",
  "txhash": "B557242F3BBF2E68D228EBF6A792C3C617C8C8C984440405A578FBBB8A385035",
  ...
}
```

Copy the **txhash** value for the next step.



## **Retrieve the Code ID**

The **Code ID** is required for creating an instance of your contract.

Set your **transaction hash** you retrieved above by executing:

```
TXHASH="your-txhash-here"
```

Query the blockchain to get the **Code ID**:

```
CODE_ID=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --output json | jq -r '.events[-1].attributes[1].value')
```

Now, display the retrieved **Code ID**:

```
echo $CODE_ID
```

Example output:

```
1213
```



## **Instantiate the Contract**

&#x20;When you uploaded the **WASM bytecode**, you stored the **contract code** on the blockchain, but no contract instance was created yet. The uploaded code can be used to create multiple contract instances, each with its own state.

Each contract instance requires a **unique initialization message** based on its expected parameters. In the case of the [**Counter** contract](https://github.com/burnt-labs/cw-counter/blob/main/src/msg.rs#L4-L6), the only required variable is `count`, which is of type `i32` (a 32-bit integer).

Set the contract's initialization message by executing:

```
MSG='{ "count": 1 }'
```

Instantiate the contract with the **Code ID** from the previous step:

```
xiond tx wasm instantiate $CODE_ID "$MSG" \
  --from $WALLET \
  --label "cw-counter" \
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



## **Retrieve the Contract Address**

Once a contract instance is created, it is assigned a **unique contract address** and its own **data store**, which can be queried for state changes and interactions.

Set the new transaction hash from the contract instantiation step:

```
TXHASH="your-txhash-here"
```

Query the blockchain to get the **contract address**:

```
CONTRACT=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --output json | jq -r '.events[] | select(.type == "instantiate") | .attributes[] | select(.key == "_contract_address") | .value')
```

Display the contract address:

```
echo $CONTRACT
```

Example output:

```
xion1v6476wrjmw8fhsh20rl4h6jadeh5sdvlhrt8jyk2szrl3pdj4musyxj6gl
```



## Querying the Contract

The deployed contract includes a method to query the **current count value**. You can use this to retrieve the stored state.

To fetch the current count, use the following:

```sh
QUERY='{"get_count":{}}'
```

Run the following command to send the query request to the contract:

```sh
xiond query wasm contract-state smart $CONTRACT "$QUERY" --output json --node https://rpc.xion-testnet-2.burnt.com:443
```

This command will return the **current count value** stored in the contract.



## Execute Transactions

Now that the contract is deployed, let’s interact with it by executing transactions. The contract supports two transaction types:

1. **Increment the count** → Increases the counter by **+1**
2. **Reset the count** → Sets the counter to a specified value

Since these transactions modify the contract’s internal state, **gas fees** are required for execution.

### **Increment the Contract’s Count**

Let’s send a transaction to **increase the count by 1**:

```sh
TRY_INCREMENT='{"increment": {}}'
xiond tx wasm execute $CONTRACT "$TRY_INCREMENT" \
  --from $WALLET \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --chain-id xion-testnet-2
```

After executing this transaction, you can run the **get\_count query** again to verify that the count has increased by **+1** from its previous value.

### **Reset the Contract’s Count**

Now, let’s send a transaction to **reset the count** to a specific value (e.g., `0`):

```sh
RESET='{"reset": {"count": 0}}'
xiond tx wasm execute $CONTRACT "$RESET" \
  --from $WALLET \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y \
  --node https://rpc.xion-testnet-2.burnt.com:443 \
  --chain-id xion-testnet-2
```

Like the increment transaction, the **reset transaction** also modifies the contract’s state and requires gas fees.

After querying the contract again you will confirm that the count has been reset to **0** or the value you specified.
