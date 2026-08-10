# Integrate with Smart Contracts

For seamless integration with other CosmWasm contracts, it's essential to grasp the concept of entry points and their utilization. Entry points in a smart contract serve as the means through which interactions occur.

In CosmWasm smart contracts, three fundamental entry points are defined: instantiate, execute, and query.

* **instantiate**: This method is invoked during the contract's deployment on the network. Its primary purpose is to set up and initialize the contract's state.
* **execute**: This method gets called when a transaction is sent to the contract. Its role is to modify and update the contract's state based on the transaction's input.
* **query**: This method is used exclusively for reading data from the contract's state. It does not make any changes to the state itself.

When developing a smart contract that requires interaction with another contract, it's essential to thoroughly understand the functionality of these entry points and manage the interaction within these methods. Consequently, you'll need to acquaint yourself with the parameters accepted by each method and the values they return.

For instance, if the other contract features an execute method that takes a **Transfer** message as input, it's imperative to be well-versed in the specific fields and data that the **Transfer** message expects. This knowledge is crucial for seamless and error-free integration between the two contracts.

In CosmWasm, inter-contract calls are enabled through the use of the **CosmosMsg::Wasm(WasmMsg::Execute{})** message type. This feature allows your contract to specify a series of messages it intends to dispatch to other contracts during execution. These messages will be passed back to the runtime and executed once your contract's execution is successfully completed.

The **WasmMsg::Execute** type accepts the following parameters:

* **contract\_addr:** The address of the contract you intend to invoke.
* **msg:** The message you want to transmit to the contract. This message must adhere to the contract's message schema and be formatted as a JSON object.
* **send:** The coins you wish to transfer to the contract as part of the call.

## Making Calls to the Other Contract's Entry Points

To engage with the execute method of another contract, you can employ the **CosmosMsg::Wasm(WasmMsg::Execute{})** message type. Here's an illustrative example of how to achieve this:

```rust
// Construct the Transfer message for the other contract
let transfer_msg = to_binary(&Transfer {
    recipient: deps.api.addr_humanize(&recipient_raw)?,
    amount,
})?;
// Construct the WasmMsg
let msg = WasmMsg::Execute {
    contract_addr: other_contract_addr.to_string(),
    msg: transfer_msg,
    send: vec![],
};
// Return the WasmMsg to be executed by the runtime
Ok(Response::new()
    .add_attribute("action", "call_other_contract")
    .add_message(msg))
```

In this instance, **Transfer** serves as one of the entry points in the other contract. Please note that the above illustration is solely for explanatory purposes.

It's crucial to bear in mind that the **WasmMsg::Execute message** will only execute after the successful completion of your contract's execution. In the event of a failure in your contract's execution, the **WasmMsg::Execute message** will not be executed.

##
