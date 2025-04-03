---
description: Recommended system specs
---

# System Specifications

{% hint style="info" %}
🐧  **Recommended operating systems**

We've tested our technology on several Linux distributions.

If you're considering running another OS, your mileage may vary, and we'll be unable to assist you to get up and running.
{% endhint %}

## Hardware Requirements

The minimum recommended specs for running an instance of `xiond` on the Burnt Networks are:

| Network          | CPU cores         | RAM    | Disk (SSD) | Bandwidth  |
| ---------------- | ----------------- | ------ | ---------- | ---------- |
| `xion-testnet-2` | 8 (gen-purpose)   | 32 GB  | 128 GB     | \~120 Mbps |
| `xion-mainnet-1` | 8 (compute-optim) | 32 GB  | 256 GB     | \~120 Mbps |

{% hint style="info" %}
:warning: **Monitor your system**

As the network grows, the minimum hardware requirements will also grow.&#x20;

It is recommended that you monitor your system so you can prevent it from running out of resources.
{% endhint %}

## Commonly used ports

The Xion daemon uses the following TCP ports. Please adapt their settings to match your needs.

In most cases,  node operators only need to open the following port:

* `26656`: The default port for the P2P protocol.&#x20;
  * This port is used to communicate with other nodes and must be open to join a network. However, it does not have to be open to the public. For validator nodes, configuring `persistent_peers` and closing this port to the public are recommended.

Additional ports:

* `1317`: The default port for the Lite Client Daemon (LCD).&#x20;
  * The LCD provides a RESTful API to allow applications and services to interact with your instance. In the default configuration, it is disabled.
* `26657`: The default port for the Tendermint RPC protocol.&#x20;
  * Because this port is used for querying and sending transactions, it must be open for serving queries. In the default configuration it is listening on `127.0.0.1`.
* `26660`: The default port for interacting with the [Prometheus](https://prometheus.io) database
  * This port can be used to monitor the node itself, and gather information about the network. In the default configuration, it is disabled.

{% hint style="info" %}
:warning: **Caution**

Do not open ports `1317` and / or `26657` to the public unless you explicitly plan to run a publicly-accessible node.
{% endhint %}

