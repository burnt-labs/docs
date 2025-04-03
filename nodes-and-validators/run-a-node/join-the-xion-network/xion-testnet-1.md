---
description: Details to join the xion-testnet-2 network.
---

# xion-testnet-2

## Hosts and Endpoints
The different Hosts and Endpoints of `xion-testnet-2` are:
*   **Seeds**
    * `20e1000e88125698264454a884812746c2eb4807@testnet-seeds.lavenderfive.com:22356`
    * `ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:22356`
    * `69e1aa5800ffa82615986eac5f99b77c2b8f1ccb@burnt-testnet-seed.itrocket.net:55656`
* **Peers**
  * `0f1c8ed0d4c4a52ecb8fb6c6b2c6f7d1856326d5@65.21.10.115:22356`
  * `5166bcee9d5db28dfede2a936f2a60e07778fa89@65.109.113.219:22356`
  * `7108f0b946f7a4b5b88f01bd29edbaf87d4dbfb4@65.109.89.19:22356`
  * `73b62ebfb71023900d8debb6e6dc7f1379c34686@65.109.58.86:22356`
  * `85e868567ca46f8d94b1fba87b2fa5b42a271439@141.94.240.117:22356`

## Download the Genesis JSON file

We provide a sample snippet which will download the `genesis.json` to the correct destination.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
GENESIS_JSON="https://raw.githubusercontent.com/burnt-labs/xion-testnet-2/refs/heads/main/config/genesis.json"

curl ${GENESIS_JSON} -o ${BASEDIR}/config/genesis.json -s
```

## Specify the Seed node to discover peers

We provide a sample snippet which will edit the `config.toml` with our Seed node.

```
#!/usr/bin/env bash
set -euxo pipefail

SEED_NODE="20e1000e88125698264454a884812746c2eb4807@testnet-seeds.lavenderfive.com:22356,ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:22356"

sed -i.bak -E "s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"$SEED_NODE\"|" ${BASEDIR}/config/config.toml
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
TAR_LZ4="https://snapshots.polkachu.com/testnet-snapshots/xion/xion_1647937.tar.lz4"

curl -o - -L ${TAR_LZ4} | lz4 -c -d - | tar -x -v -C ${BASEDIR}

```

### Restore a State Sync snapshot

Syncing the chain using State Sync is the fastest way to catch up with the latest block.

We provide a sample snippet, with which to edit the `config.toml` for State Sync.

```
#!/bin/bash
set -euxo pipefail

SNAP_RPC="https://rpc.xion-testnet-2.burnt.com:443"

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

