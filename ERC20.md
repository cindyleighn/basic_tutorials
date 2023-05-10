# ERC20 Token Tutorial

## Step 1: Install OpenZeppelin

<br />

Install OpenZeppelin contracts:
```bash
yarn add @openzeppelin/contracts
```

---

<br />

## Step 2: Write an ERC20 Token with OpenZeppelin

<br />

You can create an ERC20 token by inheriting the OpenZeppelin ERC20 immplementation. 
[OpenZeppelin Wizard](https://docs.openzeppelin.com/contracts/4.x/wizard) is a helpful tools to setup an ERC20 token contract.  The default sample is:
> ```typescript
> // SPDX-License-Identifier: MIT
> pragma solidity ^0.8.9;
> 
> import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
> 
> contract MyToken is ERC20 {
>     constructor() ERC20("MyToken", "MTK") {}
> }
> ```

<br />

We will change the values in the constructor, and add a parameter to accept the initial supply amount:
```typescript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract RaboCoin is ERC20 {
    constructor(uint256 initialSupply) ERC20("RaboCoin", "RBC") {
        _mint(msg.sender, initialSupply * 10 ** decimals());
    }
}
```

Create a new contract under the contracts directory called `RaboCoin.sol' and save your smart contract code

<br />

Compile the contract:
```bash
yarn hardhat compile
```

---

<br />

## Step 3: Write a deploy script

<br />

Create a deploy script (adapted from https://docs.openzeppelin.com/learn/deploying-and-interacting) under the scripts directory called `deploy_RaboCoin.ts`:
```typescript
import { ethers } from "hardhat";

async function main() {
  const initialSupply = 10000;

  const RaboCoin = await ethers.getContractFactory("RaboCoin");
  const token = await RaboCoin.deploy(initialSupply);

  await token.deployed();

  const totalSupply = await token.totalSupply()

  console.log(
    `RaboCoin deployed to ${token.address} with an initialSupply ${totalSupply}`
  );
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

<br />

Deploy the smart contract using the `deploy_RaboCoin.ts` script:
```bash
yarn hardhat run scripts/deploy_RaboCoin.ts
```
> Output:
> ```
> RaboCoin deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3 with an initialSupply 10000000000000000000000
> ```

---

<br />

## Step 4: Write Unit Test

<br />

In the hardhat official tutorial, there is a detailed explanation of unit test at https://hardhat.org/tutorial/testing-contracts.html. We adapted the test script in it with several necessary changes.

<br />

In the test directory, create the `RaboCoin.test.ts`:
```typescript
// We import Chai to use its asserting functions here.
import { loadFixture } from "@nomicfoundation/hardhat-network-helpers";
import { expect } from "chai";
import { ethers } from "hardhat";

describe("RaboCoin", function () {
    // We define a fixture to reuse the same setup in every test.
    // We use loadFixture to run this setup once, snapshot that state,
    // and reset Hardhat Network to that snapshot in every test.

    const initialSupply = 10000;

    async function deployRaboCoinFixture() {

      // Contracts are deployed using the first signer/account by default
      const [owner, otherAccount] = await ethers.getSigners();

      const RaboCoin = await ethers.getContractFactory("RaboCoin");
      const token = await RaboCoin.deploy(initialSupply);

      return { token, owner, otherAccount };
    }

    describe("Deployment", function () {
      it("Should assign the total supply of tokens to the owner", async function () {
        const { token, owner } = await loadFixture(deployRaboCoinFixture);
        const total = await token.totalSupply();
        expect(total).to.equal(await token.balanceOf(owner.address));
      });

    });

    describe("Transaction", function () {

        it("Should transfer tokens between accounts", async function () {
            const { token, owner, otherAccount } = await loadFixture(deployRaboCoinFixture);

            const ownerBalance = await token.balanceOf(owner.address);

            await token.transfer(otherAccount.address, 50);
            const addr1Balance = await token.balanceOf(otherAccount.address);
            expect(addr1Balance).to.equal(50);

            const ownerNewBalance = await token.balanceOf(owner.address);
            expect(ownerNewBalance).to.equal(ownerBalance.sub(50));
        });

        it("Should fail if sender doesn’t have enough tokens", async function () {
            const { token, owner, otherAccount } = await loadFixture(deployRaboCoinFixture);

            // Transfer 10001 RBC tokens from owner to otherAccount
            await expect(
             token.transfer(otherAccount.address, ethers.utils.parseEther('10001'))
            ).to.be.revertedWith("ERC20: transfer amount exceeds balance");
        });        

      });

});
```

Run unit tests:
```bash
yarn hardhat test test/RaboCoin.test.ts
```
> Output:
> ```bash
>   RaboCoin
>     Deployment
>       ✔ Should assign the total supply of tokens to the owner (918ms)
>     Transaction
>       ✔ Should transfer tokens between accounts
>       ✔ Should fail if sender doesn’t have enough tokens
> 
>   3 passing (974ms)
> ```

---

<br />

## Step 5: Deploy smart contract to localhost and interact with it

<br />

Open a terminal session and run a Hardhat localhost node:
```bash
yarn hardhat node
```

<br />

In your working terminal, deploy the contract to localhost:
```bash
yarn hardhat run scripts/deploy_RaboCoin.ts --network localhost
```
> Output:
> ```
> RaboCoin deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3 with an initialSupply 10000000000000000000000
> ```

<br />

Open a hardhat console session:
```bash
yarn hardhat console --network localhost
```

<br />

Get a smart contract instance and retrieve read-only data:
```typescript
fromWei = ethers.utils.formatEther;
toWei = ethers.utils.parseEther;

const address = '0x5FbDB2315678afecb367f032d93F642f64180aa3';
const token = await ethers.getContractAt("RaboCoin", address);

const accounts = await hre.ethers.getSigners();
owner = accounts[0].address;
//'0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266'
toAddress = accounts[1].address;
//'0x70997970C51812dc3A010C7d01b50e0d17dc79C8'

await token.symbol()
//'RBC'

totalSupply = await token.totalSupply();
fromWei(totalSupply)
//10000.0
```

Some explanation on how to get contract instance:
- We can get contract instance using Ethers.js directly ([docs](https://docs.ethers.io/v5/api/contract/contract/#Contract--creating)): new ethers.Contract( address , abi , signerOrProvider ). By this way, we need to get abi from the compile output (in artifacts directory).
- We can use hardhat-ethers plugin's helper function ethers.getContractAt("RaboCoin", address) ([docs](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-ethers#helpers)) as we do in the above code snippet.

<br />

You can also transfer token from your current address to another address by calling transfer(recipient, amount). This is a state-changing function of the ERC20 contract:
```typescript
await token.transfer(toAddress, toWei('100'))

ownerBalance = await token.balanceOf(owner);
fromWei(ownerBalance);
//'9900.0'

toBalance = await token.balanceOf(toAddress);
fromWei(toBalance)
//'100.0'
```

---

<br />

[Next: ERC721 NFT Tutorial](ERC721.md)

[Home](README.md)

---

*Adapted from fangjun's Series: [A Concise Hardhat Tutorial Series' Articles](https://dev.to/yakult/series/16254)*