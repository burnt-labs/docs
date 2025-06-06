# Public Endpoints & Resources

This page provides an overview of publicly accessible endpoints and resources available for interacting with the **XION** network. These endpoints allow developers to query blockchain data, submit transactions, and integrate apps with the XION ecosystem.

While the XION Foundation maintains official endpoints for all networks, the broader XION community also provides alternative resources for each network. To ensure transparency and accessibility, these community-supported resources are cataloged in the [**Cosmos Chain Registry**](https://github.com/cosmos/chain-registry), a central repository where anyone can contribute by submitting pull requests (PRs) to add or update endpoints.

For the most up-to-date and reliable network resources, it is always recommended to refer to the [**Cosmos Chain Registry**](https://github.com/cosmos/chain-registry), as it serves as the primary source for tracking official and community-managed endpoints.

### Mainnet

The XION Mainnet is the live production network where fully deployed apps interact with real users and assets. It is designed for stability, security, and high-performance execution of smart contracts. Developers should ensure their apps are thoroughly tested on the testnet before deploying to the mainnet to minimize risks.

The table below lists the available resources for this network.

<table data-full-width="false"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td>RPC Endpoint</td><td><a href="https://rpc.xion-mainnet-1.burnt.com">https://rpc.xion-mainnet-1.burnt.com:443</a></td></tr><tr><td>Rest Endpoint</td><td><a href="https://api.xion-mainnet-1.burnt.com">https://api.xion-mainnet-1.burnt.com</a></td></tr><tr><td>gRPC</td><td><a href="https://grpc.xion-mainnet-1.burnt.com/">https://grpc.xion-mainnet-1.burnt.com</a></td></tr><tr><td>Websocket</td><td>wss://rpc.xion-mainnet-1.burnt.com/websocket</td></tr><tr><td>Chain ID</td><td>xion-mainnet-1</td></tr><tr><td>Block Explorer</td><td><a href="https://explorer.burnt.com/xion-mainnet-1">https://explorer.burnt.com/xion-mainnet-1</a></td></tr><tr><td>Genesis File</td><td><a href="https://github.com/burnt-labs/burnt-networks/blob/main/mainnet/xion-mainnet-1/genesis.json">https://github.com/burnt-labs/burnt-networks/blob/main/mainnet/xion-mainnet-1/genesis.json</a></td></tr><tr><td>Persistent Peers</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/xion/chain.json#L95-L136">https://github.com/cosmos/chain-registry/blob/master/xion/chain.json#L95-L136</a></td></tr><tr><td>Seeds</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/xion/chain.json#L78-L93">https://github.com/cosmos/chain-registry/blob/master/xion/chain.json#L78-L93</a></td></tr></tbody></table>

For additional community-supported RPC, API, and gRPC endpoints, refer to the [**XION Mainnet Chain Registry**](https://github.com/cosmos/chain-registry/blob/master/xion/chain.json). This registry contains the latest publicly available infrastructure contributed by the community, ensuring developers have access to a diverse and up-to-date set of endpoint options.

### Testnet

The XION Testnet is designed for experimentation and refining apps before deploying to Mainnet. It provides a safe environment for developers to test functionality, optimize performance, and resolve issues without affecting real assets.

The table below lists the available resources for this network.

<table><thead><tr><th width="238"></th><th></th></tr></thead><tbody><tr><td>RPC Endpoint</td><td><a href="https://rpc.xion-testnet-2.burnt.com">https://rpc.xion-testnet-2.burnt.com:443</a></td></tr><tr><td>Rest Endpoint</td><td><a href="https://api.xion-testnet-2.burnt.com">https://api.xion-testnet-2.burnt.com</a></td></tr><tr><td>gRPC</td><td>grpc.xion-testnet-2.burnt.com:443</td></tr><tr><td>Websocket</td><td>wss://rpc.xion-testnet-2.burnt.com:443/websocket</td></tr><tr><td>Chain ID</td><td>xion-testnet-2</td></tr><tr><td>Block Explorer</td><td><a href="https://www.mintscan.io/xion-testnet">https://www.mintscan.io/xion-testnet</a></td></tr><tr><td>Genesis File</td><td><a href="https://raw.githubusercontent.com/burnt-labs/xion-testnet-2/config/genesis.json">https://raw.githubusercontent.com/burnt-labs/xion-testnet-2/config/genesis.json</a></td></tr><tr><td>Persistent Peers</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/chain.json#L85-L111">https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/chain.json#L85-L111</a></td></tr><tr><td>Seeds</td><td><a href="https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/chain.json#L78-L84">https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/chain.json#L78-L84</a></td></tr></tbody></table>

For additional community-supported RPC, API, and gRPC endpoints, refer to the [**XION Testnet Chain Registry**](https://github.com/cosmos/chain-registry/blob/master/testnets/xiontestnet2/chain.json#L114-L119)**.** This registry provides the latest publicly available infrastructure contributed by the community, ensuring developers have access to diverse and up-to-date endpoint options.

### Localnet

If you choose to run a local testnet using our standard Docker setup, the default configuration will match the information provided in the table below.

<table><thead><tr><th width="325"></th><th></th></tr></thead><tbody><tr><td>RPC Endpoint</td><td><a href="http://localhost:26657">http://localhost:26657</a></td></tr><tr><td>Rest Endpoint</td><td><a href="http://localhost:1317">http://localhost:1317</a></td></tr><tr><td>chain_id</td><td>xion-local-testnet-1</td></tr><tr><td>Block Explorer</td><td>N/A</td></tr></tbody></table>
