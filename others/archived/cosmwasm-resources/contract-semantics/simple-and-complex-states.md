# Simple and Complex States

### Simple State

The state serves as the repository for storing and accessing data within a smart contract. Conceptually, it operates in a manner akin to the database interaction layer found in conventional applications.

A basic approach to managing state entails the storage of a single entity. For instance, in the case of the cw20 contract, the TokenInfo is established and recorded during the contract's initialization phase.

Initially, a TokenInfo type is defined in state.rs:

```
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
#[serde(rename_all = "snake_case")]
pub struct TokenInfo {
  pub name: String,
  pub symbol: String,
  pub decimals: u8,
  pub total_supply: Uint128,
  pub mint: Option<MinterData>,
}
```

Then the storage is initialized:

```
pub const TOKEN_INFO: Item<TokenInfo> = Item::new("token_info");
```

Within the contract, the instantiate function demonstrates the process of saving data to this:

```
let data = TokenInfo {
  name: msg.name,
  symbol: msg.symbol,
  decimals: msg.decimals,
  total_supply,
  mint,
};
TOKEN_INFO.save(deps.storage, & data) ?;
```

### Complex State

In more intricate solutions, there might be a requirement to store supplementary information. One approach to accomplish this is by serializing comprehensive JSON data structures, which enables data retrieval using key-value pairs.

In the context of CW20, the association of addresses with their CW20 balances is accomplished using a Map data structure

```
pub const BALANCES: Map<&Addr, Uint128> = Map::new("balance");
```

You can locate the code for this right [here.](https://github.com/CosmWasm/cw-plus/blob/main/contracts/cw20-base/src/state.rs#L35)

Here's an illustrative example of how to work with the value within the BALANCES map ([seen here](https://github.com/CosmWasm/cw-plus/blob/main/contracts/cw20-base/src/contract.rs#L250-L258)), as demonstrated in the subsequent code snippet:

```
let rcpt_addr = deps.api.addr_validate(&recipient)?;
BALANCES.update(
   deps.storage,
   &info.sender,
   |balance: Option<Uint128>| -> StdResult<_> {
      Ok(balance.unwrap_or_default().checked_sub(amount)?)
   },
)?;
```

There's a lot of complexity here, so let's dissect it step by step:

1. **deps.storage** is an input provided, originating from the contract context. Think of **deps** as similar to the **ctx** you might have encountered in the Cosmos SDK.
2. **\&rcpt\_addr** is a borrowed reference to the validated recipient address. It's been verified and confirmed as valid; otherwise, the "let" statement would have triggered an error. This reference corresponds to the key within the key/value pair.
3. The third statement consists of an anonymous function (lambda) that returns a **StdResult** and carries out a computation based on the current value of **balance**. In this context, **balance** signifies the value within the key/value pair, while **\&rcpt\_addr** serves as the key.

