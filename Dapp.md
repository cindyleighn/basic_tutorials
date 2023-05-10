# Nodejs Express Dapp using ethers.js

## Step 1: Create project and install dependendencies

<br />

Create a new directory for your project:

```bash
mkdir dapp && cd dapp
```

<br />

Initiate your project:

```bash
yarn init --yes
```

<br />

Install dependencies:

```bash
yarn add express ethers --save-dev
```

<br />

---

<br />

## Step 2: Create an Express Server

<br />

Create a src directory and create a new file called `app.js`:

```bash
mkdir src && touch src/app.js
```

<br />

Open the `app.js` file and add the following code:

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get("/", (req, res) => {
  res.send("Hello World!");
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

<br />

Update `main` value in `package.json`:

```json
"main": "src/app.js",
```

<br />

Add a new scripts to `package.json`:

```json
  "scripts": {
    "clean": "rm -rf node_modules/ yarn.lock",
    "start": "node src/app.js"
  },
```

Full `package.json` file:

```json
{
  "name": "dapp",
  "version": "1.0.0",
  "main": "src/app.js",
  "license": "MIT",
  "scripts": {
    "clean": "rm -rf node_modules/ yarn.lock",
    "start": "node src/app.js"
  },
  "dependencies": {
    "ethers": "^6.3.0",
    "express": "^4.18.2"
  }
}
```

<br />

Run the server:

```bash
yarn start
```

<br />

Open the app in your browser by going to http://localhost:3000.  You should see a page with "Hello World!" displayed.

<br />

To stop the server in the terminal window press ctrl+c

---

<br />

## Step 3: Install Metamask browser extension and configure Hardhat network connection

<br />

If you do not have metamask installed, download and install the metamask extension from [metamask.io](https://metamask.io/download/) using one of the supported browsers (Chrome, Firefox, Brave, Edge or Opera)

At install, it will ask some initial questions. You will want to `create a new wallet` and create a password. Choose a good password, you can not change the password later on.

Choose `Secure your wallet` to write down and backup your 12-word secret Recovery Phrase. Save it in a place that you trust and only you can access (password manager, safe deposit or write down and store in own secret place)

Finish the initial configuration.

<br />

The initial setup is now done, next we will add a new network. Go to the `Settings` menu, in there open the `Networks` tab and choose `Add a network` (add a network manually). Fill in the following network settings:

- Network name: Hardhat
- New RPC URL: http://127.0.0.1:8545/
- Chain ID: 31337
- Currency symbol: ETH

Then switch to this network.

<br />

Choose `Import account` and import the following accounts:

- Account #0 with private key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
- Account #1 with private key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d

---

<br />

## Step 4: Connect to Metamask and show the connected wallet details

<br />

First we will create a basic html page for our dapp.

<br />

Create a new file called `index.html` in the src directory:

```bash
touch src/index.html
```

<br />

Open the `index.html` in the src directory and add the following html:

```html
<html>
    <head>
        <title>Rabo-Coin Dapp</title>
    </head>
    <body>
        <script src="https://cdn.ethers.io/lib/ethers-5.7.2.umd.min.js" type="application/javascript"></script>
        <script src="https://cdn.jsdelivr.net/npm/big.js@6.2.1/big.min.js" type="application/javascript"></script>
        <script src="js/index.js" type="module"></script>
    </body>
</html>
```

> We will add the `js/index.js` page in the steps below.

<br />
We need a class to store our commonly used functions and values. Create a new directory called js in the src directory and create file in the new directory called `common.js`:

```bash
mkdir src/js && touch src/js/common.js
```

<br />

Open the `common.js` in the src/js directory and add the following:

```javascript
// configuration values
const networks = {
    '0x7a69': {
        chainId: '0x7a69',
        chainName: 'Hardhat localhost',
        nativeCurrency: { decimals: 18, symbol: 'ETH' },
        rpcUrls: ['http://127.0.0.1:8545/'],
    },
    '0x5': {
        chainId: '0x5',
        chainName: 'Goerli test network',
        nativeCurrency: { decimals: 18, symbol: 'GoerliETH' },
        rpcUrls: ['https://goerli.infura.io/v3/'],
        blockExplorerUrls: ['https://goerli.etherscan.io'],
    },
    '0xaa36a7': {
        chainId: '0xaa36a7',
        chainName: 'Sepolia test network',
        nativeCurrency: { decimals: 18, symbol: 'ETH' },
        rpcUrls: ['https://sepolia.infura.io/v3/'],
        blockExplorerUrls: ['https://sepolia.etherscan.io'],
    },
}

const defaultNetwork = '0x7a69'

const contracts = {
    '0x7a69': {
        contract: '0x5FbDB2315678afecb367f032d93F642f64180aa3',
        name: 'RaboCoin',
        symbol: 'RBC'
    },
    '0x5': {
        contract: '',
        name: 'RaboCoin',
        symbol: 'RBC'
    },
    '0xaa36a7': {
        contract: '',
        name: 'RaboCoin',
        symbol: 'RBC'
    },
}

export const config = {
    networks,
    contracts,
    defaultNetwork
}

// functions for building ui elements
function createMain() {
    const main = document.createElement('center')
    document.body.replaceChildren(main)
    createAndAppend(main, 'h2', 'Rabo-Coin dAPP')
    return main
}

function showMessage(text) {
    const main = createMain()
    createAndAppend(main, 'p', text)
}

function createAndAppend(parent, type, text) {
    const element = document.createElement(type)
    element.textContent = text
    parent.appendChild(element)
    return element
}

function showConnectButton(connectHandler) {
    let main = ui.createMain()
    let button = ui.createAndAppend(main, 'button', 'Connect my Metamask')
    button.setAttribute('class', 'connectButton')
    button.addEventListener('click', connectHandler)
}

export const ui = {
    createMain,
    showMessage,
    createAndAppend,
    showConnectButton
}
```

> Notes on the `common.js` class:
>
> Configuration values (config):
> - *networks*: array of configured networks that can be used in the dApp
> - *contracts*: array of deployed contracts per defined network
> - *defaultNetork*: the default network to start up on (default is Hardhat local node)
>
> Functions for creating UI elements (ui):
> - *createHeader*: creates a header for the page, and is used as the initial object for appending other elements to the page
> - *showMessage*: used to show a message to the screen with instructions to the user if metamask can't be connected
> - *createAndAppend*: used to create new elements and append it to a parent element
> - *showConnectButton*: used to display the connect screen

<br />

Next we need another class that will serve our html page. Create a file in the src/js directory called `index.js`:

```bash
touch src/js/index.js
```

<br />

Open the `index.js` in the src/js directory and add the following:
```javascript
import { config, ui } from './common.js'

let provider
let signer
let currentNetwork

async function checkWeb3() {
    if (typeof window.ethereum !== 'undefined') {
        console.log('Web3 provider is installed!')

        // add Metamask change handlers
        ethereum.on('chainChanged', handleChainChanged)
        ethereum.on('accountsChanged', handleAccountsChanged)

        provider = new ethers.providers.Web3Provider(window.ethereum)
        signer = provider.getSigner()
        try {
            // check if already connected by getting the wallet address
            await signer.getAddress()
            console.log('Metamask connected!')
            await checkNetwork()
        } catch (err) {
            // failed to get wallet address, show the connect button
            console.log('Metamask not connected!')
            ui.showConnectButton(() => { handleConnectClick() })
        }
    } else {
        console.log('Please install MetaMask!')
        ui.showMessage('Please install MetaMask!')
    }
}

async function handleConnectClick() {
    console.log('Connecting...')
    let connectButton = document.querySelector('.connectButton')
    connectButton.setAttribute('disabled', true)
    try {
        // connect
        await ethereum.request({ method: 'eth_requestAccounts' })
        await checkNetwork()
    } catch (err) {
        connectButton.removeAttribute('disabled')
        if (err.code === 4001) {
            // user rejected the connection
            console.log('Please connect to MetaMask.')
        } else {
            console.error(err)
            ui.showConnectButton(() => { handleConnectClick() })
        }
    }
}

async function checkNetwork() {
    let chainId = await ethereum.request({ method: 'eth_chainId' })

    if (config.networks[chainId] !== undefined) {
        currentNetwork = config.networks[chainId]
        await showMain()
    } else if (ethereum.isMetaMask) {
        // current network not found in configuration, try switch to default network
        await switchNetwork(config.defaultNetwork)
    } else {
        ui.showMessage('Please switch to one of the supported networks.')
    }
}

async function switchNetwork(chainId) {
    try {
        await ethereum.request({
            method: 'wallet_switchEthereumChain',
            params: [{ chainId: config.networks[chainId].chainId }],
        })
    } catch (switchError) {
        if (switchError.code === 4902) {
            // network is not configured, try to add it
            await addNetwork(chainId)
        }
    }
    provider = new ethers.providers.Web3Provider(window.ethereum)
    signer = provider.getSigner()
}

async function addNetwork(chainId) {
    try {
        await ethereum.request({
            method: 'wallet_addEthereumChain',
            params: [config.networks[chainId]],
        })
    } catch (addError) {
        if (addError.code === 4001) {
            console.log('Please approve the network.')
        } else {
            console.error(addError)
        }
    }
}

async function handleChainChanged(chainId) {
    console.log('Chain changed to ' + chainId)
    currentNetwork = config.networks[chainId]
    // reload provider and signer on chain change
    provider = new ethers.providers.Web3Provider(window.ethereum)
    signer = provider.getSigner()
    if (currentNetwork !== undefined) {
        // show main section
        await showMain()
    } else {
        // reload or ask to change network
        ui.showMessage('Please switch to one of the supported networks.')
    }
}

async function handleAccountsChanged(account) {
    console.log('Account changed to ' + account)
    if (account.length === 0) {
        // MetaMask is locked or the user has not connected any accounts
        ui.showConnectButton(() => { handleConnectClick() })
    } else {
        if (currentNetwork !== undefined) await showMain()
    }
}

async function showMain() {
    // Create root element for ui
    let main = ui.createMain()

    // Show connected address
    const address = await signer.getAddress()
    console.log('Address ' + address)
    ui.createAndAppend(main, 'p', 'Account: ' + address)

    // Show network selector
    ui.createAndAppend(main, 'text', 'Network: ')
    let networkSelector = ui.createAndAppend(main, 'select', '')
    for (const [chainId, network] of Object.entries(config.networks)) {
        const opt = ui.createAndAppend(networkSelector, 'option', network.chainName)
        opt.setAttribute('value', chainId)
        if (currentNetwork.chainId == chainId) opt.setAttribute('selected', 'true')
    }
    networkSelector.addEventListener('change', async (event) => {
        const chainId = event.target.value
        console.log('Switch to chainId ' + chainId)
        await setupNetwork(chainId)
    })

    // Show balance of native currency on connected account
    const symbol = currentNetwork.nativeCurrency.symbol
    const balance = await signer.getBalance()
    console.log('Balance ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + symbol)
    ui.createAndAppend(main, 'p', 'Balance: ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + symbol)

    // Section break
    ui.createAndAppend(main, 'p', '---')

    // Create message element for displaying messages
    const messageDiv = createAndAppendDiv(main, '')
    messageDiv.setAttribute('class', 'message')
}

async function showConnectionDetails(main, address) {
    // Show connected address
    console.log('Address ' + address)
    ui.createAndAppend(main, 'p', 'Account: ' + address)

    // Show network selector
    ui.createAndAppend(main, 'text', 'Network: ')
    let networkSelector = ui.createAndAppend(main, 'select', '')
    for (const [chainId, network] of Object.entries(config.networks)) {
        const opt = ui.createAndAppend(networkSelector, 'option', network.chainName)
        opt.setAttribute('value', chainId)
        if (currentNetwork.chainId == chainId) opt.setAttribute('selected', 'true')
    }
    networkSelector.addEventListener('change', async (event) => {
        const chainId = event.target.value
        console.log('Switch to chainId ' + chainId)
        await switchNetwork(chainId)
    })

    // Show balance of native currency on connected account
    const symbol = currentNetwork.nativeCurrency.symbol
    const balance = await signer.getBalance()
    console.log('Balance ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + symbol)
    ui.createAndAppend(main, 'p', 'Balance: ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + symbol)

    // Section break
    ui.createAndAppend(main, 'p', '---')
}

async function showMain() {
    // create root element for ui
    let main = ui.createMain()

    // append connection details elements to main element
    const address = await signer.getAddress()
    await showConnectionDetails(main, address)

    // append message element for displaying messages
    let messageDiv = ui.createAndAppend(main, 'p', '')
    messageDiv.setAttribute('class', 'message')
}

// App entry point
checkWeb3()
```

> Notes on the `index.js` class:
> - *checkWeb3*: checks if an ethereum browser client is installed and checks if it is connect (app entry point)
> - *handleConnectClick*: handles the connect to metamask button click
> - *checkNetwork*: checks if the current network is supported and will try to switch to our configured default network (Hardhat)
> - *switchNetwork*: switches the network to the desired network by chainId
> - *handleChainChanged*: called when the network is changed in Metamask
> - *handleAccountsChanged*: called when the account is changed in Metamask
> - *createAndAppend*: used to create new elements and append it to a parent element
> - *showConnectionDetails*: displays the connected wallet details
> - *showMain*: called if network and account connections are successful

<br />

Open the `app.js` file and replace all code with the following to the following:
```javascript
const express = require('express');
const app = express();
const port = 3000;
const path = require('path');

app.use('/js', express.static(path.join(__dirname, 'js')));

app.get('/', function (req, res) {
    res.sendFile('index.html', { root: __dirname });
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`);
});
```

<br />

Make sure your Hardhat localhost environment is running in a separate terminal instance:

```bash
yarn hardhat node
```

<br />

In your working terminal instance, start the dapp:

```bash
yarn start
```

<br />

Open http://localhost:3000/ in your browser. Click on the button to connect to Metamask. You will need to approve the request with metamask. Once connected you will see your connected account address, the network you are connected to, and the balance of the account in ETH.

---

<br />

## Step 5: Connect to Rabo-Coin smart contract and create transfer functionality

<br />

We need to add the smart contract interface (abi) to be able to call the functions. Create a new file in the src/js directory called `abi.js`:

```bash
touch src/js/abi.js
```

<br />

Open the `abi.js` in the src/js directory and add export variable for you smart contract's abi (application binary interface):

```javascript
export const raboCoinABI = <<The value (array) of the abi property in your compiled smart contract code under /rabo-coin/artifacts/contracts/RaboCoin.sol/RaboCoin.json>>;
```

<br />

Add new import statement in to the top of the `index.js` file under the src/js directory:

```javascript
import { raboCoinABI } from './abi.js'
```

<br />

Add the following new functions above the `showMain()` function:

```javascript
async function handleAddTokenClick(button, contract) {
    button.setAttribute('disabled', true)
    try {
        await ethereum.request({
            method: 'wallet_watchAsset',
            params: {
                type: 'ERC20',
                options: {
                    address: contract.address,
                    symbol: await contract.symbol(),
                    decimals: await contract.decimals(),
                }
            }
        })
        console.log('Successfully added!')
    } catch (err) {
        console.log(err)
        document.querySelector('.message').textContent = (err.data !== undefined) ? err.data.message : err.message
    }
    button.removeAttribute('disabled')
}

async function waitForReceipt(tx) {
    while (true) {
        const r = await provider.getTransactionReceipt(tx.hash)
        // sleep
        await new Promise(resolve => setTimeout(resolve, 2500))
        if (r != null) return r
    }
}

async function handleTransferSubmit(button, contract) {
    button.setAttribute('disabled', true)
    // get transfer details
    const amount = document.querySelector('.amount').value
    const address = document.querySelector('.address').value
    console.log('amount: ' + amount)
    console.log('address: ' + address)
    try {
        // execute contract transfer function
        const tx = await contract.transfer(address, ethers.utils.parseUnits(amount, 18))
        console.log(tx)
        document.querySelector('.message').textContent = 'Transaction submitted, waiting for confirmation...'
        try {
            await waitForReceipt(tx)
            await showMain()
            document.querySelector('.message').textContent = 'Transfer confirmed!'
        } catch (err) {
            console.log(err)
            document.querySelector('.message').textContent = (err.data !== undefined) ? err.data.message : err.message
        }
    } catch (err) {
        console.log(err)
        document.querySelector('.message').textContent = (err.data !== undefined) ? err.data.message : err.message
    }
    button.removeAttribute('disabled')
}

async function showContractSection(main, address) {
    // Show balance of RBC token on connected account
    const rbcConfig = config.contracts[currentNetwork.chainId];

    if (rbcConfig.contract !== '') {
        const contract = new ethers.Contract(rbcConfig.contract, raboCoinABI, signer);
        const balance = await contract.balanceOf(address)
        console.log(rbcConfig.name + ' RBC Balance ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + rbcConfig.symbol)
        ui.createAndAppend(main, 'p', rbcConfig.name + ' Balance: ' + ethers.utils.commify(ethers.utils.formatEther(balance)) + ' ' + rbcConfig.symbol)

        // Show button to allow user to import RBC token into Metamask
        if (ethereum.isMetaMask) {
            let button = ui.createAndAppend(main, 'button', 'Add ' + rbcConfig.symbol + ' token into MetaMask')
            button.addEventListener('click', () => {
                handleAddTokenClick(button, contract)
            })
        }

        // If the connected account has RBC tokens show the transfer form
        if (!balance.isZero()) {
            let block = ui.createAndAppend(main, 'p', 'Transfer ')

            let inputAmount = ui.createAndAppend(block, 'input', '')
            inputAmount.setAttribute('class', 'amount')

            ui.createAndAppend(block, 'text', ' ' + rbcConfig.symbol + ' to address ')

            let inputAddress = ui.createAndAppend(block, 'input', '')
            inputAddress.setAttribute('class', 'address')

            ui.createAndAppend(block, 'text', ' ')
            let button = ui.createAndAppend(block, 'button', 'Submit')
            button.addEventListener('click', async () => {
                handleTransferSubmit(button, contract)
            })
        } else {
            ui.createAndAppend(main, 'p', 'No balance, cannot transfer ' + rbcConfig.symbol + ' tokens')
        }
    } else {
        ui.createAndAppend(main, 'p', rbcConfig.symbol + ' contract address missing for selected network')
    }

    // Section break
    ui.createAndAppend(main, 'p', '---')
}
```

> Notes on additional `index.js` functions:
> - *handleAddTokenClick*: attempts to add the token to Metamask on the connected account (button click handler)
> - *waitForReceipt*: waits for a receipt for a transaction (called in handleTransferSubmit)
> - *handleTransferSubmit*: attempts to execute a transfer transaction for the provided input (button click handler)
> - *showContractSection*: creates all the UI elements to be displayed in this step in the exercise

<br />

Update the `showMain()` function to include a call to the new `showContractSection` function we added with the following:

```javascript
async function showMain() {
    // create root element for ui
    let main = ui.createMain()

    // append connection details elements to main element
    const address = await signer.getAddress()
    await showConnectionDetails(main, address)

    // append contract section elements to main element
    await showContractSection(main, address)

    // append message element for displaying messages
    let messageDiv = ui.createAndAppend(main, 'p', '')
    messageDiv.setAttribute('class', 'message')
}
```

---

[Home](README.md)
