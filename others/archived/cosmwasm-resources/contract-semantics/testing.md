# Testing

Over time, testing emerges as an essential element within the Smart Contract development process. These tests play a vital role in facilitating the seamless integration of modifications into the contract's codebase, preventing any disturbances to other components. Typically, a robust collection of contracts includes a thorough suite of tests categorized into two main types: Unit Testing and Integration Testing.

For unit testing, click here.

## Conducting Integration Testing Using cw-multi-test

The cw-multi-test package, available in the cw-plus repository, offers an intriguing method for testing your smart contracts without the necessity of complete deployment onto a testnet. Prior to the introduction of the multi-test package, the usual process involved establishing a pipeline to deploy contracts on a designated chain (like a testnet or local network), conducting tests, and subsequently, when feasible, terminating or self-destructing those contracts.

## Fundamental Concepts of cw-multi-test

Before you can simulate a blockchain environment in Rust and conduct tests involving interactions between contracts and banks, there are several fundamental concepts you should grasp.

In this section, we will adopt a step-by-step methodology to compose a test utilizing cw-multi-test while elucidating significant concepts throughout the process.

To kick off, we require a sample contract, such as the [cw-template](https://github.com/CosmWasm/cw-template/blob/main/src/contract.rs). This contract serves as a basic template and features two functions: Increment and Reset.

Our initial step involves the creation of a new test file, incorporating the following imports:

```
use cosmwasm_std::testing::{mock_env, MockApi, MockQuerier, MockStorage, MOCK_CONTRACT_ADDR};
use cw_multi_test::{App, BankKeeper, Contract, ContractWrapper};
```

The previously mentioned imports offer us a diverse array of tools for crafting a test. Among these imports, the initial one of significance is App, as it functions as the simulated blockchain environment within which our tests will be executed.

## **Main Entry Points: App and Querier**

### **App: The Heart of the System**

At the core of the system lies the App, which serves as the gateway to your blockchain application. This integral component keeps tabs on critical details such as the block height and time, allowing you to manipulate them to simulate multiple blocks. You can employ the app.update\_block(next\_block) method to advance the timestamp by 5 seconds and the block height by 1, effectively mimicking the creation of a new block. Additionally, you have the freedom to craft custom mutators to progress these values further.

The App provides the App.execute entry point, enabling the execution of any CosmosMsg message, wrapping it into an atomic transaction. A successful execution triggers state commitment and yields data along with a list of events. In case of an error, it returns an Err(String). For your convenience, a set of helper methods tied to the Executor trait generates CosmosMsg messages, simplifying the API. These methods include instantiate\_contract, execute\_contract, and send\_tokens, each executing a single CosmosMsg message atomically, as if initiated by a user. If you wish to run multiple messages together and revert all state changes if any of them fail, you can make use of the execute\_multi method.

### **Querier: Streamlining Blockchain Queries**

Another vital entry point within the App is the Querier interface it implements. By employing App.wrap(), you can obtain a QuerierWrapper, which grants access to a range of user-friendly APIs for querying the blockchain. These APIs encompass functions like all\_balances and query\_wasm\_smart, simplifying your ability to interact with the Storage, execute contracts, banks, and queries. Moreover, you can effortlessly update the current BlockInfo with a concise and straightforward API. Behind the scenes, the system handles all messages returned from contracts, manages token transfers within the "bank," and invokes other contracts seamlessly.

To craft an App for utilization in your test code,  proceed with the following:

```
fn mock_app() -> App {
    let env = mock_env();
    let api = Box::new(MockApi::default());
    let bank = BankKeeper::new();
    App::new(api, env.block, bank, Box::new(MockStorage::new()))
}
```

### Mocking Contracts

In the realm of multi-testing, the practice of mocking your contracts stands as a foundational principle, albeit one that presents significant challenges in the testing process. Initially, it's imperative that any contract under evaluation must either be mocked or wrapped. With the assistance of cw-multi-test, a ContractWrapper facilitates the encapsulation of the essential elements of your contract, including instantiation, execution, and queries, enabling deployment to a mocked network.

Mocking all your contracts and subsequently conducting tests on each can be approached in a scripting style. However, to ensure maintainability and efficiency, it's advisable to define all your wrapped contracts as functions. This approach promotes reusability and enhances the overall testing process

```
use crate::contract::{execute, instantiate, query, reply};
pub fn contract_stablecoin_exchanger() -> Box<dyn Contract<Empty>>{
    let contract = ContractWrapper::new_with_empty(
        execute,
        instantiate,
        query,
    ).with_reply(reply);
    Box::new(contract)
}
```

## **Working with Complex Examples**

### _Understanding the Components_

The example provided above may appear complex, but let's swiftly dissect it. In this breakdown, we commence by importing crucial functions, including execute, instantiate, Query, and Reply, all of which come into play during contract runtime. These functions set the stage for creating our wrapper, a pivotal element in our testing process.

**Information to Consider**

{% hint style="info" %}
**Information to Consider**

_"To Reply or Not to Reply: That Is the Question"_

The need for with\_reply in your ContractWrapper depends on your contract's structure. If your contract lacks an implemented Reply function, it may not be necessary.
{% endhint %}

**Following the Mocking Process**

After successfully mocking a contract, the subsequent steps include storing the code and then configuring a contract using the code object. Notably, this process mirrors the deployment procedure for a testnet or mainnet chain. In unit tests, you interact with a mocked\_env, leveraging mock\_dependencies and supplying mock\_info as part of the process.

## **Storing and Instantiating a Contract**

### Preparing for Instantiation

To successfully instantiate a contract within a cw-multi-test environment, it must initially undergo a storing process. After storage, the contract becomes ready for instantiation, utilizing its corresponding code\_id.

```
let contract_code_id = router.store_code(contract_stablecoin_exchanger());
```

### Instantiating from the new code object

```
let mocked_contract_addr = router
        .instantiate_contract(contract_code_id, owner.clone(), &msg, &[], "super-contract", None)
        .unwrap();
```

## **Handling Mocked Contracts and Potential Errors**

### Dealing with Common Errors

During your testing process, you might encounter error messages such as:

1. "No ContractData"
2. "Contract \<contract> does not exist"

These errors often indicate that a crucial element is missing in your mocking setup. In the cw-multi-test environment, any component that your contract interacts with, even simple utility contracts, should be properly mocked. This applies to services that your contract interacts with, even if your immediate intention is not to test them.

### **Addressing Common Causes**

Examine your contract closely to identify instances where you're passing dummy contract addresses, which is a common root cause of these errors. If you do find such cases, the following steps are necessary:

1. Mock it out using the method described earlier.
2. Instantiate it using the method mentioned previously.
3. Capture its address.
4. Replace the dummy address with the captured one.

{% hint style="info" %}
_**Important Note**_

_Remember that valid addresses must consist of lowercase alphanumeric characters. For instance, "owner" is valid, whereas "OWNER" is not._
{% endhint %}

### **Bringing Everything Together**

```
use cosmwasm_std::testing::{mock_env, MockApi, MockQuerier, MockStorage, MOCK_CONTRACT_ADDR};
use cw_multi_test::{App, BankKeeper, Contract, ContractWrapper};
use crate::contract::{execute, instantiate, query, reply};
use crate::msg::{InstantiateMsg, QueryMsg}
fn mock_app() -> App {
    let env = mock_env();
    let api = Box::new(MockApi::default());
    let bank = BankKeeper::new();
    App::new(api, env.block, bank, Box::new(MockStorage::new()))
}
pub fn contract_counter() -> Box<dyn Contract<Empty>>{
    let contract = ContractWrapper::new_with_empty(
        execute,
        instantiate,
        query,
    );
    Box::new(contract)
}
pub fn counter_instantiate_msg(count: Uint128) -> InstantiateMsg {
    InstantiateMsg {
        count: count
    }
}
#[test]
fn counter_contract_multi_test() {
    // Create the owner account
    let owner = Addr::unchecked("owner");
    let mut router = mock_app();
    let counter_contract_code_id = router.store_code(contract_counter());
    // Setup the counter contract with an initial count of zero
    let init_msg = InstantiateMsg {
        count: Uint128::zero()
    }
    // Instantiate the counter contract using its newly stored code id
    let mocked_contract_addr = router
        .instantiate_contract(counter_contract_code_id, owner.clone(), &init_msg, &[], "counter", None)
        .unwrap();
    // We can now start executing actions on the contract and querying it as needed
    let msg = ExecuteMsg::Increment {}
    // Increment the counter by executing the prepared msg above on the previously setup contract
    let _ = router.execute_contract(
            owner.clone(),
            mocked_contract_addr.clone(),
            &msg,
            &[],
        )
        .unwrap();
    // Query the contract to verify the counter was incremented
    let config_msg =  QueryMsg::Count{};
    let count_response: CountResponse = router
        .wrap()
        .query_wasm_smart(mocked_contract_addr.clone(), &config_msg)
        .unwrap();
    asserteq!(count_response.count, 1)
    // Now let's reset the counter with the other ExecuteMsg
    let msg = ExecuteMsg::Reset {}
    let _ = router.execute_contract(
            owner.clone(),
            mocked_contract_addr.clone(),
            &msg,
            &[],
        )
        .unwrap();
    // And again use the available contract query to verify the result
    // Query the contract to verify the counter was incremented
    let config_msg =  QueryMsg::Count{};
    let count_response: CountResponse = router
        .wrap()
        .query_wasm_smart(mocked_contract_addr.clone(), &config_msg)
        .unwrap();
    asserteq!(count_response.count, 0)
}
```

## **Simulating External Contracts**

#### Understanding the Scope of Mocking

If you've reviewed the previous section, you likely have a grasp of the effort involved in mocking your own contracts. However, as you proceed with mocking and testing, you might encounter a challenge when your contracts interact with external services like Terraswap, Anchor, or other IBC-related services. Rest assured, it's a surmountable issue.

**Addressing External Services**

You might initially attempt to mock one of these services using the same method outlined earlier, only to realize that you lack access to their contract code. Contract code is essential for importing functions like execute, instantiate, and Query. In the case of protocols, their Rust packages typically don't include contract code; they provide what's necessary for interaction, such as messages and certain helper functions.

**The Path Forward**

Despite these challenges, there's still a path forward. You can make progress by crafting a simplified mock of the external service you're engaging with. The process parallels that of mocking your contracts, but you'll need to implement all the required functionality. The task is eased by the ability to create a more compact ExecuteMsg containing only the functions you intend to use or a mock Query handler featuring only the queries required. Below, you'll find an example of a mock for third-party contract:

```
pub fn contract_ping_pong_mock() -> Box<dyn Contract<Empty>> {
    let contract = ContractWrapper::new(
        |deps, _, info, msg: MockExecuteMsg| -> StdResult<Response> {
            match msg {
                MockExecuteMsg::Receive(Cw20ReceiveMsg {
                    sender: _,
                    amount: _,
                    msg,
                }) => {
                    let received: PingMsg = from_binary(&msg)?;
                    Ok(Response::new()
                        .add_attribute("action", "pong")
                        .set_data(to_binary(&received.payload)?))
                }
                }})}
        |_, _, msg: MockQueryMsg| -> StdResult<Binary> {
            match msg {
                MockQueryMsg::Pair {} => Ok(to_binary(&mock_pair_info())?),
```

**Tailoring Your Mocked Contracts to Fit Your Needs**

In the process of defining your custom mocked contracts, you enjoy a considerable degree of flexibility. You can conveniently skip the inclusion of dependencies, environmental variables, and information variables that go unused, simply by using an underscore (\_). Furthermore, you hold the power to furnish any desired responses for specific ExecuteMsg or Query requests. The real challenge lies in effectively mocking these diverse services.

<br>
