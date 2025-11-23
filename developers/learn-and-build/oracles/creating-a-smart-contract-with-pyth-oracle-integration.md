# Creating a Smart Contract with Pyth Oracle Integration

Blockchains are **self contained networks** that cannot directly access external data, such as asset prices or market trends from the web. This limitation makes **oracles** essential as they serve as a bridge between **on-chain** smart contracts and **off-chain** real world information.

The **Pyth Network** is a decentralized oracle that provides **real-time, high frequency price feeds** for assets like cryptocurrencies, stocks, and commodities. This guide will walk you through integrating Pyth's price feed data into your smart contracts and also querying the data from a frontend app.

## How Pyth pricing works on XION

Pyth is a **pull (on-demand) oracle**, not a push oracle. That means:

* Pyth publishers update prices [off-chain on Pythnet](https://docs.pyth.network/price-feeds/core/how-pyth-works/cross-chain) about every \~400ms.
* **XION does not automatically receive those updates.**
* A price only gets written on XION **when someone submits an update transaction to the Pyth contract on the Xion chain**. If nobody triggers updates, the on-chain values can look outdated.

So if you query a feed on XION and see a publish time from months ago, it usually **doesn’t mean the feed is broken,** it means no one has recently pushed an update on XION.

## Pyth Contracts

Pyth deploys contracts on supported chains, enabling smart contracts on those networks to access pricing data through its decentralized system. You can find the Pyth contract address for XION's networks [here](https://docs.pyth.network/price-feeds/contract-addresses/cosmwasm).

| Network        | Contract Address                                                |
| -------------- | --------------------------------------------------------------- |
| Xion Testnet 2 | xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl |

## Price Feeds

Pyth provides price feeds for various assets. Each feed has a unique **feed ID**, which is required to query the data. You can find the complete list of available feeds for XION chains [here](https://www.pyth.network/developers/price-feed-ids#cosmwasm-stable).

### Keeping price feeds updated on XION

You have two options, depending on your app:

#### **Option A: Update prices inside your app flow (recommended)**

Whenever your app needs a fresh price (e.g., before a swap, mint, borrow, or liquidation), do this:

1. **Fetch the latest Pyth update data off-chain** from Hermes / Price Service.
   * This update blob is signed and represents the newest price.
2. **Estimate the update fee** from the Pyth contract on XION.
3. **Submit your normal execute transaction**, but include:
   * the update data, and
   * enough fee to pay the Pyth contract for writing the update on chain.
4. **Read the price in the same transaction** (or immediately after).\
   Now your contract is using a current price.

**Key idea:** your app _pulls_ the price update exactly when it needs it, so you pay gas only when the price is used.\
If you’re building a front end, treat “get price” as a 2-step process:

* `get_update_data(feed_ids) → update_blob` (off-chain)
* `execute(update_price_feeds, update_blob) → query_price` (on-chain)

This prevents “stale price” confusion during testing.

#### **Option B: Run a scheduler that pushes prices periodically**

If your app wants prices to stay fresh **even when nobody is using the App** (like a dashboard, keeper bots, or always-on DeFi protocols), run Pyth’s **Price Pusher**:

* It’s an off-chain service that **monitors selected feeds** and **regularly pushes updates** to a chain.
* Anyone can run it. You choose the interval and feeds ([https://docs.pyth.network/price-feeds/core/schedule-price-updates](https://docs.pyth.network/price-feeds/core/schedule-price-updates)). **Here's a** link to [Pyth’s Price Pusher guide](https://docs.pyth.network/price-feeds/core/schedule-price-updates/using-price-pusher).

**When to use this:**

* you need “near-real-time” prices always available on XION
* you don’t want end-users to be the ones paying update gas
* you run protocol bots/keepers anyway

### Fetching Price Data

A variety of queries can be made to the **Pyth contract**, including:

* **PriceFeed**
* **GetUpdateFeeForDenom**
* **GetUpdateFee**
* **GetValidTimePeriod**

For our use case, the key query is **PriceFeed**, as it retrieves the most recent price data for a given asset pair. This query requires only the **feed ID**, which you can find [here](https://www.pyth.network/developers/price-feed-ids#cosmwasm-stable).

### Query Price Feed from Contract

To query a price feed from the Pyth oracle in a your smart contract, you need to send a `WasmQuery::Smart` request to the Pyth contract with the correct `feed_id`. Below is an example:

```rust
use cosmwasm_std::{Deps, StdResult, WasmQuery, QueryRequest, to_binary};
use schemars::JsonSchema;
use serde::{Deserialize, Serialize};

const PYTH_CONTRACT: &str = "xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl";

// Store with 0x prefix (matches Pyth documentation format)
const PRICE_FEED_ID: &str = "0x436ccb0d465f3cb48554bcc8def65ff695341b3ebe0897563d118b9291178d0f";

// Query message must be an enum wrapper, not a plain struct
// Pyth expects: {"price_feed": {"id": "..."}}
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum PythQueryMsg {
    PriceFeed { id: String },
}

// Response structure matching Pyth's actual format
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct PriceFeedResponse {
    pub price_feed: PriceFeed,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct PriceFeed {
    pub id: String,
    pub price: Price,
    pub ema_price: Price,
}

// Price fields are returned as strings by Pyth, not integers
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Price {
    pub price: String,
    pub conf: String,
    pub expo: i32,
    pub publish_time: i64,
}

/// Queries the price feed from the Pyth contract
pub fn query_pyth_price_feed(deps: Deps) -> StdResult<PriceFeedResponse> {
    // Strip 0x prefix for CosmWasm (unlike EVM chains)
    let feed_id = PRICE_FEED_ID.strip_prefix("0x").unwrap_or(PRICE_FEED_ID);

    let query_msg = PythQueryMsg::PriceFeed {
        id: feed_id.to_string(),
    };

    let response: PriceFeedResponse = deps.querier.query(&QueryRequest::Wasm(WasmQuery::Smart {
        contract_addr: PYTH_CONTRACT.to_string(),
        msg: to_binary(&query_msg)?,
    }))?;

    Ok(response)
}

/// Helper function to convert the string price to a decimal value
pub fn get_price_as_f64(price: &Price) -> Option<f64> {
    let price_int: i64 = price.price.parse().ok()?;
    let expo = price.expo as f64;
    Some(price_int as f64 * 10_f64.powf(expo))
}
```

### Query Price Feed from Frontend

Below is JavaScript code to query the price feed from the Pyth contract using `cosmjs`:

```javascript
import { CosmWasmClient } from "@cosmjs/cosmwasm-stargate";

// Constants
const RPC_ENDPOINT = "https://rpc.xion-testnet-2.burnt.com:443";
const PYTH_CONTRACT = "xion1wptw89weav8tnpgxg4fyhcahgk8yy99lka3w308536ktadkvjwxqe463hl";
const PRICE_FEED_ID = "0x436ccb0d465f3cb48554bcc8def65ff695341b3ebe0897563d118b9291178d0f";

// Function to fetch price feed
async function fetchPythPriceFeed() {
    try {
        // Connect to the blockchain
        const client = await CosmWasmClient.connect(RPC_ENDPOINT);

        // Remove "0x" prefix for CosmWasm (unlike EVM chains)
        const feedIdWithoutPrefix = PRICE_FEED_ID.startsWith("0x")
            ? PRICE_FEED_ID.slice(2)
            : PRICE_FEED_ID;

        // Use lowercase "price_feed" not "PriceFeed"
        const queryMsg = {
            price_feed: { id: feedIdWithoutPrefix },
        };

        console.log("Querying with:", queryMsg);

        // Execute query
        const response = await client.queryContractSmart(PYTH_CONTRACT, queryMsg);

        console.log("Success! Pyth Price Feed Response:", response);
        return response;
    } catch (error: any) {
        if (error.message?.includes("PriceFeedNotFound")) {
            console.error("❌ Error: Price feed not found on testnet contract");
            console.error("   This is expected - the testnet has no price feeds loaded yet.");
            console.error("   Your query format is CORRECT, the data just doesn't exist.");
            console.error("❌ Error fetching price feed:", error.message);
        } else {
            console.error("❌ Error fetching price feed:", error.message);
        }
    }
}

// Call the function
fetchPythPriceFeed();
```

**`queryContractSmart(PYTH_CONTRACT, queryMsg)`**: Sends a query to the Pyth contract to fetch the price feed.
