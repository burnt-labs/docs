# xion-testnet-1 <=> osmo-test-5

**Channel Information // transfer**

```
# Chain: osmo-test-5
  - Client: 07-tendermint-3125
    * Connection: connection-2826
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-6668
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-490
# Chain: xion-testnet-1
  - Client: 07-tendermint-121
    * Connection: connection-57
      | State: OPEN
      | Counterparty state: OPEN
      + Channel: channel-490
        | Port: transfer
        | State: OPEN
        | Counterparty: channel-6668
```

**Denominations**

* OSMO on Xion

```
balances:
  - denom: ibc/484532EB74866F3FB8A71F909F7B1B470FE2E66313DA0A1F9EE5B7C5C046D195
```

* XION on Osmosis

```
balances:
- denom: ibc/5C274155106FEB0C093F76A12BB67222E479E1EBE043F319CE4463E54F7A357C
```
