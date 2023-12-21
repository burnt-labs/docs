# Math

In this section, you will find examples of a CosmWasm contract that demonstrates basic mathematical operations, including addition, subtraction, multiplication, division, modulo, and exponentiation.&#x20;

The "execute" function showcased here accepts an enum of "ExecuteMsg," which encapsulates all the contract functions and matches them with the function the user is invoking.

```
pub fn execute(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    match msg {
    ExecuteMsg::Operations { a, b } => execute_operations(deps,a,b),
}
}
```

The "execute" function receives an enum of "ExecuteMsg," which serves as a container for all the contract functions and matches them with the user's requested function. Subsequently, it invokes the "execute\_operations" function:

```
pub fn execute_operations(deps: DepsMut, a: u128, b: u128) -> Result<Response, ContractError> {
        // Checking if numbers are not zero
        if a == 0 && b == 0 {
            return Err(ContractError::CanNotBeZero());
        }
        
        // Addition
        let addition_result = a + b;
        
        // Subtraction
        let subtraction_result = a - b;
        
        // Multiplication
        let multiplication_result = a * b;
        
        // Division
        let division_result = a / b;
        
        // Modulo
        let modulo_result = a % b;
        
        // Exponentiation
        let exponent: u32 = 3;
        let exponentiation_result: u128 = a.pow(exponent);
        
        // Create the response
        let response = OperationsResponse {
            addition_result,
            subtraction_result,
            multiplication_result,
            division_result,
            modulo_result,
            exponentiation_result,
        };
        
        // Fetching the state
        RESULT.load(deps.storage).unwrap();
        
        // Update the state
        RESULT.save(deps.storage, &response).unwrap();
        
        let res = Response::new().add_attributes(vec![
            ("action", "operations"),
            ("a", &a.to_string()),
            ("b", &b.to_string()),
            ("addition_res", &addition_result.to_string()),
            ("substraction_res", &subtraction_result.to_string()),
            ("multiplicationn_res", &multiplication_result.to_string()),
            ("division_res", &division_result.to_string()),
            ("modulo_res", &modulo_result.to_string()),
            ("exponential_res", &exponentiation_result.to_string()),
        ]);
        
        Ok(res)
    }


```

The execute\_operations function accepts two parameters, 'a' and 'b,' for mathematical operations and stores the resulting value in the RESULT global state variable, which is located within the state.rs file:

```
pub const RESULT: Item<OperationsResponse> = Item::new("result");
```

You can utilize the following query endpoint to retrieve the results of mathematical operations:

```
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> Result<QueryResponse, StdError> {
    match msg {
        QueryMsg::GetResponse {} => get_response(deps),
    }
}
```

The query function above accepts an enum of QueryMsg, which encapsulates all the contract query functions and aligns them with the user's requested function, specifically 'GetResponse' in our case. Subsequently, it invokes the get\_response function:

```
pub fn get_response(deps: Deps) -> Result<QueryResponse, StdError> {
    let result = RESULT.load(deps.storage)?;
    to_binary(&result)
}
```

The get\_response function mentioned above returns the result of the mathematical operation.

## Example

To employ mathematical operations within CosmWasm, you should generate the following files: lib.rs, contract.rs, msg.rs, error.rs, and state.rs.

## lib.rs

```
pub mod contract;
mod error;
pub mod msg;
pub mod state;
pub use crate::error::ContractError;
```

## contract.rs

```
#[cfg(not(feature = "library"))]
use cosmwasm_std::entry_point;
use cosmwasm_std::{
    to_binary, Deps, DepsMut, Env, MessageInfo, QueryResponse, Response, StdError,
};
use cw2::set_contract_version;

use crate::error::ContractError;
use crate::msg::{ExecuteMsg, InstantiateMsg, QueryMsg};
use crate::state::{OperationsResponse, RESULT};

// version info for migration info

const CONTRACT_NAME: &str = "crates.io:cosmwasm-math";
const CONTRACT_VERSION: &str = env!("CARGO_PKG_VERSION");

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn instantiate(

    /* Deps allows to access:
    1. Read/Write Storage Access
    2. General Blockchain APIs
    3. The Querier to the blockchain (raw data queries) */
    
    deps: DepsMut,
    
    /* env gives access to global variables which represent environment information.
    For exaample:
    - Block Time/Height
    - contract address
    - Transaction Info */
    
    _env: Env,
    
    /* Message Info gives access to information used for authorization.
    1. Funds sent with the message.
    2. The message sender (signer). */
    
    _info: MessageInfo,
    _msg: InstantiateMsg,
) -> Result<Response, ContractError> {

    /* Instantiating the state that will be stored to the blockchain */
    
    let operation_response = OperationsResponse {
        addition_result: 0,
        subtraction_result: 0,
        multiplication_result: 0,
        division_result: 0,
        modulo_result: 0,
        exponentiation_result: 0,
    };
    
    set_contract_version(deps.storage, CONTRACT_NAME, CONTRACT_VERSION).unwrap();
    
    // Save the stete in deps.storage which creates a storage for contract data on the blockchain.
    RESULT.save(deps.storage, &operation_response).unwrap();
    Ok(Response::new().add_attribute("method", "instantiate"))
}

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn execute(
    deps: DepsMut,
    _env: Env,
    _info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> {
    match msg {
        ExecuteMsg::Operations { a, b } => execute::execute_operations(deps, a, b),
    }
}
pub mod execute {
    use super::*;
    pub fn execute_operations(deps: DepsMut, a: u128, b: u128) -> Result<Response, ContractError> {
       
         // Checking if numbers are not zero
         
        if a == 0 && b == 0 {
            return Err(ContractError::CanNotBeZero());
        }
        
        // Addition
        let addition_result = a + b;
        // Subtraction
        let subtraction_result = a - b;
        // Multiplication
        let multiplication_result = a * b;
        // Division
        let division_result = a / b;
        // Modulo
        let modulo_result = a % b;
        // Exponentiation
        let exponent: u32 = 3;
        let exponentiation_result: u128 = a.pow(exponent);
        // Create the response
        
        let response = OperationsResponse {
            addition_result,
            subtraction_result,
            multiplication_result,
            division_result,
            modulo_result,
            exponentiation_result,
        };
        
        // Fetching the state
        
        RESULT.load(deps.storage).unwrap();
        
        // Update the state
        
        RESULT.save(deps.storage, &response).unwrap();
        let res = Response::new().add_attributes(vec![
            ("action", "operations"),
            ("a", &a.to_string()),
            ("b", &b.to_string()),
            ("addition_res", &addition_result.to_string()),
            ("substraction_res", &subtraction_result.to_string()),
            ("multiplicationn_res", &multiplication_result.to_string()),
            ("division_res", &division_result.to_string()),
            ("modulo_res", &modulo_result.to_string()),
            ("exponential_res", &exponentiation_result.to_string()),
        ]);
        Ok(res)
    }
}

#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> Result<QueryResponse, StdError> {
    match msg {
        QueryMsg::GetResponse {} => query::get_response(deps),
    }
}
pub mod query {
    use super::*;
    pub fn get_response(deps: Deps) -> Result<QueryResponse, StdError> {
        let result = RESULT.load(deps.storage)?;
        to_binary(&result)
    }
}
#[cfg(test)]
mod tests {
    use super::*;
    use cosmwasm_std::testing::{mock_dependencies, mock_env, mock_info};
    use cosmwasm_std::{coins, from_binary};
    #[test]
    fn proper_initialization() {
        let mut deps = mock_dependencies();
        let msg = InstantiateMsg {};
        let info = mock_info("creator", &coins(1000, "earth"));
        
        // we can just call .unwrap() to assert this was a success
        
        let res = instantiate(deps.as_mut(), mock_env(), info, msg).unwrap();
        assert_eq!(0, res.messages.len());
        
        // it worked, let's query the state
        
        let res = query(deps.as_ref(), mock_env(), QueryMsg::GetResponse {}).unwrap();
        let value: OperationsResponse = from_binary(&res).unwrap();
        assert_eq!(0, value.addition_result);
        assert_eq!(0, value.subtraction_result);
        assert_eq!(0, value.multiplication_result);
        assert_eq!(0, value.division_result);
        assert_eq!(0, value.modulo_result);
        assert_eq!(0, value.exponentiation_result);
    }
    
    #[test]
    fn increment() {
        let mut deps = mock_dependencies();
        let msg = InstantiateMsg {};
        let info = mock_info("creator", &coins(2, "token"));
        let _res = instantiate(deps.as_mut(), mock_env(), info, msg).unwrap();
        
        // testing operation function
        
        let info = mock_info("anyone", &coins(2, "token"));
        let msg = ExecuteMsg::Operations { a: 5, b: 5 };
        let _res = execute(deps.as_mut(), mock_env(), info, msg).unwrap();
        
        // should get basic math operation for 5 and 5
        
        let res = query(deps.as_ref(), mock_env(), QueryMsg::GetResponse {  }).unwrap();
        let value: OperationsResponse = from_binary(&res).unwrap();
        assert_eq!(10, value.addition_result);
        assert_eq!(0, value.subtraction_result);
        assert_eq!(25, value.multiplication_result);
        assert_eq!(1, value.division_result);
        assert_eq!(0, value.modulo_result);
        assert_eq!(125, value.exponentiation_result);
    }
}

```

## msg.rs

```
 use cosmwasm_schema::{cw_serde, QueryResponses};
#[cw_serde]
pub struct InstantiateMsg {}
#[cw_serde]
pub enum ExecuteMsg {
    Operations { a: u128, b: u128 },
}
#[cw_serde]
#[derive(QueryResponses)]
pub enum QueryMsg {
    #[returns(u128)]
    GetResponse {},
}
```

## error.rs

```
use thiserror::Error;
#[derive(Error, Debug)]
pub enum ContractError {
    #[error("Numbers can not be zero")]
    CanNotBeZero(),
}
```

## state.rs

```
use cosmwasm_schema::cw_serde;
use cw_storage_plus::Item;
#[cw_serde]
pub struct OperationsResponse {
    pub addition_result: u128,
    pub subtraction_result: u128,
    pub multiplication_result: u128,
    pub division_result: u128,
    pub modulo_result: u128,
    pub exponentiation_result: u128,
}

// Mapping of result of two numbers

pub const RESULT: Item<OperationsResponse> = Item::new("result");

```



{% hint style="info" %}
Attribution: The code and examples are sourced from "[CosmWasm by Example](https://www.cosmwasmbyexample.com/examples)." You can review the code on [GitHub](https://github.com/athena-consulting/cosmwasm-by-example/tree/main/cosmwasm-math).
{% endhint %}
