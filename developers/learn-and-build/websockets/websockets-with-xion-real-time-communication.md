# WebSockets with Xion: Real-Time Communication

WebSockets are a powerful tool for enabling real-time, bidirectional communication between a client and a server. Within the Xion ecosystem by integrating WebSockets, you can:

* Enable real-time updates for decentralized apps (dApps).
* Stream blockchain events (e.g., transactions, smart contract triggers) to clients.
* Build interactive and responsive user experiences.
* Reduce latency compared to traditional polling methods.



## **Prerequisites**

Before diving in, ensure you have [NPM and Node.js](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) installed on your machine.:



## **Create a New Project**

### **Initialize a New Node.js Project**

Make sure you have `npm` and `node` installed. Then, create a new Node.js project by running:

```bash
npm init -y
```

This command creates a `package.json` file with default settings.

#### **Install Required Dependencies**

The most basic method for interacting with WebSockets in a Node.js environment is via the `ws` package. Install  `ws` via the following command:

```bash
npm install ws
```

We will also use `uuid` to generate a unique id for our more complex example. Install `uuid` using the following command:

```bash
npm install uuid
```

### **Setting up** the Main Script

#### **Create an `index.js` File**

Within your project directory, create an `index.js.` This file will contain all the code we'll create in the sections below.



## Integrating WebSockets into your Project

Below, we’ll cover the simplest way to set up WebSockets in your JavaScript project.

### **Establishing a WebSocket Connection**

You can find websocket endpoints for any of the available XION networks on the [Public Endpoints & Resources](../../section-overview/public-endpoints-and-resources.md) page. Here’s how to connect to a Xion node using a WebSocket endpoint (add to your index.js file):

```javascript
const WebSocket = require('ws');

const socket = new WebSocket('wss://rpc.xion-testnet-2.burnt.com:443/websocket');

socket.on('open', () => {
  console.log('WebSocket connection established');
});

socket.on('close', () => {
  console.log('WebSocket connection closed');
});

socket.on('error', (error) => {
  console.error('WebSocket error:', error);
});
```

### **Subscribing to Blockchain Events**

One of the most powerful features of WebSockets is the ability to subscribe to blockchain events in real time. For example, you can listen for new transactions or smart contract events.

#### **Example: Subscribing to New Blocks**

Add the following to your index.js file:

```javascript
socket.on('open', () => {
  console.log('WebSocket connection established');

  const subscribeMessage = JSON.stringify({
    jsonrpc: '2.0',
    id: 1,
    method: 'subscribe',
    params: {
      query: "tm.event='NewBlock'"
    }
  });
  
  socket.send(subscribeMessage);
});

socket.on('message', (data) => {
  console.log('Received data:', data.toString());
  
  try {
    const message = JSON.parse(data.toString());
    if (message.result && message.result.data && message.result.data.value) {
      console.log('New block:', message.result.data.value);
    }
  } catch (err) {
    console.error('Error parsing message:', err);
  }
});
```

### **Running the Project**

#### **Execute the Script**

Run the `index.js` script using Node.js:

```bash
node index.js
```

You should see output indicating that the connection to Websocket endpoint has been established, and new blocks and transactions will be logged to the console as they occur.



## **Example Project: Real-Time Transaction Tracker**

This code creates a real-time transaction monitoring system where any time a set address receives tokens the tracker will spit out the details of the transaction. Let's break it down section by section:

### Import and Setup

```javascript
const WebSocket = require('ws');
const { v4: uuidv4 } = require('uuid');
const address = 'xion1l5fq4la4jjh827wnay8g022z69mumz84jwl6zt';
let websocket;
let wsQuery;
```

This section imports the necessary libraries and sets up initial variables:

* The `ws` library provides WebSocket functionality for real-time communication
* `uuid` generates unique identifiers for each subscription request
* `address` stores the blockchain address we're monitoring
* `websocket` and `wsQuery` are initialized as empty variables to store the connection and query

### Main Function: queryForBalanceUpdate()

```javascript
const queryForBalanceUpdate = () => {
  try {
    websocket = new WebSocket('wss://rpc.xion-testnet-2.burnt.com:443/websocket');
    wsQuery = {
      jsonrpc: '2.0',
      method: 'subscribe',
      id: uuidv4().toString(),
      params: {
        query: `tm.event = 'Tx' AND transfer.recipient CONTAINS '${address}'`,
      },
    };
    // Event handlers follow...
```

This function:

1. Establishes a WebSocket connection to the XION testnet RPC endpoint
2. Creates a subscription request
3. Uses a unique ID (via UUID) for each request
4. Sets up a query filter that watches for transactions where our address appears as a recipient

### Event Handlers

```javascript
websocket.on('open', () => {
  console.log('WebSocket connection established');
  websocket.send(JSON.stringify(wsQuery));
});

websocket.on('message', (event) => {
  const eventData = JSON.parse(event);
  if (eventData && eventData.result && eventData.result.data) {
    console.log('Matching transaction found: ' + JSON.stringify(eventData.result.data));
    disconnectFromWebsocket();
  }
});

websocket.on('error', (error) => {
  console.error(error);
  disconnectFromWebsocket();
});
```

These event handlers manage the WebSocket lifecycle:

* `open`: When connection is established, it sends our subscription request
* `message`: When data is received, it parses the JSON response, logs matching transactions, and disconnects
* `error`: Handles and logs any errors, then disconnects

### Cleanup Function: disconnectFromWebsocket()

```javascript
const disconnectFromWebsocket = () => {
  if (!websocket || websocket.readyState !== WebSocket.OPEN) return;
  websocket.send(JSON.stringify({ ...wsQuery, method: 'unsubscribe' }));
  websocket.close();
  websocket = null;
  wsQuery = null;
};
```

This function ensures proper cleanup:

1. Checks if the connection is still open
2. Sends an unsubscribe request using the same ID as the subscription
3. Properly closes the connection
4. Resets the variables to null

### Process Management

```javascript
process.on('exit', () => {
  disconnectFromWebsocket();
});

queryForBalanceUpdate();
```

The final section:

1. Sets up an event listener to clean up the connection when the Node.js process exits
2. Starts the monitoring by calling the main function

### **Putting It All Together**

#### **Example: Real-Time Transaction Tracker**

Here’s the complete example.

```javascript
// Import the WebSocket library for real-time bidirectional communication.
const WebSocket = require('ws');
// Import the UUID library to generate a unique ID for each subscription request.
const { v4: uuidv4 } = require('uuid');
// Define the address that is checked in the transactions.
const address = 'xion1...';
// Initialize websocket and wsQuery variables.
let websocket;
let wsQuery;
// This function initiates a WebSocket connection and sends a subscription request to track transactions that fulfill certain conditions.
const queryForBalanceUpdate = () => {
  try {
    // Open a new WebSocket connection to the specified URL.
    websocket = new WebSocket('wss://rpc.xion-testnet-2.burnt.com:443/websocket');
    // Define the subscription request. It asks for transactions where the recipient address, and checks for transactions to be published.
    wsQuery = {
      jsonrpc: '2.0',
      method: 'subscribe',
      id: uuidv4().toString(),
      params: {
        query: `tm.event = 'Tx' AND transfer.recipient CONTAINS '${address}'`,
      },
    };
    // When the WebSocket connection is established, send the subscription request.
    websocket.on('open', () => {
      websocket.send(JSON.stringify(wsQuery));
    });
    // When a message (i.e., a matching transaction) is received, log the transaction and close the WebSocket connection.
    websocket.on('message', (event) => {
      const eventData = JSON.parse(event);
      if (eventData && eventData.result && eventData.result.data) {
        console.log('Matching transaction found: ' + JSON.stringify(eventData.result.data));
        disconnectFromWebsocket();
      }
    });
    // If an error occurs with the WebSocket, log the error and close the WebSocket connection.
    websocket.on('error', (error) => {
      console.error(error);
      disconnectFromWebsocket();
    });
  } catch (err) {
    // If an error occurs when trying to connect or subscribe, log the error and close the WebSocket connection.
    console.error(err);
    disconnectFromWebsocket();
  }
};
// This function closes the WebSocket connection and resets the websocket and wsQuery variables.
const disconnectFromWebsocket = () => {
  // If the WebSocket isn't open, exit the function.
  if (!websocket || websocket.readyState !== WebSocket.OPEN) return;
  // Send an 'unsubscribe' message to the server.
  websocket.send(JSON.stringify({ ...wsQuery, method: 'unsubscribe' }));
  // Close the WebSocket connection.
  websocket.close();
  // Reset the websocket and wsQuery variables.
  websocket = null;
  wsQuery = null;
};
// When the process is exiting, close the WebSocket connection if it's still open.
process.on('exit', () => {
  disconnectFromWebsocket();
});
// Start the process by calling the queryForBalanceUpdate function.
queryForBalanceUpdate();
```

#### **Execute the Script**

Run the `index.js` script using Node.js:

```bash
node index.js
```

When tokens are transferred to the address set in the script the script will print out the details of the transfer.
