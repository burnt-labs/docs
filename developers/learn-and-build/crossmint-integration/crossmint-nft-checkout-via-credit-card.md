# Crossmint Checkout - NFT Collection

The Crossmint integration with XION enables a seamless, credit card based NFT checkout experience. This is ideal for onboarding non-crypto native users into platforms where NFT assets are used, such as games, marketplaces, or digital collectibles.

## Crossmint Staging Environment

To get started, create a Crossmint staging account so you can test the integration in a sandbox environment.

1. Go to [https://staging.crossmint.com](https://staging.crossmint.com/) and click the "**Console**" button in the site header

<figure><img src="../../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

2. You will be taken to the **Signin** page. Make sure "**Staging**" is selected and then enter your sign in details

<figure><img src="../../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

3. If you're new, you'll be guided through the account creation process
4. Once your account is created you will be asked to create your first project
5. Once your project is created you will be taken to your console's overview page



### Create API Key

From the sidebar, click "**Integrate**", then click "**API Keys**".

![](<../../../.gitbook/assets/image (95).png>)\


#### Server Key

A **server key** is intended for backend (server-side) applications and should not be used in frontend code, as doing so will result in CORS (Cross-Origin Resource Sharing) errors. In this guide, we will not be using a server key since we’re building a frontend application that interacts with the Crossmint API.

If you want to test Crossmint’s APIs using their [API Reference](https://docs.crossmint.com), you’ll need to generate a server key to authorize your requests.

To create one, click **“Create new key”** under the **“Server-side keys”** section in the Crossmint Console.

{% hint style="info" %}
Server side keys offers a lot more scopes than Client side keys.
{% endhint %}

#### Client Key

Click the "**Create new key**" button under the "**Client-side keys**" section. Select the following scope to enable order execution via Crossmint’s checkout:

* `orders.create`

Then click "**Save client key**" Your client key will now appear in the client key list.



### Create Collection

The Crossmint team went ahead and allowed for the creation of a NFT collection within the Console. This NFT collection is based on the "[**CW-721 Metadata Onchain**](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-metadata-onchain)" contract. You are also able to import your custom NFT Contract which should be based on the "[**CW-721 Metadata Onchain**](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-metadata-onchain)".  To create your collection follow these steps:

The Crossmint team has enabled the creation of NFT collections directly within the Console. These collections are based on the [**CW-721 Metadata Onchain**](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-metadata-onchain) contract. You can also import a custom NFT contract, as long as it is built on the [**CW-721 Metadata Onchain**](https://github.com/public-awesome/cw-nfts/tree/main/contracts/cw721-metadata-onchain) standard.

To create your collection, follow these steps:

1. Click the "**Token collections**" link in the menu sidebar and then click the "**New collection**" button

<figure><img src="../../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

2. Enter the collection information and click "**Next**"
3. On the "**Create or import your collection contract**" step select "**Create a new contract**" and then click "**Next**". If you have your own custom NFT contract select "**Import an existing contract**"
4. On the "What do you want to do with this collection?" section select "**Sell NFTs**" and click "**Next**"
5. On "**Choose a chain**" select "**Xion**" and click "**Next**"
6. On "**Payment settings**" enter a price for the collection, select who pays the fees and enter an address that will receive the revenue from sales
7. Review details and click the "**Create collection**" button to complete the process



### Create an NFT

On the "**Token collections**" page select the collection created above. The NFTs tab is selected by default, click "**manual upload**" and in the window that pops up enter the NFT information.

<figure><img src="../../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

Click the "**Create NFT**" button when done.



### Update Checkout Settings

Click the "**Checkout**" tab and make sure the "**NFT Price**" and "**Recipient address**" are filled in and then click "**Save changes**".

Also make sure "**Enable Credit Card payments"** is enabled under "**Payment methods**".

<figure><img src="../../../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

## Deploy a Treasury Contract <a href="#deploying-a-treasury-contract-for-gasless-transactions" id="deploying-a-treasury-contract-for-gasless-transactions"></a>

Before integrating the **Abstraxion SDK** into the application, we first need to deploy a **Treasury Contract**. This contract facilitates **gasless transactions** for your smart contract by handling **fee grants** on behalf of users as well as allowing users to grant authorization(s) to your app to execute certain transactions on their behalf.

### Steps to Deploy a Treasury Contract <a href="#steps-to-deploy-a-treasury-contract" id="steps-to-deploy-a-treasury-contract"></a>

1. Login to the [XION Developer Portal](https://dev.testnet.burnt.com/).
2. Click the **"New Treasury"** button to create a new treasury contract instance.
3. **Select the appropriate configurations**. The default "**Fee Grant (Allowance)**" and "**Grant Config (Permission)**" should be enough for this application.

You will then copy the treasury contract address which will be required in the frontend setup below.



## Building the Frontend <a href="#building-the-frontend" id="building-the-frontend"></a>

We've created a frontend which is a [Next.js](https://nextjs.org/) application built to interact with the NFT smart contract created by the Crossmint platform. The frontend app can be downloaded at [https://github.com/burnt-labs/xion-crossmint-hosted-checkout-frontend](https://github.com/burnt-labs/xion-crossmint-hosted-checkout-frontend). It showcases how users can connect their wallets and purchase NFTs via Crossmint's hosted checkout solution.

### Manual Installation <a href="#manual-installation" id="manual-installation"></a>

You will first need to clone the repository:

```bash
git clone https://github.com/burnt-labs/xion-crossmint-hosted-checkout-frontend
```

After doing so you will have to change into the newly created directory and then execute the steps below.

1. Install dependencies:

```bash
cd xion-crossmint-hosted-checkout-frontend
npm install
```

2. Copy the **`.env.example`** file and name it **`.env.local`** and set the values with the correct information:

```javascript
NEXT_PUBLIC_TREASURY_ADDRESS=your_treasury_contract_address
NEXT_PUBLIC_RPC_URL="https://rpc.xion-testnet-2.burnt.com:443"
NEXT_PUBLIC_REST_URL="https://api.xion-testnet-2.burnt.com"
NEXT_PUBLIC_CROSSMINT_API_KEY=your_crossmint_api_key
```

| Variable                          | Description                                                                 |
| --------------------------------- | --------------------------------------------------------------------------- |
| NEXT\_PUBLIC\_TREASURY\_ADDRESS   | Treasury address used for gasless transactions and grantz authorization     |
| NEXT\_PUBLIC\_RPC\_URL            | RPC endpoint for Xion (default: `https://rpc.xion-testnet-2.burnt.com:443`) |
| NEXT\_PUBLIC\_REST\_URL           | REST endpoint for Xion (default: `https://api.xion-testnet-2.burnt.com`)    |
| NEXT\_PUBLIC\_CROSSMINT\_API\_KEY | Crossmint API key (https://www.crossmint.com/console)                       |



3. Update collection list:

This list contains the NFT contract addresses and their corresponding collection IDs that users will be able to purchase.\
Open the `src/app/collections.json` file and add your collections using the following format:

```json
{
  "collections": [
    {
      "id": "your-collection-id",
      "contractAddress": "your-nft-contract-address"
    }
  ]
} 
```

You can find the collection ID on the listing page for each collection. To copy it, simply click the copy icon next to the desired collection ID.

<figure><img src="../../../.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

To find the NFT contract click on the collection and then click the "**Smart contract**" tab and copy the smart "**Contract address**".

<figure><img src="../../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

4. Build and start the application:

```
npm run dev
```

You can now access the app at [http://localhost:3000](http://localhost:3000/) in your browser.
