---
description: XION's protocol-level account abstraction solution
---

# Intro to Account Abstraction

In general, blockchains have two types of accounts:

1. externally owned accounts (EOAs)
2. smart contract accounts (SCAs)

### Externally-Owned Account (EOA)

Traditionally users interact with blockchains through externally owned accounts (EOAs) which involve the use of asymmetric cryptography. These accounts have a public/private key pair, whereby the public key is stored on the blockchain, and the private key is stored off-chain by the user. The private key, known only to the user, is used to sign transactions, while the public key is used to verify the signature’s authenticity. EOAs, however, have many drawbacks: they lack the ability to implement additional authentication mechanisms, they aren’t able to perform autonomous operations or smart contract executions, and a private/public keypair can not be changed. As a result, EOAs are a central point of failure for users. If a user loses access to their private key, they lose access to their account entirely. Similarly, if a user’s private key is compromised, their account is irreversibly compromised.&#x20;

### Smart Contract Account (SCA)

The second type of account, smart contract accounts (SCAs), are governed by code on the blockchain. Their creation involves being deployed to the blockchain through a transaction initiated by an EOA. Once deployed, these smart contracts reside at a specific address on the blockchain and their code dictates the rules and conditions under which they operate. These rules are executed autonomously when certain conditions are met or when they are triggered by transactions or other smart contracts. Traditionally, they are unable to initiate transactions due to an absence of private keys.&#x20;

### Account Abstraction (AA)

As a solution to EOA drawbacks mentioned above, account abstraction has been proposed as a way to enable SCAs to initiate transactions. Rather than having transaction authentication determined by a predefined set of rules at the state machine level, account abstraction delegates this task to SCAs. SCAs can then implement customized authentication logic as needed, such as accommodating key rotation, performing autonomous tasks, integrating multiple-factor authentication, and much more.\
