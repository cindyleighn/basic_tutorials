# Hardhat Tutorial

## Step 1: Prepare directory and install Hardhat

<br />

Open a bash terminal session in the directory you will be saving your code to and do the following:

Create a new directory for your project:
```bash
mkdir rabo-coin && cd rabo-coin
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
> 2.14.0
> ```

---

<br />

## Step 2: Initiate hardhat project

Initiate the hardhat project:
```bash
yarn hardhat
```
- On the first project choose Create a TypeScript project
- Leave defaults for the rest of the prompts (press enter for all)

<br />

Open the rabo-coin directory in VSCode:
```
code .
```

<br />

Explore files in default project with sample contract and tests

---

<br />

## Step 3: Compile, test and deploy

Open a terminal session in VSCode, and ensure you are in the project directory (rabo-coin)

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
> Lock with 0.001ETH and unlock timestamp 1679119583 deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3
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
>   Transaction:         0x26b0d85de3bf5fa7530264fef2edfc89bd9eaba89730b9c338d4c5b7583ac11f
>   From:                0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
>   Value:               0.001 ETH
>   Gas used:            326016 of 326016
>   Block #1:            0x24288deb19e27e3ba6aa08dec267d4091c228984ec939ea2a9161cb872dbf905
> ```
> You will need the ***Contract address*** when interacting with the contract

<br />

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
> {
>   hash: '0x3a0269dc290e47e4001b9a8fd1c0e7a93f4443f6673c62f21d1960e3894fcca2',
>   type: 2,
>   accessList: [],
>   blockHash: '0x24b49f383d7cd24704595ece03abeb9e848b3b0f15fb1484db06c717c73185f3',
>   blockNumber: 2,
>   transactionIndex: 0,
>   confirmations: 1,
>   from: '0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266',
>   gasPrice: BigNumber { value: "768002200" },
>   maxPriorityFeePerGas: BigNumber { value: "0" },
>   maxFeePerGas: BigNumber { value: "972002784" },
>   gasLimit: BigNumber { value: "36493" },
>   to: '0x5FbDB2315678afecb367f032d93F642f64180aa3',
>   value: BigNumber { value: "0" },
>   nonce: 1,
>   data: '0x3ccfd60b',
>   r: '0x0ab5399e355a4795535827edb08c55efc63a412176b1fd3977b02923607c4b57',
>   s: '0x528706dd1b049ec0ad6ff85d3746e24a4dfbef4447e2b572b0d053ec0be8f28c',
>   v: 1,
>   creates: null,
>   chainId: 31337,
>   wait: [Function (anonymous)]
> }
> ```

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

Compile and redeploy the smart contract and execute the `withdraw()` function again and note the console in the Hardhat instance terminal:
> ```
> console.log:
>     Unlock time is '1679121200' and block timestamp is '1679121264'
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