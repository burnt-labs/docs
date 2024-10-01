# Result and Option

In the Rust programming language, Result and Option are enum types that encompass values within their respective variants:

```
enum Option<T> {
  Some(T),
  // existence
  None, // non-existence
}
enum Result<T, E> {
  Ok(T),
  // success
  Err(E), // failure
}
```

## Result

Result is an enum type, Result\<T, E>, where both T and E are generics, symbolizing success and failure. Result types find common use in entry points and handlers:

**Ok(T)**: A Result container signifying success, containing the value T.

**Err(E)**: A Result container indicating failure, containing the value E.

Numerous contract entry points are of the type Result\<Response, ContractError. In this context, Response represents the Right or Success branch, while ContractError corresponds to the Left or failure case.&#x20;

For instance, in the case of CW20-base, the execute function is typed as Result\<Response, ContractError. Specifically, for the function that corresponds to ExecuteMsg::Transfer, namely execute\_transfer, this demonstrates how the result is returned.

```
pub fn execute_transfer(
  deps: DepsMut,
  _env: Env,
  info: MessageInfo,
  recipient: String,
  amount: Uint128,
) -> Result<Response, ContractError> {
  if amount == Uint128::zero() {
    return Err(ContractError::InvalidZeroAmount {});
  }
  let rcpt_addr = deps.api.addr_validate(&recipient)?;
  BALANCES.update(
    deps.storage,
    &info.sender,
    |balance: Option<Uint128>| -> StdResult<_> {
      Ok(balance.unwrap_or_default().checked_sub(amount)?)
    },
  )?;
  BALANCES.update(
    deps.storage,
    &rcpt_addr,
    |balance: Option<Uint128>| -> StdResult<_> { Ok(balance.unwrap_or_default() + amount) },
  )?;
  let res = Response::new()
    .add_attribute("action", "transfer")
    .add_attribute("from", info.sender)
    .add_attribute("to", recipient)
    .add_attribute("amount", amount);
  Ok(res)
}

```

## StdResult&#x20;

### The Significance of StdResult

It is equally essential to remain vigilant about StdResult, given its common application in query handlers and the associated functions.

### Illustration from the Nameservice Contract

For instance, in the nameservice contract, you can readily identify the utilization of StdResult. This construct operates in a manner akin to Result but does not include a predefined error branch.

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> StdResult<Binary> {
  match msg {
    QueryMsg::ResolveRecord { name } => query_resolver(deps, env, name),
    QueryMsg::Config {} => to_binary(&config_read(deps.storage).load()?),
  }
}
```

Let's examine the implementation of the query\_resolver:

```
fn query_resolver(deps: Deps, _env: Env, name: String) -> StdResult<Binary> {
  let key = name.as_bytes();
  let address = match resolver_read(deps.storage).may_load(key)? {
    Some(record) => Some(String::from(&record.owner)),
    None => None,
  };
  let resp = ResolveRecordResponse { address };
  to_binary(&resp)
}
```

Precisely matching or unwrapping your container types is crucial to effectively access and manipulate the enclosed values.

## Option

### Handling Absence in Rust

In the Rust programming language, there is no concept of nil or null, a departure from many other widely used programming languages. Instead, Rust employs the Option type to capture the idea of presence or absence within a container type.

### The Option Enum Type

Option is an enum type that features two variants:

**Some()**: It wraps an inner value, which can be accessed using .unwrap().

**None**: This variant signifies the absence of a value and is frequently employed as a default or placeholder value when a value is either not yet known or cannot be determined.

#### Example Usage

The following code snippet provides an example of how Rust's Option is utilized to handle optional values of purchase\_price and transfer\_price

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
  pub purchase_price: Option<Coin>,
  pub transfer_price: Option<Coin>,
}
```

When dealing with instances of the InstantiateMsg mentioned earlier, there will be either an outcome or nothing. To address this situation, it is customary to utilize the match operator for pattern matching the two possible cases.

```
let address = match resolver_read(deps.storage).may_load(key)? {
  Some(record) => Some(String::from( & record.owner)),
  None => None,
};
```

If utilizing None to signify an error state, the conventional recommendation is to raise an error rather than dealing with the None value.

\
\


\
\
