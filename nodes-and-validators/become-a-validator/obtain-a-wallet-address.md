---
description: How to create a Wallet and obtain Funds
---

# Obtain a Xion Wallet Address

{% hint style="info" %}
:warning: **Disclaimer**

While setting up a rudimentary validator node is easy, running a production-grade validator node with a robust architecture and security features requires a more involved setup, which is beyond the scope of this guide.
{% endhint %}

## Create a new Wallet

We provide a sample snippet which will create a new key in your local keychain:

<pre><code><strong>$ xiond keys add \
</strong>    my-wallet-name \
    --keyring-backend test \
    --home /home/xiond/.xiond
</code></pre>

When running this command, you will not be prompted to enter your keyring password, due to `--keyring-backend` being set to `test`. This is the least secure setting.

Please refer to the [Cosmos SDK documentation](https://docs.cosmos.network/v0.46/run-node/keyring.html) for a more in-depth discussion of the different keyring backends.

The output of this command contains the wallet address, as well as its associated mnemonic.

{% hint style="info" %}
:warning: **Keep your Mnemonic Safe**

**Be sure to preserve and protect your mnemonic.**

You should take the utmost precautions when managing the mnemonic for any of your cryptocurrency wallets.
{% endhint %}

## Restore a Wallet from a Mnemonic

We also provide a sample snippet which will help recover a wallet from its mnemonic:

```
$ xiond keys add \
    my-wallet-name \
    --keyring-backend test \
    --home /home/xiond/.xiond \
    --recover
```

As above, you will not be prompted to enter your keyring password, due to `--keyring-backend` being set to `test`.

However, you will be prompted to enter your BIP39 mnemonic; upon completion, your wallet will be restored and available for use.
