# Hardhat Tutorial

## Step 1: Prepare directory and install Hardhat

<br />

Open a bash terminal session in the directory you will be saving your code to and do the following:

Create a new directory for your project:
```bash
mkdir sample-coin && cd sample-coin
```

<br />

Initiate your project:
```bash
yarn init --yes
```

<br />

Install project dependencies:
```bash
yarn add hardhat
```

<br />

Check hardhat installation:
```bash
yarn hardhat --version
```
> Output:
> ```
> 2.18.3
> ```

---

<br />

## Step 2: Initiate hardhat project

Initiate the hardhat project:
```bash
yarn hardhat init
```
- On the first project choose Create a TypeScript project
- Leave defaults for the rest of the prompts (press enter for all)

<br />

Open the sample-coin directory in VSCode:
```
code .
```

<br />

Explore files in default project with sample contract and tests

---

<br />

## Step 3: Compile, test and deploy

Open a terminal session in VSCode, and ensure you are in the project directory (sample-coin)

Compile the sample project:
```bash
yarn hardhat compile
```
> Note the artifacts directory.  If you inspect the artifacts/contracts/Lock.json file you will see the resulting ABI (application binary interface) from the compiled contract which can be used for integration into your applications for interacting with the contract.

<br />

Run tests:
```bash
yarn hardhat test
```

<br />

Deploy (in-process blockchain testnet):
```bash
yarn hardhat run scripts/deploy.ts
```
> Output: 
> ```
> Lock with 0.001ETH and unlock timestamp 1698225041 deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3
> ```

---

<br />

## Step 4: Run standalone testnet, deploy smart contract, and interact with it using Hardhat console

Run the localhost testnet:
```bash
yarn hardhat node
```

<br />

In a new terminal session, deploy the smart contract to the localhost testnet:
```bash
yarn hardhat run scripts/deploy.ts --network localhost
```
> Note the transaction details in the terminal running the hardhat localhost testnet
> ```
> eth_sendTransaction
>   Contract deployment: Lock
>   Contract address:    0x5fbdb2315678afecb367f032d93f642f64180aa3
>   Transaction:         0x5c0035470fb12ca9fac7b7aa74a1c511d447d3236aa88bb4a381ef824b30acc2
>   From:                0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
>   Value:               0.001 ETH
>   Gas used:            326112 of 30000000
>   Block #1:            0x00e8e554ffe5311427114994c7aeb6ca6d148ea64aec59db516b51c9cbb25687
> ```
> You will need the ***Contract address*** when interacting with the contract

<br />

NOTE: The Lock contract will lock the allocated ETH for 1 minute on deployment of the contract. A withdrawal will fail if you execute it within 1 minute of contract deployment.

Return to the terminal used to deploy the smart contract and start an instance of Hardhat console:
```bash
yarn hardhat console --network localhost
```

<br />

In the Hardhat console session, get the contract object using the contract address you got at deployment, and execute the contract `withdraw()` function:
```bash
const address = '0x5FbDB2315678afecb367f032d93F642f64180aa3';
const contract = await ethers.getContractAt("Lock", address);

//call withdraw
await contract.withdraw()
```
> Output:
> ```json
> ContractTransactionResponse {
>   provider: HardhatEthersProvider {
>     _hardhatProvider: LazyInitializationProviderAdapter {
>       _providerFactory: [AsyncFunction (anonymous)],
>       _emitter: [EventEmitter],
>       _initializingPromise: [Promise],
>       provider: [BackwardsCompatibilityProviderAdapter]
>     },
>     _networkName: 'localhost',
>     _blockListeners: [],
>     _transactionHashListeners: Map(0) {},
>     _eventListeners: []
>   },
>   blockNumber: 2,
>   blockHash: '0x51284d053a28ad78adf18c297abc4b4e4536198df37bf02ad246cfdf4ee60e8f',
>   index: undefined,
>   hash: '0x4a1811e3c96fa406d451bd39730ae89f383ec645b12bd8855071120a97ac3993',
>   type: 2,
>   to: '0x5FbDB2315678afecb367f032d93F642f64180aa3',
>   from: '0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266',
>   nonce: 1,
>   gasLimit: 30000000n,
>   gasPrice: 768002900n,
>   maxPriorityFeePerGas: 0n,
>   maxFeePerGas: 972003670n,
>   data: '0x3ccfd60b',
>   value: 0n,
>   chainId: 31337n,
>   signature: Signature { r: "0x9adcdb381ca7819f6eb3325e0ef4999eb71465e06dab3157e88839ca2d1a388a", s: "0x3d28475451929f9c48fa0c917d2aee7aaa89b4f335cb84fcdc883285a10718e4", yParity: 0, networkV: null },
>   accessList: []
> }
> ```

To exit the hardhat console press Ctrl+C and then press Ctrl+C again.

---

<br />

## Step 5: Modify contract to use `console.log`

In the contracts/Lock.sol smart contract, uncomment lines 5 and 25:
> ```
> // Uncomment this line to use console.log
> import "hardhat/console.sol";
> ...
>     function withdraw() public {
>         // Uncomment this line, and the import of "hardhat/console.sol", to print a log in your terminal
>         console.log("Unlock time is %o and block timestamp is %o", unlockTime, block.timestamp);
> ...
> ```

<br />

Compile and redeploy the smart contract and execute the `withdraw()` function again and note the console in the Hardhat node instance terminal:
> ```
> console.log:
>     Unlock time is '1698226099' and block timestamp is '1698226214'
> ```

---

<br />

## Step 6: Clean up

To clean up the Hardhat project, kill any running nodes and run:
```
yarn hardhat clean
```

---

<br />

[Next: ERC20 Token Tutorial](ERC20.md)

[Home](README.md)

---

*Adapted from fangjun's Series: [A Concise Hardhat Tutorial Series' Articles](https://dev.to/yakult/series/16254)*