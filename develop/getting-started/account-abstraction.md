---
description: A protocol level account abstraction solution
---

# Account Abstraction

On smart contract-enabled blockchains, a user will typically encounter two types of accounts:

### Externally-Owned Account (EOA)

This type of account is managed through a private key. It's referred to as "external" because the private key isn't stored on the blockchain itself. Instead, only the corresponding public key is uploaded.&#x20;

### Smart Contract Account (SCA)

These accounts are governed by binary code that resides on the blockchain. The control is enforced through the code's execution.

In the traditional setup, only EOAs have the ability to initiate transactions (txs). They achieve this by securely signing the transactions using their private keys. The authenticity of a transaction is confirmed if it comes with a valid signature.

Contrastingly, SCAs lack the capability to initiate transactions due to the absence of private keys. This absence creates ambiguity in verifying transaction authenticity.

#### So, what makes this problematic?

EOAs exhibit notable security vulnerabilities. To begin, altering the private/public key associated with an EOA is not feasible. Consequently, losing your seed phrase results in a permanent and irreversible loss of account access. Similarly, if an unauthorized individual gains possession of your seed phrase, they acquire perpetual and irreversible access to your account. In contrast, nearly all web2 logins permit password changes or recovery.

Furthermore, EOAs lack the versatility for implementing advanced authentication mechanisms like two-factor authentication (2FA), which is commonplace in most web2 logins.

Realistically, envisioning the widespread adoption of web3 becomes challenging unless its account system offers a security level comparable to that of web2



### So, what's the solution to tackle these challenges?&#x20;

The Ethereum community has proposed a remedy known as account abstraction (AA).

In essence, this approach entails enabling SCAs to initiate transactions. Rather than having transaction authentication determined by a predefined set of rules at the state machine level, AA delegates this task to SCAs. SCAs can then implement customized authentication logic as needed, such as accommodating key rotation or integrating two-factor authentication (2FA). Instead of relying solely on EOAs, users have the freedom to select SCAs that align with their specific requirements.

One predicament faced by Ethereum is its established EVM framework, which already supports a multitude of contracts. This situation restricts core developers from implementing extensive changes, as doing so could potentially disrupt numerous existing protocols. However, in the case of Cosmos, its modular design offers the advantage of introducing AA without causing disruptions. The subsequent section will delve into the mechanics of this process.

_Keep in mind, our objective revolves around shifting the responsibility of transaction authentication from the state machine to the SCA._

\
To accomplish this transition, the SCA needs to incorporate two essential methods: `before_tx` and `after_tx`:

```
enum SudoMsg {
   BeforeTx {
        msgs:       Vec<Any>,
        tx_bytes:   Binary,
        cred_bytes: Option<Binary>,
        simulate:   bool,
    },
    AfterTx {
        simulate: bool,
    },
}
```



The state machine initiates a call to the `before_tx` method just before executing a transaction, followed by invoking the `after_tx` method immediately after execution.

Within the `before_tx` method, the SCA receives comprehensive information about the transaction and its associated signing credentials. This presents an opportunity for signature verification and any additional programmed actions.

Conversely, the `after_tx` method is only triggered if both `before_tx` and ALL messages within the transaction are executed successfully. This stage enables the SCA to carry out programmed actions. For instance, if the transaction involves a trade on a decentralized exchange (DEX), the SCA can evaluate slippage and potentially reject the transaction (via error generation) should it exceed a predetermined threshold.

#### To illustrate this in a graph:

<figure><img src="../../.gitbook/assets/Account Abstration Doc - Figure 1 (1).png" alt="" width="279"><figcaption></figcaption></figure>

### The State Machine Side

Now, let's delve into how this concept integrates within the transaction execution flow of the Cosmos SDK.

In the Cosmos ecosystem, every transaction comprises one or multiple messages (msgs). These messages essentially serve as execution directives, encompassing actions like token transfers, validator delegations, contract invocations, and more.

<figure><img src="../../.gitbook/assets/Account Abstration Doc - Figure 2 (1).png" alt="" width="279"><figcaption></figcaption></figure>

Upon delivery of a transaction (tx) to the state machine, it undergoes the following execution process:

Initially, it undergoes processing through the AnteHandler. This phase comprises a sequence of functions, known as decorators, executed before each transaction. The Cosmos SDK offers a range of built-in decorators, encompassing tasks like configuring the gas meter, validating signatures, advancing the account sequence number, and more.

Subsequently, the tx proceeds to execute its individual messages (msgs) in a sequential manner.

Lastly, the PostHandler stage comes into play. Similar to the AnteHandler, the PostHandler encompasses its own array of decorators, which are executed as part of the process.

<figure><img src="../../.gitbook/assets/Account Abstration Doc - Figure 3 (1).png" alt=""><figcaption></figcaption></figure>

In the process of implementing account abstraction, we substitute the default SigVerificationDecorator, responsible for authentication, with our bespoke `BeforeTxDecorator`.

The mechanism of the `BeforeTxDecorator` is remarkably simple:

```
# pseudocode
if signer_is_smart_contract(tx):
  call_smart_contract_before_tx()
else:
  run_default_sig_verification_decorator()
```

Initially, the decorator assesses whether the transaction's signer is an SCA. In such an instance, the decorator triggers the execution of the SCA's before\_tx method. Conversely, if the signer is not an SCA, the decorator proceeds with the default logic encapsulated by the `SigVerificationDecorator.`

For the PostHandler segment, we introduce a fresh `AfterTxDecorator`, which in turn invokes the SCA's `after_tx` method.

In essence, that's the entirety of the account abstraction process—straightforward and devoid of any disruptions to existing structures. To encapsulate: two novel sudo methods integrated within the contract's realm and two additional Ante/PostHandler decorators seamlessly integrated within the state machine's framework.

### Parameters

Parameters are updatable by the module's authority, typically set to the gov module account.

* `max_gas_before` and `max_gas_after`\
  Certain chains might also find it necessary to impose restrictions on the amount of gas that the Before/AfterTx hooks can consume. The primary concern here is the potential for Denial of Service (DoS) attacks. In the event that a malicious account incorporates an infinite loop within one of these hooks, a significant portion of CPU resources could be monopolized. However, because the transaction ultimately fails—surpassing the gas limit set by the block—it absolves the attacker from any gas fee expenditure. To avert such scenarios, it's advisable to establish gas limits through the configuration of these two parameters.\
