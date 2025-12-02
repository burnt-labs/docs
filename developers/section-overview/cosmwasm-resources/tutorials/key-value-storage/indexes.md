# Indexes

Indexes play a vital role in facilitating the process of iterating through primary keys while providing valuable information. Let's delve into a model that involves multiple tokens within a system, with each token being uniquely owned. In this context, it is essential to establish an association between an owner and a token, enabling seamless querying of tokens based on their respective owners.

```
struct Token {
  pub owner: Addr,
  pub ticker: String
}
```

Tokens are distinguishable through an auto-incremented key, serving as the primary identifier, guaranteeing the uniqueness of each token.

```
(TokenPK) -> Token
```

The owner's index would have the following structure:

```
(owner, TokenPK) -> Token
```

TokenPK is a reference to Token data, and the owner:TokenPK key is a reference to a specific Token. By executing two database queries, one can access the Token data. To obtain all the tokens associated with a specific owner, a prefix range query, as depicted above, can be employed.

```
pub const TOKENS: Map<U8Key, Token> = Map::new("tokens");
// (owner Address, Token PK) -> u8 key
pub const OWNER_INDEX: Map<(&Addr, U8Key), &[u8]> = Map::new("owner_tokenpk");

```

Utilizing the owner information as the key streamlines token access. Nonetheless, it's important to note that any alterations to the state of TOKENS necessitate corresponding updates to the owner field.

This concept aligns with storage-plus indexing.

The previously mentioned solution works, but its efficiency is compromised by the extensive code complexity. This is where storage-plus/IndexedMap comes into the picture. IndexedMap is a storage manager that incorporates built-in indexing capabilities.&#x20;

It encompasses two types of indexes: **Unique Indexes** and **Multi Indexes**.

### &#x20;Unique Indexes

Maintaining the exclusivity of a data field in a database is a typical necessity. UniqueIndex is an indexing utility that aids in accomplishing this functionality.

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Token {
  pub owner: Addr,
  pub ticker: String,
  pub identifier: u8, // <---- unique value
}
// TokenIndexes structs keeps a list of indexers
pub struct TokenIndexes<'a> {
  // token.identifier
  pub identifier: UniqueIndex<'a, U8Key, Token>,
}
// IndexList is just boilerplate code for fetching a struct's indexes
impl<'a> IndexList<Token> for TokenIndexes<'a> {
  fn get_indexes(&'_ self) -> Box<dyn Iterator<Item=&'_ dyn Index<Token>> + '_> {
    let v: Vec<&dyn Index<Token>> = vec![&self.identifier];
    Box::new(v.into_iter())
  }
}
// tokens() is the storage access function.
pub fn tokens<'a>() -> IndexedMap<'a, &'a [u8], Token, TokenIndexes<'a>> {
  let indexes = TokenIndexes {
    identifier: UniqueIndex::new(|d| U8Key::new(d.identifier), "token_identifier"),
  };
  IndexedMap::new(TOKEN_NAMESPACE, indexes)
}

```

Let's go over the code step by step:

```
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Token {
  pub owner: Addr,
  pub ticker: String,
  pub identifier: u8, // <---- unique value
}
```

Within the attributes of a Token, the identifier stands out as a distinct and unique value:

```
// TokenIndexes structs keeps a list of indexers
pub struct TokenIndexes<'a> {
  // token.identifier
  pub identifier: UniqueIndex<'a, U8Key, Token>,
}

```

TokenIndexes is a struct for defining indexes of Token struct:

```
impl<'a> IndexList<Token> for TokenIndexes<'a> {
  fn get_indexes(&'_ self) -> Box<dyn Iterator<Item=&'_ dyn Index<Token>> + '_> {
    let v: Vec<&dyn Index<Token>> = vec![&self.identifier];
    Box::new(v.into_iter())
  }
}
```

IndexList is an interface for building the indexes:

```

pub fn tokens<'a>() -> IndexedMap<'a, &'a [u8], Token, TokenIndexes<'a>> {
  let indexes = TokenIndexes {
    identifier: UniqueIndex::new(|d| U8Key::new(d.identifier), "token_identifier"),
  };
  IndexedMap::new(TOKEN_NAMESPACE, indexes)
}
tokens() is storage function used to build IndexedMap.
    identifier: UniqueIndex::new( | d| U8Key::new(d.identifier), "token_identifier"),

```

The above code is an index builder function. It builds composite keys with the given function, and accepts a key to identify the index bucket.

See test code below:

```
#[test]
fn test_tokens() {
  let mut store = MockStorage::new();
  let owner1 = Addr::unchecked("addr1");
  let ticker1 = "TOKEN1".to_string();
  let token1 = Token {
    owner: owner1.clone(),
    ticker: ticker1,
    identifier: 0,
  };
  let token_id = increment_tokens(store.borrow_mut()).unwrap();
  tokens().save(store.borrow_mut(), &U64Key::from(token_id).joined_key(), &token1).unwrap();
  let ticker2 = "TOKEN2".to_string();
  let token2 = Token {
    owner: owner1.clone(),
    ticker: ticker2,
    identifier: 0,
  };
  let token_id = increment_tokens(store.borrow_mut()).unwrap();
  // identifier clashes, must return error
  tokens().save(store.borrow_mut(), &U64Key::from(token_id).joined_key(), &token1).unwrap();
}

```

The last line will crash with an error:

```
called `Result::unwrap()` on an **Err** value: GenericErr { msg: "Violates unique constraint on index" }
thread 'state::test::test_tokens' panicked at 'called `Result::unwrap()` on an **Err** value: GenericErr { msg: "Violates unique constraint on index" }', src/state.rs:197:90
stack backtrace:

```

### Multi indexes

Multi indexes are used when the structure is indexed by non-unique values. Here is a case from the [cw721 smart contract](https://github.com/CosmWasm/cw-nfts/blob/main/packages/cw721/README.md):

```
pub struct TokenIndexes<'a> {
  // secondary index by owner address
  // the last U64Key is the primary key which is an auto incremented token counter
  pub owner: MultiIndex<'a, (Vec<u8>, Vec<u8>), Token>,
}
// this may become a macro, not important just boilerplate, builds the list of indexes for later use
impl<'a> IndexList<Token> for TokenIndexes<'a> {
  fn get_indexes(&'_ self) -> Box<dyn Iterator<Item=&'_ dyn Index<Token>> + '_> {
    let v: Vec<&dyn Index<Token>> = vec![&self.owner];
    Box::new(v.into_iter())
  }
}
const TOKEN_NAMESPACE: &str = "tokens";
pub fn tokens<'a>() -> IndexedMap<'a, &'a [u8], Token, TokenIndexes<'a>> {
  let indexes = TokenIndexes {
    owner: MultiIndex::new(
      |d, k| (index_string(d.owner.as_str()), k),
      TOKEN_NAMESPACE,
      "tokens__owner",
    )
  };
  IndexedMap::new(TOKEN_NAMESPACE, indexes)
}

```

We observe that the owner index is a MultiIndex, which permits duplicate keys. This is why the primary key is appended as the final element in the multi-index key. As the name suggests, this is an index organized by owner, enabling us to manage and iterate through all the tokens associated with a particular owner.

It's essential to emphasize that the key, along with its components in the case of a composite key, must adhere to the PrimaryKey trait. In this instance, both the 2-tuple (\_, \_) and Vec are compliant with the PrimaryKey requirement.

```
 impl<'a, T: PrimaryKey<'a> + Prefixer<'a>, U: PrimaryKey<'a>> PrimaryKey<'a> for (T, U) {
  type Prefix = T;
  type SubPrefix = ();
  fn key(&self) -> Vec<&[u8]> {
    let mut keys = self.0.key();
    keys.extend(&self.1.key());
    keys
  }
}
 pub fn tokens<'a>() -> IndexedMap<'a, &'a str, TokenInfo, TokenIndexes<'a>> {
  let indexes = TokenIndexes {
    owner: MultiIndex::new(
      |d, k| (Vec::from(d.owner.as_ref()), k),
      "tokens",
      "tokens__owner",
    ),
  };
  IndexedMap::new("tokens", indexes)
}
```

During index creation, we must supply an index function per index.

```
 owner: MultiIndex::new(|d, k| (Vec::from(d.owner.as_ref()), k),
```

The index function plays a crucial role in generating the index key from the value and the primary key (always in Vec format) of the original map. Naturally, this necessitates the presence of the essential elements required for constructing the index key within the value.

In addition to the index function, it is essential to specify both the primary key's namespace and the namespace for the new index.

```
IndexedMap::new("tokens", indexes)
```

In this context, it's essential that the primary key's namespace corresponds to the one utilized during the creation of the index. We supply our TokenIndexes, specified as an IndexList-type parameter, as the second argument. This effectively links the underlying Map with the primary key and the defined indexes.

IndexedMap (along with the other Indexed\* variants) essentially acts as an enhanced wrapper for the Map, offering a range of index functions and designated namespaces for establishing indexes over the original Map data. Moreover, it takes care of executing these index functions seamlessly during data storage, modification, or removal, allowing you to work with indexed data without having to be concerned with implementation intricacies.

Here is an example of how to use indexes in code:

```
#[test]
fn test_tokens() {
  let mut store = MockStorage::new();
  let owner1 = Addr::unchecked("addr1");
  let ticker1 = "TOKEN1".to_string();
  let token1 = Token {
    owner: owner1.clone(),
    ticker: ticker1,
  };
  let ticker2 = "TOKEN2".to_string();
  let token2 = Token {
    owner: owner1.clone(),
    ticker: ticker2,
  };
  let token_id = increment_tokens(store.borrow_mut()).unwrap();
  tokens().save(store.borrow_mut(), &U64Key::from(token_id).joined_key(), &token1).unwrap();
  let token_id = increment_tokens(store.borrow_mut()).unwrap();
  tokens().save(store.borrow_mut(), &U64Key::from(token_id).joined_key(), &token1).unwrap();
  // want to load token using owner1 and ticker1
  let list: Vec<_> = tokens()
    .idx.owner
    .prefix(index_string(owner1.as_str()))
    .range(&store, None, None, Order::Ascending)
    .collect::<StdResult<_>>().unwrap();
  let (_, t) = &list[0];
  assert_eq!(t, &token1);
  assert_eq!(2, list.len());
}

```

### Composite Multi Indexing

Let's delve into the following scenario: we have multiple batches stored based on their numeric batch ID, and these IDs can change. Furthermore, these batches need to be automatically promoted after any modifications. Our goal is to process all the pending batches with a status ranging from "Pending" to "Promoted," depending on various interactions with them. Additionally, each batch comes with an associated expiration time. We are primarily interested in those pending batches that have already expired, as these can be promoted. To accomplish this, we can create an index for the batches, using a composite key that incorporates both the batch status and its expiration timestamp. This composite key allows us to exclude both the already promoted batches and the pending ones that haven't reached their expiration.

To achieve this, we'll construct the index, generate the composite key, and iterate through all the pending batches with an expiration timestamp earlier than the current time.

Here's an example illustrating how to accomplish this using the Batch struct:

```
/// A Batch is a group of members who got voted in together. We need this to
/// calculate moving from *Paid, Pending Voter* to *Voter*
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct Batch {
  /// Timestamp (seconds) when all members are no longer pending
  pub grace_ends_at: u64,
  /// How many must still pay in their escrow before the batch is early authorized
  pub waiting_escrow: u32,
  /// All paid members promoted. We do this once when grace ends or waiting escrow hits 0.
  /// Store this one done so we don't loop through that anymore.
  pub batch_promoted: bool,
  /// List of all members that are part of this batch (look up ESCROWS with these keys)
  pub members: Vec<Addr>,
}
IndexedMap definitions:
// We need a secondary index for batches, such that we can look up batches that have
// not been promoted, ordered by expiration (ascending) from now.
// Index: (U8Key/bool: batch_promoted, U64Key: grace_ends_at) -> U64Key: pk
pub struct BatchIndexes<'a> {
  pub promotion_time: MultiIndex<'a, (U8Key, U64Key, U64Key), Batch>,
}
impl<'a> IndexList<Batch> for BatchIndexes<'a> {
  fn get_indexes(&'_ self) -> Box<dyn Iterator<Item=&'_ dyn Index<Batch>> + '_> {
    let v: Vec<&dyn Index<Batch>> = vec![&self.promotion_time];
    Box::new(v.into_iter())
  }
}
pub fn batches<'a>() -> IndexedMap<'a, U64Key, Batch, BatchIndexes<'a>> {
  let indexes = BatchIndexes {
    promotion_time: MultiIndex::new(
      |b: &Batch, pk: Vec<u8>| {
        let promoted = if b.batch_promoted { 1u8 } else { 0u8 };
        (promoted.into(), b.grace_ends_at.into(), pk.into())
      },
      "batch",
      "batch__promotion",
    ),
  };
  IndexedMap::new("batch", indexes)
}

```

This example is similar to the previous one, above. The only differences are:

The composite key now has three elements: the batch status, the expiration timestamp, and the batch id (which is the primary key for the Batch data). We're using a U64Key for the batch id / pk. This is just for convenience. We could as well have used a plain Vec for it.

Now, here's how to use the indexed data:

```
let batch_map = batches();
// Limit to batches that have not yet been promoted (0), using sub_prefix.
// Iterate which have expired at or less than the current time (now), using a bound.
// These are all eligible for timeout-based promotion
let now = block.time.nanos() / 1_000_000_000;
// as we want to keep the last item (pk) unbounded, we increment time by 1 and use exclusive (below the next tick)
let max_key = (U64Key::from(now + 1), U64Key::from(0)).joined_key();
let bound = Bound::Exclusive(max_key);
let ready = batch_map
              .idx
              .promotion_time
              .sub_prefix(0u8.into())
              .range(storage, None, Some(bound), Order::Ascending)
              .collect::<StdResult<Vec<_ >>>() ?;

```

### A couple of important points to note:

**joined\_key() Function**: The joined\_key() function is utilized to generate the range key. This helpful function takes the (partial) composite key, which consists of the batch expiration timestamp and batch ID, and transforms it into a Vec with the correct format. This Vec is then used to establish a range bound.

**sub\_prefix() Function**: The sub\_prefix() function is employed to adjust the initial element of the composite key, which corresponds to the batch status. This adjustment is necessary because using prefix() in this context, where we have a 3-tuple, would mean fixing the first two elements of the key, which isn't required for our purpose.

The iteration begins from a None state and progresses to the bound key formed from the current timestamp. This approach allows us to effectively list only the pending batches that have already expired but remain unprocessed.



That's the gist of it. Following this process, we can iterate through the results and carry out actions such as changing their status from "Pending" to "Promoted," or any other necessary tasks.

\
\
<br>
