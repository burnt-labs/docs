# Creating a Smart Contract with Pyth Oracle Integration

Blockchains are **self contained networks** that cannot directly access external data, such as asset prices or market trends from the web. This limitation makes **oracles** essential as they serve as a bridge between **on-chain** smart contracts and **off-chain** real world information.

The **Pyth Network** is a decentralized oracle that provides **real-time, high frequency price feeds** for assets like cryptocurrencies, stocks, and commodities. This guide will walk you through integrating Pyth's price feed data into your smart contracts and also querying the data from a frontend dapp.



## Pyth Contracts

Pyth deploys contracts on supported chains, enabling smart contracts on those networks to access pricing data through its decentralized system. You can find the Pyth contract address for XION's networks [here](https://docs.pyth.network/price-feeds/contract-addresses/cosmwasm).

| Network        | Contract Address                                                |
| -------------- | --------------------------------------------------------------- |
| Xion Testnet 2 | xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl |



## Price Feeds

Pyth provides price feeds for various assets. Each feed has a unique **feed ID**, which is required to query the data. You can find the complete list of available feeds for XION chains [here](https://www.pyth.network/developers/price-feed-ids#cosmwasm-stable).



## Fetching Price Data

A variety of queries can be made to the **Pyth contract**, including:

* **PriceFeed**
* **GetUpdateFeeForDenom**
* **GetUpdateFee**
* **GetValidTimePeriod**

For our use case, the key query is **PriceFeed**, as it retrieves the most recent price data for a given asset pair. This query requires only the **feed ID**, which you can find [here](https://www.pyth.network/developers/price-feed-ids#cosmwasm-stable).



### Query Price Feed from Contract

To query the price feed from the Pyth oracle contract in a CosmWasm smart contract, you need to send a `WasmQuery::Smart` request to the Pyth contract with the correct `feed_id`. Below is an example implementation:

```rust
use cosmwasm_std::{Deps, StdResult, Binary, WasmQuery, QueryRequest, to_binary};
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};

const PYTH_CONTRACT: &str = "xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl";
const PRICE_FEED_ID: &str = "0x436ccb0d465f3cb48554bcc8def65ff695341b3ebe0897563d118b9291178d0f";

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct PriceFeedQueryMsg {
    pub id: String,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct PriceFeedResponse {
    pub price_feed: PriceFeed,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct PriceFeed {
    pub price: i64,         // price in integer format
    pub conf: u64,          // confidence interval
    pub expo: i32,          // exponent for scaling
    pub publish_time: i64,  // timestamp of the price
}

/// Queries the price feed from the Pyth contract
pub fn query_pyth_price_feed(deps: Deps) -> StdResult<PriceFeedResponse> {
    let query_msg = PriceFeedQueryMsg {
        id: PRICE_FEED_ID.to_string(),
    };

    let response: PriceFeedResponse = deps.querier.query(&QueryRequest::Wasm(WasmQuery::Smart {
        contract_addr: PYTH_CONTRACT.to_string(),
        msg: to_binary(&query_msg)?,
    }))?;

    Ok(response)
}

```



### Query Price Feed from Frontend

Below is the JavaScript code to query the price feed from the Pyth contract using `cosmjs`:

```javascript
import { CosmWasmClient } from "@cosmjs/cosmwasm-stargate";

// Constants
const RPC_ENDPOINT = "https://rpc.xion.network"; // Replace with the correct RPC
const PYTH_CONTRACT = "xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl";
const PRICE_FEED_ID = "0x436ccb0d465f3cb48554bcc8def65ff695341b3ebe0897563d118b9291178d0f";

// Function to fetch price feed
async function fetchPythPriceFeed() {
    try {
        // Connect to the blockchain
        const client = await CosmWasmClient.connect(RPC_ENDPOINT);

        // Query message for the Pyth contract
        const queryMsg = {
            PriceFeed: { id: PRICE_FEED_ID },
        };

        // Execute query
        const response = await client.queryContractSmart(PYTH_CONTRACT, queryMsg);

        console.log("Pyth Price Feed Response:", response);
        return response;
    } catch (error) {
        console.error("Error fetching price feed:", error);
    }
}

// Call the function
fetchPythPriceFeed();
```

**`queryContractSmart(PYTH_CONTRACT, queryMsg)`**: Sends a query to the Pyth contract to fetch the price feed.
