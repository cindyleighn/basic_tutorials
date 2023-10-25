# ERC20 Token Tutorial

This uses the base project created in the [Hardhat Tutorial](Hardhat.md). You should be in the `/sample-coin>` directory on your terminal instance.

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
[OpenZeppelin Wizard](https://wizard.openzeppelin.com/) is a helpful tools to setup an ERC20 token contract.  The default sample is:
> ```typescript
> // SPDX-License-Identifier: MIT
> pragma solidity ^0.8.20;
> 
> import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
> import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";
> 
> contract MyToken is ERC20, ERC20Permit {
>     constructor() ERC20("MyToken", "MTK") ERC20Permit("MyToken") {}
> }
> ```

<br />

For our sample coin contract, we will create an initial supply of our sample coins and the entire supply will be minted and sent the the wallet used to deploy the contract, during deployment. The contract will leverage contracts available in the OpenZeppelin library. This is our final contract::
```typescript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract SampleCoin is ERC20 {
    constructor(uint256 initialSupply) ERC20("SampleCoin", "SPC") {
        _mint(msg.sender, initialSupply * 10 ** decimals());
    }
}
```

Create a new contract under the contracts directory called `SampleCoin.sol' and save your smart contract code

<br />

The contract we will create will rely on solidity version 0.8.20. At the time of writting the `hardhat.config.ts` defaults to 0.8.19. To support our contract we need to change the configuration as follows:
```
const config: HardhatUserConfig = {
  solidity: "0.8.20",
};
``` 

<br />

Compile the contract:
```bash
yarn hardhat compile
```

---

<br />

## Step 3: Write a deploy script

<br />

Create a deploy script (adapted from https://docs.openzeppelin.com/learn/deploying-and-interacting) under the scripts directory called `deploy_SampleCoin.ts`:
```typescript
import { ethers } from "hardhat";

async function main() {
  const initialSupply = 10000;

  const SampleCoin = await ethers.getContractFactory("SampleCoin");
  const token = await SampleCoin.deploy(initialSupply);

  await token.waitForDeployment();

  const totalSupply = await token.totalSupply()

  console.log(
    `SampleCoin deployed to ${await token.getAddress()} with an initialSupply ${totalSupply}`
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

Deploy the smart contract using the `deploy_SampleCoin.ts` script:
```bash
yarn hardhat run scripts/deploy_SampleCoin.ts
```
> Output:
> ```
> SampleCoin deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3 with an initialSupply 10000000000000000000000
> ```
> 
> NOTE: Our sample coin has 18 decimals. We can't use decimal values in the contracts, hence our supply shows 10000000000000000000000, which is in fact 10000 sample coins.
---

<br />

## Step 4: Write Unit Test

<br />

In the hardhat official tutorial, there is a detailed explanation of unit test at https://hardhat.org/tutorial/testing-contracts.html. We adapted the test script in it with several necessary changes.

<br />

In the test directory, create the `SampleCoin.test.ts`:
```typescript
// We import Chai to use its asserting functions here.
import { loadFixture } from "@nomicfoundation/hardhat-network-helpers";
import { expect } from "chai";
import { ethers } from "hardhat";

describe("SampleCoin", function () {
  // We define a fixture to reuse the same setup in every test.
  // We use loadFixture to run this setup once, snapshot that state,
  // and reset Hardhat Network to that snapshot in every test.

  const initialSupply = 10000;

  async function deploySampleCoinFixture() {
    // Contracts are deployed using the first signer/account by default
    const [owner, otherAccount] = await ethers.getSigners();

    const SampleCoin = await ethers.getContractFactory("SampleCoin");
    const token = await SampleCoin.deploy(initialSupply);

    return { token, owner, otherAccount };
  }

  describe("Deployment", function () {
    it("Should assign the total supply of tokens to the owner", async function () {
      const { token, owner } = await loadFixture(deploySampleCoinFixture);
      const total = await token.totalSupply();
      expect(total).to.equal(await token.balanceOf(owner.address));
    });
  });

  describe("Transaction", function () {
    it("Should transfer tokens between accounts", async function () {
      const { token, owner, otherAccount } = await loadFixture(
        deploySampleCoinFixture
      );

      const ownerBalance = await token.balanceOf(owner.address);

      await token.transfer(otherAccount.address, 50);
      const addr1Balance = await token.balanceOf(otherAccount.address);
      expect(addr1Balance).to.equal(50);

      const ownerNewBalance = await token.balanceOf(owner.address);
      expect(ownerNewBalance).to.equal(ownerBalance - 50n);
    });

    it("Should fail if sender doesn’t have enough tokens", async function () {
      const { token, owner, otherAccount } = await loadFixture(
        deploySampleCoinFixture
      );

      // Transfer 10001 SPC tokens from owner to otherAccount
      await expect(
        token.transfer(otherAccount.address, ethers.parseEther("10001"))
      ).to.be.revertedWithCustomError(token, "ERC20InsufficientBalance");
    });
  });
});
```

Run unit tests:
```bash
yarn hardhat test test/SampleCoin.test.ts
```
> Output:
> ```bash
>   SampleCoin
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
yarn hardhat run scripts/deploy_SampleCoin.ts --network localhost
```
> Output:
> ```
> SampleCoin deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3 with an initialSupply 10000000000000000000000
> ```

<br />

Open a hardhat console session:
```bash
yarn hardhat console --network localhost
```

<br />

Get a smart contract instance and retrieve read-only data:
```typescript
fromWei = ethers.formatEther;
toWei = ethers.parseEther;

const address = '0x5FbDB2315678afecb367f032d93F642f64180aa3';
const token = await ethers.getContractAt("SampleCoin", address);

const accounts = await hre.ethers.getSigners();
owner = accounts[0].address;
//'0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266'
toAddress = accounts[1].address;
//'0x70997970C51812dc3A010C7d01b50e0d17dc79C8'

await token.symbol()
//'SPC'

totalSupply = await token.totalSupply();
fromWei(totalSupply)
//10000.0
```

Some explanation on how to get contract instance:
- We can get contract instance using Ethers.js directly ([docs](https://docs.ethers.io/v5/api/contract/contract/#Contract--creating)): new ethers.Contract( address , abi , signerOrProvider ). By this way, we need to get abi from the compile output (in artifacts directory).
- We can use hardhat-ethers plugin's helper function ethers.getContractAt("SampleCoin", address) ([docs](https://hardhat.org/hardhat-runner/plugins/nomiclabs-hardhat-ethers#helpers)) as we do in the above code snippet.

<br />

You can also transfer token from your current address to another address by calling transfer(recipient, amount). This is a state-changing function of the ERC20 contract:
```typescript
await token.transfer(toAddress, toWei('100'));

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