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



For more information see [here](../../../nodes-and-validators/run-a-node/build-the-xion-daemon.md).

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

## Common use case for the CLI (Locally)

### Listing keys available to the cli

```bash
$ xiond keys list
```

Output

```bash

- address: xion1e2fuwe3uhq8zd9nkkk876nawrwdulgv460vzg7
  name: local-testnet-faucet
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"Ayrlj6q3WWs91p45LVKwI8JyfMYNmWMrcDinLNEdWYE4"}'
  type: local
- address: xion1h495zmkgm92664jfnc80n9p64xs5xf56qrg4vc
  name: local-testnet-validator
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"AiBl5S4fP+Ra2LD/EZ2XtWwNu9otOCFMTs8tfq5fwIXm"}'
  type: local
```

The `name` field is an alias that can be used in some other commands.

### Checking an account balance

To get the current balance in the banking module in a given address.&#x20;

```bash
$ xiond query bank balances <address>
```

Output:

```bash
balances:
- amount: "10000000000000"
  denom: uxion
pagination:
  next_key: null
  total: "0"
```

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

### Generating more keys

```
$ xiond keys add <alias>
```

Generates a key and adds it to your local keyring. The alias must be unique or it will overwrite an existing key.

### List WASM Contracts currently deployed

```
$ xiond query wasm list-codes
```

If any contracts are deployed they will appear here along with their code id.
