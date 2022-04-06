# GateChain Grant Delivery report

## `Milestone 1` — Smart Contract Development

| Delivery Date | Deliverable | Specification |
|-|-|-|
| 06.04 | Smart Contracts | 0. We will Research about GateChain standards and other factors & protocols that distinguish it from other chains<br/>1. Transferring (ERC-721/ERC-1155) - we will develop & add transferring functionality<br/>2. Freeze/Unfreeze - we will develop and add locking/unlocking functionality for Native NFTs<br/>3. Minting/Burning - we will develop minting/burning functionality for wrapped NFTs<br/>4. TX fees - we will develop the functionality allowing to withdraw on the target chain TX fees in native tokens<br>5. Multisig - we will add the Schorr signature verification functionality<br/>6. Whitelisting - we will develop & add NFT smart contract whitelisting functionality<br/>7. Pause/Unpause - we will implement pausing/unpausing functionality<br/>8. Reimbursing of the expences - we will add the validators' expences reimbursing functionality|

## `Milestone 2` — Integrating into the Bridge Components
Work-in-Progress<br/>
https://github.com/XP-NETWORK/xpjs/commit/06533f92431ef1e27142607136430865ba1972e1


## `Milestone 3` — Testing & Documenting

Work-in-Progress

| Delivery Date | Deliverable | Specification |
|-|-|-|
| 13.04 | Tests & Documentation | 1. Documentation - we will provide bridge documentation allowing to interact with the bridge UI and the JS library.<br/>2. Unit-testing - The code will have proper unit-test coverage 85% to ensure functionality and robustness.<br/>3. Testing SCs - We will deploy and test the contracts in the testnet environment (See below)|

## 3.1 Testing Documentation

### 3.1.1 Initiating a Node.js project
```bash
mkdir testing_gate_chain_project
cd testing_gate_chain_project/
yarn init -y
```

### 3.1.2 Installing the JavaScript Library

```bash
yarn add "git+https://github.com/xp-network/xpjs#bleeding-edge" @elrondnetwork/erdjs ethers @taquito/taquito @temple-wallet/dapp dotenv
```

### 3.1.3 Importing the dependancies

```ts
import {
    ChainFactoryConfigs,  ChainFactory,
    ElrondHelper,         ElrondParams,
    TronHelper,           TronParams,
    Web3Helper,           Web3Params,
    AppConfigs,
    NftMintArgs,          Chain
} from "xp.network";
import {config} from 'dotenv';
config();
  
// Instantiate the chain factory for the TESTNET
const testnetConfig = ChainFactoryConfigs.TestNet();
const factory = ChainFactory(AppConfigs.TestNet(), testnetConfig);
```

### 3.1.4 Creating a signer object

```bash
touch .env
echo "SK=<replace with your Provate Key>" >> .env
```

```ts
// EVM chains compatible wallet:
import { Wallet } from "ethers";
// EVM signer for testing in the BE
const signer = new Wallet(
  //  Private Key Of the Signer
  process.env.SK!,
  testnetConfig.iotexParams?.provider
);
// Print out your signer object to see that it matches the expected wallet
console.log("signer", signer);
```

### 3.1.5 Creating inner Blockchain objects

```ts
(async () => {
  
  // Inner Object ============= Chain Nonce == Chain Nonce ==
  // GateChain:
  const gatechain = await factory.inner<23>(Chain.GATECHAIN);
  // Other EVM-compatible chains:
  const bsc       = await factory.inner<4>(Chain.BSC);
  const ethereum  = await factory.inner<5>(Chain.ETHEREUM);
  const avax      = await factory.inner<6>(Chain.AVALANCHE);
  const polygon   = await factory.inner<7>(Chain.POLYGON);
  const fantom    = await factory.inner<8>(Chain.FANTOM);
  const IoTeX     = await factory.inner<19>(Chain.IoTeX);
  const gnosis    = await factory.inner<14>(Chain.XDAI);
  const harmony   = await factory.inner<12>(Chain.HARMONY);
  const iotex     = await factory.inner<20>(Chain.IOTEX);

  // Non-EVM chains:
  const tron      = await factory.inner<9>(Chain.TRON);
  const elrond    = await factory.inner<2>(Chain.ELROND);
  const avalanche = await factory.inner<15>(Chain.ALGORAND);
  const tezos     = await factory.inner<18>(Chain.TEZOS);
  // To view an inner object:
  console.log("gatechain:", gatechain);
})();
```

### 3.1.6 Minting NFTs for testing
```ts
(async () => {
   const nftResult = await factory.mint(
      gatechain,
      process.env.SK!,
      {
        contract: "0x9cdda01E00A5A425143F952ee894ff99B5F7999F",
        uris:["<Replace with your metadata URI>"]
      } as NftMintArgs
   );
   console.log("Minting result:", nftResult);
})();
```

### 3.1.7 Getting a list of NFTs a user owns on a Blockchain
```ts
(async () => {
  // Getting a list of NFTs
  const gateChainNFTs = await factory.nftList(
    gatechain,      // The inner chain object
    signer.address  // The public key of the user
  );
  // To view a list of NFTs:
  console.log("NFTs:", gateChainNFTs);
})();
```
### 3.1.8  Transferring an NFT
```ts
(async () => {
    // Calling the function:
    const web3Result = await factory.transferNft(
    gatechain,                  // The Source Chain.
    ethereum,                   // The Destination Chain.
    gateChainNFTs[0],           // The NFT selected by the index.
    signer,                     // The web3 signer object.
    "ADDRESS OF THE RECEIVER"   // The address who you are transferring the NFT to.
  );
  console.log(web3Result);
})();
```

## 3.3 Deployed Smart Contracts

|Name|GateChain Testnet Address|
|:-:|:-:|
|UserNftMinter|0x9cdda01E00A5A425143F952ee894ff99B5F7999F|
|Erc1155Minter|0xeBCDdF17898bFFE81BCb3182833ba44f4dB25525|
|XPNft1155|0x8CEe805FE5FA49e81266fcbC27F37D85062c1707|
|XPNft|0x3fe9EfFa80625B8167B2F0d8cF5697F61D77e4a2|
|Minter|0x2B24de7BFf5d2ab01b1C53682Ee5987c9BCf1BAc|