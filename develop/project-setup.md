# Project Setup

### Creating a Rust project

Since smart contracts are Rust library crates, the user will begin with creating one:

```
$ cargo new --lib ./empty-contract
```

The user has now created a simple Rust library, though it is not yet ready to be a smart contract. **The first thing to do is to update the Cargo.toml file:**

```
[package]
name = "contract"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
cosmwasm-std = { version = "1.0.0-beta8", features = ["staking"] }

```

The user now added a crate-type field for the library section. It is required to generate the cdylib to create a proper web assembly binary.

_**Note:**_ Having such a library cannot be used as a dependency for other Rust crates.

Additionally, there is one core dependency for smart contracts: **the cosmwasm-std**. This crate is a standard library for smart contracts. It provides essential utilities for communication with the outside world and a couple of helper functions and types. Every smart contract a user builds will use this dependency.

## Creating a Smart Contract

Now that the user has created a Rust library, they can now create a simple smart contract.

#### Install the following:

```
cargo new my_cosmwasm_contract
cd my_cosmwasm_contract

```

#### Writing the smart contract code(s):&#x20;

First, open the `src/lib.rs file` in my project directory and replace its contents with the following example code:

```
use cosmwasm_std::{Beinary, Deps, DepsMut, Env, MessageInfo, Response, StdResult};

pub fn instantiate(_deps: DepsMut, _env: Env, _info: MessageInfo, _msg: Binary) -> StdResult<Response> {
    Ok(Response::default())
}

pub fn execute(_deps: DepsMut, _env: Env, _info: MessageInfo, _msg: Binary) -> StdResult<Response> {
    Ok(Response::default())
}

#[cfg(test)]
mod tests {
    use super::*;
    use cosmwasm_std::testing::{mock_dependencies, mock_env, mock_info};

    #[test]
    fn test_instantiate() {
        let mut deps = mock_dependencies(&[]);
        let env = mock_env();
        let info = mock_info("creator", &[]);
        let msg = Binary::from(r#"{"some_key": "some_value"}"#);

        let res = instantiate(deps.as_mut(), env.clone(), info.clone(), msg.clone()).unwrap();
        assert_eq!(0, res.messages.len());

        // You can add more test cases here
    }

    #[test]
    fn test_execute() {
        let mut deps = mock_dependencies(&[]);
        let env = mock_env();
        let info = mock_info("sender", &[]);
        let msg = Binary::from(r#"{"some_key": "some_value"}"#);

        let res = execute(deps.as_mut(), env.clone(), info.clone(), msg.clone()).unwrap();
        assert_eq!(0, res.messages.len());

        // You can add more test cases here
    }
}

```

#### Build and Test the Smart Contract

In the project directory, build and test your **CosmWasm smart contract** by doing the following.

1. Create a new file directory called .`cargo`&#x20;
2. Inside the `.cargo` directory, create a file named `config` if it doesn't already exist.
3. Open the `config` file in your text editor.
4. Add the following lines to the `config` file:

```
[alias]
wasm = "build --release --lib --target wasm32-unknown-unknown"
```

1. Save the `config` file.

Now, your project should have a `.cargo/config` file with the specified alias for building the CosmWasm smart contract. You can now run the following comands.

```
cargo wasm
cargo test
```

The `cargo wasm` command will use the alias defined in the `.cargo/config` file to build your CosmWasm smart contract with the specified options.

### Deploying Contract on TestNet

Use the CosmWasm CLI to deploy the created contract to the testnet. The specific command and options may vary depending depending on version.

```bash
xiond tx wasm store contract.tar.gz --from <sender_address> --chain-id <chain_id> --gas auto --gas-prices <gas_prices> --node <testnet_rpc_url>
```

Replace `<sender_address>, <chain_id>, <gas_prices>, and <testnet_rpc_url>` with the appropriate values for your testnet environment.

**Where to find appropriate values?**&#x20;

`<sender_address>:`

This is the address of your account on the testnet. If you don't have one, you'll need to create an account on the XION's testnet and obtain the address.&#x20;

`<chain_id>:`

The chain ID is a unique identifier for the specific blockchain network you're deploying to. You can usually find the chain ID in the XION's testnet's documentation or information.

`<gas_prices>:`

Gas prices are typically denominated in the native token of the blockchain. You may need to refer to the testnet's documentation or forums to find the appropriate gas prices.

`<testnet_rpc_url>:`

You'll need this URL to submit transactions and query data. Testnets often provide RPC endpoints that you can use. These can be found in the XION's testnet's documentation or developer resources.

_Remember that these values are specific to the XION testnet you''ll be deploying to._&#x20;

## Interacting with the Deployed Contract

Now that the contract is deployed, the user can interact with it using the CosmWasm CLI or other tools. For example, to execute a contract message, you can use a command such as the following.

```
xiond tx wasm execute <contract_address> '{"set": {"new_value": 42}}' --from <sender_address> --chain-id <chain_id> --gas auto --gas-prices <gas_prices> --node <testnet_rpc_url>
```

Replace '`<contract_address>','<sender_address>','<chain_id>','<gas_prices>',` and '`<testnet_rpc_url>'` with the appropriate values.

## Confirming Change Took Place

If a user would like to verify that the change took place after executing a contract message, they can query the contract state before and after the execution.&#x20;

**Here's how a user can achieve that:**

```
// Define variables
CONTRACT_ADDRESS="your_contract_address"
SENDER_ADDRESS="your_sender_address"
CHAIN_ID="your_chain_id"
GAS_PRICES="your_gas_prices"
TESTNET_RPC_URL="your_testnet_rpc_url"

// Query the contract state before execution
QUERY_BEFORE=$(xiond query wasm contract-state smart $CONTRACT_ADDRESS '{"get": {}}' --node $TESTNET_RPC_URL)

// Execute the contract message
xiond tx wasm execute $CONTRACT_ADDRESS '{"set": {"new_value": 42}}' --from $SENDER_ADDRESS --chain-id $CHAIN_ID --gas auto --gas-prices $GAS_PRICES --node $TESTNET_RPC_URL

// Query the contract state after execution
QUERY_AFTER=$(xiond query wasm contract-state smart $CONTRACT_ADDRESS '{"get": {}}' --node $TESTNET_RPC_URL)

// Print the before and after query results
echo "Contract State Before Execution:"
echo $QUERY_BEFORE
echo "Contract State After Execution:"
echo $QUERY_AFTER

```

**In this code block:**

Replace '`your_contract_address', 'your_sender_address', 'your_chain_id', 'your_gas_prices',` and `'your_testnet_rpc_url'` with the appropriate values for the user

The xiond query wasm contract-state command is used to query the contract state. The smart argument indicates that you're using a smart query.

The contract state is queried twice: before executing the message and after executing the message.

The results of the queries are stored in the `QUERY_BEFORE` and `QUERY_AFTER` variables.

The script then prints the contract state before and after the execution for comparison.

## Viewing Contract Transactions

The user can use a block explorer specific to the testnet to view the created contract's transactions, state changes, and other interactions that take place. Many testnets provide block explorers that allow you to search for addresses, transactions, and smart contract details.

Keep in mind, the process of deploying and interacting with smart contracts on testnets can vary significantly for users depending on the testnet they are using.&#x20;

Testnets are designed for testing and experimentation, so the testnet tokens you use are not real value. Remember to be cautious with private keys and mnemonic phrases, and do not use the same keys on testnets that you use on mainnet.

