# Setting Up Environment

A user can choose to either set up a local testnet or connect to an existing network. For easy testing, the XION testnet is available for deploying and running ones contracts.

To set up a local testnet, see Running a Local Testnet. For details on the available XION networks, see the [Networks](../../networks.md) page.

To work with CosmWasm smart contract, a user will need rust installed on their machine. If the user does not have it installed, they can find installation instructions on the Rust website.

Additionally, the user will need the Wasm rust compiler backend installed to build Wasm binaries.&#x20;

Also, to be able to upload Rust Wasm Contracts into the blockchain, a user will need to install docker.&#x20;

&#x20;To minimize their contract sizes, it will be required to run CosmWasm Rust Optimizer; without that, more complex contracts might exceed a size limit.&#x20;



### cosmwasm-check utility

An additional helpful tool for building smart contracts is the `cosmwasm-checkutility`. It allows a user to check if the wasm binary is a proper smart contract ready to upload into the blockchain. A user can install it using cargo:

`$ cargo install cosmwasm-check`

If the installation succeeds, a user should be able to execute the utility from your command line.

`$ cosmwasm-check --version`

`Contract checking 1.2.3`



### Verifying the installation

To guarantee a user is ready to build their smart contracts, they will need to make sure they can build examples. Checkout the cw-plus repository and run the testing command in its folder:

`$ git clone git@github.com:CosmWasm/cw-plus.git`

`$ cd ./cw-plus`

`cw-plus $ cargo test`

A user should see that everything in the repository gets compiled, and all tests pass.

`cw-plus` is a great place to find example contracts - look for them in contracts directory. The repository is maintained by CosmWasm creators, so contracts in there should follow good practices.\


To verify the `cosmwasm-check` utility, first, a user will need to build a smart contract. Go to some contract directory, for example, `contracts/cw1-whitelist`, and call `cargo wasm`:

`cw-plus $ cd contracts/cw1-whitelist`

`cw-plus/contracts/cw1-whitelist $ cargo wasm`



A user should be able to find their output binary in the t`arget/wasm32-unknown-unknown/release/` of the root repo directory - not in the contract directory itself! Now they can check if contract validation passes:

`cw-plus/contracts/cw1-whitelist $ cosmwasm-check ../../target/wasm32-unknown-unknown/release/cw1_whitelist.wasm`

`Available capabilities: {"iterator", "cosmwasm_1_1", "cosmwasm_1_2", "stargate", "staking"}`

`../../target/wasm32-unknown-unknown/release/cw1_whitelist.wasm: pass`

`All contracts (1) passed checks!`

If you intend to use xiond as your preferred client, we recommend you to set up these variables. Otherwise you will have to type in node, chain id and gas-price details with every command you execute.

```bash
# bash
export NODE="--node $RPC"
export TXFLAG="${NODE} --chain-id xion-testnet-1 --gas-prices 0.25uxion --gas auto --gas-adjustment 1.4"

# zsh
export NODE=(--node $RPC)
export TXFLAG=($NODE --chain-id xion-testnet-1 --gas-prices 0.25uxion --gas auto --gas-adjustment 1.4)
```

{% hint style="info" %}
Get the RPC endpoint from the [networks.md](../../networks.md "mention") section
{% endhint %}

