# Math

## **Math Functions in CosmWasm**

The mathematical functions employed in CosmWasm are built upon standard Rust. However, there are helper functions provided for u128, u64, and decimal data types.

### **Uint128**

Uint128 is a lightweight wrapper for u128 that utilizes string-based JSON encoding and decoding. This enables the complete u128 range to be accessible for clients that process JSON numbers as floats, such as JavaScript and jq.

### **Uint64**

Uint64 is a minimal layer that wraps u64 and employs strings for JSON encoding and decoding. This approach allows the full u64 range to be leveraged by clients that convert JSON numbers to floats, like JavaScript and jq.

### **Decimal**

Decimal is a fixed-point decimal value characterized by 18 fractional digits. For example, Decimal(1\_000\_000\_000\_000\_000\_000) is equivalent to 1.0. The largest possible value that can be represented is 340282366920938463463.374607431768211455, which is calculated as (2^128 - 1) divided by 10^18.
