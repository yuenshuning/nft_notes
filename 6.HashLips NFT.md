## 6.HashLips NFT

1. Mint an NFT for free on [Mintable.app](https://mintable.app/). Aother website called [opensea]() is more popular but it's not possible to sell for free.

2. How to create an NFT collection

   1. [HashLips Art Engine](https://github.com/HashLips/hashlips_art_engine) Code description

      1. `layers/`: basic assets of image generation and fusion

      2. `src/config.js`: configuration of image generation. The `layersOrder` object describes the order of generative images (the order matters), where the values of key-value pairs <u>should correspond to</u> the folder name of `layers/` one by one

      3. If we want to set the rarity of layers, set the file name of layers as `xxx#weight.png`, otherwise, the weight is equal to default weight (1). See `src/main.js/getRarityWeight`

      4. If we want to run two or more different configurations at the same time, we need to grow the amount of `growEditionSizeTo`

         ```js
         const layerConfigurations = [
           {
             // 5 eye images
             growEditionSizeTo: 5,
             layersOrder: [ { name: "Background" }, { name: "Eyeball" }, { name: "Eye color" }, { name: "Iris" }, { name: "Shine" }, { name: "Bottom lid" }, { name: "Top lid" } ]
           }, {
             // 3 bear images
             growEditionSizeTo: 8,
             layersOrder: [ { name: "bear_background" }, { name: "bear_body" }, { name: "bear_clothes" }, { name: "bear_hair" } ]
           }
         ];
         ```

      5. If we want to change the image baseUri from defalut `ipfs://NewUriToReplace` to other value, change the `baseUri` in `src/config.js`

      6. Customize the description of each image by changing the `description` in `src/config.js`

      7. Shuffle the generated images by setting the `shuffleLayerConfigurations` in `src/config.js` to `true`

      8. Resize the size of generated images by changing the `format` in `src/config.js`

      9. Setting a base color as background by setting the `background` in `src/config.js`

      10. Run `node index.js` to generate images according to `src/config.js` and `layers/` assets.

      11. Run `node utils/xxx,js` to use the utils

      12. The generative images and corresponding JSON files (metadata) locate in `build/`

   2. Metadata is neccessary, because metadata is the information of a particular image. It's in the format of JSON object. That is why we generate images with JSON files.

   3. Upload our generated images (JSON files cannot be uploaded) to [pinata](https://app.pinata.cloud/) ipfs hosting server, and get an unique image CID. Go to `src/config.js`, change the `baseUri` to `ipfs://{image CID}`, run `node utils/update_info.js` to <u>only</u> update the JSON files (do not re-generate images)

   4. Upload our generated JSON files to [pinata](https://app.pinata.cloud/) ipfs hosting server, and get an unique metadata CID.

   5. Upload our hidden_image and hidden_metadata the same way, and get unique hidden_image CID and hidden_metadata CID.

   6. [HashLips NFT Contract](https://github.com/HashLips/hashlips_nft_contract) Code description

      1. Change the `maxSupply` in `SimpleNft_flat.sol` to the amount of images we generated
      2. Change other variables such as `cost`, `maxMintAmount`, `paused`, `revealed`
      3. make sure the solidity compile matches the pragma
      4. make sure we are on `Injected Web3` environment
      5. configure the Metamask and choose test net (rinkeby). Then Remember the id under the `Injected Web3` environment shown on remix, which should look like `Custom (4) network`
      6. make sure we choose the right contract on remix to deploy (Bear.sol) and set parameters. Note:
         1. the `_initBaseURI` should be `ipfs://{metadata CID}/`. Such as `ipfs://QmdVXTnbXPLBk5JjiXcZCyS6z42ugEh8265apaa7LACW4F/`
         2. the `_initNotRevealedUri` should be `ipfs://{hidden_metadata CID}/hidden.json`. Such as `ipfs://QmdwkKuAkgx2CBUMGYJMp68kiJwEYU6dhcGT2KC7G71HWL/hidden.json`
      7. Before click the `transact` button, click the small `copy` icon left to it, and save the information to local .txt file. We need this information to verify the contract.
      8. Then click the `transact` button to deploy the contract. When deployed, call the `pause` function on remix and change `pause` to `false`. Set value from `0 wei` to `cost`
      9. Click the small `copy` button icon of `Deployed Contracts` and save the information to local .txt file.
      10. Click the `mint`, `reveal` (show the real images rather than the hidden image) and other functions to have a try.
      11. Then go to OpenSea. Not the normal OpenSea, but the [testnets OpenSea](https://testnets.opensea.io/) the normal OpenSea only show the items on network with `id=1` which is the main net. We can see the hidden_bear on OpenSea testnet (if the OpenSea is delayed, try refreshing).
      
   7. [HashLips NFT Minting Dapp](https://github.com/HashLips/hashlips_nft_minting_dapp) Code description

      1. Set up: run `npm i` to install the dependency
      2. Run locally: run `npm run start`
      3. Packup and prepare for deployment: run `npm run build`. If the website deployed on server has some failed images, change the access permission of the images.
      4. `public/config/images`: basic assets/images
      5. `public/config/theme.css`: global css style
      6. `public/config/config.js`:  we need to change the configure according to our deployed contract, the `CONTRACT_ADDRESS`, `NETWORK.ID` and so on. The network id of ethereum main net is `1`, the id of polygon is `137`
      7. `public/config/abi.json`: we need to copy our contract's abi from remix (at the bottom of compiler page, a small icon named ABI) or truffle (?) to here
