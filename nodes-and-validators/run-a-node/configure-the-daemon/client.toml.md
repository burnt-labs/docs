---
description: Sample client.toml file
---

# client.toml

{% hint style="info" %}
:warning: **Use at Your Own Risk**

The configuration below is provided as-is, with no guarantee of any kind. Any mishaps or unfortunate events resulting from using this configuration are purely your responsibility.

Specifically, the `keyring-backend` is set to `test`, which is the least secure setting.&#x20;

You definitely want to change that.
{% endhint %}

```
# This is a TOML config file.
# For more information, see https://github.com/toml-lang/toml

###############################################################################
###                           Client Configuration                            ###
###############################################################################

# The network chain ID
chain-id = "xion-testnet-2"
# The keyring's backend, where the keys are stored (os|file|kwallet|pass|test|memory)
keyring-backend = "test"
# CLI output format (text|json)
output = "json"
# <host>:<port> to Tendermint RPC interface for this chain
node = "tcp://localhost:26657"
# Transaction broadcasting mode (sync|async)
broadcast-mode = "sync"

```

