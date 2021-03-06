# Building a Decentralized Blockchain

Liz Garcia

Full Stack Solutions Architect

[lizgarseeyah@gmail.com](lizgarseeyah@gmail.com)

[LinkedIn](https://www.linkedin.com/in/lizgarseeyah/)

[GitHub](https://www.github.com/lizgarseeyah/)

GitHub Page for this project: https://lizgarseeyah.github.io/Blockchain/

This project walks through the steps required to create a simple, immutable, decentralized blockchain using JavaScript. The blockchain will be encrypted using the SHA256 algorithm. The UI web app, called Block Explorer, was developed using HTML and CSS. Postman API app was used to send API commands to the endpoints. The Block Explorer program allows the user to send transactions, mine blocks, retrieve block information, and broadcast new nodes to the rest of the blockchain.

### Topics covered:
- Part I: Creating the Blockchain Data Structure
- Part II: Building out the API Endpoints
- Part III: Creating a Decentralized Network
- Part IV: Blockchain UI via Block Explorer

### Prerequisites (based on Mac OS 10.15.6):
- Node.js: terminal=>`brew install node` (also installs npm, requires Homebrew on system)
  - v14.14.0
- Express JS Library: terminal=> `npm i express`
- Body Parser: terminal=>`npm i body-parser --save`
- Request Promise Library: terminal => `npm install request-promise --save`
- [Postman App (v7.31.0)](https://www.postman.com/downloads/)
- [JSON Formatter(optional)](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa?hl=en)

## Part I: Creating the Blockchain

### Simple Blockchain Constructor Function:

The data structure of the Blockchain is created from a series of JavaScript constructors. The first step is to create an empty blockchain which is simply an array that stores our values:


`function Blockchain() {
   this.chain = [];
   this.pendingTransactions = [];
};`

### createNewBlock Function:

The next function below creates a new, empty block to be added to our blockchain:

```markdown
`Blockchain.prototype.createNewBlock = function(nonce, previousBlockHash, hash) {
    const newBlock = {
        index: this.chain.length + 1,
        timestamp: Date.now(),
        transactions: this.pendingTransactions,
        nonce: nonce,
        hash: hash,
        previousBlockHash: previousBlockHash 
    };

    this.pendingTransactions = []; //clear out array to start over for new block
    this.chain.push(newBlock);

    return newBlock;
};`
```

The createNewBlock function creates, in other words ‘mines’, a new block by which it is added onto the blockchain and attaches the following information:

- index: position on blockchain
- timestamp: when the block was created (i.e. mined)
- transactions: contains transaction data (amount, sender, recipient)
- nonce: part of proof-of-work; an arbitrary number used once and that miners seek to validate.
- hash: immutable, fixed-length SHA256 proof-of-work hash value.
- previousBlockHash: previous block’s hash value as part of a method to ensure the blockchain has not been tampered with.

To implement the above functions in the blockchain.js file, the line of code below exports the Blockchain function to be used in other programs:

`module.exports = Blockchain; //export blockchain.js
const Blockchain = require('./blockchain'); //added to test.js`

**Testing the Blockchain Function:**

The code below tests whether the function is able to create a blockchain structure:
`
const Blockchain = require('./blockchain');
const bitcoin = new Blockchain();
// prints blockchain
console.log(bitcoin);
`
To run the file, run the command below:

`node dev/test.js`

Expected Output: 

`Blockchain { 
    chain [ ], 
    newTransactions: [ ] 
 } `

### getLastBlock Function

This function retrieves the last block in the blockchain:

```markdown
`Blockchain.prototype.getLastBlock = function() {
    return this.chain[this.chain.length - 1];
};`
```

At this point, 3 fundamental Blockchain functions have been created using JavaScript: one creates a simple, empty blockchain, a second function creates new blocks (the mining function), and a third function that retrieves the last value from our blockchain. 

### createNewTransaction Function

Now that a basic blockchain structure has been created, the next step is to populate the blockchain with data. The function below accomplishes this by creating ‘transactions’ to be stored in an array:

```markdown
   `Blockchain.prototype.createNewTransaction = function(amount, sender, recipient) {
	   const newTransaction = {
	       amount: amount,
	       sender: sender,
	       recipient: recipient,
	   }
   };`
   ```

### How the transaction function works:

The function will receive user input for amount, sender information, and recipient information. Each time a new transaction is created it gets pushed into the ‘newTransactions’ array. However, the transactions that are in the ‘newTransactions’ array are not recorded onto the blockchain until the block has been mined (i.e. a new block is created). So transactions in the array are considered pending transactions until the function ‘createNewBlock’ is invoked and the data moves from ‘newTransactions’ array to ‘chain’ array.

### hashBlock Function

To secure the blockchain and ensure it’s integrity, we need to encrypt the data using SHA256. SHA256 is a widely known and used encryption algorithm, it’s more secure than other algorithms, impossible for two data blocks to have the same hash value (2^256 possibilities), and currently doesn’t have any known vulnerabilities that other algorithms have.

The function works by taking in a block and hashes it into a fixed-length, random string, for example DFJKA304004523. It uses a mathematical function to create this random value and outputs it as a string:

```markdown
`Blockchain.prototype.hashBlock = function(previousBlockHash, currentBlockData, nonce) {
   const dataAsString = previousBlockHash + nonce.toString() + JSON.stringify(currentBlockData)
   const hash = sha256(dataAsString);
   return hash;
};`
```

To make sure each block is legitimate, we need to repeatedly hash the previousBlockHash, currentblockData, and nonce until an acceptable hash is generated with the prefix: ‘0000’.

```markdown
`Blockchain.prototype.proofOfWork = function(previousBlockHash, currentBlockData) {
   let nonce = 0; //we use let here since this will be changing
   let hash = this.hashBlock(previousBlockHash, currentBlockData, nonce); // use let here since variable will be changing

If our hash value doesn’t start with 0000, increment nonce and run hashBlock again.

Generate a new hash if it still doesn’t start with 0000 increment nonce and start again….this could take 10 - 10,0000 times. This method takes alot of calculations and computing power.
   while (hash.substring(0, 4) !== '0000') {
       nonce++;
       hash = this.hashBlock(previousBlockHash, currentBlockData, nonce);
   }
Once we find the correctly generated hash, return nonce that gives us a valid hash
   return nonce;
};`
```

This ensures our blockchain is secure. In order to recreate a block, the 'proofOfWork function' will need to run hundreds of thousands of times and will require a lot of computing power to create the correct hash. This is how it secures the blockchain. It makes it hard to add more blockchain or modify it without first trying to decrypt the hash value. It also takes in the previous hash value which is another layer of complexity. All are linked together by their data. Re-mining and recreating the blocks is not feasible.

The complete blockchain source code can be accessed here.

## Part II: Building the API

This section focuses on building the API that is used to interact with our blockchain. This document will cover three blockchain API endpoints:

- app.get(‘/blockchain’, function(req, res){}); => sends back our entire blockchain
- app.post(‘/transaction’, function(req, res){});  => creates a new transaction on the blockchain
- app.get(‘/mine’, function(req, res){});  => create a new block for us.

Where 'req' is request, and 'res' is response.

As the decentralized network gets built, more APIs are added to handle the different blockchain functionalities. The networkNode.js file has a total of 13 defined API endpoints and can be viewed here. 

**Installing Express JS and Postman**

First, the Express JS library is used to create the server that communicates with the APIs. To setup Express JS, I ran the following commands in terminal to install dependencies:

`npm i express`

To use Express JS, I inserted the following lines of code into my networkNode.js file:

`const express = require('express');`
`const app = express();`
Source: https://www.npmjs.com/package/express 

Run server by running:

`node dev/networkNode.js.`

The second resource required to interact with the APIs is the Postman app. Postman allows us to make calls and send data to our three endpoints. The app can be downloaded from this link: https://www.postman.com.

### Testing out Postman

To test the Postman connection, select POST and type in http://localhost:3000/transaction/. Select JSON under the Body tab and type the following JSON object as a test case and hit send:

```markdown
`{
   "amount": 100,
   "sender": "FJMKDAJFEI9R23S",
   "recipient": "FADJFQ98U32QHUDS"
}`
```

A common error can be the missing body-parser dependency. To install it run the following commands in terminal:

`npm i body-parser --save`
`npm start`

The body-parser will check if JSON or a urlencoded form comes through, parse the data so we can access through any routes defined in the endpoints.
 
If everything works out, navigating to localhost:3000/blockchain will yield the following output:


Installing a JSON formatter will make the output more readable:

### Building the API Foundation:

**First API endpoint: retrieve entire Blockchain**

```markdown
`const bitcoin = new Blockchain(); //references blockchain.js file

// get entire blockchain


app.get('/blockchain', function (req, res) {
    res.send(bitcoin);
});`
```
Simply returns the entire blockchain.

**Second API endpoint: Creating a new transaction**

This API uses the createNewTransactions function to add a new transaction to the block.This endpoint expects three values: amount, sender, and recipient as input, because that is what the function calls for. A block number that the new transaction is added to is returned.

```markdown
`app.post('/transaction', function (req, res) {
	bitcoin.createNewTransaction(req.body.amount, req.body.sender, req.recipient);
});`
```

**Third API endpoint: Mine and create a new block**

This API uses the createNewBlock method. The createNewBlock method takes in three parameters by which additional calculations are needed to get these values. These parameters are: nonce, previousBlockHash, and hash. These values must be defined and included in our API endpoint.

`app.get('/mine', function(req, res) {
	const lastBlock = bitcoin.getLastBlock();
	const previousBlockHash = lastBlock['hash'];
	const currentBlockData = {
		transactions: bitcoin.pendingTransactions,
		index: lastBlock['index'] + 1
	};
	const nonce = bitcoin.proofOfWork(previousBlockHash, currentBlockData);
	const blockHash = bitcoin.hashBlock(previousBlockHash, currentBlockData, nonce);
	const newBlock = bitcoin.createNewBlock(nonce, previousBlockHash, blockHash);
	const requestPromises = [];
	bitcoin.networkNodes.forEach(networkNodeUrl => {
		const requestOptions = {
			uri: networkNodeUrl + '/receive-new-block',
			method: 'POST',
			body: { newBlock: newBlock },
			json: true
		};`
		
At this point, we have one single blockchain controlled by our APIs => centralized network. This section focuses on building out a decentralized network. Having a decentralized network improves security of the blockchain. We’re going to make many instances of the API which represents a network node that works together to host our blockchain so no one ba d player can cheat the system and we can verify with the other nodes.

## Part III: Creating a Decentralized Blockchain

At this point, we have one single blockchain controlled by our APIs => centralized network. This section focuses on building out a decentralized network. Having a decentralized network improves security of the blockchain. We’re going to make many instances of the API which represents a network node that works together to host our blockchain so no one bad player can cheat the system and we can verify with the other nodes.

**Creating Multiple Nodes:**

Instead of relying on one API node, we are going to create a collection of API nodes. To accomplish this, we have to make our port a variable instead of hard coded so that it runs on multiple ports (3001, 3002, etc.). 
In the networkNode.json API file, we defined the port as a variable:

`const port = process.argv[3];`

This refers to the nodemon command in the package.json file where the first two elements in argv array are nodemon server commands and the 3rd element is the port number:
```markdown
 `"scripts": {

   "test": "echo \"Error: no test specified\" && exit 1",
   "node_1": "nodemon --watch dev -e js dev/networkNode.js 3001 http://localhost:3001",
   "node_2": "nodemon --watch dev -e js dev/networkNode.js 3002 http://localhost:3002",
   "node_3": "nodemon --watch dev -e js dev/networkNode.js 3003 http://localhost:3003",
   "node_4": "nodemon --watch dev -e js dev/networkNode.js 3004 http://localhost:3004",
   "node_5": "nodemon --watch dev -e js dev/networkNode.js 3005 http://localhost:3005"`
   ```

Removing hard coding:

In terminal run: 
`npm run node_1`

To run multiple nodes:

`npm run node_2`
`npm run node_3`
`npm run node_4`
`npm run node_5`

### Additional APIs:

- **Register-and-broadcast-node:** Register node and broadcast it to the network.

- **Register-node:** register a node with the network

![reg-and-broadcast](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/reg-and-broadcast.jpeg)

To add 3009 to the network, hit the /register-and-broadcast-node endpoint on any node. The new node URL ,localhost:3009, is passed in as data. So it registers the data on it’s own node, then broadcasts it to other nodes.

![reg-node](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/reg-node.jpeg)

The data is passed along to the other nodes by calling the  /register-node endpoints at each node.
Then the original node calls the /register-nodes-bulk endpoint back to the new node and sends the network node data. So now the new node is part of the network.

![reg-node-bulk](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/reg-node-bulk.jpeg)

`// register a node and broadcast it the network
app.post('/register-and-broadcast-node', function(req, res) {


	const newNodeUrl = req.body.newNodeUrl;
If not already present then add it
	if (bitcoin.networkNodes.indexOf(newNodeUrl) == -1) bitcoin.networkNodes.push(newNodeUrl);
	const regNodesPromises = [];
To broadcast
	bitcoin.networkNodes.forEach(networkNodeUrl => {
Everytime we register a node, we need to define it’s options to use for each request.
		const requestOptions = {
			uri: networkNodeUrl + '/register-node',
			method: 'POST',
			body: { newNodeUrl: newNodeUrl },
			json: true
		};`

To use these options. We get back an array of promises. These requests just register new node with network. These requests are asynchronous; we don’t know how long it’s going to calculate this data because it is reaching an outside source which are the other nodes in the network. Since it’s asynch we’ll just place it in an array. regNodesPromises.

		regNodesPromises.push(rp(requestOptions));

	});
To allow the node to make a request to all the other nodes, we install the request-promise library
Npm install request-promise --save

// register a node with the network




app.post('/register-node', function(req, res) {
Take input url and save it
	const newNodeUrl = req.body.newNodeUrl;


	const nodeNotAlreadyPresent = bitcoin.networkNodes.indexOf(newNodeUrl) == -1;


	const notCurrentNode = bitcoin.currentNodeUrl !== newNodeUrl;
Add to network if not already present
	if (nodeNotAlreadyPresent && notCurrentNode) bitcoin.networkNodes.push(newNodeUrl);


	res.json({ note: 'New node registered successfully.' });


});

## Part IV: Block Explorer

The Block Explorer web app relies on several more endpoints to retrieve the data:

![code](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/code.png)

**Testing Block Explorer:**

In terminal, navigate to the folder containing the project.
Start the server by running the following in terminal:

- `npm run node_1`
- `npm run node_2`
- `npm run node_3`
- `npm run node_4`
- `npm run node_5`

Go to a chrome browser and navigate to: http://localhost:3001/block-explorer 

![block-explorer](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/block-explorer.png)

The next step is to register and connect all nodes using the `register-and-broadcast-node` API since none of the nodes are currently connected in the network.

Go to Postman and enter the following information:

- Method=> POST: http://localhost:3001/register-node 
- JSON object: { “newNodeUrl” : “http://localhost:3001” }
- Click on “Send”
- Repeat replacing 3001 with 3002, 3003, 3004, 3005 in the JSON object code.

![postman](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/postman.jpeg)

Now the nodes are registered:

![rboutput](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/rboutput.jpeg)

Before adding data, the next step is to mine (or create) a few blocks. Navigate to localhost:3005/mine and localhost:3004/mine. The following message should appear indicating successful creation of blocks:

![mine](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/mine.png)

To send the data, enter the following in Postman:
- POST http://localhost:3002/transaction/broadcast
- JSON Object:
`{
   "amount": 4300,
   "sender": "JKFADJREPQ8489",
   "recipient": "KJFDAJE9PAE8"
}`

Repeat the previous step a few more times, changing the JSON Object and sending the data to the blockchain.

Hitting the refresh button on the /mine endpoint will yield the following results:

Now that we have a blockchain, we use the information above to query the block information using the Block Explorer:

![final](https://github.com/lizgarseeyah/Blockchain/blob/gh-pages/img/final.png)
