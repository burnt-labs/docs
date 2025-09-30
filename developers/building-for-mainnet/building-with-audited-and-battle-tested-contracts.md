# Building with Audited & Battle-Tested Contracts

To ensure security, efficiency, and a smoother path to **Mainnet** approval, developers should prioritize using **audited** and **battle-tested** smart contracts whenever possible. Instead of building everything from scratch, integrating **trusted, widely used contracts** as building blocks can reduce risks, lower costs, and increase the likelihood of governance approval.

## **Why Use Audited & Battle-Tested Contracts?**

### **Security & Reliability**

Audited contracts provide verified security, having undergone professional reviews to identify and eliminate vulnerabilities. Battle-tested contracts offer proven reliability, having been extensively used and validated by the community over time.

A prime example is the [**cw-plus**](https://github.com/CosmWasm/cw-plus) collection of contracts. While this repository previously housed all [**cw-20**](https://github.com/CosmWasm/cw-tokens) token contracts and various [**NFT**](https://github.com/CosmWasm/cw-nfts) standards, these have since been migrated to dedicated repositories. The cw-plus repository continues to serve as a valuable reference point, directing developers to these essential, community-proven contract implementations.

### **Lower Costs & Faster Development**

Using pre-audited contracts offers significant advantages across your development lifecycle. First and foremost, it dramatically reduces costs - security audits are expensive, and building on verified code minimizes the amount of new code requiring review. This established foundation also streamlines governance approval for Mainnet deployment, as committees can more readily trust contracts with proven security credentials.

Beyond cost savings, these battle-tested solutions accelerate development by providing ready-made functionality out of the box. Instead of reinventing core components, your team can focus their energy on creating unique, value-added features that differentiate your app. The cw-plus contracts exemplify this efficiency, offering standardized implementations that have been optimized through extensive real-world use.



## **Where to Find Reusable & Audited Contracts**

Developers can leverage the following repositories to find contracts that are **audited, widely used, or community-validated**:

### **Xion Mainnet Contracts Repository**

* [**Xion Contracts Repository**](https://github.com/burnt-labs/contracts) – This repository lists **all contracts deployed on Mainnet** along with their **Code IDs**, making it easy for developers to instantiate existing contracts instead of deploying new ones.
* **Before using a contract on Mainnet, you should first test its equivalent on Testnet.** Ensure your integration works correctly before submitting a governance proposal for Mainnet deployment. Within the contracts repository you will see the version being used under the release column and the link will take you to the actual code you will need to deploy on Testnet.

### **Commonly Used CosmWasm Contracts**

* [**CW-Plus**](https://github.com/CosmWasm/cw-plus) – Standardized CosmWasm contracts, including CW20 (tokens), CW721 (NFTs), multisigs, and more.
* [**CW-Minus**](https://github.com/CosmWasm/cw-minus) – Lightweight alternatives to CW-Plus contracts with reduced complexity.
* [**CW-Tokens**](https://github.com/CosmWasm/cw-tokens) – Specialized token contracts that expand on CW20 and CW721 standards.
* [**Public Awesome CW-NFTs**](https://github.com/public-awesome/cw-nfts) – A collection of NFT-specific smart contracts that follow the CW721 standard.

These repositories contain **battle-tested** smart contracts that have been used across multiple projects in the Cosmos ecosystem. The list does not end here and so we encourage exploring additional repositories for specialized needs.&#x20;
