# Events

## **Return Types in Entry Point Functions**

The typical return type for entry point functions is Result\<Response, ContractError, where Response acts as a container for Events in the Cosmos SDK.

### **Usage of Response Type**

In the context of a contract's entry point, like instantiate or execute, Response should be returned as the successful outcome. It can be declared as mutable and modified within the function's body. However, a more prevalent approach is to construct it at the end and return it if all computations have been successful. In the forthcoming examples, Response is wrapped by Ok since it is returned as part of a function yielding the Result type, with Response signifying the Right or success branch.

### **Query Entry Point Exception**

The Query entry point is an exception to this pattern, as it returns StdResult in accordance with the Cosmos SDK interface.

For a deeper understanding of the Response struct, you can explore the source code [here](https://github.com/CosmWasm/cosmwasm/blob/main/packages/std/src/results/response.rs#L65).

## **Basic Usage of Response**

The most straightforward use of Response involves:

```
Ok(Response::default ())
```

This is a common occurrence in instantiate functions when there is no message to be returned to the client. Nevertheless, in the majority of cases when dealing with the execute function, it is expected that a Response should be returned, like so.&#x20;

```
let res = Response::new()
.add_attribute("action", "transfer")
.add_attribute("from", info.sender)
.add_attribute("to", recipient)
.add_attribute("amount", amount);
Ok(res)
```

**In-Depth Analysis of the Code Snippet**

This code snippet involves several additional steps, so let's delve deeper into its workings.

_To explore the source code for this example, you can access it by clicking_ [_here._](https://github.com/CosmWasm/cw-plus/blob/main/contracts/cw20-base/src/contract.rs#L239)

**Steps in the Code**

1. A new Response is generated.
2. Several key/value pairs are added to this Response.
3. The Response is returned, encapsulated within a Result type using Ok.

**Command-Line Interface Observations**

When you invoke your contract through the command-line interface (CLI), you'll notice these steps logged as part of the "raw\_log" response, alongside other SDK events.

**Utilizing add\_event**

Instead of solely appending attributes, the add\_event function can be utilized to include an unencapsulated event that can be interacted with by other clients or contracts.

