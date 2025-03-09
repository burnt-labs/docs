# IBC Denoms on XION Networks

IBC denoms are unique identifiers for tokens that can be transferred between different blockchains using the IBC protocol.  A **denom** typically looks something like `ibc/...`, where `...` is a hash representing the specific token and its path through the IBC channels.

Below, we provide a list of popular IBC denoms for each network, along with an endpoint to access all available IBC denoms within the respective network.

## Mainnet

Here is a list of IBC denoms currently active on XION's **Mainnet**:

<table><thead><tr><th>Denom</th><th>Symbol</th><th width="186">Network</th><th>IBC Channel Info</th></tr></thead><tbody><tr><td>ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349</td><td>USDC</td><td>Noble</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/xion/assetlist.json#L60C11-L68C12">View</a></td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-mainnet-1.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/xion/assetlist.json) in the chain registry.



## Testnet 1

Here is a list of IBC denoms currently active on XION's **Testnet-1**:

<table><thead><tr><th>Denom</th><th>Symbol</th><th width="186">Network</th><th>IBC Channel Info</th></tr></thead><tbody><tr><td>ibc/57097251ED81A232CE3C9D899E7C8096D6D87EF84BA203E12E424AA4C9B57A64</td><td>UUSDC</td><td>Noble</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/assetlist.json#L156-L167">View</a></td></tr><tr><td>ibc/33517D439F5E545A1AAB148FAE43AAE17CF68FFB9BC97AE0048A3E3B64518C58</td><td>UAXL</td><td>Axelar</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/assetlist.json#L57-L68">View</a></td></tr><tr><td>ibc/484532EB74866F3FB8A71F909F7B1B470FE2E66313DA0A1F9EE5B7C5C046D195</td><td>UOSMO</td><td>Osmosis</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/assetlist.json#L106-L117">View</a></td></tr><tr><td>ibc/05314A48723E06A1B1B666066B6BEC89F3708E8854DF2E5E9193387AA9653036</td><td>INJ</td><td>Injective</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/assetlist.json#L208-L218">View</a></td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-testnet-1.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet/assetlist.json) in the chain registry.
