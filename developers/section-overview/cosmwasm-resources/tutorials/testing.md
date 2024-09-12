# Testing

This section provides insights into coding and contract testing practices. It covers various aspects related to cosmwasm smart contracts and offers valuable best practices and tips for effective testing:

#### 1. Testing Query Functions

Learn how to write and execute unit tests for cosmwasm smart contract query functions. Best practices in this area involve testing query functions with a range of parameters to validate their responses. This approach ensures that users can seamlessly interact with the smart contract as intended.

#### 2. Testing Error Handling and Edge Cases

Discover the importance of testing error handling scenarios and edge cases within your contract. This testing ensures that your smart contract behaves correctly when faced with unexpected situations or unusual inputs, thereby enhancing its robustness and reliability.

#### 3. Testing with Custom Mock Dependencies

Explore the concept of creating custom mock dependencies for your tests. These dependencies simulate various scenarios and conditions that may arise during the execution of your smart contract. By doing so, you can verify your contract's behavior under diverse circumstances, ensuring its adaptability and effectiveness.

\
For comprehensive guidance on conducting unit testing for cosmwasm smart contracts, refer to the following tutorial linked [here](https://vimeo.com/457705991).&#x20;

By this stage, your code should be successfully compiled, even though you may not have yet confirmed its functionality. While it's possible to deploy the code to the blockchain after every change, this approach can be time-consuming and inefficient. It's also essential to maintain the contract's integrity and ensure thorough testing for future modifications.

```
#[cfg(test)]
mod tests {
  use super::*;
  use cosmwasm_std::testing::{mock_dependencies, mock_env, mock_info, MOCK_CONTRACT_ADDR};
  use cosmwasm_std::{attr, coins, CosmosMsg};
```

You can choose whether to house your tests and code within the same file or separate files. An example can be found [here](https://github.com/deus-labs/cw-contracts/blob/main/contracts/simple-option/src/contract.rs).

### Test Initialization

For each test, it is important to mock specific variables, such as block time and state. Writing a function for easy setup can make this process more manageable

```
#[test]
fn proper_initialization() {
  let mut deps = mock_dependencies(&[]);
  let msg = InitMsg {
    counter_offer: coins(40, "ETH"),
    expires: 100_000,
  };
  let info = mock_info("creator", &coins(1, "BTC"));
  
  // we can just call .unwrap() to assert this was a success
  
  let res = init(deps.as_mut(), mock_env(), info, msg).unwrap();
  assert_eq!(0, res.messages.len());
  
  // it worked, let's query the state
  
  let res = query_config(deps.as_ref()).unwrap();
  assert_eq!(100_000, res.expires);
  assert_eq!("creator", res.owner.as_str());
  assert_eq!("creator", res.creator.as_str());
  assert_eq!(coins(1, "BTC"), res.collateral);
  assert_eq!(coins(40, "ETH"), res.counter_offer);
}

```

Excellent, you've established a foundational test environment initializer. This one is relatively straightforward, allowing you to pass variables to the function and implement diverse adjustments. For additional customization options, consider exploring [cosmwasm-plus](https://github.com/CosmWasm/cw-plus).

### Mocking Dependencies, Environment, and Message Information

We should enhance three key mocking tools:

```
/// All external requirements that can be injected for unit tests.
/// It sets the given balance for the contract itself, nothing else

pub fn mock_dependencies(
  contract_balance: &[Coin],
) -> OwnedDeps<MockStorage, MockApi, MockQuerier> {
  let contract_addr = HumanAddr::from(MOCK_CONTRACT_ADDR);
  OwnedDeps {
    storage: MockStorage::default(),
    api: MockApi::default(),
    querier: MockQuerier::new(&[(&contract_addr, contract_balance)]),
  }
}

```

### mock\_dependencies is used for mocking storage, api, and querier.

```
/// Returns a default enviroment with height, time, chain_id, and contract address.
/// You can submit as is to most contracts, or modify height/time if you want to
/// test for expiration.
///
/// This is intended for use in test code only.

pub fn mock_env() -> Env {
  Env {
    block: BlockInfo {
      height: 12_345,
      time: 1_571_797_419,
      time_nanos: 879305533,
      chain_id: "xion-testnet-1".to_string(),
    },
    contract: ContractInfo {
      address: HumanAddr::from(MOCK_CONTRACT_ADDR),
    },
  }
}
```

### mock\_env is used for mocking blocks, and contract environments.

```
/// Just set sender and sent funds for the message.
/// This is intended for use in test code only.

pub fn mock_info<U: Into<HumanAddr>>(sender: U, sent: &[Coin]) -> MessageInfo {
  MessageInfo {
    sender: sender.into(),
    sent_funds: sent.to_vec(),
  }
}
```

### mock\_info is used for mocking transaction environments.

Test Transfer Handler

```
#[test]
fn transfer() {
  let mut deps = mock_dependencies(&[]);
  let msg = InitMsg {
    counter_offer: coins(40, "ETH"),
    expires: 100_000,
  };
  let info = mock_info("creator", &coins(1, "BTC"));
  
  // we can just call .unwrap() to assert this was a success
  
  let res = init(deps.as_mut(), mock_env(), info, msg).unwrap();
  assert_eq!(0, res.messages.len());
  
  // random cannot transfer
  
  let info = mock_info("anyone", &[]);
  let err = handle_transfer(deps.as_mut(), mock_env(), info, HumanAddr::from("anyone"))
    .unwrap_err();
  match err {
    ContractError::Unauthorized {} => {}
    e => panic!("unexpected error: {}", e),
  }
  
  // owner can transfer
  
  let info = mock_info("creator", &[]);
  let res =
    handle_transfer(deps.as_mut(), mock_env(), info, HumanAddr::from("someone")).unwrap();
  assert_eq!(res.attributes.len(), 2);
  assert_eq!(res.attributes[0], attr("action", "transfer"));
  
  // check updated properly
  
  let res = query_config(deps.as_ref()).unwrap();
  assert_eq!("someone", res.owner.as_str());
  assert_eq!("creator", res.creator.as_str());
}
```

Test Execute

```
#[test]
fn execute() {
  let mut deps = mock_dependencies(&[]);
  let amount = coins(40, "ETH");
  let collateral = coins(1, "BTC");
  let expires = 100_000;
  let msg = InitMsg {
    counter_offer: amount.clone(),
    expires: expires,
  };
  let info = mock_info("creator", &collateral);
  // we can just call .unwrap() to assert this was a success
  let _ = init(deps.as_mut(), mock_env(), info, msg).unwrap();
  // set new owner
  let info = mock_info("creator", &[]);
  let _ = handle_transfer(deps.as_mut(), mock_env(), info, HumanAddr::from("owner")).unwrap();
  // random cannot execute
  let info = mock_info("creator", &amount);
  let err = handle_execute(deps.as_mut(), mock_env(), info).unwrap_err();
  match err {
    ContractError::Unauthorized {} => {}
    e => panic!("unexpected error: {}", e),
  }
  // expired cannot execute
  let info = mock_info("owner", &amount);
  let mut env = mock_env();
  env.block.height = 200_000;
  let err = handle_execute(deps.as_mut(), env, info).unwrap_err();
  match err {
    ContractError::OptionExpired { expired } => assert_eq!(expired, expires),
    e => panic!("unexpected error: {}", e),
  }
  // bad counter_offer cannot execute
  let msg_offer = coins(39, "ETH");
  let info = mock_info("owner", &msg_offer);
  let err = handle_execute(deps.as_mut(), mock_env(), info).unwrap_err();
  match err {
    ContractError::CounterOfferMismatch {
      offer,
      counter_offer,
    } => {
      assert_eq!(msg_offer, offer);
      assert_eq!(amount, counter_offer);
    }
    e => panic!("unexpected error: {}", e),
  }
  // proper execution
  let info = mock_info("owner", &amount);
  let res = handle_execute(deps.as_mut(), mock_env(), info).unwrap();
  assert_eq!(res.messages.len(), 2);
  assert_eq!(
    res.messages[0],
    CosmosMsg::Bank(BankMsg::Send {
      from_address: MOCK_CONTRACT_ADDR.into(),
      to_address: "creator".into(),
      amount,
    })
  );
  assert_eq!(
    res.messages[1],
    CosmosMsg::Bank(BankMsg::Send {
      from_address: MOCK_CONTRACT_ADDR.into(),
      to_address: "owner".into(),
      amount: collateral,
    })
  );
  // check deleted
  let _ = query_config(deps.as_ref()).unwrap_err();
}
```

Now you are prepared to run the tests:

```
cargo test
```
