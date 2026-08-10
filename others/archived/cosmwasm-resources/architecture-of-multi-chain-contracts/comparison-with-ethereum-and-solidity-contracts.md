# Comparison With Ethereum and Solidity Contracts

CosmWasm employs a distinctive approach in contrast to Ethereum when it comes to deploying and executing smart contracts.&#x20;

The process involves three phases to establish a functioning smart contract:

* **Upload Code**: In this initial step, you exclusively upload optimized wasm code. Unlike Ethereum smart contracts, this phase doesn't require consideration of state or contract addresses.
* **Instantiate Contract**: During this stage, you create a code reference with an initial state and generate a contract address.
* **Execute Contract**: Once your contract is live, it can accommodate various calls based on your specific contract design.

**Similarities with Ethereum include:**

* Both contract instantiation and execution consume gas.
* Signers have the option to send tokens to the contract alongside the message during both instantiation and execution.

**Notably different from Ethereum:**

* Directly sending tokens to a contract (via SendMsg) does not automatically trigger any contract code in CosmWasm. Contract execution must be explicitly requested. This design choice in CosmWasm helps mitigate potential attack vectors. For instance, in Ethereum, when tokens are transferred to a contract, the contract's fallback function is automatically activated. If this fallback function is not well-implemented or contains security vulnerabilities, it can expose the contract or even the entire system to various forms of attacks, including reentrancy attacks.

## Smart Contract Instantiation

In CosmWasm, the process of uploading a contract's code and instantiating a contract is treated as distinct actions, in contrast to Ethereum's approach. This separation is designed to enable a select set of well-vetted contract archetypes to exist as multiple instances, all sharing the same underlying code while allowing for individualized configuration with different parameters.

A vesting contract, for instance, is a specific type of smart contract that locks up tokens and gradually releases them over a predefined period. By employing different parameters during instantiation, you have the flexibility to create multiple instances of the same vesting contract template, tailoring each instance to specific use cases.

For example, if a vesting contract supports the following parameters:

```
// VestingContract.wasm
{
  "vesting_start_time": "1616678400", // Unix timestamp for vesting start time
  "vesting_duration": "31536000",     // Vesting duration in seconds (1 year)
  "total_tokens": "1000000",          // Total tokens to be vested
  "beneficiary_address": "xion1xyz" // Address of the beneficiary
}
```

By instantiating this vesting contract with distinct parameter values, you have the capability to generate multiple instances, each tailored to accommodate diverse vesting scenarios. For instance:

```
Instance A: Employee A receives 1,000,000 tokens vested over three years.
Instance B: Employee B receives 500,000 tokens vested over two years.
Instance C: Advisor A receives 250,000 tokens vested over one year with a six-month cliff.
```

Utilizing a single vesting contract template and adjusting the parameters during instantiation enables the creation of distinct vesting contracts tailored to individual beneficiaries and specific vesting conditions.

## Avoiding Reentrancy Attacks

\
CosmWasm takes a proactive approach to prevent reentrancy attacks, which is a critical concern given that a significant number of exploits in Ethereum stem from such attacks. To illustrate how reentrancy attacks function in Ethereum smart contracts, consider the following scenario:

During the execution of a function on Contract A, an implicit or explicit call is triggered, transferring control to Contract B, which can now execute its code. Subsequently, Contract B calls back into Contract A once again. With two instances of Contract A running concurrently, meticulous state management becomes imperative before executing any remote contract. Alternatively, stringent gas limits must be enforced in sub-calls. Reentrancy attacks can provoke undefined behavior within Contract A, setting the stage for vulnerabilities and exploits, reminiscent of the Ethereum DAO hack.

CosmWasm takes a distinct approach to entirely mitigate reentrancy attacks by disallowing direct contract-to-contract calls. While it seeks to facilitate contract composition, it avoids inline function calls to potentially malicious code. Instead, CosmWasm permits any contract to return a list of messages to be executed within the same transaction. This means that a contract must conclude its execution before initiating a call to another contract. Should any of the subsequent messages fail, the entire transaction reverts, including any updates to the contract's state. This approach in CosmWasm ensures atomic composition while eliminating the risk of reentrancy attacks.

## Resource Limits

\
Denial of Service (DoS) attacks represent another potential threat vector for smart contracts. For instance, a malicious actor could deploy a contract designed to execute an infinite loop, effectively halting the blockchain, or flood storage capacity with excessive data writes. Fortunately, WebAssembly's inherent sandboxing, with no default access to the underlying operating system, allows us to focus primarily on establishing strict resource limits for smart contracts. It is imperative for all developers to be cognizant of these limitations.

* **Memory Usage**: Upon instantiating a Wasm VM, it is allocated a default of 32MB of RAM. This allocation serves to accommodate the bytecode as well as all memory usage during contract execution, encompassing stack and heap storage. In most cases, this allocation is more than sufficient for typical contracts, but it may pose limitations for exceptionally complex zero-knowledge circuits. Nonetheless, this allocation ensures that contracts have a minimal impact on the overall memory usage of the blockchain.
* **CPU Usage**: The Wasmer Runtime employed by CosmWasm incorporates metering logic within the wasm code. This logic calculates operation prices, charges accordingly, and verifies limits before every jump statement (such as loops or function calls). Consequently, it establishes a deterministic gas price, irrespective of CPU speed or platform disparities. Prior to contract execution, a wasm gas limit is established, based on the remaining Cosmos SDK gas, and gas deductions occur upon contract completion. This approach imposes a strict ceiling on CPU computations, as users must bear the cost for the utilized cycles.
* **Disk Usage**: All disk access in CosmWasm contracts is channeled through reads and writes on the KVStore. The Cosmos SDK already enforces gas payments for KVStore access. Given that all disk interactions within the contracts are implemented through callbacks into the SDK, the associated charges are applied accordingly. In cases where CosmWasm is integrated into an alternate runtime, it becomes essential to ensure appropriate gas charges are imposed for access to disk resources within that runtime as well.

## Insights Gained from Ethereum

To develop resilient smart contract functionalities, it's essential to draw insights from both the achievements and limitations of Ethereum. Therefore, we can examine the most prevalent attack vectors in Ethereum, along with their mitigation strategies, to assess their applicability to Cosmwasm. Many of these attack vectors have been inherently addressed in Cosmwasm's design, while some challenges still exist. Plans are in place to address the remaining issues.

* **Reentrancy**: As previously discussed, CosmWasm mitigates reentrancy attacks by returning messages to execute other contracts within the same atomic operation, but after the contract's execution has completed. This approach, rooted in the actor model, eliminates the possibility of reentrancy attacks, ensuring there is never a volatile state during contract calls.
* **Arithmetic Under/Overflows**: Rust in CosmWasm allows developers to set overflow-checks = true in the Cargo manifest, causing the program to abort if any overflow is detected. There is no option to bypass safe math.
* **Delegate Call**: CosmWasm does not incorporate Delegate Call logic. While modules can be imported, they are linked together at compile time, enabling comprehensive testing of the entire contract as a unified entity. There are no concealed entry points within a contract's logic.
* **Default Visibilities**: Instead of automatically generating entry points for every function/method in the code or assuming public visibility if not specified, developers must explicitly define a list of messages to be handled and dispatch them to the relevant functions. This approach eliminates the risk of accidentally exposing a function.
* **Short Address/Parameter Attack**: This exploit capitalizes on the RLP encoding mechanism and fixed 32-byte stack size, which does not apply to CosmWasm's type-checking JSON parser.
* **Unchecked CALL Return Values**: CosmWasm does not allow direct calls to other contracts; instead, sent messages are dispatched by a router. The router verifies the result of all messages, and if any message in the chain returns an error, the entire transaction is aborted, and state changes are rolled back. This ensures safe execution and rollback of states in case of issues when scheduling calls to other contracts.
* **Uninitialized Storage Pointers**: CosmWasm requires explicit loading of variables from storage, as it does not automatically initialize them. Rust itself does not permit uninitialized variables, emphasizing the explicitness of storage handling.
* **Floating Points and Precision**: Both Solidity and CosmWasm avoid floating-point operations due to potential non-determinism in rounding, which can vary depending on the CPU. In CosmWasm, you can seamlessly import Rust packages and select a suitable one for internal use. For instance, you can utilize "rust\_decimal," a pure Rust Decimal implementation suitable for precise financial calculations, or employ fixed-point decimal math, supporting up to 128-bit numbers, accommodating a wide range of use cases.
* **Tx.Origin Authentication**: CosmWasm refrains from exposing tx.origin, relying solely on the contract or the user directly calling the contract through params.message.signer. This design ensures that only one value needs to be compared, eliminating the possibility of relying on incorrect authentication methods.

<br>

<br>

<br>
