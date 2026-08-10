# Oracles

Oracles are external data providers that enable smart contracts to interact with real-world data. In the Xion ecosystem, oracles play a crucial role in providing off-chain information such as asset prices, weather data, or sports scores, allowing developers to build dynamic and responsive decentralized applications (apps).

## Why Are Oracles Required?

Smart contracts operate within a deterministic environment, meaning that if you start from **block one** and execute all transactions to the **current block,** the result must always be the same. This determinism ensures blockchain security and consensus but also imposes a limitation as smart contracts cannot directly access data from the web or any external source that changes over time. Since web data is dynamic, integrating it directly into a blockchain would break its fundamental requirement for consistency and reproducibility.

To bridge this gap, oracles act as intermediaries that fetch, verify, and relay external data into the blockchain. By doing so, oracles enable smart contracts to securely access real-world information without compromising the deterministic nature of blockchain execution.

## Supported Oracles on Xion

Xion currently supports **Pyth** as its primary oracle provider. Pyth delivers price feeds from institutional grade market data sources, enabling smart contracts to access accurate and real-time price information without relying on centralized intermediaries. Pyth data feeds are accessible via the Xion **Testnet** and **Mainnet**.
