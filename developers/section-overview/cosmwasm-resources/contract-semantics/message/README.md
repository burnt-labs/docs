# Message

Interacting with a CosmWasm smart contract involves the use of messages. These messages are typically documented in a msg.rs file within the contract's codebase.

One of the fundamental messages is the "instantiate" message. This message is responsible for creating a new instance of a CosmWasm smart contract on the blockchain. In the contract's codebase, you'll usually find this message defined as "InstantiateMsg" in the msg.rs file. Subsequently, this message is passed to an "instantiate" function located in the main contract.rs file for further processing.

While the examples provided here are fairly straightforward, it's understandable if you have questions about the specific arguments that can be included in these messages. To gain a deeper understanding, you can refer to the contract's schema folder, which typically contains at least two pertinent files:

* "instantiate\_msg.json" - This file outlines the expected structure and data types for the "instantiate" message.
* "execute\_msg.json" - Within this file, you can find details about the expected structure and data types for each of the messages that the contract can utilize to perform various actions.

\
Contracts with extensive APIs may contain numerous schema files. Delve into these files to locate the specific message or command you are in search of.

In the example contract for nameservice, after the contract has been instantiated, you will discover that there are only two valid messages available:

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
  Register { name: String },
  Transfer { name: String, to: String },
}
```

You can find the context for this code[ here](https://github.com/deus-labs/cw-contracts/blob/main/contracts/nameservice/src/msg.rs#L13).&#x20;

Following that, the ExecuteMsg message is processed within the contract.rs file. Each of the enum values can be managed in the execute function as shown below:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
  deps: DepsMut,
  env: Env,
  info: MessageInfo,
  msg: ExecuteMsg,
) -> Result<Response, ContractError> {
  match msg {
    ExecuteMsg::Register { name } => execute_register(deps, env, info, name),
    ExecuteMsg::Transfer { name, to } => execute_transfer(deps, env, info, name, to),
  }
}
```

You can access the source code for the execute function [here](https://github.com/deus-labs/cw-contracts/blob/main/contracts/nameservice/src/contract.rs#L31).&#x20;
