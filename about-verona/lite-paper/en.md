# Verona Litepaper

## Making AI intelligent

The intelligence layer for AI. Verified facts about the real world, owned by the user, reusable by any agent.

June 2026

---

## Abstract

Verona is the intelligence layer for AI. It makes user-verified data portable, private, and programmable, letting any agent transact on information the user actually owns.

Today an agent can talk but it cannot act, because the one thing it needs, a verified fact it is allowed to use, does not exist in a form it can reach. Verona makes that fact exist. A fact is verified once at its source, the user owns it, and any agent the user authorizes reuses it without the underlying data ever being exposed. The network combines verification at the source, trusted execution, private settlement, formal verification, and programmable payments in stablecoin, and it already earns revenue from real applications. Verify once, reuse everywhere, expose nothing.

## 1 Introduction

### 1.1 The gap

AI can write, plan, and hold a conversation. It cannot act on what is true about you. Ask an agent to renew a prescription, apply for an apartment, or refinance a loan, and it stops at the first step that needs a verified fact: your income, your identity, your eligibility. The model is fluent and the task dies, because being fluent is not the same as knowing something is true. An agent that cannot act on verified information is not intelligent. Intelligence is the ability to act on what is true.

### 1.2 There is no verified data to act on

Verification happens constantly, identity checks, credit pulls, income screens, and every one of them is thrown away the moment it is used. The next service starts over. The person the data describes owns none of it, and an agent looking for a fact it is allowed to use finds nothing, because the whole stack was built for a human who could be asked to upload one more document. Agents do not upload documents. This is the gap Verona closes.

### 1.3 The unlock

Verona verifies a fact once, hands ownership to the user, and lets any agent the user authorizes reuse it while the underlying data stays where it is and is shown to no one. A single verified fact is useful. A network of reusable verified facts, owned by the people they describe and reachable by every agent, is a new layer of the internet. Verona is built on four ideas:

Verified facts as a primitive. A verification becomes a durable asset the user owns, not a one-time check that dies on use.

Private settlement. Facts and the actions taken on them settle on one chain with hybrid public and private state.

Formal verification. The chain is built to be verified against machine-checked specifications, so an agent can rely on it.

Programmable payments. Agents settle value automatically as part of an action, in stablecoin

## 2 Core architecture

Verona is one network. Real-world sources feed it, five capabilities make it work, and users, agents, enterprises, and the applications built on top draw from the same set of verified facts.

![](../../.gitbook/assets/litepaper-image8.png)

### 2.1 Verification at the source

Verona never asks you to hand over your data. It produces a proof from the system that already holds the fact, and the raw data never moves. The proof is matched to where the fact comes from: zkTLS for a web session, zkEmail for email, zkPassport for a passport, an app or enclave attestation for an application, a photo attestation for proof of a real and present person, and zkDCap, an on-chain zero-knowledge proof of a hardware attestation. Whoever consumes the proof checks it independently. The proofs and attestations trace back to their source, all smart contract and Verona source code is open and verifiable, and no part of the stack requires unverifiable trust in Verona. The provenance of the data and the validity of any selective disclosure can be audited by the recipient. Verification at the source is live today against real payroll providers, returning a proof of income and employment in seconds with no document stored.

### 2.2 Facts you own and reuse

A verified fact is encrypted to the user and held on the network. When the user authorizes a recipient, an agent, a service, or another enclave, the network re-encrypts the fact to that recipient and a trusted execution environment transforms it into exactly the answer the recipient needs. The recipient gets an answer and a proof, never the data. Nothing is exposed in transit, and the user never has to hold a key. One verification is reusable across every recipient the user authorizes, so work done once is never repeated. This is the difference between a check that ends on use and a fact that keeps paying off.

Stored data can be transformed into only the answer a recipient needs, with no room for a false or fabricated claim. Key management runs inside hardware enclaves on verifiable, reproducible builds, so the user never touches a key. Key handling is the highest-friction and highest-risk part of any blockchain tool, and here it disappears from the user's view entirely.

![](../../.gitbook/assets/litepaper-image10.png)

### 2.3 Private settlement

Facts and the actions taken on them settle on a single chain with hybrid public and private state.

![](../../.gitbook/assets/litepaper-image2.png)

What should be public is public, and what should stay confidential, balances, terms, the content of a verified fact, stays confidential on the same chain. With keys held in hardware enclaves and an open provenance stack, private data can produce public outputs that others can trust and act on. There is no second system bolted on for privacy. It is one chain.

### 2.4 Formal verification

Verona is built to be verified, not only audited. Every contract is checked against a machine-checked specification, and the specifications are produced through an adversarial process.

![](../../.gitbook/assets/litepaper-image3.png)

Several frontier models draft them, others attack the drafts, and a specification is accepted because it survives scrutiny, not because the models agree. Agreement hides shared mistakes. An adversary hunts them. This is the trust model the chain is built to, and it is what lets an agent treat the chain's behavior as something it can depend on rather than something it has to hope about.

### 2.5 Programmable payments

Native payment rails let an agent pay for what it does at the moment it does it, denominated in the stablecoin, without a person stepping in to approve each transfer. Verification, action, and settlement happen in one flow rather than three disconnected systems.

## 3 The network

Users turn real-world and digital facts into reusable proofs, builders build applications on those proofs, enterprises verify their own users and workflows, and agents act without having to trust each other or handle raw sensitive data. Every party draws on the same set of verified facts, and that is what turns a product into a network.

Verona compounds because every participant adds value to the same network of verified facts. Users add supply. Applications, enterprises, and agents create demand. Builders expand where facts can be used. Source systems increase what can be proven. Payments and settlement turn that activity into an economy.

![](../../.gitbook/assets/litepaper-image5.png)

- Users supply permissioned verified facts about themselves. They verify once, own the result, and decide which applications, enterprises, agents, or enclaves can use it. What they get back is portability, privacy, and reuse, and eventually a say in how the facts they create are monetized.

- Applications create demand for those facts. They use Verona to onboard users, check eligibility, cut fraud, and complete workflows without collecting or storing raw sensitive data, and every application gives users one more reason to verify once and reuse elsewhere.

- Builders widen the network. They ship applications, agent runtimes, workflows, and integrations on top of truth primitives instead of reconstructing verification, consent, and payment plumbing on their own.

- Enterprises participate in two ways. They come first to verify their own users, customers, workers, transactions, credentials, and eligibility. As verified facts accumulate they stay to consume and compute over permissioned data, because verification is the first use case and verified intelligence is the larger one.

- Agents put verified facts to work, for a person, for an enterprise, or with each other.

- Source systems and verifiers expand what the network can know. Payroll providers, identity systems, banks, email, passports, apps, devices, commerce platforms, and healthcare systems each produce a proof matched to the fact being verified, and every new source makes the facts on the network broader and better.

- Token and network participants run the economic layer underneath, providing settlement, security, and liquidity for everything above it.

## 4 What it powers

These are surfaces onto one network, not separate products.

![](../../.gitbook/assets/litepaper-image9.png)

### 4.1 Attribute verification

The first surface is attribute verification. Verona verifies any attribute a user holds on a website behind a login - income and employment, spending, activity, loyalty tier, account tenure, the kind of data that normally sits siloed with no way to share it. The user logs in, the network returns a cryptographic proof that the attribute meets a stated condition. No document is uploaded and no raw data is stored. It is faster, cheaper, and more reliable than the document-upload process it replaces, and it is live today with paying businesses. Income and employment verification for the property sector is one example, and the same approach extends to lending, insurance, and any business that needs to act on a verified attribute rather than a self-reported one, with each proof reused rather than re-collected.

### 4.2 Agents that act on verified facts

The second surface is agents, and they participate in more than one way.

- User agents act for a person, carrying that person's verified facts into a task without the user re-proving anything.

- Enterprise agents act only on verified inputs, so an automated decision rests on a proof rather than a claim.

- Agent to agent, autonomous systems transact by trusting each other's proofs instead of trusting each other, with no human in the middle to vouch.

![](../../.gitbook/assets/litepaper-image1.png)

![](../../.gitbook/assets/litepaper-image4.png)

Each case needs the same thing, a verified fact the agent can act on, and each reuses verifications already on the network instead of starting from nothing.

### 4.3 The verified context layer for builders

![](../../.gitbook/assets/litepaper-image6.png)

The third surface is builders. A single line of configuration gives any agent runtime that speaks the standard protocol access to verified, user-consented facts. Builders build on truth primitives instead of rebuilding verification, compliance, payments, and data ingestion from scratch. Verona sits between identity and tools as the layer that supplies verified context, and the proofs it returns are reusable across every other service on the network.

## 5 Why it compounds

A normal verification product starts over every time. A user proves something, an application checks it, the result dies inside that workflow, and nothing accumulates.

Verona works differently. Every verified fact becomes a reusable primitive controlled by the user, which gives applications and agents more trusted context to act on. New applications give users more reason to verify. New enterprise workflows create demand for facts that are accurate, permissioned, and source-backed, and new sources widen what the network can prove.

![](../../.gitbook/assets/litepaper-image11.png)

Supply and demand reinforce each other, so the network gets stronger with use instead of resetting with every check. A model that verifies and discards cannot accumulate any of this, and the lead widens with use.

The moat is not one proof, one customer, or one application. It is the growing network of reusable verified facts, owned by the people they describe and reachable by any authorized application, enterprise, or agent.

## 6 Where it leads

Verona starts by verifying facts for applications. That is the first wedge: faster income checks, employment verification, eligibility checks, credential proofs, and other workflows where businesses need to act on facts instead of claims.

As those facts accumulate, Verona becomes more than a verification layer. It becomes a permissioned intelligence layer. Enterprises and agents compute over source-backed, user-consented facts to make better decisions without exposing the underlying data. A lender can reason over verified income. An insurer can reason over verified eligibility or events. A marketplace can reason over verified reputation or transaction history. A strategy team can understand market behavior from permissioned facts instead of stale panels and inferred data.

Legacy market intelligence is built on proxies: panels, surveys, brokers, scraped signals, stale datasets, and inferred behavior. Verona replaces proxies with permissioned computation over verified facts. The user stays in control, the raw data stays private, and the output is something an application, enterprise, or agent can trust.

This is the larger direction of the network: verified data as economic infrastructure, and Verona as the place applications, enterprises, and agents go when they need to know something is true.

## 7 Token

There are two tokens: VERONA and a stablecoin.

### 7.1 A value-generating network

Verona rejects the assumption that every transaction is worth taxing. It earns by taking a share of revenue from the applications it backs, because the network succeeds when those applications succeed and earns nothing from spam. Verona earns revenue today, and that revenue funds the token. Value accrues from real economic activity, not from counting transactions.

### 7.2 How value accrues

Net revenue funds buyback and burn.

![](../../.gitbook/assets/litepaper-image7.png)

Tokens are bought back with revenue and burned, and the amount is shown on a public dashboard so anyone can watch it happen. As revenue grows to cover the cost of running the network, the inflationary share of rewards falls. The direction is set: usage and revenue rise, and the pressure on supply falls. The exact schedule is not fixed here, and we do not publish buyback rates, burn rates, or emission figures we are not ready to commit to.

### 7.3 The Stablecoin

Verona will have a one-to-one, fully reserved digital dollar from a regulated issuer. It carries reserve-generated returns under a tiered structure. Holders get a stable unit for payments and settlement across the network, and the stablecoin becomes the unit agents use when they pay for what they do.

## 8 Conclusion

AI becomes intelligent when it can act on what is true. That requires verified facts that are owned by the people they describe, reusable by any agent, and exposed to no one. Verona is the network where those facts live, and the layer the agentic economy is built on. Verify once, reuse everywhere, expose nothing. Making AI intelligent.

## References

D. Boneh and M. Franklin. Identity-Based Encryption from the Weil Pairing. CRYPTO 2001.

DomainKeys Identified Mail (DKIM) Signatures. RFC 6376.

The Transport Layer Security (TLS) Protocol Version 1.3. RFC 8446.

Intel Trust Domain Extensions (Intel TDX). Intel Corporation.

---

This document is for general information only. It is not investment advice, and it is not an offer to sell or a solicitation to buy any token or other instrument. It should not be relied on for legal, tax, or accounting decisions. Verona is under active development, and any feature described here may change. Statements about the future are subject to change without notice. The stablecoin is a regulated, fully reserved digital dollar; any reserve-generated returns are discretionary and depend on reserve performance.
