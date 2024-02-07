---
description: Channel information
---

# xion-testnet-1 <=> osmo-test-5

**transfer**

```
# Chain: osmo-test-5
  - Client: 07-tendermint-1474
    * Connection: connection-1380
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-4410
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-12
# Chain: xion-testnet-1
  - Client: 07-tendermint-22
    * Connection: connection-12
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-12
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-4410
```

**fee abstraction**

```
# Chain: osmo-test-5
  - Client: 07-tendermint-1475
    * Connection: connection-1381
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-4411
        | Port: icqhost
        | State: OPEN
        | Counterparty: channel-13
# Chain: xion-testnet-1
  - Client: 07-tendermint-23
    * Connection: connection-13
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-13
        | Port: feeabs
        | State: OPEN
        | Counterparty: channel-4411
```
