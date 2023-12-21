# Contract Semantics

The purpose of this document is to provide a clear understanding of how a CosmWasm contract engages with its surrounding context. There are primarily two categories of actions involved: mutating actions, which are equipped with DepsMut and have the capability to alter the blockchain's state, and query actions, which execute on a single node with restricted read-only access to the data.

## Execution

The upcoming section will delve into the workings of the execute call. It's crucial to emphasize that these same principles are applicable to other mutating actions like instantiate, migrate, sudo, and more.

## **Understanding the Cosmos SDK and Transaction Processing**

Before delving into the details of CosmWasm, it is essential to explore the semantics governed by the blockchain framework it integrates with – the Cosmos SDK. This framework is built upon the Tendermint BFT Consensus Engine. To gain a comprehensive understanding, let's break down the transaction processing both before they enter CosmWasm and after they exit.

**Pre-Execution Processing**

In the initial stage, the Tendermint engine strives to achieve a 2/3+ consensus on a list of transactions earmarked for inclusion in the next block. This is accomplished without actually executing the transactions. They undergo a minimal pre-filtering by the Cosmos SDK module, verifying their validity in terms of format, gas fees adequacy, and being signed by an account with sufficient funds to cover the fees. Consequently, some transactions that result in errors may still find their way into a block.

**Block Commitment**

Once a block is committed, typically occurring every 5 seconds or so, the transactions are processed sequentially by the Cosmos SDK. Each transaction yields either a result or an error, accompanied by event logs, all of which are recorded in the TxResults section of the ensuing block. The AppHash, Merkle proof, or blockchain state generated after executing the block is also included in the next block.

**Transaction Handling by Cosmos SDK's BaseApp**

Cosmos SDK's BaseApp handles each transaction within an isolated context. Initially, it verifies all signatures and deducts the applicable gas fees. It establishes a "Gas Meter" to restrict execution to the amount of gas covered by the fees. Subsequently, it creates an isolated context for transaction execution. This design allows the code to read the current chain state after the last transaction's completion, but any write operations are confined to a cache that can be either committed or rolled back in case of an error.

**Handling of Multiple Messages in a Transaction**

A transaction may consist of multiple messages, with each message being executed sequentially under the same context and gas limit. If all messages succeed, the context is committed to the underlying blockchain state, and the results of all messages are stored in the TxResult. However, if one message encounters an error, all subsequent messages are bypassed, and any state changes are reverted. This mechanism ensures the crucial property of atomicity.

This understanding of the Cosmos SDK and transaction processing provides a solid foundation for comprehending how CosmWasm contracts interact within this ecosystem.

Consider the following, suppose both Aria and Toby jointly authorize a transaction comprising two messages. In this transaction, Aria initiates a payment of 1,000 ATOM to Toby, while Toby initiates a payment of 50 ETH to Aria. However, if Toby lacks the necessary funds in his account to cover his payment, not only will Toby's payment be canceled, but Aria's payment will also be rolled back. This behavior is akin to how a conventional database transaction functions.

Within the Cosmos ecosystem, the x/wasm module stands as a custom component of the Cosmos SDK. It specializes in processing specific messages, leveraging them to upload, instantiate, and execute smart contracts. Specifically, it accepts a properly signed MsgExecuteContract and directs it to Keeper.Execute. Here, the module loads the pertinent smart contract and invokes the execute function on it.

It's worth noting that this method can yield one of two outcomes: either success, accompanied by relevant data and events, or an error. In the event of an error, the entire transaction within the block will be reversed. It is within this context that our contract handles the execute call.

### Basic execution

When implementing a contract, we provide the following entry point:

```
pub fn execute(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: ExecuteMsg,
) -> Result<Response, ContractError> { }
```

When utilizing DepsMut, this function gains the capability to both read from and write to the underlying Storage. Additionally, it can utilize the Api for address validation and perform queries on the state of other contracts or native modules. Upon completion, it provides one of two outcomes: either Ok(Response) or Err(ContractError). Now, let's explore the subsequent steps:

In the event it returns Err, this error is converted into a string representation (err.to\_string()), and this string is subsequently returned to the SDK module. All alterations to the state are undone, and x/wasm forwards this error message, which generally results in the termination of the transaction and the transmission of the same error message to the external caller.

If it returns Ok, the Response object is then parsed and processed. Let's delve into the various components of this process:

```
pub struct Response<T = Empty>
where
    T: Clone + fmt::Debug + PartialEq + JsonSchema,
{
    /// Optional list of "subcalls" to make. These will be executed in order
    /// (and this contract's subcall_response entry point invoked)
    /// *before* any of the "fire and forget" messages get executed.
    pub submessages: Vec<SubMsg<T>>,
    /// After any submessages are processed, these are all dispatched in the host blockchain.
    /// If they all succeed, then the transaction is committed. If any fail, then the transaction
    /// and any local contract state changes are reverted.
    pub messages: Vec<CosmosMsg<T>>,
    /// The attributes that will be emitted as part of a "wasm" event
    pub attributes: Vec<Attribute>,
    pub data: Option<Binary>,
}
```

Within the Cosmos SDK, a transaction provides users with a set of events, accompanied by an optional "data result." This result is hashed and incorporated into the hash of the next block, ensuring its verifiability and capacity to convey crucial state information. However, it's worth noting that client applications typically place greater emphasis on events. The result is also commonly utilized for transmitting data between contracts or modules within the SDK. It's important to mention that the ResultHash specifically encompasses the Code (non-zero values indicate an error) and the Result (data) stemming from the transaction. While events and logs can be accessed through queries, it's essential to note that no light-client proofs are available for these components.

If the contract sets data, this data will be returned in the Result field. Additionally, there is a list of {key, value} pairs known as attributes, which will be appended to a default event.

To the client, the final result takes the following format:

```
{
  "type": "wasm",
  "attributes": [
    { "key": "contract_addr", "value": "cosmos1234567890qwerty" },
    { "key": "custom-key-1", "value": "custom-value-1" },
    { "key": "custom-key-2", "value": "custom-value-2" }
  ]
}
```

## Dispatching Messages

Now, let's proceed to discuss the messages field. While some contracts operate exclusively within their own scope, like a CW20 contract that solely manages balance adjustments during transfers, many contracts have the need to transfer tokens (native or CW20) or invoke other contracts for more intricate operations. This is where messages play a pivotal role. We provide a CosmosMsg, which serves as a serializable representation of any external call that a contract can initiate.

For instance, when the stargate feature flag is enabled, it takes the following form:

```
pub enum CosmosMsg<T = Empty>
where
    T: Clone + fmt::Debug + PartialEq + JsonSchema,
{
    Bank(BankMsg),
    /// This can be defined by each blockchain as a custom extension
    Custom(T),
    Staking(StakingMsg),
    Distribution(DistributionMsg),
    Stargate {
        type_url: String,
        value: Binary,
    },
    Ibc(IbcMsg),
    Wasm(WasmMsg),
}
```

