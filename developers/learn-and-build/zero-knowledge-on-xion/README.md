# Zero Knowledge on XION

XION supports advanced **Zero Knowledge (ZK)** tools to enable developers to build privacy-preserving, verifiable applications that interact with real-world data, securely and credibly. Whether you're building identity-based apps, verification layers, or attestation protocols, the ZK stack on XION gives you the tools to prove anything online without compromising privacy.



## Why Zero Knowledge?

Zero Knowledge Proofs allow users to prove a fact is true, without revealing any underlying data. In the context of XION, this means:

* Verifying user attributes (like age, membership, or ownership) without sharing personal data.
* Connecting on-chain logic to real-world, off-chain data sources like APIs and platforms.
* Building privacy-first apps that still maintain trust and transparency.



## zkTLS: Verifying Web2 Data with Zero Knowledge

**zkTLS** is a powerful protocol that lets developers verify HTTPS data (web2 websites, dashboards, portals) using zero knowledge proofs. It bridges the gap between trusted web2 platforms and on-chain smart contracts, without needing an API or centralized integration.

#### Use Cases

* Prove you have a certain follower count on X (formerly Twitter).
* Prove attendance to a concert by showing a login to Ticketmaster.
* Verify you're a student by logging into your university portal.
* Authenticate any HTTPS session with trustless guarantees.

### How it Works on XION

XION integrates zkTLS via the [Reclaim Protocol](https://reclaimprotocol.org), allowing mobile users to generate zk-proofs of identity or status, and enabling smart contracts on XION to verify them.

Get started with our guide on [zkTLS Integration using Reclaim in a Xion Mobile App](../../mobile-app-development/zktls-integration-using-reclaim-in-a-xion-mobile-app.md).



## zkFetch: Prove Anything Behind an API

**zkFetch** extends the power of zkTLS by letting you fetch and prove arbitrary data from authenticated APIs, both public and private. It gives developers an interface to generate verifiable claims from any web service that requires login or access tokens.

### Example Use Cases

* Prove your win/loss ratio in a gaming API.
* Prove your fitness activity from Strava.
* Prove subscription status on platforms like Netflix or Spotify.
* Prove earnings from freelancing platforms like Upwork or Fiverr.

### Why it Matters

With zkFetch, you’re not limited to static web data. You can verify API-authenticated information using the Reclaim App and connect it to your XION dApp—creating rich, dynamic verification flows.

Get started with our guide on [Verifying API Data with Zero Knowledge (zkFetch)](verifying-api-data-with-zero-knowledge-zkfetch.md).



## Reclaim Protocol: Your Gateway to zk Identity

[Reclaim Protocol](https://reclaimprotocol.org) is the underlying infrastructure powering both **zkTLS** and **zkFetch** on XION. It enables users to privately generate and submit zk proofs from their mobile device based on real-world credentials.

#### Key Features

* Mobile-first integration with the Reclaim App
* OAuth2-style provider setup for developers
* Seamless zkTLS and zkFetch support
* Used in production on XION mobile dApps

Whether you're proving your online reputation, access rights, or user attributes—Reclaim gives your app verifiability, security, and user privacy out of the box.
