# DKIM Module (zkEmail)

### Verify Email Contents Cryptographically Without Exposing Inbox Data

The DKIM Module enables zero-knowledge proofs over email contents by leveraging DKIM (DomainKeys Identified Mail) signatures that email providers already attach to outgoing messages.

#### How the DKIM Module Works

Every major email provider (Gmail, Outlook, Yahoo, corporate email servers) attaches a DKIM signature to outgoing emails. This signature cryptographically proves that the email content has not been modified since it was sent by the originating server.

The DKIM Module uses these existing signatures as the basis for zero-knowledge proofs:

1. **Email Selection.** The user selects an email in their inbox that contains the data they want to prove (e.g., a purchase receipt from Amazon, an employment verification from their company, a payment confirmation from a bank).
2. **DKIM Verification.** The module verifies the email's DKIM signature to confirm it is authentic and unmodified.
3. **Selective Disclosure.** The user specifies which fields or claims to prove from the email. Only those specific data points are included in the proof.
4. **ZK Proof Generation.** A zero-knowledge proof is generated that attests to the selected claims while revealing nothing else from the email.
5. **On-Chain Verification.** The proof is submitted to XION and verified. A valid proof creates an on-chain attestation.

#### Why DKIM Is Powerful

DKIM signatures provide a unique advantage: the cryptographic proof of authenticity comes from the email provider itself, not from the user. This means:

* The user cannot forge an email from Amazon, their bank, or their employer, because they do not control the DKIM signing key.
* The proof is anchored to an institutional authority (Google, Microsoft, the employer's mail server) without requiring that institution's cooperation or knowledge.
* Emails are one of the richest sources of verified data: purchase confirmations, account notifications, salary information, medical records, legal communications, and more.

#### What Can Be Verified

**Commerce & Transactions**

* Purchase receipts from any online retailer
* Subscription confirmations and renewal notices
* Payment confirmations from banks and payment processors
* Shipping and delivery notifications

**Employment & Income**

* Payroll notifications confirming salary ranges
* Employment verification emails
* Offer letters and contract confirmations
* Performance review notifications

**Identity & Credentials**

* Account creation confirmations (proving account ownership)
* Two-factor authentication emails (proving account access)
* Government communications (tax notices, benefit confirmations)
* Educational notifications (enrollment, grades, degree completion)

**Financial**

* Bank transaction alerts (proving spending patterns without exact amounts)
* Investment account statements
* Credit card approval notifications
* Loan approval or pre-qualification confirmations

#### Security Model

* **DKIM signatures are institutional.** The proof's integrity is anchored to the email provider's signing key, which the user does not control and cannot forge.
* **Selective disclosure.** The proof reveals only the specified claim. The rest of the email (sender address, other content, attachments, timestamps) is not exposed.
* **No email access required.** Your application never accesses the user's inbox. The proof is generated on the user's device from a locally stored email.
* **Tamper detection.** Any modification to the email content invalidates the DKIM signature, making it impossible to create a valid proof from a forged email.
