# Interact with XION chain: Setup XION Daemon

The XION Daemon binary is the primary interaction point for a DAPP developer to the XION blockchain both locally and on testnet.

## Installation

1. Clone the [Xion](https://github.com/burnt-labs/xion) repository locally.
2. Run `make install` in the above directory
3. The \`xiond\` command will be installed into your $GOPATH

{% hint style="warning" %}
$GOPATH/bin must be on your $PATH for this to work. You can do this by adding the following to your .zshrc or .bashrc file.

```bash
export GOPATH=$(go env GOPATH)
export PATH=$GOPATH/bin:$PATH
```
{% endhint %}

For more information see [here](../../../../nodes-and-validators/run-a-node/build-the-xion-daemon.md).

### Alternative Installation - Homebrew

We have a [Github repo](https://github.com/burnt-labs/homebrew-xion) for Homebrew formulae

To install xiond locally with the Homebrew package manager:

1. `brew tap burnt-labs/xion`
2. `brew install xiond` will install the latest version
3. `brew install xiond@0.3.8` will install version 0.3.8, etc.

### Confirm Installation

Enter the following in your terminal

```
$  xiond 
```

And you should see the following output

<pre><code>xion daemon (server)

Usage:
  xiond [command]
<strong>
</strong>Available Commands:
  completion  Generate the autocompletion script for the specified shell
  config      Create or query an application CLI configuration file
  debug       Tool for helping with debugging your application
  export      Export state to JSON
  genesis     Application's genesis-related subcommands
  help        Help about any command
  init        Initialize private validator, p2p, genesis, and application configuration files
  keys        Manage your application's keys
  prune       Prune app history states by keeping the recent heights and deleting old heights
  query       Querying subcommands
  rollback    rollback cosmos-sdk and tendermint state by one height
  rosetta     spin up a rosetta server
  start       Run the full node
  status      Query remote node for status
  tendermint  Tendermint subcommands
  tx          Transactions subcommands
  version     Print the application binary version information

Flags:
  -h, --help                help for xiond
      --home string         directory for config and data (default "/Users/justin/.xiond")
      --log_format string   The logging format (json|plain) (default "plain")
      --log_level string    The logging level (trace|debug|info|warn|error|fatal|panic) (default "info")
      --trace               print out full stack trace on errors

Use "xiond [command] --help" for more information about a command.
</code></pre>



By default all commands will assume you are accessing a local xion testnet unless you pass a parameter.

## Testing the CLI

### Connecting to an External node

The `--node` flag allows you to provide an external RPC to query from.

#### Example: Query balance of an account on the testnet

{% code overflow="wrap" %}
```sh
$ xiond query bank balances xion14yy92ae8eq0q3ezy9nasumt65hwdgryvpkf0a4 --node https://rpc.xion-testnet-1.burnt.com:443
```
{% endcode %}

### Creating and viewing Keys

```bash
$ xiond keys list
```

**Output**

If this is a fresh installation the output is going to be&#x20;

```
[]
```

**Create a new account**

```
$ xiond keys add test
```

**Output**

```
- address: xion1vmzvym7t4kn09lzmj9clxk24yanr08xfr6fghg
  name: test
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AwrVIv+hI5g9PjnkWXPxFSpuA6h2S7hVdntt45SqlJKS"}'
  type: local


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

reject waste alien movie abstract kangaroo find second phone home pet you regret horse crunch above faint govern gaze hammer fortune midnight can elephant
```

**List Keys**

```
$ xiond keys list
```

**Output**

```
- address: xion1vmzvym7t4kn09lzmj9clxk24yanr08xfr6fghg
  name: test
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AwrVIv+hI5g9PjnkWXPxFSpuA6h2S7hVdntt45SqlJKS"}'
  type: local
```

The `name` field is an alias that can be used in some other commands.

### Checking an account balance

To get the current balance the in the banking module in a given address.&#x20;

For the below command to not return&#x20;

`Error: post failed: Post "http://localhost:26657": dial tcp [::1]:26657: connect: connection refused`

make sure you either provide a node to connect to or a have local instance of Xion chain running in the background.

**To connect to an external node**

{% code overflow="wrap" %}
```
$ xiond query bank balances xion14yy92ae8eq0q3ezy9nasumt65hwdgryvpkf0a4 --node https://rpc.xion-testnet-1.burnt.com:443
```
{% endcode %}

[To query local instance running on `http://localhost:26657`](#user-content-fn-1)[^1]

```bash
$ xiond query bank balances xion14yy92ae8eq0q3ezy9nasumt65hwdgryvpkf0a4
```

See instructions on [Setting up Local Environment](installation-prerequisites-setup-local-environment.md#start-a-local-testnet) (Start a Local Testnet) to initiate a local Xion instance

**Output**&#x20;

{% code title="amounts will vary" overflow="wrap" %}
```bash
balances:
- amount: "10000000000000"
  denom: uxion
pagination:
  next_key: null
  total: "0"
```
{% endcode %}

### Send Xion tokens to another address

To send xion to another address&#x20;

```bash
$ xiond tx bank send <sender address> <receiver address> <amount>
```

The sender must be an address in the list of keys shown above and the amount must be have a denomination such like \`100uxion\`.

```
auth_info:
  fee:
    amount: []
    gas_limit: "200000"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /cosmos.bank.v1beta1.MsgSend
    amount:
    - amount: "100"
      denom: uxion
    from_address: xion1e2fuwe3uhq8zd9nkkk876nawrwdulgv460vzg7
    to_address: xion1z448xjcrwphppyel7gwtxl96e2vjjkur2mrxjr
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]: 
```

This command is interactive. Enter `y` to execute and you will see the tx hash output along with a raw logs and various other information.&#x20;

### List WASM Contracts currently deployed

```
$ xiond query wasm list-codes
```

If any contracts are deployed they will appear here along with their code id.

[^1]: To run a local instance see [https://docs.burnt.com/xion/develop/get-started-guide/setup/installation-prerequisites-setup-local-environment#start-a-local-testnet](https://docs.burnt.com/xion/develop/get-started-guide/setup/installation-prerequisites-setup-local-environment#start-a-local-testnet)
