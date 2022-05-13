# GateChain Grant Delivery report

## `Milestone 1` — Bridge Integration

| Delivery Date | Deliverable | Specification |
|:-:|:-:|:-|
|2022/04/06|Integration|1.1. We've' researched about GateChain standards and other factors & protocols that distinguish it from other chains<br/>1.2. [Validation](https://github.com/XP-NETWORK/migration-validator) - we've' added the validation logic relevant for the GateChain part of the bridge<br/>1.3. [TX fee estimation]() - we've' integrated GateChain to the [TX fee estimator](https://github.com/XP-NETWORK/estimator)<br/>1.4. [Heartbeat](https://github.com/XP-NETWORK/validator-pinger) - we've' plugged GateChain to the bridge heartbeat<br/>1.5.Node integration - we've'plugged to a [node](), started [testnet](https://testnet.gatenode.cc) and [mainnet](https://evm.gatenode.cc) event listening and TX submission<br/>1.6. UI - we've'integrated GateChain in the bridge UI<br/>1.7. Smart Contracts - we've' deployd [smart contracts](#33-deployed-smart-contracts) on the testnet<br/>1.8. [JS library](https://github.com/XP-NETWORK/xpjs/commit/06533f92431ef1e27142607136430865ba1972e1) - we will add GateChain to the bridge<br/>1.9. REST API - we will add GateChain to the REST API<br/>1.10. Widget - we've' added GateChain to the bridge widget<br/>1.11. Testing SCs - We've tested the contracts in the testnet environment|

## `Milestone 2` Mainnet Launch

### 2.1

| Estimated Delivery Date | Deliverable | Specification |
|:-:|:-:|:-|
| 2022/04/21 | Launch | 2.1 We have deployed smart contracts on the mainnet.<br/>2.2 We have integrated GateChain the:<br/> + [JavaScript Library](https://github.com/XP-NETWORK/xpjs/blob/9a46fbaa13f443c4fb4b7f1747e7786050d414be/src/factory/factories.ts#L474), <br/>+ [NFT-Indexer](https://github.com/XP-NETWORK/nft-index/search?q=gatechain), <br/>+ [Relay Validators](https://github.com/XP-NETWORK/migration-validator/blob/15d3f4f7d824767682fa022f2e69075539652a1c/src/config.ts#L248)<br/>2.3. We will make announcements about integrating GateChain in the bridge|

### 2.2 3.3 Deployed Smart Contracts on the Mainnet

|Name|GateChain Mainnet Address|
|:-:|:-:|
|Bridge SC|0xFc7f7fD2DBdAF6D8F3ee3f222b3a6a9f89729f05|
|ERC721|0xD6939f722B977afd7DD934A31bc94d08d4ea4336|
|ERC-1155|0xc45759e51CdDBa46db4D1becC8B8Bcbe5d4a9bB4|

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

## 3.3 Deployed Smart Contracts on the Testnet

Since there are no marketplaces on most of the testnets, use `UserNftMinter` contract for minting NFTs on the GateChain testnet. 

|Name|GateChain Testnet Address|
|:-:|:-:|
|UserNftMinter|0x9cdda01E00A5A425143F952ee894ff99B5F7999F|
|ERC-1155|0x8CEe805FE5FA49e81266fcbC27F37D85062c1707|
|ERC0-721|0x3fe9EfFa80625B8167B2F0d8cF5697F61D77e4a2|
|Bridge SC|0x2B24de7BFf5d2ab01b1C53682Ee5987c9BCf1BAc|