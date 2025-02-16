# Creating, Minting, and Interacting with a Token Factory Token

The **Token Factory module** provides a simple framework for creating and managing **custom native tokens**. It allows users to create, mint, distribute and interact with their own tokens seamlessly. Each token is uniquely tied to its creator’s address and follows the standardized format `factory/{creator address}/{subdenom}.`

The module is particularly useful for **developers** and **projects** looking to **issue chain-native assets** without the complexity of deploying custom smart contracts. Regardless of the token’s intended use the **Token Factory** streamlines the entire **lifecycle**, ensuring a secure and scalable approach to token management on XION.



## **Prerequisites**

Before proceeding, make sure you have the following:

1. **XION Daemon (`xiond`) Installed:**\
   Ensure that `xiond` is installed on your system. If not, follow the [Setting up your Local Development Environment](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/installation-prerequisites-setup-local-environment#xiond) guide to set it up.
2. **An Active Account:**\
   Set up an account using `xiond` by following the [Generate an account](https://docs.burnt.com/xion/developers/featured-guides/setup-local-environment/interact-with-xion-chain-setup-xion-daemon#generate-an-account) guide. You will use this account to execute transactions.
3. **A Funded Account:**\
   You need an account with sufficient **native XION tokens** to cover transaction fees. You can obtain testnet tokens through the [**XION testnet faucet**](../../section-overview/xion-testnet-1.md).



## **Creating a Token**

To create a new token using the **Token Factory module**, you would execute the following command:

```sh
xiond tx tokenfactory create-denom <your-denom> --from <wallet-name> --chain-id <chain-id> --node <node> --gas auto --gas-adjustment <gas-adjustment> --gas-prices <gas-prices> 
```

**Parameters:**

* `<your-denom>` – The unique token name (e.g., `customtoken`).
* `<wallet-name>` – Your configured wallet name in `xiond`.
* `<chain-id>` – The chain ID of the XION network.
* `<node>` – The ID or address of the blockchain node you are connecting to.
* `--gas auto` – Automatically estimates the gas required for the transaction.
* `<gas-adjustment>` – Adjusts the estimated gas amount by **30%** to account for possible variations in actual consumption.
* `<gas-prices>` – The fee rate for gas in **uxion** (the smallest unit of XION).

**Example:**

```sh
xiond tx tokenfactory create-denom mytoken --from mywallet --chain-id xion-testnet-1 --node https://rpc.xion-testnet-1.burnt.com:443 --gas auto --gas-prices 0.025uxion
```

The following is an example of the initial output:

```
gas estimate: 2091194
auth_info:
  fee:
    amount:
    - amount: "52280"
      denom: uxion
    gas_limit: "2091194"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /osmosis.tokenfactory.v1beta1.MsgCreateDenom
    sender: xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r
    subdenom: emp
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]:
```

After typing **y** and pressing **Enter**, you may see the following output:

```
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: ""
timestamp: ""
tx: null
txhash: 4AC13595B2E92DD5D9B27111E71B34D598E2C19EFE7865602C3110FDEA3676F3
```

Upon successful execution, a unique token will be created with a denomination formatted as:

```
factory/<creator-address>/mytoken
```



## **Querying Token Data**

After creating a token, you may want to **verify its metadata** and check its initial state on the blockchain. This can be done using the **denom-metadata query** within the **bank** module, which retrieves essential details about the token, including its base denomination, display name, and symbol.

To query the token data, run the following command:

```sh
xiond query bank denom-metadata factory/<creator-address>/<denom> --node <rpc-endpoint>
```

**Example:**

```sh
xiond query bank denom-metadata factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp --node https://rpc.xion-testnet-1.burnt.com:443
```

**Example Output:**

```yaml
metadata:
  base: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  denom_units:
  - denom: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  display: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  name: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  symbol: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
```

**Understanding the Output:**

* **`base`** – The full unique identifier of the token.
* **`denom_units`** – The denomination structure for different decimal representations.
* **`display`** – The default way the token is displayed in applications.
* **`name`** – The registered name of the token.
* **`symbol`** – The token’s ticker symbol (if set during metadata configuration).



## **Updating Token Metadata**

Before minting new tokens, it is important to **configure the token’s metadata** to ensure frontends and dapps can properly **query and display token information** and to set the required decimal precision to be used by the token.

**Updatable Fields:**

* **`ticker-symbol`** – The short symbol representing the token (e.g., `XION`).
* **`description`** – A brief description of the token.
* **`exponent`** – The decimal precision used for display purposes.

To update the token metadata, run the following command:

```sh
xiond tx tokenfactory modify-metadata <denom> <ticker-symbol> <description> <exponent> --from <wallet-name> --chain-id <chain-id> --node <node> --gas auto --gas-adjustment <gas-adjustment> --gas-prices <gas-prices>
```

**Example:**

```sh
xiond tx tokenfactory modify-metadata factory/xion1xyz.../mytoken MYT "My custom token" 6 --from mywallet --chain-id xion-testnet-1 --node tcp://127.0.0.1:26657 --gas auto --gas-adjustment 1.7 --gas-prices 0.025uxion
```

This command updates the metadata for the `mytoken` token, setting:

* **Ticker Symbol** → `MYT`
* **Description** → `"My custom token"`
* **Exponent** → `6` (indicating 6 decimal places for display)

The following is an example of the initial output:

```
gas estimate: 68088
auth_info:
  fee:
    amount:
    - amount: "1703"
      denom: uxion
    gas_limit: "68088"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /osmosis.tokenfactory.v1beta1.MsgSetDenomMetadata
    metadata:
      base: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
      denom_units:
      - aliases:
        - EMP
        denom: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
        exponent: 0
      - aliases:
        - factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
        denom: EMP
        exponent: 6
      description: My custom token
      display: EMP
      name: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
      symbol: EMP
      uri: ""
      uri_hash: ""
    sender: xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]: 

```

After typing **y** and pressing **Enter**, you may see the following output:

```
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: ""
timestamp: ""
tx: null
txhash: 9583BEC1A62884395E466C190B1A5B3DE5EC152D0CE875BCBDE3DD333BB9E0C9
```

You can then query the metadata to see the updates:

```sh
xiond query bank denom-metadata factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp --node https://rpc.xion-testnet-1.burnt.com:443
```

**Possible Output:**

```yaml
metadata:
  base: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  denom_units:
  - aliases:
    - EMP
    denom: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  - aliases:
    - factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
    denom: EMP
    exponent: 6
  description: My custom token
  display: EMP
  name: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
  symbol: EMP
```



## **Minting Tokens**

After successfully creating your token, you will notice that no tokens have actually been minted. To establish or increase the token supply, you must explicitly **mint new tokens**. The **minting process** allows you to generate additional tokens under your control, which can then be used within your dapps, or distributed to other accounts.

To mint tokens, execute the following command:

```sh
xiond tx tokenfactory mint <amount><denom> --from <wallet-name> --chain-id <chain-id> --node <node> --gas auto --gas-adjustment <gas-adjustment> --gas-prices <gas-prices>
```

**Parameters:**

* `<amount>` – The quantity of tokens to mint.
* `<denom>` – The full denomination (including `factory/<creator-address>/<denom>`).
* `<wallet-name>` – Your configured wallet name.
* `<chain-id>` – The chain ID of the XION network.
* `<node>` – The ID or address of the blockchain node you are connecting to.
* `--gas auto` – Automatically estimates the gas required for the transaction.
* `<gas-adjustment>` – Adjusts the estimated gas amount by **30%** to account for possible variations in actual consumption.
* `<gas-prices>` – The fee rate for gas in **uxion** (the smallest unit of XION).

**Example:**

```sh
xiond tx tokenfactory mint 1000000000000factory/xion1xyz.../mytoken --from mywallet --chain-id xion-testnet-1 --node https://rpc.xion-testnet-1.burnt.com:443 --gas auto --gas-adjustment 1.5 --gas-prices 0.025uxion
```

The following is an example of the initial output:

```
gas estimate: 95267
auth_info:
  fee:
    amount:
    - amount: "2382"
      denom: uxion
    gas_limit: "95267"
    granter: ""
    payer: ""
  signer_infos: []
  tip: null
body:
  extension_options: []
  memo: ""
  messages:
  - '@type': /osmosis.tokenfactory.v1beta1.MsgMint
    amount:
      amount: "1000000000000"
      denom: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
    mintToAddress: ""
    sender: xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r
  non_critical_extension_options: []
  timeout_height: "0"
signatures: []
confirm transaction before signing and broadcasting [y/N]: 
```

After typing **y** and pressing **Enter**, you may see the following output:

```
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: ""
timestamp: ""
tx: null
txhash: BDB5A322400076D14C831497407AF1026D6A3DD923EF1E36366C0ED0C3D5ECE1
```

If your **exponent** was set to **6**, then **`1000000000000`** would mint **1 million `mytoken` tokens** since the exponent determines how many decimal places the token uses.



## **Querying Token Balance**

Once you have minted or received tokens, you may want to **check your wallet’s balance** to see the amount of **native XION, custom tokens, and IBC-transferred assets** you hold. The **bank balance query** allows you to retrieve a complete breakdown of all tokens associated with your address.

To check the token balances for an account execute:

```sh
xiond q bank balances <your-address> --node <node>
```

Example:

```sh
xiond q bank balances xion1xyz... --node https://rpc.xion-testnet-1.burnt.com:443
```

The following is an example of the output, with `factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp` as the custom token:

```
balances:
- amount: "1000000000000"
  denom: factory/xion1ka5gdcv4m7kfzxkllapqdflenwe0fv8ftm357r/emp
- amount: "201000000000"
  denom: ibc/57097251ED81A232CE3C9D899E7C8096D6D87EF84BA203E12E424AA4C9B57A64
- amount: "4903089571"
  denom: uxion
pagination:
  total: "3"
```



## **Sending Tokens to Another Address**

Once you have minted tokens, you may want to **transfer them to another wallet**, whether for payments, rewards, or distribution to users. The **bank send transaction** allows you to send your custom tokens (or any other assets in your wallet) to another address on the **XION blockchain**.

To send tokens, use the following command:

```sh
xiond tx bank send <your-address> <recipient-address> <amount><denom> --node <node> --from <wallet-name> --chain-id <chain-id> --gas auto --gas-adjustment <gas-adjustment> --gas-prices <gas-prices>
```

**Parameter Breakdown:**

* **`<your-address>`** – The sender’s wallet address.
* **`<recipient-address>`** – The recipient’s wallet address.
* **`<amount>`** – The number of tokens to send.
* **`<denom>`** – The full token denomination, including the `factory/{creator-address}/{denom}` prefix.
* **`<node>`** – The RPC node used to broadcast the transaction.
* **`<wallet-name>`** – The name of your configured wallet in `xiond`.
* **`<chain-id>`** – The XION network’s chain ID.
* **`--gas auto`** – Enables automatic gas estimation.
* **`<gas-adjustment>`** – Adjusts the estimated gas usage (e.g., `1.5` for 50% buffer).
* **`<gas-prices>`** – Sets the gas fee rate in `uxion`.

#### **Example: Sending 500 `mytoken` Tokens**

```sh
xiond tx bank send xion1ka5... xion193h... 500factory/xion1ka5.../mytoken --node https://rpc.xion-testnet-1.burnt.com:443 --from mywallet --chain-id xion-testnet-1 --gas auto --gas-adjustment 1.5 --gas-prices 0.025uxion
```

**What Happens?**

* This **transfers 500 `mytoken` tokens** from the sender (`xion1ka5...`) to the recipient (`xion193h...`).
* The transaction is **broadcasted to the network** via the specified RPC node.
* If successful, the **recipient’s balance** is updated, and the sender’s balance is **reduced accordingly**.



You have now created, minted, transferred, burned, and updated a token using the **XION Token Factory** module. These steps provide full control over your token and allow seamless management within the XION ecosystem.
