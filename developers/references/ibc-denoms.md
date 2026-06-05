# IBC Denoms on XION Networks

IBC denoms are unique identifiers for tokens that can be transferred between different blockchains using the IBC protocol.  A **denom** typically looks something like `ibc/...`, where `...` is a hash representing the specific token and its path through the IBC channels.

Below, we provide a list of popular IBC denoms for each network, along with an endpoint to access all available IBC denoms within the respective network.

## Mainnet

Here is a list of IBC denoms currently active on XION's **Mainnet**:

<table><thead><tr><th>Denom</th><th>Symbol</th><th width="186">Network</th><th>IBC Channel Info</th></tr></thead><tbody><tr><td>ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349</td><td>UUSDC</td><td>Noble</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/_IBC/noble-xion.json">View</a></td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-mainnet-1.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/xion/assetlist.json) in the chain registry.


## Testnet

Here is a list of IBC denoms currently active on XION's **Testnet**:

<table><thead><tr><th>Denom</th><th>Symbol</th><th width="186">Network</th><th>IBC Channel Info</th></tr></thead><tbody><tr><td>ibc/6490A7EAB61059BFC1CDDEB05917DD70BDF3A611654162A1A47DB930D40D8AF4</td><td>UUSDC</td><td>Noble</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/_IBC/nobletestnet-xiontestnet2.json">View</a></td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-testnet-2.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/assetlist.json) in the chain registry.
