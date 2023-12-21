---
description: How to convert a Full Node to a Validator Node
---

# Create Validator

{% hint style="info" %}
:warning: **Disclaimer**

While setting up a rudimentary validator node is easy, running a production-grade validator node with a robust architecture and security features requires a more involved setup, which is beyond the scope of this guide.
{% endhint %}

This section assumes that:

* You've successfully setup a Full Node
* You've joined one of the Xion Networks and have synced the chain
* You've funded a wallet on the Full Node



## Run the Create Validator command

We provide a sample snippet which will create a new Validator:

```
$ xiond tx staking create-validator \
    --amount "5000000uxion" \
    --pubkey $(xiond tendermint show-validator) \
    --moniker "copypaste.com" \
    --identity "my-keybase-id" \
    --security-contact "email@copypaste.com" \
    --website "www.copypaste.com" \
    --details "I copy-pasted this command." \
    --chain-id "xion-testnet-1" \
    --commission-rate "0.05" \
    --commission-max-rate "0.25" \
    --commission-max-change-rate "0.1" \
    --min-self-delegation "1" \
    --gas "auto" \
    --gas-prices "0.025uxion" \
    --gas-adjustment 1.2 \
    --from my-wallet-name \
    --keyring-backend test \
    --home /home/xiond/.xiond
```



## Confirm that your Validator is Active

If running the following command returns something, your validator is active:

```
$ xiond query tendermint-validator-set | grep "$(xiond tendermint show-validator)"
```

{% hint style="info" %}
:warning: **Active Set is 100 Validators**

The Active Set for the Xion Networks cuts off at 100 Validators.

If you're not in the active set, you will need to court delegations to increase your total stake, such that it is greater than the total stake of the 100th Validator.
{% endhint %}

