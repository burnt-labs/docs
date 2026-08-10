# Unit Test

Unit testing plays a vital role in software development, serving to verify the accuracy of your code and enabling the early detection of any bugs or problems. Within this section, we will delve into the procedure for running unit tests within a CosmWasm smart contract.

## Writing Unit Tests

In Rust, when creating unit tests for CosmWasm smart contracts, it's necessary to utilize the #test attribute to designate test functions. These functions should be organized within a tests module within your smart contract's source code, commonly guarded with a #cfg(test) attribute to ensure compilation solely during testing. Macros such as assert!, assert\_eq!, and assert\_ne! can be employed to verify your code's functionality against anticipated results.

## Running Unit Tests

Execute the following command to perform the available unit tests:

```
RUST_BACKTRACE=1 cargo unit-test
```

Following several compilation steps, you should notice:

```
running 15 tests
test coin_helpers::test::assert_sent_sufficient_coin_works ... ok
test tests::test_module::fails_on_register_insufficient_fees ... ok
test tests::test_module::fails_on_register_wrong_fee_denom ... ok
test tests::test_module::proper_init_no_fees ... ok
test tests::test_module::fails_on_register_already_taken_name ... ok
test tests::test_module::fails_on_transfer_insufficient_fees ... ok
test tests::test_module::proper_init_with_fees ... ok
test tests::test_module::register_available_name_and_query_works ... ok
test tests::test_module::fails_on_transfer_non_existent ... ok
test tests::test_module::fails_on_transfer_from_nonowner ... ok
test tests::test_module::register_available_name_fails_with_invalid_name ... ok
test tests::test_module::register_available_name_and_query_works_with_fees ... ok
test tests::test_module::returns_empty_on_query_unregistered_name ... ok
test tests::test_module::transfer_works ... ok
test tests::test_module::transfer_works_with_fees ... ok

test result: ok. 15 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

```

Enabling **RUST\_BACKTRACE=1** will furnish comprehensive stack traces for error tracking, a valuable tool. Please note that this feature exclusively functions with unit tests, which assess native Rust code rather than compiled wasm code. Furthermore, if you're interested in the sources of cargo wasm and cargo unit-test, they are merely aliases specified in the .cargo/config file situated within the project directory. A closer examination of the file's contents will provide you with insights into the cargo flags.

## Mocking

Mocking and dependency injection are strategies that enable you to segregate particular segments of your smart contract for testing. In the context of mocking, you generate imaginary objects or functions that substitute the real dependencies of your smart contract, granting you control over their conduct during testing. On the other hand, dependency injection entails supplying these mocked dependencies as parameters to your smart contract functions, enabling the simulation of diverse conditions and scenarios without impacting the authentic contract state or dependencies.



## Best Practices to Keep in Mind

* **Organize Test Modules**: Arrange your tests by grouping them in separate modules or files according to the functionality they evaluate.
* **Adhere to Naming Conventions**: Employ descriptive names for your tests that explicitly convey what aspect they are assessing, for instance, using names like `test_transfer_funds_success` or `test_invalid_name_rejection`.
* **Maintain Clarity and Conciseness**: Develop tests that are lucid and succinct, concentrating on specific components of the smart contract. Avoid creating overly intricate or unrelated tests.
* **Cover Edge Cases and Failures**: Ensure your tests encompass edge cases and potential failure scenarios, thus verifying that your smart contract can gracefully handle a variety of situations.
* **Isolate Test Cases**: Keep your tests independent of one another, so that the failure of one test does not impact the execution of others. This enhances the reliability of your test suite
