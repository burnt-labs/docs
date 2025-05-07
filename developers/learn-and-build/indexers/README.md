---
description: The Key to Efficient Blockchain Data Retrieval
---

# Indexers

Indexers are specialized services that process and organize blockchain data, converting raw on-chain information into structured, easily searchable datasets. They enable developers to retrieve specific data points efficiently without scanning the entire blockchain, enhancing performance and simplifying data access.

From a high level you can look at Indexers as middleware between the blockchain and your application, providing several critical functions:

* **Data Extraction**: They extract raw data from the blockchain by monitoring blocks, transactions, and events.
* **Data Transformation**: They process and organize this data into structured formats optimized for queries.
* **Data Storage**: They maintain databases that allow for efficient searching and filtering of blockchain data.
* **API Access**: They expose this processed data through developer-friendly APIs.



## Why use an Indexer?

1. **Performance**: Applications can query indexed data much faster than directly querying the blockchain, leading to smoother user experiences.
2. **Reduced Chain Load**: By offloading read operations to indexers, your application reduces the load on the blockchain network.
3. **Complex Queries**: Perform complex data filtering and aggregation that would be impractical directly on-chain.



## Indexing CW721 NFTs: A Practical Example

When developing dApps on XION, direct blockchain queries might suffice initially but as your dApps grow in complexity, the need for efficient data retrieval becomes more apparent. This is particularly true when working with CW721 NFTs.



### **The NFT Ownership Challenge**

Suppose you need to display all NFTs owned by a specific user in multiple Collections. Standard CW721 contract methods provide:

* **`NumTokens`**`:` Retrieves the total number of NFTs in a collection.
* **`OwnerOf`**`:` Fetches the owner of a specific token ID.
* **`Tokens`**`:` Lists all token\_ids that belong to a given owner, with pagination.
* **`AllTokens:`** Lists all token\_ids controlled by the contract.
* **`NftInfo`**`:`This returns metadata about one particular token.
* **`AllNftInfo`**`:` Returns the result of both `NftInfo` and `OwnerOf` for a token.

Without an indexer, fetching ownership details requires multiple blockchain queries of each collection and iterating through each token info individually. This can lead to performance bottlenecks and a sluggish user experience.



### Example of a Complex Query: **"Retrieve All NFTs Owned by a User in the Last 30 Days Across Multiple Collections"**

This query requires fetching all NFTs owned by a specific owner across multiple NFT collections, but only those acquired in the past 30 days. Doing this directly on-chain would be extremely inefficient and require multiple queries to gather, filter, and sort the data. An indexer simplifies this by maintaining a structured database of ownership changes, allowing for a single optimized query.



### **How Many Calls Would Be Required If Queried Directly on the Blockchain?**

If an indexer were not available, querying this information manually would involve the following steps:

1. **Fetch All CW721 Collections**
   * Call the registry or manually input known NFT contracts.
   * **1 query per chain supporting multiple CW721 contracts** (or a manually maintained list).
2. **Query Transfers for Each Collection Over the Last 30 Days**
   * Use CosmWasm event queries to find all transfer events.
   * If not, iterate through every block for the last 30 days and extract relevant events.
   * **Potentially thousands of queries if no event indexing is available**.
3. **Filter Events for the Specific Wallet**
   * Each event contains `from` and `to` fields.
   * **Processing step (not a separate query but requires parsing)**.
4. **Fetch Metadata for Each Token Acquired in the Last 30 Days**
   * Call `NftInfo` for each token to retrieve metadata (name, image, attributes).
   * **1 query per NFT** (potentially dozens, hundreds or thousands).
5. **Resolve Metadata Links (IPFS, Arweave, etc.)**
   * Fetch JSON metadata from IPFS or other storage solution.
   * **1 external HTTP request per NFT**.
6. **Sort and Paginate Data**
   * If needed, apply filters in the frontend.
   * **Processing step (not a separate query, but costly in terms of UX and performance).**

#### **Estimated Calls Without an Indexer**

* **50 NFT collections** → **50 queries**
* **Transfers over 30 days** → **Potentially 1,500-3,000 queries** (assuming 30–60 transfers per collection)
* **Fetching metadata** → **500+ queries** (assuming 10 NFTs acquired per collection)
* **IPFS requests** → **500+ external calls**

💡 **Total Queries: 2,050 - 3,500+ (depending on number of NFTs and collections).**



### **How an Indexer Solves This Problem**

An indexer would pre-process and store ownership transfer events and metadata in an optimized database. This allows for a single query like:

```
GET /api/nfts?owner=xion12345...&from_date=2025-01-28&to_date=2025-02-28&limit=50&offset=0
```

💡 **Total Queries with an Indexer: 1 API Call**

* The indexer would fetch ownership records, filter by the date range, include metadata, and return structured results instantly.

This approach drastically reduces load on the blockchain, improves application performance, and enables real-time, user-friendly data retrieval.
