# IBC Relayers & Cross-Chain Communication

The Inter-Blockchain Communication (IBC) protocol enables XION to interoperate with other blockchains in the Cosmos ecosystem and beyond. This document outlines XION's current IBC connections, and provides information on IBC token denominations.



## Established IBC Connections <a href="#established-ibc-connections" id="established-ibc-connections"></a>

XION maintains active IBC connections with multiple networks. Below are the details of the currently established channels.

### Mainnet Connections <a href="#testnet-connections" id="testnet-connections"></a>

| Destination Chain | Source Channel | Destination Channel | IBC Channel Info                                                                |
| ----------------- | -------------- | ------------------- | ------------------------------------------------------------------------------- |
| axelar            | cchannel-3     | channel-161         | [View](https://assets.xion.burnt.com/chain-registry/_IBC/axelar-xion.json)      |
| elys              | channel-9      | cchannel-8          | [View](https://assets.xion.burnt.com/chain-registry/_IBC/elys-xion.json)        |
| injective         | channel-4      | channel-387         | [View](https://assets.xion.burnt.com/chain-registry/_IBC/injective-xion.json)   |
| kyve              | channel-5      | channel-7           | [View](https://assets.xion.burnt.com/chain-registry/_IBC/kyve-xion.json)        |
| noble             | channel-2      | channel-113         | [View](https://assets.xion.burnt.com/chain-registry/_IBC/noble-xion.json)       |
| osmosis           | channel-1      | channel-89321       | [View](https://assets.xion.burnt.com/chain-registry/_IBC/osmosis-xion.json)     |
| quicksilver       | channel-8      | channel-229         | [View](https://assets.xion.burnt.com/chain-registry/_IBC/quicksilver-xion.json) |

### Testnet Connections <a href="#testnet-connections" id="testnet-connections"></a>

| Destination Chain | Source Channel | Destination Channel | IBC Channel Info                                                                                    |
| ----------------- | -------------- | ------------------- | --------------------------------------------------------------------------------------------------- |
| nobletestnet      | channel-3      | channel-333         | [View](https://assets.xion.burnt.com/chain-registry/testnets/_IBC/nobletestnet-xiontestnet2.json)   |
| oosmosistestnet   | channel-2      | channel-10231       | [View](https://assets.xion.burnt.com/chain-registry/testnets/_IBC/osmosistestnet-xiontestnet2.json) |



## Current IBC Tokens on XION <a href="#current-ibc-tokens-on-xion" id="current-ibc-tokens-on-xion"></a>

### Mainnet IBC Tokens

The following table lists the IBC tokens currently available on XION **Mainnet**:

<table><thead><tr><th width="112.359375">Symbol</th><th width="109.796875">Base Denom</th><th width="131.73046875">Source Chain</th><th>IBC Denom on XION</th></tr></thead><tbody><tr><td>USDC</td><td>uusdc</td><td>Noble</td><td>ibc/F082B65C88E4B6D5EF1DB243CDA1D331D002759E938A0F5CD3FFDC5D53B3E349</td></tr><tr><td>OSMO</td><td>uosmo</td><td>Osmosis</td><td>ibc/0471F1C4E7AFD3F07702BEF6DC365268D64570F7C1FDC98EA6098DD6DE59817B</td></tr><tr><td>AXLUSDC</td><td>uusdc</td><td>Axelar</td><td>ibc/6490A7EAB61059BFC1CDDEB05917DD70BDF3A611654162A1A47DB930D40D8AF4</td></tr><tr><td>AXL</td><td>uaxl</td><td>Axelar</td><td>ibc/9463E39D230614B313B487836D13A392BD1731928713D4C8427A083627048DB3</td></tr><tr><td>axlWETH</td><td>weth-wei</td><td>Axelar</td><td>ibc/AAD7136DD626569C3DDE7C5F764968BB2E939875EFC568AE5712B62081850814</td></tr><tr><td>axlARB</td><td>arb-wei</td><td>Axelar</td><td>ibc/E706A0C6CACB374ADC2BCF6A74FE1B260840FC822E45DCB776DEA962A57FED30</td></tr><tr><td>KYVE</td><td>ukyve</td><td>Kyve</td><td>ibc/056EA54C3D9B49B3C0418955A27980A91DD4F210914BFE240A1DB19E27895ECA</td></tr><tr><td>ELYS</td><td>uelys</td><td>Elys Network</td><td>ibc/DBE9697AC1044255A305A2034AD360B4152632BFBFB5785234731F60196B9645</td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-mainnet-1.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/xion/assetlist.json) in the chain registry.



### Testnet IBC Tokens

The following table lists the IBC tokens currently available on XION **Testnet**:

<table><thead><tr><th width="120.79296875">Symbol</th><th width="122.109375">Base Denom</th><th width="155.8203125">Source Chain</th><th>IBC Denom on XION</th></tr></thead><tbody><tr><td>OSMO</td><td>UOSMOS</td><td>Osmosis</td><td>ibc/13B2C536BB057AC79D5616B8EA1B9540EC1F2170718CAFF6F0083C966FFFED0B</td></tr><tr><td>USDC</td><td>UUSDC</td><td>Noble</td><td>ibc/6490A7EAB61059BFC1CDDEB05917DD70BDF3A611654162A1A47DB930D40D8AF4</td></tr></tbody></table>

**Note:** This list is subject to change as new IBC denoms are established. You can find the full list of IBC denoms [here](https://api.xion-testnet-2.burnt.com/cosmos/bank/v1beta1/denoms_metadata). You can also view the [asset list](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/assetlist.json) in the chain registry.
