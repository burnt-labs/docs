# Confirming node is running

If all is setup correctly, you will see a stream of messages like the snippet below:

```bash
3:50PM INF received proposal module=consensus proposal="Proposal{4/0 (FF2048471E9D5C9BC10CEB45C0C7F018C520E76C0C68B178827CFD4FD4402688:1:168EC863D01F, -1) 704E32F3A363 @ 2024-06-24T22:50:37.401132Z}" proposer=BC872B003191D49EB8F900AA0698DF218BD0D0A7
3:50PM INF received complete proposal block hash=FF2048471E9D5C9BC10CEB45C0C7F018C520E76C0C68B178827CFD4FD4402688 height=4 module=consensus
3:50PM INF finalizing commit of block hash=FF2048471E9D5C9BC10CEB45C0C7F018C520E76C0C68B178827CFD4FD4402688 height=4 module=consensus num_txs=0 root=92AD0010299BAB47510BA13C9711437AAC6F2745DC3AB9C0E014B7C2C9FB959B
3:50PM INF minted coins from module account amount=2uxion from=mint module=x/bank
3:50PM INF executed block height=4 module=state num_invalid_txs=0 num_valid_txs=0
3:50PM INF commit synced commit=436F6D6D697449447B5B32362032353220333320313330203336203932203437203232332037312031333820323235203230362031383620323337203135372033302034302031393820373320393020313535203737203732203320323332203720313034203233322032323120313131203430203231375D3A347D module=server
3:50PM INF committed state app_hash=1AFC2182245C2FDF478AE1CEBAED9D1E28C6495A9B4D4803E80768E8DD6F28D9 height=4 module=state num_txs=0
3:50PM INF indexed block events height=4 module=txindex
```

To confirm the node is running run the following command:

If on the same machine run `xiond status`, otherwise run `xiond status --node <RPC URL>`.

You should see output like this:

```json
{
  "NodeInfo": {
    "protocol_version": {
      "p2p": "8",
      "block": "11",
      "app": "0"
    },
    "id": "f10f5001726e95c62a763f2d020df6b53b19f44a",
    "listen_addr": "65.108.134.47:22356",
    "network": "xion-testnet-1",
    "version": "0.37.4",
    "channels": "40202122233038606100",
    "moniker": "e4fed392-45ec-5eed-98f2-ca1ff10a2409",
    "other": {
      "tx_index": "on",
      "rpc_address": "tcp://0.0.0.0:22357"
    }
  },
  "SyncInfo": {
    "latest_block_hash": "AA34628DD2703228D7B1F7526824A6BFD92426A53E5396D8AA3AE5DE786E38EA",
    "latest_app_hash": "162655290CEE5D8E33800A7FD8197FFC833FFE701E6B2AE0ED45D6336DD8E890",
    "latest_block_height": "8524122",
    "latest_block_time": "2024-06-24T22:55:26.352076939Z",
    "earliest_block_hash": "91960249D3B580C9A7A8964FF32C8EBCF9F3ECAF976DB3472A7F17EE1F829F7D",
    "earliest_app_hash": "DF28B965DC616AD1E911486C310AFF9DD16A8EE436F59510D5EB52944EE15277",
    "earliest_block_height": "8221187",
    "earliest_block_time": "2024-06-06T08:28:57.373856111Z",
    "catching_up": false
  },
  "ValidatorInfo": {
    "Address": "CB63B900E0031FA6F4D67B7308A39197B87AAC2F",
    "PubKey": {
      "type": "tendermint/PubKeyEd25519",
      "value": "VH+Q/cJBj5h2OmG3bBtKi/oTbYXywTuHa9h2BbZrX64="
    },
    "VotingPower": "0"
  }
}


```



