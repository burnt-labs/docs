# Interacting with Xion Chain using Xion Daemon

`xiond` is the command-line interface (CLI) daemon for interacting with the **Xion blockchain**. It allows users to manage accounts, send transactions, query blockchain data, and operate validator nodes.&#x20;



## **Prerequisites**

Before proceeding with this guide, ensure that `xiond` is installed on your system. Follow the installation steps outlined in the [Installation Prerequisites](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment#xiond) guide.

To verify your installation, run:

```
xiond
```

If installed correctly, you should see the following output:

```
xion daemon (server)

Usage:
  xiond [command]

Available Commands:
  comet                 CometBFT subcommands
  completion            Generate the autocompletion script for the specified shell
  config                Utilities for managing application configuration
  debug                 Tool for helping with debugging your application
  export                Export state to JSON
  genesis               Application's genesis-related subcommands
  help                  Help about any command
  init                  Initialize private validator, p2p, genesis, and application configuration files
  keys                  Manage your application's keys
  module-hash-by-height Get module hashes at a given height
  prune                 Prune app history states by keeping the recent heights and deleting old heights
  query                 Querying subcommands
  rollback              rollback Cosmos SDK and CometBFT state by one height
  rosetta               spin up a rosetta server
  snapshots             Manage local snapshots
  start                 Run the full node
  status                Query remote node for status
  tx                    Transactions subcommands
  version               Print the application binary version information

Flags:
  -h, --help                help for xiond
      --home string         directory for config and data (default "/Users/adrianthompson/.xiond")
      --log_format string   The logging format (json|plain) (default "plain")
      --log_level string    The logging level (trace|debug|info|warn|error|fatal|panic|disabled or '*:<level>,<key>:<level>') (default "info")
      --log_no_color        Disable colored logs
      --trace               print out full stack trace on errors

Use "xiond [command] --help" for more information about a command.
```

This output displays the available commands within `xiond`. Each command may include additional subcommands. To get more details about a specific command, use the `--help` flag.



## Generate an account

To execute transactions on-chain, you need at least one funded account. This section will guide you through the process of creating and funding an account using `xiond`.

### Generate an Account Key Pair

To interact with the XION blockchain, you need a cryptographic key pair, consisting of a **public key** and a **private key**. The public key is used to derive your address, while the private key is required to sign transactions.

#### **Generate a New Key Pair**

Use the following command to create a new key pair and add it to your local key store:

```sh
xiond keys add <keyname>
```

Replace `<keyname>` with a name of your choice for easy reference.

#### **Retrieve Public Key**

If you have already generated a key pair and need to retrieve its public key, use the following command:

```sh
xiond keys show <keyname>
```

Replace `<keyname>` with the name of your key to display its public key.



### Fund Your Account

Your account is not fully registered on-chain until it is involved in a transaction. The easiest way to achieve this is by **funding your account**, which allows it to interact with the network.

#### **Request Testnet Tokens**

You can obtain testnet tokens through one of the following methods:

* **Discord Faucet**: Request tokens by using the faucet bot in the **XION Discord**.
* **Faucet Web Page**: Visit the [XION Faucet](https://faucet.xion.burnt.com/) and follow the instructions to receive testnet tokens.

For more details on accessing testnet tokens, see our [Faucet Page](https://docs.burnt.com/xion/developers/section-overview/xion-testnet).

#### **Mainnet Tokens**

If you’re deploying contracts on **XION Mainnet**, you can acquire XION tokens through various **decentralized** and **centralized exchanges**.



## **Connecting to Different Xion Chain Instances**

By default, `xiond` uses a local network node for executing transactions and queries. However, if you need to connect to a different chain, such as a public testnet or mainnet, you must specify the appropriate **chain ID** and **node URL**.



### **Finding Chain IDs and Public Nodes**

You can find the **chain IDs** for all Xion chains, along with available public RPC nodes, in the [Public Endpoints and Resources](https://docs.burnt.com/xion/developers/section-overview/public-endpoints-and-resources) section of the documentation.



### **Executing Transactions**

To interact with a different Xion blockchain for sending transactions, you need to explicitly set:

* The **chain ID** using the `--chain-id` flag
* The **node URL** using the `--node` flag

#### **Example: Sending tokens to an account**

```sh
xiond tx bank send <your-wallet> <recipient-address> <amount>uxion \
  --chain-id <target-chain-id> \
  --node <node-url> \
  --from <your-wallet> \
  --gas-prices 0.025uxion \
  --gas auto \
  --gas-adjustment 1.3 \
  -y
```

* Replace `<target-chain-id>` with the appropriate chain ID.
* Replace `<node-url>` with the RPC endpoint of the target chain.

#### Possible Output

```
gas estimate: 70320
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: ""
timestamp: ""
tx: null
txhash: 20511E66575DCECF23EBFF7D848DA75DA88A474CD17089C5225C1D04C9561A57
```

### **Executing Queries**

For queries, you only need to set the `--node` flag, as queries do not require the `--chain-id` parameter.

#### **Example: Querying Account Balance**

```sh
xiond query bank balances <wallet-address> --node <node-url>
```

#### Possible Output

```
balances:
- amount: "1223782"
  denom: uxion
pagination:
  total: "1"
```
