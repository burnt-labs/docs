# Block Explorers

Block explorers provide a user-friendly interface to view detailed information about blocks, transactions, accounts, and other on-chain data. The XION network is supported by multiple community-operated explorers, helping developers, validators, and end-users track on-chain activity.

We maintain a list of recommended block explorers for both **mainnet** and **testnet** in the [Cosmos chain registry](https://github.com/cosmos/chain-registry).

* **Mainnet Explorers**\
  [XION Mainnet explorer in the chain registry](https://github.com/cosmos/chain-registry/blob/master/xion/chain.json#L226-L257)
* **Testnet Explorers**\
  [XION Testnet explorers in the chain registry](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/chain.json#L184-L202)

***

### Mainnet Explorers

#### Mintscan

* **URL**: [https://www.mintscan.io/xion](https://www.mintscan.io/xion)
* **Transaction Page**: `https://www.mintscan.io/xion/txs/${txHash}`
* **Account Page**: `https://www.mintscan.io/xion/account/${accountAddress}`

#### PingPub

* **URL**: [https://ping.pub/xion](https://ping.pub/xion)
* **Transaction Page**: `https://ping.pub/xion/tx/${txHash}`
* **Account Page**: `https://ping.pub/xion/account/${accountAddress}`

#### Staking Explorer

* **URL**: [https://staking-explorer.com/explorer/xion](https://staking-explorer.com/explorer/xion)
* **Transaction Page**: `https://staking-explorer.com/transaction.php?chain=xion&tx=${txHash}`
* **Account Page**: `https://staking-explorer.com/account.php?chain=xion&addr=${accountAddress}`

#### NodeStake

* **URL**: [https://explorer.nodestake.org/xion](https://explorer.nodestake.org/xion)
* **Transaction Page**: `https://explorer.nodestake.org/xion/tx/${txHash}`
* **Account Page**: `https://explorer.nodestake.org/xion/account/${accountAddress}`

#### Nodes.Guru

* **URL**: [https://xion.explorers.guru](https://xion.explorers.guru)
* **Transaction Page**: `https://xion.explorers.guru/transactions/${txHash}`
* **Account Page**: `https://xion.explorers.guru//account/${accountAddress}`

***

### Testnet Explorers

#### XION Foundation

* **URL**: [https://explorer.burnt.com/xion-testnet-1](https://explorer.burnt.com/xion-testnet-1)
* **Transaction Page**: `https://explorer.burnt.com/xion-testnet-1/tx/${txHash}`
* **Account Page**: `https://explorer.burnt.com/xion-testnet-1/account/${accountAddress}`

#### ITRocket

* **URL**: [https://testnet.itrocket.net/burnt](https://testnet.itrocket.net/burnt)
* **Transaction Page**: `https://testnet.itrocket.net/burnt/tx/${txHash}`
* **Account Page**: `https://testnet.itrocket.net/burnt/account/${accountAddress}`

#### Nodes.Guru

* **URL**: [https://testnet.xion.explorers.guru](https://testnet.xion.explorers.guru)
* **Transaction Page**: `https://testnet.xion.explorers.guru/transactions/${txHash}`
* **Account Page**: `https://testnet.xion.explorers.guru//account/${accountAddress}`



_(Note: Additional testnet explorers may be available in the_ [_chain registry’s testnet file_](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/chain.json)_.)_

***

### How to Add or Update Explorers

If you operate a block explorer or notice outdated explorer information, please consider opening a Pull Request (PR) in the [Cosmos chain registry](https://github.com/cosmos/chain-registry). You can update or add explorers by modifying the relevant XION files:

* [Mainnet `xion/chain.json`](https://github.com/cosmos/chain-registry/blob/master/xion/chain.json)
* [Testnet `xiontestnet/chain.json`](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/chain.json)

For more details on how to submit a PR or update existing entries, refer to the [Cosmos chain registry guidelines](https://github.com/cosmos/chain-registry#contributing).



If you have any questions or need more help, feel free to reach out in our [Discord channel](https://discord.gg/...) or open an issue on [GitHub](https://github.com/burnt-labs/xion).
