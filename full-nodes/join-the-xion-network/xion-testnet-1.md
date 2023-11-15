---
description: Details to join the xion-testnet-1 network
---

# xion-testnet-1

## Hosts and Endpoints

The different Hosts and Endpoints of `xion-testnet-1` are:

*   **Seeds**

    * `7900d5f177228def24170f9631db76afab20278e@seed.xion-testnet-1.burnt.com:11656`


*   **Peers**

    * `93db3539d6cc9603bbb6b9e43bfb473bdaff7e4b@sentry-use1a.xion-testnet-1.burnt.com:32656`
    * `0f2ccb6d7e8f233c03f91dee690f5ff714319fba@sentry-use1b.xion-testnet-1.burnt.com:33656`
    * `f684e3873191d62a74e5431202581d99fe3439b7@sentry-use1c.xion-testnet-1.burnt.com:34656`


*   **API**

    * `https://api.xion-testnet-1.burnt.com`


*   **RPC**

    * `https://rpc.xion-testnet-1.burnt.com:443`


*   **gRPC**

    * `https://grpc.xion-testnet-1.burnt.com:443`


* **Websocket**
  * `wss://websocket.xion-testnet-1.burnt.com/websocket`

## Download the Genesis JSON file

We provide a sample snippet which will download the `genesis.json` to the correct destination.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
GENESIS_JSON="https://files.xion-testnet-1.burnt.com/genesis.json"

curl ${GENESIS_JSON} -o ${BASEDIR}/config/genesis.json -s
```

## Download an Addressbook JSON file

We provide a sample snippet which will download the `addrbook.json` to the correct destination.

```
#!/usr/bin/env bash
set -euxo pipefail

BASEDIR="/home/xiond/.xiond"
ADDRBOOK_JSON="https://files.xion-testnet-1.burnt.com/addrbook.json"

curl ${ADDRBOOK_JSON} -o ${BASEDIR}/config/addrbook.json -s
```

## Specify the Seed node to discover peers

We provide a sample snippet which will edit the `config.toml` with a Seed node.

```
#!/usr/bin/env bash
set -euxo pipefail

SEED_NODE="7900d5f177228def24170f9631db76afab20278e@seed.xion-testnet-1.burnt.com:11656"

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

