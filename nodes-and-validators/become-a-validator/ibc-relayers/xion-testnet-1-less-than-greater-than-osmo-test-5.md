# xion-testnet-1 <=> osmo-test-5

**Channel Information // transfer**

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

**Channel Information // fee abstraction**

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

**Denominations**

* OSMO on Xion

```
balances:
  - denom: ibc/6AE2756AA7EAA8FA06E11472EA05CA681BD8D3FBC1AAA9F06C79D1EC1C90DC9B
```

* XION on Osmosis

```
balances:
- denom: ibc/3642669AD14386D3E38F43F30CFCA859B3E8A05BF6BD6A23DEBD2115AD1325E9
```
