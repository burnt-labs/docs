# Deploying Smart Contracts to Mainnet

This guide will walk you through the process of deploying your smart contract to the XION mainnet.

## Prerequisites

Before deploying to mainnet, ensure you have:

1. Thoroughly tested your contract on testnet
2. Conducted security audits if necessary
3. XION tokens in your wallet for deployment fees
4. The compiled `.wasm` file of your smart contract (must be identical to the version deployed and tested on testnet)

## Required Governance Steps

Contract deployment on XION mainnet requires following a governance process. Here are the mandatory steps:

1. **Create a Signaling Proposal**
   - Post your proposal in the [Mainnet Contract Deployments category](https://discourse.xion.burnt.com/c/govpros/mainnet-contract-deployments/6) on the XION forum
   - Announce your signaling proposal in the "XION Developer Ecosystem" Telegram channel
   - Wait for community feedback and respond accordingly

2. **Submit Governance Proposal**
   - After a 3-day waiting period from your signaling proposal
   - Go to [staking.burnt.com/governance](https://staking.burnt.com/governance/) to submit the formal governance proposal on-chain
   - Announce the governance proposal in the "XION Developer Ecosystem" Telegram channel
   - Monitor the proposal's voting period

3. **Submit Pull Request**
   - Once the governance proposal passes
   - Follow the instructions to submit a pull request against the [XION contracts repository](https://github.com/burnt-labs/contracts)
   - Complete all items in the pull request checklist
   - Wait for review and approval from the team
