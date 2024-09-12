# Submessages

Messages serve as a means to interact with both SDK modules and CW smart contracts. It's important to note that messages are executed in a 'set-and-forget' manner, meaning you won't receive immediate feedback about the success or failure of the call.

Nevertheless, obtaining the result of your call can be highly advantageous in the following scenarios:

* When you're instantiating a new contract and need to acquire the contract address.
* When you're executing an action and need to confirm that the result was successful. For instance, you might want to ensure that a specific token amount has been correctly transferred to your contract.
* When you wish to handle an error from your cross-contract call instead of rolling back the transaction.

To retrieve the result of the message sent from your smart contract, you will need to initiate a submessage. For further insights into the semantics of submessages and the order of submessage execution, you can refer to additional documentation located [here](https://github.com/CosmWasm/cosmwasm/blob/main/SEMANTICS.md#submessages).

## Creating a Submessage

A submessage encapsulates a CosmosMsg within a SubMsg structure:

```
pub struct SubMsg<T> {
    pub id: u64,                // reply_id that will be used to handle the reply
    pub msg: CosmosMsg<T>,      // message to be sent
    pub gas_limit: Option<u64>, // gas limit for the submessage
    pub reply_on: ReplyOn,      // a flag to determine when the reply should be sent
}
```

The source code for the SubMsg struct can be located [here](https://github.com/CosmWasm/cosmwasm/blob/main/packages/std/src/results/submessages.rs).&#x20;

Now, let's examine an illustration of instantiating a cw20 token using a submessage:

```
const INSTANTIATE_REPLY_ID = 1u64;
// Creating a message to create a new cw20 token
let instantiate_tx = WasmMsg::Instantiate {
    admin: None,
    code_id: msg.cw20_code_id,
    msg: to_binary(&Cw20InstantiateMsg {
        name: "new token".to_string(),
        symbol: "nToken".to_string(),
        decimals: 6,
        initial_balances: vec![],
        mint: Some(MinterResponse {
            minter: env.contract.address.to_string(),
            cap: None,
        }),
    })?,
    funds: vec![],
    label: "".to_string(),
};
// Creating a submessage that wraps the message above
let submessage = SubMsg::reply_on_success(instantiate_tx.into(), INSTANTIATE_REPLY_ID);
// Creating a response with the submessage
let response = Response::new().add_submessage(submessage);
```

## Reply Strategies&#x20;

Submessages provide four distinct response options for the recipient contract to offer:

```
pub enum ReplyOn {
    /// Always perform a callback after SubMsg is processed
    Always,
    /// Only callback if SubMsg returned an error, no callback on success case
    Error,
    /// Only callback if SubMsg was successful, no callback on error case
    Success,
    /// Never make a callback - this is like the original CosmosMsg semantics
    Never,
}
```

Previously, we generated the submessage using the SubMsg::reply\_on\_success shortcut. Nevertheless, it's worth noting that we can also create a submessage while explicitly specifying the reply strategy.

```
let submessage = SubMsg {
    gas_limit: None,
    id: INSTANTIATE_REPLY_ID,
    reply_on: ReplyOn::Success,
    msg: instantiate_tx.into()
}
```

## Handling a Reply

To process the response from the other contract, the calling contract must incorporate a new entry point. Below, you'll find two examples illustrating how to manage these responses:

#### Instantiating a new contract:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn reply(deps: DepsMut, _env: Env, msg: Reply) -> StdResult<Response> {
    match msg.id {
        INSTANTIATE_REPLY_ID => handle_instantiate_reply(deps, msg),
        id => Err(StdError::generic_err(format!("Unknown reply id: {}", id))),
    }
}
fn handle_instantiate_reply(deps: DepsMut, msg: Reply) -> StdResult<Response> {
    // Handle the msg data and save the contract address
    // See: https://github.com/CosmWasm/cw-plus/blob/main/packages/utils/src/parse_reply.rs
    let res = parse_reply_instantiate_data(msg)?;
    // Save res.contract_address
    Ok(Response::new())
}
```

#### Handling a reply from a token transfer:

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn reply(deps: DepsMut, msg: Reply) -> StdResult<Response> {
    match msg.id {
        CW20_TRANSFER_REPLY_ID => handle_transfer_reply(deps, msg),
        id => Err(StdError::generic_err(format!("Unknown reply id: {}", id))),
    }
}
fn handle_transfer_reply(deps: DepsMut, msg: Reply) -> StdResult<Response> {
    // Handle the msg data and save the contract address
    // See: https://github.com/CosmWasm/cw-plus/blob/main/packages/utils/src/parse_reply.rs
    let data = msg.result.into_result().map_err(StdError::generic_err);
    // Search for the transfer event
    // If there are multiple transfers, you will need to find the right event to handle
    let transfer_event = msg
        .events
        .iter()
        .find(|e| {
            e.attributes
                .iter()
                .any(|attr| attr.key == "action" && attr.value == "transfer")
        })
        .ok_or_else(|| StdError::generic_err(format!("unable to find transfer action"))?;
    // Do whatever you want with the attributes in the transfer event
    // Reference to the full event: https://github.com/CosmWasm/cw-plus/blob/main/contracts/cw20-base/src/contract.rs#L239-L244
    Ok(Response::new())
}
```

### Transmitting Context Between Contracts

In order to prevent reentrancy attacks, CosmWasm prohibits the storage of context within the contract memory. To propagate state between contracts, there are two available methods:

* All events produced by the submessage can be accessed from the Reply message.
* Temporary state can be stored using cw\_storage\_plus::Item and subsequently loaded into the reply handler.
