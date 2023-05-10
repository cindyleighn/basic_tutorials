# Prerequisites

## NodeJS

Download: https://nodejs.org/en/download

<br />

or bash script:
```bash
curl "https://nodejs.org/dist/latest/node-${VERSION:-$(wget -qO- https://nodejs.org/dist/latest/ | sed -nE 's|.*>node-(.*)\.pkg</a>.*|\1|p')}.pkg" > "$HOME/Downloads/node-latest.pkg" && sudo installer -store -pkg "$HOME/Downloads/node-latest.pkg" -target "/"
```

<br />

or homebrew:
```bash
brew install node
```

<br />

Check installation:
```bash
node --version
```
> Output:
> ```
> v18.12.1
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
