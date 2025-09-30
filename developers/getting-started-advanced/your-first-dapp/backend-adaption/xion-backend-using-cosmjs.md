# Interact with XION via your Backend Service

Learn how to interact with the XION blockchain from a backend environment using **CosmJS**. This guide covers setting up your environment, connecting to the XION network, querying blockchain data, and executing transactions.



## Setting Up Your Environment

First, create a new project and install the necessary dependencies:

```bash
# Create project directory
mkdir xion-blockchain-example
cd xion-blockchain-example

# Initialize project
npm init -y

# Install required dependencies
npm install @cosmjs/stargate @cosmjs/proto-signing @cosmjs/encoding dotenv

# For TypeScript support (recommended)
npm install --save-dev typescript ts-node @types/node
npx tsc --init
```



## Managing Credentials Securely

To securely store and access your wallet credentials, we’ll use environment variables. This approach helps keep sensitive information safe and prevents it from being hardcoded in your project. Start by creating a `.env` file in the root directory of your project and add the following:

```
XION_MNEMONIC="your wallet mnemonic phrase here"
XION_RPC_URL=https://rpc.xion-testnet-2.burnt.com
XION_CHAIN_ID=xion-testnet-2
```

{% hint style="info" %}
See [public-endpoints-and-resources.md](../../section-overview/public-endpoints-and-resources.md "mention")for list of RPC endpoints for each network.
{% endhint %}

Create a file named `config.js` to load these environment variables:

```javascript
// config.js
require('dotenv').config();

module.exports = {
  // Network configuration
  XION_RPC_URL: process.env.XION_RPC_URL || "https://rpc.xion-testnet-2.burnt.com",
  CHAIN_ID: process.env.XION_CHAIN_ID || "xion-testnet-2",
  
  // Wallet configuration
  MNEMONIC: process.env.XION_MNEMONIC,
  
  // Make sure we have required values
  validateConfig: function() {
    if (!this.MNEMONIC) {
      throw new Error("XION_MNEMONIC is required in .env file");
    }
    if (!this.XION_RPC_URL) {
      throw new Error("XION_RPC_URL is required in .env file");
    }
    return true;
  }
};
```

{% hint style="warning" %}
**Security Note**: Add `.env` to your `.gitignore` file to prevent accidentally committing sensitive information to your git repository.
{% endhint %}

```
# .gitignore
node_modules/
.env
```



## Connecting to XION Network

Now, set up a connection to the XION blockchain for querying data and submitting transactions. Create a file named `xion-connect.js` (or `xion-connect.ts` for TypeScript).

```javascript
// xion-connect.js
const { StargateClient, SigningStargateClient, GasPrice } = require("@cosmjs/stargate");
const { DirectSecp256k1HdWallet } = require("@cosmjs/proto-signing");
const config = require('./config');

// Validate configuration before proceeding
config.validateConfig();

/**
 * Creates a read-only client for querying the blockchain
 * This client can be used for all operations that don't require signing
 */
async function getQueryClient() {
  return await StargateClient.connect(config.XION_RPC_URL);
}

/**
 * Creates a signing client that can perform transactions
 * By default uses the mnemonic from environment variables
 * Can optionally accept a different mnemonic for multi-wallet scenarios
 */
async function getSigningClient(mnemonic = config.MNEMONIC) {
  // Create wallet from mnemonic
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, {
    prefix: "xion" // XION address prefix
  });
  
  // Create and return a signing client
  return await SigningStargateClient.connectWithSigner(
    config.XION_RPC_URL,
    wallet,
    { gasPrice: GasPrice.fromString("0.025uxion") }
  );
}

module.exports = {
  getQueryClient,
  getSigningClient,
  CHAIN_ID: config.CHAIN_ID
};
```



## Querying the Blockchain

Next, retrieve data from the XION blockchain using read-only operations that do not alter the blockchain state. Create a file named `xion-queries.js`:

```javascript
// xion-queries.js
const { getQueryClient } = require("./xion-connect");

/**
 * Gets an account's balance for a specific token
 * 
 * @param {string} address - The account address to check
 * @param {string} denom - The token denomination (default: uxion)
 * @returns {string} The account balance amount
 */
async function getBalance(address, denom = "uxion") {
  const client = await getQueryClient();
  const balance = await client.getBalance(address, denom);
  return balance.amount;
}

/**
 * Gets detailed account information
 * 
 * @param {string} address - The account address to check
 * @returns {object} Account information including sequence numbers
 */
async function getAccount(address) {
  const client = await getQueryClient();
  return await client.getAccount(address);
}

/**
 * Gets transaction details by hash
 * 
 * @param {string} hash - The transaction hash
 * @returns {object} Complete transaction details
 */
async function getTransaction(hash) {
  const client = await getQueryClient();
  return await client.getTx(hash);
}

/**
 * Gets block data at a specific height
 * If no height is provided, gets the latest block
 * 
 * @param {number} height - The block height (optional)
 * @returns {object} Block data including transactions
 */
async function getBlock(height) {
  const client = await getQueryClient();
  return await client.getBlock(height);
}

/**
 * Gets the current blockchain height
 * 
 * @returns {number} The current block height
 */
async function getChainHeight() {
  const client = await getQueryClient();
  return await client.getHeight();
}

/**
 * Queries a smart contract
 * 
 * @param {string} contractAddress - The contract address
 * @param {object} queryMsg - The query message in JSON format
 * @returns {object} Query result from the contract
 */
async function queryContract(contractAddress, queryMsg) {
  const client = await getQueryClient();
  return await client.queryContractSmart(contractAddress, queryMsg);
}

module.exports = {
  getBalance,
  getAccount,
  getTransaction,
  getBlock,
  getChainHeight,
  queryContract
};
```



## Working with Wallets

We'll now work with XION blockchain wallets, including deriving addresses from mnemonics. Create a file named `xion-wallets.js`:

```javascript
// xion-wallets.js
const { DirectSecp256k1HdWallet } = require("@cosmjs/proto-signing");
const config = require('./config');

/**
 * Gets the address associated with the configured mnemonic
 * 
 * @returns {Promise<string>} The wallet address
 */
async function getMyAddress() {
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(config.MNEMONIC, {
    prefix: "xion" // XION address prefix
  });
  
  const [firstAccount] = await wallet.getAccounts();
  return firstAccount.address;
}

/**
 * Generates a new wallet (useful for creating recipient wallets for testing)
 * 
 * @returns {Promise<object>} Object containing mnemonic and address
 */
async function generateWallet() {
  const wallet = await DirectSecp256k1HdWallet.generate(24, {
    prefix: "xion" // XION address prefix
  });
  
  const [firstAccount] = await wallet.getAccounts();
  
  return {
    mnemonic: wallet.mnemonic,
    address: firstAccount.address
  };
}

/**
 * Gets address from a specific mnemonic
 * 
 * @param {string} mnemonic - The wallet mnemonic
 * @returns {Promise<string>} The wallet address
 */
async function getAddressFromMnemonic(mnemonic) {
  const wallet = await DirectSecp256k1HdWallet.fromMnemonic(mnemonic, {
    prefix: "xion" // XION address prefix
  });
  
  const [firstAccount] = await wallet.getAccounts();
  return firstAccount.address;
}

module.exports = {
  getMyAddress,
  generateWallet,
  getAddressFromMnemonic
};
```



## Executing Transactions

This section covers operations that modify blockchain state. Create a file named `xion-transactions.js`:

```javascript
// xion-transactions.js
const { getSigningClient } = require("./xion-connect");
const { getMyAddress } = require("./xion-wallets");
const config = require('./config');

/**
 * Sends tokens from your address to a recipient
 * Uses the mnemonic from environment variables by default
 * 
 * @param {string} recipientAddress - The recipient's address
 * @param {string} amount - The amount to send
 * @param {string} denom - The token denomination (default: uxion)
 * @param {string} memo - Optional transaction memo
 * @returns {object} Transaction result with hash and gas usage
 */
async function sendTokens(recipientAddress, amount, denom = "uxion", memo = "") {
  const client = await getSigningClient();
  const senderAddress = await getMyAddress();
  
  const result = await client.sendTokens(
    senderAddress,
    recipientAddress,
    [{ denom, amount }],
    "auto", // fee calculation
    memo || "Transfer via XION backend"
  );
  
  return {
    transactionHash: result.transactionHash,
    gasUsed: result.gasUsed,
    gasWanted: result.gasWanted
  };
}

/**
 * Executes a smart contract function
 * 
 * @param {string} contractAddress - The contract address
 * @param {object} msg - The execute message in JSON format
 * @param {array} funds - Optional funds to send with the execution
 * @returns {object} Transaction result with hash and gas usage
 */
async function executeContract(contractAddress, msg, funds = []) {
  const client = await getSigningClient();
  const senderAddress = await getMyAddress();
  
  const result = await client.executeContract(
    senderAddress,
    contractAddress,
    msg,
    "auto",
    "Execute contract via XION backend",
    funds
  );
  
  return {
    transactionHash: result.transactionHash,
    gasUsed: result.gasUsed,
    gasWanted: result.gasWanted
  };
}

/**
 * Uploads a smart contract WASM binary
 * 
 * @param {Uint8Array} wasmBinary - The contract WASM binary
 * @returns {object} Upload result with code ID and transaction hash
 */
async function uploadContract(wasmBinary) {
  const client = await getSigningClient();
  const senderAddress = await getMyAddress();
  
  const result = await client.upload(
    senderAddress,
    wasmBinary,
    "auto"
  );
  
  return {
    codeId: result.codeId,
    transactionHash: result.transactionHash
  };
}

/**
 * Instantiates a smart contract from an uploaded code ID
 * 
 * @param {number} codeId - The uploaded contract code ID
 * @param {object} initMsg - Initialization message in JSON format
 * @param {string} label - Human-readable label for the contract
 * @param {array} funds - Optional funds to send with instantiation
 * @returns {object} Result with contract address and transaction hash
 */
async function instantiateContract(codeId, initMsg, label, funds = []) {
  const client = await getSigningClient();
  const senderAddress = await getMyAddress();
  
  const result = await client.instantiate(
    senderAddress,
    codeId,
    initMsg,
    label,
    "auto",
    { funds }
  );
  
  return {
    contractAddress: result.contractAddress,
    transactionHash: result.transactionHash
  };
}

module.exports = {
  sendTokens,
  executeContract,
  uploadContract,
  instantiateContract
};
```



## Usage Examples

Create a file named `examples.js` which will hold practical examples of how to use the functions created above:

```javascript
// examples.js
const fs = require('fs');
const { getMyAddress } = require('./xion-wallets');
const { getBalance, getAccount, queryContract, getChainHeight } = require('./xion-queries');
const { sendTokens, executeContract, uploadContract, instantiateContract } = require('./xion-transactions');

// Main function to run all examples
async function runExamples() {
  try {
    // Get blockchain height to verify connection
    const height = await getChainHeight();
    console.log(`Connected to XION blockchain at height: ${height}`);
    
    // Get your wallet address from the environment mnemonic
    const myAddress = await getMyAddress();
    console.log(`Your wallet address: ${myAddress}`);
    
    // Check your balance
    const balance = await getBalance(myAddress);
    console.log(`Your balance: ${balance} uxion`);
    
    // Example: Send tokens
    if (parseInt(balance) > 1000) {
      console.log("\n--- Token Transfer Example ---");
      
      // For testing, you could create a recipient address
      // In production, you'd use a real recipient address
      const { address: recipientAddress } = await require('./xion-wallets').generateWallet();
      console.log(`Generated test recipient address: ${recipientAddress}`);
      
      // Send a small amount of tokens
      const sendResult = await sendTokens(recipientAddress, "1000");
      console.log(`Transfer complete! Transaction hash: ${sendResult.transactionHash}`);
      console.log(`Gas used: ${sendResult.gasUsed}`);
    } else {
      console.log("Insufficient balance for transfer example");
    }
    
    // Example: Query a contract (replace with an actual contract address)
    /*
    console.log("\n--- Contract Query Example ---");
    const contractAddress = "your_contract_address";
    const queryMsg = { get_count: {} }; // Example for a counter contract
    const queryResult = await queryContract(contractAddress, queryMsg);
    console.log("Contract query result:", queryResult);
    
    // Example: Execute a contract function
    console.log("\n--- Contract Execution Example ---");
    const executeMsg = { increment: {} }; // Example for a counter contract
    const executeResult = await executeContract(contractAddress, executeMsg);
    console.log(`Contract execution complete! Transaction hash: ${executeResult.transactionHash}`);
    */
    
    // Example: Upload and instantiate a contract
    /*
    console.log("\n--- Contract Deployment Example ---");
    const wasmBinary = fs.readFileSync('./counter.wasm');
    const uploadResult = await uploadContract(wasmBinary);
    console.log(`Contract uploaded with code ID: ${uploadResult.codeId}`);
    
    const initMsg = { count: 0 };
    const instantiateResult = await instantiateContract(
      uploadResult.codeId,
      initMsg,
      "My Counter Contract"
    );
    console.log(`Contract instantiated at address: ${instantiateResult.contractAddress}`);
    */
    
  } catch (error) {
    console.error("Error running examples:", error);
    console.error(error.stack);
  }
}

// Execute the examples
runExamples();
```

To run the examples:

```bash
node examples.js
```

## Best Practices for Backend Implementation

When implementing these functions in a production backend environment:

1. **Secure Mnemonic Storage**:
   * Never store mnemonics in plaintext
   * Use a secure vault, HSM, or encrypted storage
   * Consider key management services
2.  **Error Handling**:

    ```javascript
    try {
      const result = await sendTokens(mnemonic, recipientAddress, amount);
      // Process successful transaction
    } catch (error) {
      if (error.message.includes("insufficient funds")) {
        // Handle specific error cases
      } else {
        // Log the error details
        console.error("Transaction failed:", error);
      }
    }
    ```
3. **Transaction Batching**:
   * For multiple operations, consider batching transactions when possible
4.  **Retrying Failed Transactions**:

    ```javascript
    async function sendTokensWithRetry(mnemonic, recipient, amount, maxRetries = 3) {
      for (let attempt = 1; attempt <= maxRetries; attempt++) {
        try {
          return await sendTokens(mnemonic, recipient, amount);
        } catch (error) {
          if (attempt === maxRetries) throw error;
          console.log(`Attempt ${attempt} failed, retrying...`);
          await new Promise(resolve => setTimeout(resolve, 2000));
        }
      }
    }
    ```

### Common Queries and Transactions Examples

#### Token Transfers

```javascript
// Transfer tokens
const transferResult = await sendTokens(
  "your wallet mnemonic",
  "recipient_burnt_address",
  "1000000", // 1 XION (1,000,000 uxion)
  "uxion"
);
console.log(`Transfer hash: ${transferResult.transactionHash}`);
```

#### Account Queries

```javascript
// Get multiple token balances
async function getTokenBalances(address, denomList) {
  const balances = {};
  for (const denom of denomList) {
    balances[denom] = await getBalance(address, denom);
  }
  return balances;
}

// Usage
const balances = await getTokenBalances("burnt_address", ["uxion", "uatom"]);
console.log(balances);
```

#### Smart Contract Interactions

```javascript
// Query a token contract balance
async function getTokenBalance(contractAddress, address) {
  const queryMsg = { 
    balance: { 
      address: address 
    } 
  };
  const result = await queryContract(contractAddress, queryMsg);
  return result.balance;
}

// Transfer tokens via a token contract
async function transferTokens(mnemonic, contractAddress, recipient, amount) {
  const executeMsg = {
    transfer: {
      recipient: recipient,
      amount: amount
    }
  };
  
  return await executeContract(mnemonic, contractAddress, executeMsg);
}
```

#### Getting Transaction History

```javascript
const { getQueryClient } = require("./xion-connect");

// Get transaction history for an address
// Note: This uses a more advanced query approach that may require
// additional dependencies depending on the node's capabilities
async function getAddressTransactions(address, limit = 10) {
  const client = await getQueryClient();
  
  // Query sent transactions
  const sentQuery = `message.sender='${address}'`;
  const sentTxs = await client.searchTx(sentQuery, { limit });
  
  // Query received transactions
  const receivedQuery = `transfer.recipient='${address}'`;
  const receivedTxs = await client.searchTx(receivedQuery, { limit });
  
  // Combine and sort by height (descending)
  const allTxs = [...sentTxs, ...receivedTxs]
    .sort((a, b) => b.height - a.height)
    .slice(0, limit);
    
  return allTxs;
}
```
