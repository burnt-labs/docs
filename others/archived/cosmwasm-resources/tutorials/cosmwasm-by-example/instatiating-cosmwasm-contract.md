# Instantiating CosmWasm Contract

In this section, we elucidate the process of initializing a smart contract. This is achieved by presenting the structure of the "InstantiateMsg" passed during contract creation and the "Instantiate" function that is executed when the contract is initiated.

### Explanation

The InstantiateMsg serves as the initialization message, responsible for handling the variables provided when the contract is instantiated.

This function can be located within the msg.rs file.

```
/// Variables for contract instantiation.
/// Exclude variables susceptible to replay attacks or secret data.

pub struct InstantiateMsg {
    pub sent_message: String,
}
```

{% hint style="info" %}
Ensure that sensitive data vulnerable to replay attacks is not included.
{% endhint %}

### Instantiate

Upon the creation of the contract, the instantiate function configures the initial state of the smart contract, performs essential validations, and can act in a manner similar to an execute message.

You can find this function in the contract.rs file:

```
pub fn instantiate(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {
    // Initialize contract's state.
    let state = State {
        global_var: msg.sent_message,
    };
    // Save state to the blockchain.
    STATE.save(deps.storage, &state)?;
    Ok(Response::new().add_attribute("instantiated", "true"))
}

```

## Example

To instantiate contracts using CosmWasm, you should generate the following files: lib.rs, contract.rs, msg.rs, error.rs, state.rs, and helpers.rs.

### lib.rs

```
pub mod contract;
mod error;
pub mod helpers;
pub mod msg;
pub mod state;
pub use crate::error::ContractError;
```

### contract.rs

```
#[cfg(not(feature = "library"))]
use cosmwasm_std::entry_point;
use cosmwasm_std::{Binary, Deps, DepsMut, Env, MessageInfo, Response, StdResult, to_binary};

// use cw2::set_contract_version;
use crate::error::ContractError;
use crate::msg::{ExecuteMsg, InstantiateMsg, QueryMsg};
use crate::state::{State, STATE};
/*

// version info for migration info
const CONTRACT_NAME: &str = "crates.io:instantiation";
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");
*/

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: InstantiateMsg,
) -> Result<Response, ContractError> {

    /* Code is executed at contract creation and accepts an `InstantiateMsg`
    Used to set initial (default) state variables of the smart contract
    Can perform checks and acts like an execute message.
     */

    let state = State {
        global_var: msg.sent_message,
    };

    // Pushes the data to blockchain storage
    STATE.save(deps.storage, &state)?;
    Ok(Response::new()
    .add_attribute("instantiated", "true"))
}

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
    _deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    _msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    unimplemented!()
}

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
    match msg {
        QueryMsg::GetMessage {} => to_binary(&query::message(deps)?),
    }
}
pub mod query {
    use crate::msg::GetMessageResponse;
    use super::*;
    pub fn message(deps: Deps) -> StdResult<GetMessageResponse> {
        let state = STATE.load(deps.storage)?;
        Ok(GetMessageResponse { message: state.global_var })
    }
}
#[cfg(test)]
mod tests {
    use cosmwasm_std::{testing::{mock_dependencies, mock_env, mock_info}, Addr, from_binary};
    use crate::msg::GetMessageResponse;
    use super::*;
    #[test]
    fn instantiate_test() {
        let mut deps = mock_dependencies();
        let env = mock_env();
        let msg = InstantiateMsg { sent_message: "Hey!".to_string()};
        let info = mock_info("creator", &[]);
        instantiate(deps.as_mut(), env.clone(), info, msg).unwrap();

        // Query data from blockchain
        let res = query(deps.as_ref(), env, QueryMsg::GetMessage {  }).unwrap();
        let message: GetMessageResponse = from_binary(&res).unwrap();
        assert_eq!("Hey!".to_string(), message.message);

    }   
}
```

### msg.rs

```
use cosmwasm_schema::{cw_serde, QueryResponses};
#[cw_serde]

/* variables to be passed to contract at instantiation.
secret variables or any variable that is open to a replay attack should not be 
part of the InstantiateMsg 
*/

pub struct InstantiateMsg {
    pub sent_message: String
}

#[cw_serde]
pub enum ExecuteMsg {}
#[cw_serde]
#[derive(QueryResponses)]
pub enum QueryMsg {
    #[returns(GetMessageResponse)]
    GetMessage {}
}

#[cw_serde]
pub struct GetMessageResponse {
    pub message: String
}

```

### error.rs

```
use cosmwasm_std::StdError;
use thiserror::Error;
#[derive(Error, Debug)]
pub enum ContractError {
    #[error("{0}")]
    Std(#[from] StdError),
    #[error("Unauthorized")]
    Unauthorized {},

    // Add any other custom errors you like here.
    // Look at https://docs.rs/thiserror/1.0.21/thiserror/ for details.

}
```

### state.rs

```
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};
use cw_storage_plus::Item;
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
pub struct State {
    pub global_var: String,
}
pub const STATE: Item<State> = Item::new("state");
```

### helpers.rs

```
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};
use cosmwasm_std::{to_binary, Addr, CosmosMsg, StdResult, WasmMsg};
use crate::msg::ExecuteMsg;

/// CwTemplateContract is a wrapper around Addr that provides a lot of helpers
/// for working with this.

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, Eq, JsonSchema)]
pub struct CwTemplateContract(pub Addr);
impl CwTemplateContract {
    pub fn addr(&self) -> Addr {
        self.0.clone()
    }
    pub fn call<T: Into<ExecuteMsg>>(&self, msg: T) -> StdResult<CosmosMsg> {
        let msg = to_binary(&msg.into())?;
        Ok(WasmMsg::Execute {
            contract_addr: self.addr().into(),
            msg,
            funds: vec![],
        }
        .into())
    }
}
```



{% hint style="info" %}
Attribution: The code and examples are sourced from "[CosmWasm by Example](https://www.cosmwasmbyexample.com/examples)." You can review the code on [GitHub](https://github.com/athena-consulting/cosmwasm-by-example/tree/main/instantiation),
{% endhint %}
