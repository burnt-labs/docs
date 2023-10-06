# Deployment and Interaction

You now have the wasm binary ready. It's time to deploy it to the XIOND testnet and begin interacting with your smart contract. You can use the XION CLI, XION Developer CLI, or the xiond.js, depending on your preference.

## Deploy contract

On the "Compile Contract" page, we've created a binary executable in the form of a WebAssembly (wasm) file. You have the option to upload this code to the blockchain. After the upload process is finished, you'll be able to retrieve the bytecode for verification purposes.

### Deploy using XIOND

ext, you'll store the WebAssembly (wasm) bytecode of the "cw\_namespace" contract on the blockchain and acquire the associated code ID. This code ID will be essential for creating an instance of the "cw\_namespace" contract later in the process.

Replace "mywallet" with the name of the wallet you established during the environment setup on the previous page.

Execute the following commands to store the contract on chain:

#### Mainnet

```
// RES=$(...)
```

#### Testnet

```
// RES=$(...)
```

The response contains the Code Id of the uploaded wasm binary:

```
echo $RES
```

The following is a simpler method to obtain the Code ID from the response:

```
CODE_ID=$(echo $RES | jq -r '.logs[0].events[] | select(.type=="store_code") | .attributes[] | select(.key=="code_id") | .value')
```

```
echo $CODE_ID
```

To view the contracts instantiated with the CODE\_ID generated above, execute the following command:

#### Mainnet

```
// Some code
```

#### Testnet

```
// Some code
```

The response should be an empty list as no contracts have been instantiated at this point.

```
{"contracts":[],"pagination":{"next_key":null,"total":"0"}}
```

Before proceeding to instantiate a contract using the Code ID and interact with it, let's first confirm that the code stored on the blockchain matches the cw\_namespace.wasm binary you uploaded.&#x20;

To do this, download the wasm binary from the blockchain and perform a comparison with the original one:

#### Mainnet

```
// Some code
```

#### Testnet

```
// Some code
```

Both binaries should be identical:

```
// diff artifacts/cw_nameservice.wasm download.wasm
```

If the 'diff' command produces no output, it signifies that the two files being compared are identical.

```
// Some code
```

If the diff command yields an empty output, this indicates that the two files being compared are identical.

#### Deploy using XION developer cli

If your contract was created using the **{INSERT OUR FLAG}** command, you can deploy the contract using the following command:

```
// Some code
```

For details on how to create a CosmWasm project with the XIOND CLI, see Creating a project.

## Instantiating the contract

You can now create an instance of the wasm contract. After instantiation, you can make queries and execute transactions.

### Instantiate using xiond

#### mainnet

```
// Some code
```

#### testnet

```
// Some code
```

## Contract Interaction

Now that the contract is instantiated, you can register a name and transfer it to another address by paying the transfer fee.

### Interact using xiond

#### mainnet

```
// Some code
```

#### testnet

```
// Some code
```

## &#x20;

\
