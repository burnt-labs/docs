---
description: How to join the network with a configured daemon
---

# Join the Xion Network

With a configured Xion Daemon, we can attempt to join one of the Xion Networks.

## Select a Network

| Network          | Genesis JSON                                                        | Addressbook JSON                                                      | Node Snapshot                                                                          |
| ---------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `xion-testnet-1` | [genesis.json](https://files.xion-testnet-1.burnt.com/genesis.json) | [addrbook.json](https://files.xion-testnet-1.burnt.com/addrbook.json) | [latest.tar.lz4](https://files.xion-testnet-1.burnt.com/xion-testnet-1-latest.tar.lz4) |
| `xion-mainnet-1` | TBD                                                                 | TBD                                                                   | TBD                                                                                    |

- The Genesis JSON file specifies the account balances and parameters at the inception of the network, and anchors the application state to a point in time.
- The Addressbook lists a selection of peers for your node to dial, such that it may discover other nodes in the network.&#x20;

## Configure the Network information

For detailed information on how to join each network, please refer to its corresponding subsection.

<table data-view="cards"><thead><tr><th></th><th align="center"></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td align="center">xion-testnet-1</td><td></td><td><a href="xion-testnet-1.md">xion-testnet-1.md</a></td></tr></tbody></table>

## Start the Xion Daemon

Finally, with all the configurations in place, we can start synchronizing our node:

```
xiond start  --home /root/.xiond
```
