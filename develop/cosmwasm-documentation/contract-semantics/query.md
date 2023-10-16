# Query

### **Understanding Querying**

Querying is a process employed to extract data from a database or access state information. The available query messages are typically located in either msg.rs or query.rs, depending on the structure chosen by the contract author.

### **Performing Queries**

Queries can be executed using an external client, such as an API or through the CLI, or within a contract. For further insights into this process, refer to the section on Querying contract state.

### **Querying in Read-Only Mode**

Queries interact with a contract's data store exclusively in a read-only mode. They can retrieve data and conduct additional processing as necessary. However, it's important to note that there is a gas limit associated with query operations.

### **Managing Queries**

Queries are represented as entries within the [QueryMsg](https://github.com/deus-labs/cw-contracts/blob/main/contracts/nameservice/src/msg.rs#L20) enum and are handled within the contract's Query function.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
  // ResolveAddress returns the current address that the name resolves to
  ResolveRecord { name: String },
  Config {},
}
```

Subsequently, the contract proceeds to handle this within the query function.

```
#[cfg_attr(not(feature = "library"), entry_point)]
pub fn query(deps: Deps, env: Env, msg: QueryMsg) -> StdResult<Binary> {
  match msg {
    QueryMsg::ResolveRecord { name } => query_resolver(deps, env, name),
    QueryMsg::Config {} => to_binary(&config_read(deps.storage).load()?),
  }
}
```

In this context, query\_resolver serves as just another function, while config\_read acts as a helper function that encapsulates data store access.
