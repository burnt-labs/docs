# Compiling a Contract

Within this section, our objective is to retrieve the code for a sample contract and transform it into a wasm binary executable.

## Prerequisites

Before you proceed, ensure that you have installed the necessary dependencies as detailed in the "[Setting Up Environment](setting-up-environment.md)" guide.

## Downloading the Contract

To get started, initiate the download of the [cw-contracts ](https://github.com/deus-labs/cw-contracts)repository. The contract we'll be compiling is the _**nameservice**_ contract.

**Clone the following repository:**

```
git clone https://github.com/InterWasm/cw-contracts
cd cw-contracts
git checkout main
cd contracts/nameservice
```

## Compile Contract

In this section, we will go over how to compile using Cargo.

#### Compile using Cargo

Execute the following command to compile the contract:

```
cargo wasm
```

After the compilation process, you should find the file _target/wasm32-unknown-unknown/release/cw\_nameservice.wasm_ generated. Its size is roughly 1.9 MB, indicating that it's a release build but hasn't undergone the removal of all redundant code yet. To deploy the contract on-chain, optimization is necessary. Refer to the Optimized Compilation"section below for guidance on optimizing a contract.

## Optimized Compilation&#x20;

In order to minimize gas costs, it is essential to minimize the binary size, resulting in more cost-effective deployment and reduced fees for every interaction. Fortunately, there are available tools to aid in this process. These optimization tools generate reproducible builds of CosmWasm smart contracts, ensuring that third parties can verify the contract's contents as claimed.

#### Optimize using Cargo

Executing the following command should yield an optimized contract suitable for storage on the blockchain:

```
RUSTFLAGS='-C link-arg=-s' cargo wasm
```

#### Optimize using Rust-Optimizer

{% hint style="info" %}
_You will have needed to already install Docker in order to run the rust-optimizer_
{% endhint %}

Navigate to the project root and run the following command:

```
docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.12
```

By running this command, the .wasm file will undergo optimization, resulting in an optimized .wasm file located in the artifacts directory.

####
