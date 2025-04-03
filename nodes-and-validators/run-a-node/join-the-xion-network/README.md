---
description: How to join the network with a configured daemon
---

# Join the Xion Network

With a configured Xion Daemon, we can attempt to join one of the Xion Networks.

## Select a Network

| Network          | Genesis JSON                                                        | Addressbook JSON                                                      | Node Snapshot                                                                          |
| ---------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `xion-testnet-2` | [genesis.json](https://raw.githubusercontent.com/burnt-labs/xion-testnet-2/refs/heads/main/config/genesis.json) | [addrbook.json](https://snapshots.polkachu.com/testnet-addrbook/xion/addrbook.json) | [latest.tar.lz4](https://snapshots.polkachu.com/testnet-snapshots/xion/xion_1647937.tar.lz4) |
| `xion-mainnet-1` | TBD                                                                 | TBD                                                                   | TBD                                                                                    |

* The Genesis JSON file specifies the account balances and parameters at the inception of the network, and anchors the application state to a point in time.
* The Addressbook lists a selection of peers for your node to dial, such that it may discover other nodes in the network.&#x20;

## Configure the Network information

For detailed information on how to join each network, please refer to its corresponding subsection.

<table data-view="cards"><thead><tr><th></th><th align="center"></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td align="center">xion-testnet-2</td><td></td><td><a href="xion-testnet.md">xion-testnet.md</a></td></tr></tbody></table>

## Start the Xion Daemon

Finally, with all the configurations in place, we can start synchronizing our node:

```
$ xiond start --x-crisis-skip-assert-invariants --home /home/xiond/.xiond
```

