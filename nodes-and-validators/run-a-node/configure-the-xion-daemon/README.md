---
description: How to initialize and configure the xiond binary
---

# Configure the Xion Daemon

As an application-specific blockchain using the Cosmos SDK, we benefit from its extensive configuration options.

{% hint style="info" %}
:warning: **Custom Configuration**

The configuration in this guide is provided as-is, with no guarantee of any kind.

You are highly encouraged to tweak the Cosmos SDK configuration values to accommodate your specific environment and use case.

The [Cosmos SDK documentation](https://docs.cosmos.network/v0.47) is a good place to start exploring the different options.
{% endhint %}

## Initialize the Xion Daemon

* As is the case for most Cosmos-based chains, the daemon is initialized like so:

```
$ xiond init my-awesome-moniker \
    --chain-id xion-testnet-2 \
    --home /tmp/node
```

## Refer to the config directory

* The `xiond init` command will output this structure to the specified `--home:`

```
.                                   # /tmp/node
  |- data                           # Contains the databases used by the node.
  |- config/
      |- app.toml                   # Application-related configuration file.
      |- config.toml                # Tendermint-related configuration file.
      |- genesis.json               # The genesis file.
      |- node_key.json              # Private key to use for node authentication in the p2p protocol.
      |- priv_validator_key.json    # Private key to use as a validator in the consensus protocol.
```

{% hint style="info" %}
:warning: **Node Keys**

Take note of the `node_key.json` and `priv_validator_key.json`files.

The combination of these two private keys serves to uniquely identify your node on the Xion Network. If the purpose of your node is ephemeral, such that it can be discarded with no adverse effects, then these files can be discarded and regenerated at will.

However, if the purpose of your node is persistent, such as running a Validator, then protecting and preserving these files becomes paramount.
{% endhint %}

<table data-view="cards"><thead><tr><th></th><th align="center"></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td align="center"><code>app.toml</code></td><td></td><td><a href="app.toml.md">app.toml.md</a></td></tr><tr><td></td><td align="center"><code>client.toml</code></td><td></td><td><a href="client.toml.md">client.toml.md</a></td></tr><tr><td></td><td align="center"><code>config.toml</code></td><td></td><td><a href="config.toml.md">config.toml.md</a></td></tr></tbody></table>

