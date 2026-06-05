---
description: Use xiond CLI to query and transact on XION
icon: terminal
---

# Interacting with Xion Chain using Xion Daemon

`xiond` is the command-line interface (CLI) daemon for interacting with the **XION blockchain**. It allows users to manage accounts, send transactions, query blockchain data, and operate validator nodes.

## Prerequisites

Before proceeding, ensure that `xiond` is installed on your system. Follow the setup in:

- [Setting up your Local Smart Contract Development Environment for XION](../computation/local-development/setting-up-env/installation-prerequisites-setup-local-environment.md)

To verify installation, run:

```sh
xiond
```

If installed correctly, you should see command output similar to:

```text
xion daemon (server)

Usage:
  xiond [command]
```

This output confirms that `xiond` is available.

Typical command groups include:

- `keys` - manage local keys
- `query` - read chain state
- `tx` - broadcast transactions
- `status` - inspect node status
- `start` - run a local node process

Use `xiond [command] --help` for command-specific details.

## Command reference quick list

Below is a practical command map for day-to-day development and operations.

| Command group | Purpose | Example |
| --- | --- | --- |
| `keys` | Manage local keyring entries | `xiond keys list` |
| `query` | Read on-chain data from an RPC node | `xiond query bank balances <addr> --node <rpc>` |
| `tx` | Build, sign, and broadcast transactions | `xiond tx bank send ...` |
| `status` | Check remote node status | `xiond status --node <rpc>` |
| `version` | Show binary version | `xiond version` |
| `config` | Manage local CLI/app config | `xiond config --help` |
| `comet` | CometBFT-related subcommands | `xiond comet --help` |
| `genesis` | Inspect or manage genesis state for local node workflows | `xiond genesis --help` |
| `start` | Run a full node process | `xiond start` |
| `init` | Initialize local node files | `xiond init <moniker>` |
| `export` | Export app state to JSON | `xiond export --help` |
| `rollback` | Roll back Cosmos SDK + CometBFT state by one height | `xiond rollback --help` |
| `prune` | Prune local app history | `xiond prune --help` |
| `snapshots` | Manage local snapshots | `xiond snapshots --help` |
| `debug` | Debug-oriented utilities | `xiond debug --help` |
| `rosetta` | Run Rosetta server mode | `xiond rosetta --help` |
| `module-hash-by-height` | Query module hashes for a target height | `xiond module-hash-by-height --help` |

## Generate an account

To execute transactions on-chain, you need at least one funded account.

Key pairs include:

- A **public key / address** used to receive funds.
- A **private key** used to sign transactions.

### Generate a new key pair

```sh
xiond keys add <keyname>
```

Replace `<keyname>` with a name you want to use locally.

### Retrieve a key

```sh
xiond keys show <keyname>
```

Useful key commands:

```sh
# List all locally stored keys
xiond keys list

# Show account address only
xiond keys show <keyname> -a
```

## Fund your account

You can request testnet tokens with:

- **Discord faucet**
- **Web faucet**: [XION Faucet](https://faucet.xion.burnt.com/)

For additional options, see:

- [Requesting XION Testnet Tokens](../references/xion-testnet.md)

For **mainnet** usage, fund your wallet from supported exchanges before broadcasting transactions.

## Connect to different XION networks

For chain IDs and public RPC nodes, see:

- [Public Endpoints & Resources](../references/public-endpoints-and-resources.md)

When sending transactions to a non-local network, set both:

- `--chain-id` for the target chain
- `--node` for the RPC endpoint

For queries, only `--node` is required.

### Example: Send tokens

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

Expected successful output includes:

```text
code: 0
txhash: <transaction-hash>
```

If `code` is non-zero, inspect `raw_log` for the failure reason.

### Example: Query balances

```sh
xiond query bank balances <wallet-address> --node <node-url>
```

Typical query output:

```text
balances:
- amount: "1223782"
  denom: uxion
pagination:
  total: "1"
```

## Common workflows

### Check account sequence and account number before signing

```sh
xiond query auth account <wallet-address> --node <node-url>
```

### Estimate transaction before broadcasting

```sh
xiond tx bank send <from> <to> <amount>uxion \
  --chain-id <target-chain-id> \
  --node <node-url> \
  --from <from> \
  --gas auto \
  --gas-adjustment 1.3 \
  --gas-prices 0.025uxion \
  --dry-run
```

### Query a transaction by hash

```sh
xiond query tx <txhash> --node <node-url>
```

## Troubleshooting

- `insufficient fees`: raise `--gas-prices` or verify fee denomination for the target network.
- `account sequence mismatch`: query account again, then retry with updated sequence (or avoid parallel tx from same account).
- `wrong chain-id`: confirm `--chain-id` matches the node endpoint you are using.
- `connection refused / timeout`: verify `--node` RPC URL and network reachability.
- non-zero `code` in tx result: inspect `raw_log` to identify module-level failure reason.

## Next steps

- [Deploying Your First Smart Contract on XION](../computation/local-development/deploy-a-cosmwasm-smart-contract.md)
- [Xion Agent Toolkit Tutorial](./xion-toolkit.md)
