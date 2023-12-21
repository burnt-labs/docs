---
description: Channel information
---

# xion-testnet-1 <=> osmo-test-5

**transfer**

```
# Chain: osmo-test-5
  - Client: 07-tendermint-713
    * Connection: connection-645
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-1299
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-8
# Chain: xion-testnet-1
  - Client: 07-tendermint-13
    * Connection: connection-8
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-8
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-1299
```

**fee abstraction**

```
# Chain: osmo-test-5
  - Client: 07-tendermint-967
    * Connection: connection-867
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-1575
        | Port: icqhost
        | State: OPEN
        | Counterparty: channel-11
# Chain: xion-testnet-1
  - Client: 07-tendermint-19
    * Connection: connection-11
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-11
        | Port: feeabs
        | State: OPEN
        | Counterparty: channel-1575
```
