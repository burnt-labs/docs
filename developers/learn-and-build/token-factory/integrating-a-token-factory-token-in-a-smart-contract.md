# Integrating a Token Factory Token in a Smart Contract

This guide will walk you through integrating a **Token Factory token** into a **smart contract**. The contract will allow users to:

* Retrieve their Token Factory token balance.
* Send Token Factory tokens to another user.



## **Prerequisites**

Before deploying your smart contract on-chain, ensure you have completed the following setup steps:

* **Set up your local environment**: Follow the [installation and setup guide](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment) to configure your development environment.
* **Install the XION daemon**: Set up the XION CLI by following the [installation instructions](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/interact-with-xion-chain-setup-xion-daemon) to interact with the blockchain.
* Make sure you have [Docker](https://www.docker.com/get-started) installed and running, as it is required to compile your contract.



## **Creating the Smart Contract**

### **Initialize a New Contract**

We are going to use the [cw-counter](https://github.com/burnt-labs/cw-counter) contract as a base for our contract. Run the following command to create a new contract:

```sh
cargo generate --git https://github.com/burnt-labs/cw-counter --name token_factory_contract
cd token-factory-contract
```



### **Implementing Core Logic**

#### **Remove some files**

We need to remove the **src/helpers.rs** and **src/integration\_test.rs** files. Then update the **lib.rs** file with the following:

```rust
pub mod contract;
mod error;
pub mod msg;
pub mod state;

pub use crate::error::ContractError;
```

#### **Store the Token Address in Contract State**

Modify `state.rs` to store the **token denomination** in the contract’s persistent storage.

```rust
use cw_storage_plus::Item;

pub const TOKEN_DENOM: Item<String> = Item::new("token_denom");
```

#### **Define Contract Messages**

Delete the contents of `src/msg.rs` and replace with the following:

```rust
use cosmwasm_schema::{cw_serde, QueryResponses};

#[cw_serde]
pub struct InstantiateMsg {
    pub token_denom: String, // Token denom (e.g., factory/xion.../mytoken)
}

#[cw_serde]
pub enum ExecuteMsg {
    SendToken {
        recipient: String,
    },
}

#[cw_serde]
#[derive(QueryResponses)]
pub enum QueryMsg {
    #[returns(BalanceResponse)]
    GetBalance { address: String },
}

#[cw_serde]
pub struct BalanceResponse {
    pub balance: String,
}
```

#### **Contract Logic**

Modify `src/contract.rs` to implement the **core logic of the contract**:

```rust
use cosmwasm_std::{
    entry_point, to_binary, Addr, Binary, Coin, Deps, DepsMut, Env, MessageInfo, QueryRequest,
    Response, StdResult, BankMsg, BankQuery, Uint128,
};
use crate::msg::{BalanceResponse, ExecuteMsg, InstantiateMsg, QueryMsg};
use crate::state::TOKEN_DENOM;

#[entry_point]
pub fn instantiate(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: InstantiateMsg,
) -> StdResult<Response> {
    TOKEN_DENOM.save(deps.storage, &msg.token_denom)?;

    Ok(Response::new()
        .add_attribute("method", "instantiate")
        .add_attribute("token_denom", msg.token_denom))
}

#[entry_point]
pub fn execute(
    deps: DepsMut,
    _env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> StdResult<Response> {
    match msg {
        ExecuteMsg::SendToken { recipient } => execute_send_token(deps, info, recipient),
    }
}

fn execute_send_token(
    deps: DepsMut,
    info: MessageInfo,
    recipient: String,
) -> StdResult<Response> {
    if info.funds.len() != 1 {
        return Err(cosmwasm_std::StdError::generic_err(
            "Must send exactly one token type",
        ));
    }

    // Extract the token denomination and amount
    let sent_coin = &info.funds[0];
    let amount = sent_coin.amount;
    let token_denom = sent_coin.denom.clone();

    let transfer_msg = BankMsg::Send {
        to_address: recipient.clone(),
        amount: vec![Coin {
            denom: token_denom.clone(),
            amount,
        }],
    };

    Ok(Response::new()
        .add_message(transfer_msg)
        .add_attribute("action", "send_token")
        .add_attribute("sender", info.sender.to_string())
        .add_attribute("recipient", recipient)
        .add_attribute("amount", amount.to_string())
        .add_attribute("token_denom", token_denom))
}

#[entry_point]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::GetBalance { address } => to_binary(&query_balance(deps, address)?),
    }
}

fn query_balance(deps: Deps, address: String) -> StdResult<BalanceResponse> {
    let token_denom = TOKEN_DENOM.load(deps.storage)?;

    let balance_response: cosmwasm_std::BalanceResponse = deps.querier.query(&QueryRequest::Bank(
        BankQuery::Balance {
            address,
            denom: token_denom.clone(),
        },
    ))?;

    Ok(BalanceResponse {
        balance: balance_response.amount.amount.to_string(),
    })
}
```



## **Optimize Contract**

Next, compile and optimize the smart contract using the [CosmWasm Optimizing Compiler](https://github.com/CosmWasm/optimizer). You need to have **Docker** running to execute the command below. Make sure you are in the root folder of your project and then executes the following:

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/optimizer:0.16.0
```



## **Upload Optimized Contract On-chain**

First, set your wallet address or key name by executing the following in your terminal:

```
WALLET="your-wallet-address-or-key-name-here"
```

Now, upload the contract to the blockchain:

```
RES=$(xiond tx wasm store ./artifacts/cw_counter.wasm \
      --chain-id xion-testnet-1 \
      --gas-adjustment 1.3 \
      --gas-prices 0.1uxion \
      --gas auto \
      -y --output json \
      --node https://rpc.xion-testnet-1.burnt.com:443 \
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
  --node https://rpc.xion-testnet-1.burnt.com:443 \
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

To initialize the contract, set up the initialization message:

```
MSG='{ "token_denom": "factory/xion1xyz.../denom-name" }'
```

Replace `"factory/xion1xyz.../denom-name"` with the actual token factory token you created.

Now, instantiate the contract using the `CODE_ID` obtained from the previous step:

```
xiond tx wasm instantiate $CODE_ID "$MSG" \
  --from $WALLET \
  --label "token-factory-token" \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y --no-admin \
  --chain-id xion-testnet-1 \
  --node https://rpc.xion-testnet-1.burnt.com:443
```

Example output:

```
gas estimate: 217976
code: 0
txhash: 09D48FE11BE8D8BD4FCE11D236D80D180E7ED7707186B1659F5BADC4EC116F30
```

Copy the new **transaction hash** for the next step.



## **Retrieve the Contract Address**

Once the contract has been instantiated, it is assigned a unique contract address. Follow these steps to retrieve it:

Set the transaction hash (`TXHASH`) from the contract instantiation step:

```
TXHASH="your-txhash-here"
```

Replace `"your-txhash-here"` with the actual transaction hash from the previous step.

Query the blockchain to get the **contract address**:

```
CONTRACT=$(xiond query tx $TXHASH \
  --node https://rpc.xion-testnet-1.burnt.com:443 \
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

After deploying the contract, you can interact with it using queries.

The contract exposes a method to fetch the balance of a specific address. Define the query message in the following format:

```sh
QUERY='{"get_balance":{ "address": "add-address-here" }}'
```

Replace `"add-address-here"` with the actual address you want to check.

Run the following command to query the contract for the specified address's token balance:

```sh
xiond query wasm contract-state smart $CONTRACT "$QUERY" --output json --node https://rpc.xion-testnet-1.burnt.com:443
```

Where:

* `$CONTRACT` is the smart contract address retrieved in the previous step.
* `$QUERY` contains the request payload.

If the query is successful, you will receive a JSON response containing the balance of the specified address. Example output:

```
{
  "data": {
    "balance": "1000000"
  }
}
```

This indicates that the queried address holds `1,000,000` units of the token.



## Execute Transactions

Now that you have an instance of the contract, you can interact with it by executing transactions. The contract accepts a message that allows token transfers from the sender to a specified recipient.

Define the message as follows:

```sh
SEND_TOKEN='{"send_token": { "recipient": "recipient-address-here" }}'
```

Replace `"recipient-address-here"` with the actual wallet address you want to send tokens to.

Run the following command to execute the token transfer:

```sh
xiond tx wasm execute $CONTRACT "$SEND_TOKEN" \
  --from $WALLET \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.5 \
  --amount "10factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp" \
  -y \
  --node https://rpc.xion-testnet-1.burnt.com:443 \
  --chain-id xion-testnet-1
```

Where:

* `$CONTRACT`: The deployed contract address.
* `$SEND_TOKEN`: The execution message to send tokens.
* `--amount "10factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp"`: Specifies the token amount and denom being sent.

After executing this transaction, you can run the **get\_balance** query again to verify that the amount was deducted from your account and also check the recipient's address to see if they received the tokens.
