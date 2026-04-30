---
description: Publish and manage zero-knowledge verification keys on XION using xiond
icon: key
---

# Publishing ZK Verification Keys

This guide explains how to publish verification keys (vkeys) for zero-knowledge proving systems to XION using the `xiond` CLI. Verification keys are a core component of XION's [Verification Infrastructure](../../xions-core/concepts/verification-infrastructure/), enabling on-chain proof verification.

## Overview

Before verifying zero-knowledge proofs on XION, you must publish the corresponding verification key to the chain. Each vkey is:

- Identified by a unique name
- Associated with a specific proving system
- Required for on-chain proof verification

### Who Should Use This Guide

**This guide is for developers who need to:**

- Use **custom ZK circuits** (Circom, Gnark, Noir) for specialized verification logic
- Publish verification keys for any ZK proving system supported by XION

> **Note**: Most verification integrations use pre-configured vkeys managed by the protocol. This guide is primarily for **advanced use cases** requiring custom circuits. If you're integrating standard verification features, start with the [Internet Verification (zkTLS)](internet-verification/) guides instead.

## Supported Proving Systems

| Proving System    | CLI Value   | Format | Curve | Use Case                  |
| ----------------- | ----------- | ------ | ----- | ------------------------- |
| **Groth16**       | `groth16`   | JSON   | BN254 | Circom/SnarkJS circuits   |
| **Gnark Groth16** | `gnark`     | Binary | BN254 | gnark native circuits     |
| **UltraHonk**     | `ultrahonk` | Binary | —     | Noir/Barretenberg (≥0.87) |

## Prerequisites

1. **xiond installed** — See [Setting up your Local Environment](../computation/local-development/setting-up-env/installation-prerequisites-setup-local-environment.md)
2. **A funded account** — You need XION tokens to pay transaction fees ([request testnet tokens](../references/xion-testnet.md))
3. **Your verification key file** — Generated from your ZK circuit

### Environment Setup

```bash
# Set your RPC endpoint (see Public Endpoints for options)
export RPC_URL="https://rpc.xion-testnet-2.burnt.com:443"

# Set your chain ID
export CHAIN_ID="xion-testnet-2"

# Your account name in the keyring
export ACCOUNT="mykey"
```

For network endpoints, see [Public Endpoints & Resources](../references/public-endpoints-and-resources.md).

---

## Publishing VKeys

### Command Syntax

```bash
xiond tx zk add-vkey [name] [vkey-file] [description] [proof-system] [flags]
```

**Parameters:**

| Parameter      | Description                                     |
| -------------- | ----------------------------------------------- |
| `name`         | Unique identifier for your vkey (max 128 bytes) |
| `vkey-file`    | Path to your verification key file              |
| `description`  | Description of the circuit (max 1024 bytes)     |
| `proof-system` | One of: `groth16`, `gnark`, or `ultrahonk`      |

These size limits are enforced on-chain in bytes, not characters. For non-ASCII text, multi-byte UTF-8 characters count toward the byte limit according to their encoded size.
**Common Flags:**

| Flag               | Description                                 |
| ------------------ | ------------------------------------------- |
| `--from`           | Your account name or address                |
| `--chain-id`       | Chain ID (e.g., `xion-testnet-2`, `xion-1`) |
| `--node`           | RPC endpoint URL                            |
| `--gas`            | Gas limit (use `auto` for estimation)       |
| `--gas-prices`     | Gas price (e.g., `0.001uxion`)              |
| `--gas-adjustment` | Gas adjustment factor (recommended: `1.5`)  |

---

## Groth16 (Circom/SnarkJS)

Use this for verification keys exported from Circom circuits compiled with SnarkJS.

### VKey Format

Your `vkey.json` should have this structure:

```json
{
  "protocol": "groth16",
  "curve": "bn128",
  "nPublic": 88,
  "vk_alpha_1": ["..."],
  "vk_beta_2": [["..."], ["..."]],
  "vk_gamma_2": [["..."], ["..."]],
  "vk_delta_2": [["..."], ["..."]],
  "vk_alphabeta_12": [...],
  "IC": [["..."], ...]
}
```

### Publishing

```bash
# Export vkey from SnarkJS
snarkjs zkey export verificationkey circuit.zkey vkey.json

# Publish to XION
xiond tx zk add-vkey \
  email_auth \
  ./vkey.json \
  "Email authentication circuit for account abstraction" \
  groth16 \
  --from $ACCOUNT \
  --chain-id $CHAIN_ID \
  --node $RPC_URL \
  --gas auto \
  --gas-prices 0.001uxion \
  --gas-adjustment 1.5
```

### Verifying Proofs

```bash
xiond query zk verify-proof \
  ./proof.json \
  --vkey-name email_auth \
  --public-inputs "input1,input2,input3" \
  --node $RPC_URL
```

---

## Gnark (Native Groth16)

Use this for verification keys generated from gnark circuits using the native Go library.

### Generating the VKey

```go
package main

import (
    "os"

    "github.com/consensys/gnark-crypto/ecc"
    "github.com/consensys/gnark/backend/groth16"
    "github.com/consensys/gnark/frontend"
    "github.com/consensys/gnark/frontend/cs/r1cs"
)

// Define your circuit
type MyCircuit struct {
    X frontend.Variable `gnark:"x"`
    Y frontend.Variable `gnark:",public"`
}

func (circuit *MyCircuit) Define(api frontend.API) error {
    result := api.Mul(circuit.X, circuit.X)
    result = api.Mul(result, circuit.X)
    api.AssertIsEqual(result, circuit.Y)
    return nil
}

func main() {
    var circuit MyCircuit

    // Compile
    ccs, _ := frontend.Compile(ecc.BN254.ScalarField(), r1cs.NewBuilder, &circuit)

    // Setup
    pk, vk, _ := groth16.Setup(ccs)

    // Export verification key to binary file
    vkFile, _ := os.Create("vkey.bin")
    defer vkFile.Close()
    vk.WriteTo(vkFile)

    // Export proving key (for proof generation)
    pkFile, _ := os.Create("pkey.bin")
    defer pkFile.Close()
    pk.WriteTo(pkFile)
}
```

### Publishing

```bash
xiond tx zk add-vkey \
  cube_circuit \
  ./vkey.bin \
  "Cube computation circuit (x^3 = y)" \
  gnark \
  --from $ACCOUNT \
  --chain-id $CHAIN_ID \
  --node $RPC_URL \
  --gas auto \
  --gas-prices 0.001uxion \
  --gas-adjustment 1.5
```

### Generating Proofs

```go
// Create witness
assignment := MyCircuit{X: 3, Y: 27}
witness, _ := frontend.NewWitness(&assignment, ecc.BN254.ScalarField())
publicWitness, _ := witness.Public()

// Generate proof
proof, _ := groth16.Prove(ccs, pk, witness)

// Export proof
proofFile, _ := os.Create("proof.bin")
defer proofFile.Close()
proof.WriteTo(proofFile)

// Export public inputs
pubFile, _ := os.Create("public_input.bin")
defer pubFile.Close()
publicWitness.WriteTo(pubFile)
```

### Verifying Proofs

```bash
xiond query zk verify-gnark \
  ./proof.bin \
  --vkey-name cube_circuit \
  --public-inputs-file ./public_input.bin \
  --node $RPC_URL
```

---

## UltraHonk (Noir/Barretenberg)

Use this for circuits compiled with Noir and proven using Barretenberg (version ≥0.87).

### Prerequisites

```bash
# Install nargo (Noir compiler) — bb is included
curl -L https://raw.githubusercontent.com/noir-lang/noirup/main/install | bash
noirup
```

### Circuit Setup

1. Create a Noir project:

```bash
nargo new my_circuit
cd my_circuit
```

2. Define your circuit in `src/main.nr`:

```noir
fn main(x: pub Field, y: Field) {
    assert(x * x == y);
}
```

3. Compile and generate artifacts:

```bash
# Compile
nargo compile

# Generate verification key
bb write_vk -b ./target/my_circuit.json -o ./vkey.bin

# Generate proof (with inputs in Prover.toml)
bb prove -b ./target/my_circuit.json -w ./target/my_circuit.gz -o ./proof.bin
```

### Publishing

```bash
xiond tx zk add-vkey \
  square_check \
  ./vkey.bin \
  "Square verification circuit (x^2 = y)" \
  ultrahonk \
  --from $ACCOUNT \
  --chain-id $CHAIN_ID \
  --node $RPC_URL \
  --gas auto \
  --gas-prices 0.001uxion \
  --gas-adjustment 1.5
```

### Verifying Proofs

```bash
xiond query zk verify-ultrahonk \
  ./proof.bin \
  --vkey-name square_check \
  --public-inputs-file ./public_inputs.bin \
  --node $RPC_URL
```

---

## Managing VKeys

### Query Commands

```bash
# Check if a vkey exists
xiond query zk has-vkey email_auth --node $RPC_URL

# Get vkey by name
xiond query zk vkey-by-name email_auth --node $RPC_URL

# Get vkey by ID
xiond query zk vkey 0 --node $RPC_URL

# List all vkeys
xiond query zk vkeys --node $RPC_URL

# List with pagination
xiond query zk vkeys --limit 10 --page 1 --node $RPC_URL
```

### Update a VKey

Only the original uploader can update a vkey:

```bash
xiond tx zk update-vkey \
  email_auth \
  ./new_vkey.json \
  "Updated email authentication circuit v2" \
  groth16 \
  --from $ACCOUNT \
  --chain-id $CHAIN_ID \
  --node $RPC_URL \
  --gas auto \
  --gas-prices 0.001uxion \
  --gas-adjustment 1.5
```

### Remove a VKey

Only the original uploader can remove a vkey:

```bash
xiond tx zk remove-vkey old_circuit \
  --from $ACCOUNT \
  --chain-id $CHAIN_ID \
  --node $RPC_URL \
  --gas auto \
  --gas-prices 0.001uxion \
  --gas-adjustment 1.5
```

---

## Reference

### Size Limits

| Parameter               | Maximum    |
| ----------------------- | ---------- |
| VKey Size               | 256 KiB    |
| Groth16 Proof Size      | 4 KiB      |
| Groth16 Public Inputs   | 30 KiB     |
| Gnark Proof Size        | 4 KiB      |
| Gnark Public Inputs     | 10 KiB     |
| UltraHonk Proof Size    | 20 KiB     |
| UltraHonk Public Inputs | 10 KiB     |
| VKey Name               | 128 bytes  |
| VKey Description        | 1024 bytes |

### Gas Estimation

VKey upload gas is calculated as:

```
gas = base_gas + (vkey_size / chunk_size) × chunk_gas
```

Default values:

- **Chunk size:** 20 bytes
- **Chunk gas:** 10,000 gas per chunk

For a typical vkey (~10KB), expect approximately 5,000,000–5,100,000 gas, depending on `base_gas`.

### Query Module Parameters

```bash
xiond query zk params --node $RPC_URL
```

### Networks

| Network | Chain ID         | RPC URL                                    |
| ------- | ---------------- | ------------------------------------------ |
| Testnet | `xion-testnet-2` | `https://rpc.xion-testnet-2.burnt.com:443` |
| Mainnet | `xion-1`         | `https://rpc.xion-mainnet-1.burnt.com:443` |

---

## Troubleshooting

| Error                        | Cause                                             | Solution                                                            |
| ---------------------------- | ------------------------------------------------- | ------------------------------------------------------------------- |
| `vkey already exists`        | A vkey with this name is already registered       | Use a different name or `update-vkey` to modify existing            |
| `verification key not found` | No vkey with the specified name or ID exists      | Check if the vkey exists with `xiond query zk has-vkey <name>`      |
| `invalid vkey bytes`         | File format doesn't match the specified system    | Ensure JSON for `groth16`, binary for `gnark` and `ultrahonk`       |
| `vkey size exceeds limit`    | VKey is larger than the maximum allowed size      | Check limits with `xiond query zk params`                           |
| `unauthorized`               | Only the original uploader can update/remove      | Use the same account that uploaded the vkey                         |
| `proof verification failed`  | Proof doesn't match the vkey or inputs are wrong  | Verify correct vkey name, proof file, and public inputs format      |

### Getting Help

```bash
# Transaction commands help
xiond tx zk --help
xiond tx zk add-vkey --help
xiond tx zk update-vkey --help
xiond tx zk remove-vkey --help

# Query commands help
xiond query zk --help
xiond query zk verify-proof --help
xiond query zk verify-gnark --help
xiond query zk verify-ultrahonk --help
```

## Next Steps

- Learn about [XION's Verification Infrastructure](../../xions-core/concepts/verification-infrastructure/)
- Explore [zkTLS Integration](internet-verification/) for internet data verification
- See [Interacting with Xion Chain using Xion Daemon](../tools/xiond-cli.md) for general CLI usage
