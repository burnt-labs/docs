# Names and Addresses

## Names and addresses <a href="#names-and-addresses" id="names-and-addresses"></a>

Blockchains use addresses to identify external actors through a hash of a public key. On-chain addresses are represented using a concise, immutable binary format, typically 20 or 32 bytes long, often derived from a hashing function.&#x20;

Binary addresses are typically represented in a human-readable format using two common notations: IPv4 and IPv6 addresses. These addresses are used to identify devices on a network. Here are two examples of each:

#### IPv4 Addresses:

1. **IPv4 Decimal-Dotted Notation**: This is the most common way to represent IPv4 addresses. It consists of four decimal numbers separated by periods (dots), where each number can range from 0 to 255. For example: `192.168.1.1`
2. **IPv4 CIDR Notation**: Classless Inter-Domain Routing (CIDR) notation is used to represent a range of IPv4 addresses using a network address followed by a prefix length. For example, `192.168.1.0/24` represents a range of IP addresses from `192.168.1.0` to `192.168.1.255`, with a subnet mask of `255.255.255.0`.

#### IPv6 Addresses:

1. **IPv6 Colon-Hexadecimal Notation**: IPv6 addresses are much longer than IPv4 addresses and are represented as eight groups of four hexadecimal digits separated by colons. For example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
2. **IPv6 Compressed Notation**: To make IPv6 addresses more manageable, leading zeros within each group of hexadecimal digits can be omitted, and consecutive groups of zeros can be represented with a double colon (::). For example, `2001:db8::1` represents the same address as `2001:0db8:0000:0000:0000:0000:0000:0001`.

These representations allow humans to easily work with and understand IP addresses in various contexts, such as configuring network devices or specifying access control rules.

### Addresses <a href="#addr" id="addr"></a>

Addresses in the Cosmos SDK are 20-character long strings and include security checks, such as chain-prefix in Bech32, checksums in Bech32, and checksummed hex (EIP55). Since CosmWasm is an extension of the Cosmos SDK, it adheres to the same address rules; wallets, smart contracts, and modules each have an identifier address with a specific prefix.

When providing an address to smart contracts, you can submit it as a string and subsequently validate the input as an Addr. The Addr serves as a wrapper for a plain string, offering valuable utility functions such as address validation through string validation.

There is also a more uncommon representation of an address, called a **Canonical** Address, which is the binary representation used internally in the blockchain. This representation can be used for all storage lookups, and its format can be converted into multiple types of string addresses.

To better understand Canonical Addresses, we can think about Bitcoin transitioning from Base58 to Bech32 encoding, along with SegWit: once the encoding changes, **message.signer** would lose access to their own account. Canonical Addresses provide us with a stable identifier to work with.

### Naming <a href="#naming" id="naming"></a>

We can consider names as a form of address, albeit one that requires a contract query (with storage access) to resolve. It cannot be resolved merely by calling a pure function.

[\
](https://docs.archway.io/developers/cosmwasm-documentation/architecture/querying)
