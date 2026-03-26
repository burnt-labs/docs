# Deploying to Xion Mainnet

Xion **Mainnet** is a **permissioned** environment designed to host secure and community approved decentralized applications (apps). Unlike **Testnet**, deploying to **Mainnet** requires governance approval to ensure only **trusted, high-quality** contracts are stored on-chain.

This section will guide you through the **Mainnet deployment process**, including governance requirements, security best practices, and contract storage policies.

## **Key Differences Between Testnet & Mainnet**

| Feature             | Testnet                      | Mainnet                      |
| ------------------- | ---------------------------- | ---------------------------- |
| **Deployment**      | Open to all (permissionless) | Requires governance approval |
| **Contract Audits** | Optional but recommended     | **Mandatory** for approval   |
| **Security**        | Experimental environment     | High-security environment    |
| **Real Assets**     | No (test tokens)             | Yes (real value at stake)    |

## **Mainnet Deployment Steps**

To deploy a contract on **Mainnet**, developers must:

* **Thoroughly test your contract(s) on Testnet.**
* **Undergo a security audit** to validate the contract's safety.
* **Create a** [**Signaling Proposal**](https://discourse.xion.burnt.com/c/govpros/mainnet-contract-deployments/6) and inform the community and encourage them to give feedback
* **Submit a** [**governance proposal**](https://staking.burnt.com/governance) for community approval.
* **Once approved, your contract will be stored on Mainnet.**

For detailed steps on deploying your **Contracts** to **Mainnet** go through the following guide:

### Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Before deploying to mainnet, ensure you have:

1. Thoroughly tested your contract on testnet
2. Conducted security audits if necessary
3. XION tokens in your wallet for deployment fees
4. The compiled `.wasm` file of your smart contract (must be identical to the version deployed and tested on testnet)

### Required Governance Steps <a href="#required-governance-steps" id="required-governance-steps"></a>

Contract deployment on XION mainnet requires following a governance process. Here are the mandatory steps:

1. **Create a Signaling Proposal**
   * Post your proposal in the [Mainnet Contract Deployments category](https://discourse.xion.burnt.com/c/govpros/mainnet-contract-deployments/6) on the XION forum
   * Announce your signaling proposal in the ["XION Developer Ecosystem" Telegram channel](https://t.me/+5KA8xBuU5wYzZjU0)
   * Wait for community feedback and respond accordingly
2. **Submit Governance Proposal**
   * After a 3-day waiting period from your signaling proposal
   * Go to [staking.burnt.com/governance](https://staking.burnt.com/governance/) to submit the formal governance proposal on-chain
   * Announce the governance proposal in the ["XION Developer Ecosystem" Telegram channel](https://t.me/+5KA8xBuU5wYzZjU0)
   * Monitor the proposal's voting period
3. **Submit Pull Request**
   * Once the governance proposal passes
   * Follow the instructions to submit a pull request against the [XION contracts repository](https://github.com/burnt-labs/contracts)
   * Complete all items in the pull request checklist
   * Wait for review and approval from the team
