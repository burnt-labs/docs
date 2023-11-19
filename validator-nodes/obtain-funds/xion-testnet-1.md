---
description: Obtain funds from the xion-testnet-1 faucet
---

# xion-testnet-1

## Join the Burnt Discord

Your first step should be to join the community over on Discord:

<table data-view="cards"><thead><tr><th></th><th align="center"></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td></td><td align="center">Discord</td><td></td><td></td><td><a href="https://discord.gg/burnt">https://discord.gg/burnt</a></td></tr></tbody></table>



## Obtain the Validator Role

Note: The current testnet has a genesis set and the Validator role is not currently being granted. It will be expanded in the future.



## Request Funds in the Faucet channel

Once you've been granted the Validator role, you'll have access to the Validator-specific Discord channels, including the Faucet.

Among its denizens is the Beelzebot, which listens for slash commands.

Request your Funds using the `/faucet` slash command, like so:

<figure><img src="../../.gitbook/assets/Screenshot 2023-03-12 at 10.07.16 AM.png" alt=""><figcaption><p>The <code>/faucet</code> slashcommand</p></figcaption></figure>

You'll be prompted for confirmation; double-check that your destination wallet address is correct.

<figure><img src="../../.gitbook/assets/Screenshot 2023-03-12 at 10.12.35 AM (1).png" alt=""><figcaption><p>The <code>/faucet</code> slashcommand confirmation prompt</p></figcaption></figure>

* Selecting `No` will cancel the transaction.
* Selecting `Yes` will send funds to the desired wallet address.

{% hint style="info" %}
:warning: **24 Hour Cooldown**

To help mitigate bad actors spamming the faucet, we've implemented a 24-hour cooldown on each successful Faucet request.

We've also set an upper limit to the amount of funds a given address can request from the Faucet.
{% endhint %}
