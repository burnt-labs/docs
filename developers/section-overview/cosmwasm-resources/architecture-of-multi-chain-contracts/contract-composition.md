# Contract Composition

With the Actor model governing message dispatch and synchronous queries, we possess all the essential building blocks required to facilitate flexible composition of contracts, whether it involves interactions with other contracts or native modules. In this section, we will elucidate the seamless integration of these components and explore avenues for extending their functionality.

## Terminology

Please keep in mind that in this context, "Contracts" pertain to dynamic CosmWasm code uploaded to a specific blockchain address, whereas "Native Modules" are Cosmos SDK modules, typically written in Go, that are compiled directly into the blockchain binary.

While we endorse the composability of both contract types, it's crucial to scrutinize the integration with "Native Modules" more attentively, as their usage can potentially introduce portability challenges. To mitigate these concerns, we offer abstractions that revolve around "Modules."

## Messages

Both the `init` and `handle` functions have the capability to return an arbitrary number of `CosmosMsg` objects, which will then be re-dispatched within the same transaction. This mechanism ensures atomic success or rollback along with the contract execution. These messages fall into three categories:

* **Contract Messages**: These messages enable calls to a specified contract address with a provided message in serialized form.
* **Module Interfaces**: These interfaces are standardized and can be supported by any blockchain to expose native modules through a portable interface.
* **Custom Messages**: These messages encapsulate chain-specific extensions to message types, allowing calls into custom native modules. While ideally immutable within the same chain over time, they do not guarantee portability across different chains.

## Queries

Contracts have the ability to perform synchronous read-only queries within the surrounding runtime. Similar to messages, these queries can be categorized into three fundamental types:

* **Contract Queries**: These queries involve reaching out to a specified contract address with a provided message in serialized form.
* **Module Interfaces**: These interfaces are standardized and can be implemented by any blockchain to offer access to native modules through a universally portable interface.
* **Custom Queries**: These queries encapsulate chain-specific extensions for querying custom native modules. While they should ideally remain consistent within the same chain over time, they do not offer guarantees of portability across different chains.

For Cross-Contract queries, the contract needs the address of the target contract and a serialized QueryMsg in the specific format of the target contract. In return, it synchronously receives a binary serialized response in the format specified by the target contract. It's the responsibility of the calling contract to comprehend the appropriate formats. To simplify this process, contract-specific type-safe wrappers can be provided, similar to how a straightforward `query_balance` method serves as a wrapper around the query implementation offered by the Trait.

## Modules

To facilitate seamless integrations with native blockchains, we are offering a series of standardized module interfaces that aim to deliver consistent functionality across all CosmWasm chains. The foundational module is the Bank module, which grants access to the underlying native tokens. This encompasses BankMsg::Send for token transfers and BankQuery::Balance along with BankQuery::AllBalances for balance inquiries and token movement.

The second standardized module is Staking, which introduces standardized messages for Delegation, Undelegation, Redelegation, Withdrawal, and querying Validators and Delegations. These interfaces are designed to support a wide range of Proof of Stake (PoS) systems, extending beyond the existing staking module of the Cosmos SDK.

This approach fosters a cohesive design, enabling the development of contracts that can operate seamlessly on two different blockchains, even if they are extensively customized and running on distinct Cosmos SDK versions. However, it's important to note that each module interface must be incorporated into all layers of the stack, potentially causing delays in supporting custom features. Moreover, it's not feasible to offer support for every custom module across all Cosmos SDK-based blockchains.

With this in mind, it is strongly recommended to utilize the module interface whenever feasible and employ custom types as a temporary measure to maintain an agile release cycle.

## Customization

**Portability**:&#x20;

Contracts should be capable of running on two different blockchains, each with varying Golang modules and different versions of the Cosmos SDK. Ideally, this flexibility should extend to different frameworks, such as those running on Substrate. This should be achievable by avoiding Custom messages and checking the optional features being used. Currently, the supported features are Staking, which assumes a PoS system, and iterator, which assumes the ability to perform prefix scans over storage (indicating a Merkle Tree, not a Merkle Trie).

**Immutability**:&#x20;

Contracts are immutable and encode query and message formats in their bytecode. Allowing dispatching of sdk.Msg in native formats (JSON, Amino, or Protobuf) creates a risk that contract functionality could break if the format of native messages changes. This challenge is not theoretical, as major updates of the Cosmos SDK have produced such breaking changes in the past, and these changes cannot be applied to immutable contracts. Hence, the design must provide an immutable API to a potentially mutable runtime, a crucial consideration when developing standard module interfaces.

**Extensibility**:&#x20;

The design should support the addition of new interfaces to contracts and blockchains without necessitating updates to intermediate layers. For instance, if you're constructing a custom blockchain application that imports x/wasm from wasmd and intends to develop contracts calling into your custom superd modules, the ideal scenario is the ability to add these message types to an additional cw-superd library for contract import, along with adding the required callbacks in superd's Go code, all without altering the cosmwasm-std, cosmwasm-vm, go-cosmwasm, or wasmd repositories. Custom variants of CosmosMsg and QueryRequest facilitate such extensibility.

## Usability

Using JSON encoding for CosmWasm messages streamlines the process of exporting JSON schemas for each contract, enabling the automatic generation of client-side codecs.

**Checking for Support:** To assess compatibility, you can leverage feature flags exposed as wasm export functions to define the additional features required by a contract. This empowers the host chain to inspect compatibility before permitting an upload. To implement this, certain "ghost" exports, such as `requires_staking()`, are created. These exports only pass a compatibility check if the runtime also exposes the corresponding features. When initializing `x/wasm.NewKeeper()`, you have the flexibility to specify which features are supported.

**Type-Safe Wrappers:** While querying or invoking other contracts, we forego the type-checks inherent in native module interfaces, necessitating that the caller possesses the requisite knowledge. To enhance usability and maintain type-checking, it proves beneficial to implement "interface" wrappers that a contract can export. These wrappers simplify the process for other contracts to interact with it seamlessly.

### Example:

```
pub struct NameService(CanonicalAddr);

impl NameService {
  pub fn query_name(deps: &Extern, name: &str) -> CanonicalAddr { /* .. */ }
  pub fn register(api: &Api, name: &str) -> CosmosMsg { /* .. */ }
}
```

Instead of solely storing the CanonicalAddr of the other contract in our configuration, an alternative approach would be to store the NameService. This NameService serves as a zero-cost "newtype" built upon the original address, maintaining the same serialization format. However, it brings valuable advantages, offering type-safe utilities for conducting queries against the contract and generating CosmosMsg for registration purposes.

It's important to note that these type-safe wrappers are not bound to a specific contract implementation but rather to the contract's interface. Consequently, we have the flexibility to create a compact library featuring a catalog of standard and widely-used interfaces (such as the ERCxxx specifications), all represented using these "newtypes." Contract creators can import one of these wrappers, simplifying their ability to interact with the contract, regardless of its underlying implementation, as long as it adheres to the specified interface.
