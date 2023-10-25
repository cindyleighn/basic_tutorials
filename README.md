# Prerequisites

## NodeJS v20.9.0 (LTS in October 2023)

Download: https://nodejs.org/en/download

<br />

or bash script:
```bash
curl "https://nodejs.org/dist/v20.9.0/node-v20.9.0.pkg" > "$HOME/Downloads/node-v20.9.0.pkg" && sudo installer -store -pkg "$HOME/Downloads/node-v20.9.0.pkg" -target "/"
```

<br />

or homebrew:
```bash
brew install node@20
```

<br />

Check installation:
```bash
node --version
```
> Output:
> ```
> v20.9.0
> ```

---

<br />

## Yarn

Download https://code.visualstudio.com/download

<br />

Bash script:
```bash
npm install -g yarn
```

<br />

Check installation:
```bash
yarn --version
```
> Output:
> ```
> 1.22.19
> ```

---

<br />

## VSCode

Download https://code.visualstudio.com/download

---

<br />

# Tutorials

- [Hardhat Tutorial](Hardhat.md)
- [ERC20 Token Tutorial](ERC20.md)
- [ERC721 NFT Tutorial](ERC721.md)
- [Nodejs Express Dapp using ethers.js](Dapp.md)
