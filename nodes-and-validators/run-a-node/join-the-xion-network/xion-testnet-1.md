---
description: Details to join the xion-testnet-1 network.
---

# xion-testnet-1

## Upgrade Path



| Git Tag  | Docker Tag    | Git SHA                                    | Block Height | Requires Halt |
| -------- | ------------- | ------------------------------------------ | ------------ | ------------- |
| `v0.1.0` | `sha-2722f0f` | `2722f0fb1234db97e9bf9b98f744675eb8fb1b26` | `0`          | `false`       |
| `v0.3.2` | `sha-455cfa5` | `455cfa5b78e79e1c50867f6d61a99b0e9df6b9b1` | `1929544`    | `true`        |
| `v0.3.3` | `sha-79f0d91` | `79f0d91dfcd4c5916fa30ea246eed1677daf4e44` | `2147030`    | `true`        |
| `v0.3.4` | `sha-74754c7` | `74754c7c206a7d0f27a23ac9431a4d09dbacff4c` | `3278095`    | `false`       |
| `v0.3.7` | `sha-f13a74c` | `f13a74c7cf5a562d6902263e53c5ee4f0a9dfefe` | `6887750`    | `true`        |
| `v0.3.8` | `sha-34c82a9` | `34c82a927b7ae7b67e8b76a1a66fe1b36f807f82` | `7130100`    | `true`        |

## Hosts and Endpoints

The different Hosts and Endpoints of `xion-testnet-1` are:

*   **Seeds**

    * `eb029462c82b46d842a47122d860617bff627fdf@xion-testnet-1.burnt.com:11656`


* **Peers**
  * `6bb70718db6af0a473c9d76e82d9ade33618b20d@xion-testnet-1.burnt.com:32656`
  * `0f2ccb6d7e8f233c03f91dee690f5ff714319fba@xion-testnet-1.burnt.com:33656`
  * `f684e3873191d62a74e5431202581d99fe3439b7@xion-testnet-1.burnt.com:34656`

## Download the Genesis JSON file

We provide a sample snippet which will download the `genesis.json` to the correct destination.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
GENESIS_JSON="https://files.xion-testnet-1.burnt.com/genesis.json"

curl ${GENESIS_JSON} -o ${BASEDIR}/config/genesis.json -s
```

## Specify the Seed node to discover peers

We provide a sample snippet which will edit the `config.toml` with our Seed node.

```
#!/usr/bin/env bash
set -euxo pipefail

SEED_NODE="7900d5f177228def24170f9631db76afab20278e@seed.xion-testnet-1.burnt.com:11656"

sed -i.bak -E "s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"$SEED_NODE\"|" ${BASEDIR}/config/config.toml
```

## Download the Address Book JSON file

You may also want to download a fresh `addrbook.json` to help with peer discovery. This addrbook is sourced from the Seed node's public peers, and is updated periodically.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
ADDRBOOK_JSON="https://files.xion-testnet-1.burnt.com/addrbook.json"

curl ${ADDRBOOK_JSON} -o ${BASEDIR}/config/addrbook.json -s
```

## Synchronize the chain

Now that we've configured our Xion Daemon to discover other nodes in the Xion Network, we can attempt to synchronize the chain.

While it's definitely possible to synchronize the chain from Genesis, we recommend you only do so if you want to run an Archive node, and perform processing on each of the blocks as they come in.

Instead, we highly recommend you either download a Node snapshot and restore it locally, or attempt to synchronize using the State Sync feature. These methods are much faster, and provide you with a functional node in a fraction of the time.

### Download a Node Snapshot&#x20;

Downloading the most recent Node Snapshot is the most reliable way to catch up with the chain.

We produce Node Snapshots every twenty-four (24) hours, and keep the last six (6) tarballs. As a convenience, the latest Snapshot is available with the `-latest.tar.lz4` suffix.

We provide a sample snippet, with which to download our latest public snapshot.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
TAR_LZ4="https://files.xion-testnet-1.burnt.com/xion-testnet-1-latest.tar.lz4"

curl -o - -L ${TAR_LZ4} | lz4 -c -d - | tar -x -v -C ${BASEDIR}

```

### Restore a State Sync snapshot

Syncing the chain using State Sync is the fastest way to catch up with the latest block.

We provide a sample snippet, with which to edit the `config.toml` for State Sync.

```
#!/bin/bash
set -euxo pipefail

SNAP_RPC="https://rpc.xion-testnet-1.burnt.com:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.burnt/config/config.toml
```

{% hint style="info" %}
:warning: **State Sync is Flaky**

While it's a great idea in theory, and when it works it's fantastic, sometimes State Sync just won't cooperate. Notably, it doesn't handle synchronizing WASM data.

If State Sync just isn't working for you, **DON'T PANIC!**

Try the restore using a Node snapshot instead.
{% endhint %}

