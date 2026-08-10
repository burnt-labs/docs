# Deployment and Interaction

You now have the wasm binary ready. It's time to deploy it to the XIOND testnet and begin interacting with your smart contract. You can use the XION CLI, XION Developer CLI, or the xiond.js, depending on your preference.

## Deploy contract

On the "Compile Contract" page, we've created a binary executable in the form of a WebAssembly (wasm) file. You have the option to upload this code to the blockchain. After the upload process is finished, you'll be able to retrieve the bytecode for verification purposes.

### Deploy using XIOND

ext, you'll store the WebAssembly (wasm) bytecode of the "cw\_namespace" contract on the blockchain and acquire the associated code ID. This code ID will be essential for creating an instance of the "cw\_namespace" contract later in the process.

Replace "mywallet" with the name of the wallet you established during the environment setup on the previous page.

Execute the following commands to store the contract on chain:

{% code fullWidth="true" %}
```sh
RES=$(xiond tx wasm store artifacts/cw_nameservice.wasm --from mywallet $TXFLAG -y --output json)
```
{% endcode %}

{% hint style="info" %}
The TXFLAG env var was set in the [setting-up-environment.md](../setting-up-environment.md "mention") section. Please refer to it.
{% endhint %}

The response contains the Code Id of the uploaded wasm binary:

```sh
echo $RES
```

The following is a simpler method to obtain the Code ID from the response:

```bash
CODE_ID=$(echo $RES | jq -r '.logs[0].events[] | select(.type=="store_code") | .attributes[] | select(.key=="code_id") | .value')
```

```bash
echo $CODE_ID
```

To view the contracts instantiated with the CODE\_ID generated above, execute the following command:

```bash
xiond query wasm list-contract-by-code $CODE_ID $NODE --output json
```

The response should be an empty list as no contracts have been instantiated at this point.

```
{"contracts":[],"pagination":{"next_key":null,"total":"0"}}
```

Before proceeding to instantiate a contract using the Code ID and interact with it, let's first confirm that the code stored on the blockchain matches the cw\_namespace.wasm binary you uploaded.&#x20;

To do this, download the wasm binary from the blockchain and perform a comparison with the original one:

```bash
xiond query wasm code $CODE_ID $NODE download.wasm
```

Both binaries should be identical:

```bash
diff artifacts/cw_nameservice.wasm download.wasm
```

{% hint style="info" %}
If the 'diff' command produces no output, it signifies that the two files being compared are identical.
{% endhint %}



## Instantiating the contract

You can now create an instance of the wasm contract. After instantiation, you can make queries and execute transactions.

### Instantiate using xiond

```bash
# Prepare the instantiation message
INIT='{"purchase_price":{"amount":"100","denom":"uxion"},"transfer_price":{"amount":"999","denom":"uxion"}}'
# Instantiate the contract
xiond tx wasm instantiate $CODE_ID "$INIT" --from mywallet --label "name service" $TXFLAG -y --no-admin
```

## Contract Interaction

Now that the contract is instantiated, you can register a name and transfer it to another address by paying the transfer fee.

### Interact using xiond

```bash
# Register a name for the wallet address
REGISTER='{"register":{"name":"fred"}}'
xiond tx wasm execute $CONTRACT "$REGISTER" --amount 100uxion --from wallet $TXFLAG -y

# Query the owner of the name record
NAME_QUERY='{"resolve_record": {"name": "fred"}}'
xiond query wasm contract-state smart $CONTRACT "$NAME_QUERY" $NODE --output json
# {"data":{"address":"xion1pze5wsf0dg0fa4ysnttugn0m22ssf3t4a9yz3h"}}

# Transfer the ownership of the name record to wallet2 (change the "to" address to wallet2 generated during environment setup)
wasmd keys list
TRANSFER='{"transfer":{"name":"fred","to":"xion1lwcrr9ktsmn2f7fej6gywxcm8uvxlzz5ch40hm"}}'
xiond tx wasm execute $CONTRACT "$TRANSFER" --amount 999uxion --from wallet $TXFLAG -y

# Query the record owner again to see the new owner address:
NAME_QUERY='{"resolve_record": {"name": "fred"}}'
xiond query wasm contract-state smart $CONTRACT "$NAME_QUERY" $NODE --output json
# {"data":{"address":"xion1lwcrr9ktsmn2f7fej6gywxcm8uvxlzz5ch40hm"}}
```

## <br>

<br>
