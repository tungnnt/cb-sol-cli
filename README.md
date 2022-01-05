# cb-sol-cli Documentation

This CLI supports on-chain interactions with components of ChainBridge.

## Installation 

Installation requires the ABI files from the contracts which will be fetched and built from the chainbridge-solidity repo.
```
$ make install
```

## Usage 

The root command (`cb-sol-cli`) has some options:
```
--url <value>                 URL to connect to
--gasLimit <value>            Gas limit for transactions 
--gasPrice <value>            Gas price for transactions 
--networkId <value>	      Network id
```
\
The keypair used for interactions can be configured with:
```
--privateKey <value>           Private key to use
```
or
```
--jsonWallet <path>           Encrypted JSON wallet
--jsonWalletPassword <value>  Password for encrypted JSON wallet
```

There are multiple subcommands provided:

- [`deploy`](docs/deploy.md): Deploys contracts via RPC
- [`bridge`](docs/bridge.md): Interactions with the bridge contract such as registering resource IDs and handler addresses
- [`admin`](docs/admin.md): Interactions with the bridge contract for administering relayer set, relayer threshold, fees and more.
- [`erc20`](docs/erc20.md): Interactions with ERC20 contracts and handlers
- [`erc721`](docs/erc721.md): Interactions with ERC721 contracts and handler

# Deploy on Rinkeby

## Metamask wallet

1. Deployer

    Address: `0x14f3a86848765F623565ed25F1ece4478DcbEAD2`

    Private Key: `292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa`

2. Relayer
    ```
      0xff93B45308FD417dF303D6515aB04D9e89a750Ca
      0x14f3a86848765F623565ed25F1ece4478DcbEAD2
      0xBdc93a803Fe622D814e685F3829e9c4d847bD89D
      0x421464673fBE59BDD83f66429bCFb50632230900
      0xc587475D9d18DBA99fE9Eb9Ca034B6a4D5CCD097
    ```

## Deploy Contracts
Deploy Bridge and Handler contracts script:
```
  cb-sol-cli deploy \
    --chainId 0 \
    --relayerThreshold 1 \
    --expiry 30 \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 10000000000 \
    --gasLimit 10000000 \
    --bridge \
    --erc20Handler
```

Deploy ERC20 PKF token address:
```
  cb-sol-cli deploy \
    --chainId 0 \
    --relayerThreshold 1 \
    --expiry 30 \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 10000000000 \
    --gasLimit 10000000 \
    --erc20 \
    --erc20Symbol PKF \
    --erc20Name PKFToken \
    --erc20Decimals 18
```

Deployed Contract Addresses:
```
    ================================================================
    Bridge:             0xAD45532Bee08642B97d6219755Ac41A14dFe94ae
    ----------------------------------------------------------------
    Erc20 Handler:      0x03845C3597C2935C41E48f0E62527190993dcbAA
    ----------------------------------------------------------------
    Erc20:              0x69F5d11dE7C7ed5c5b43Fa13916cbf524e082F43
    ================================================================
```

## Register Resources Ethereum
Register fungile resource ID with erc20 contract:

PKF resource ID: `0x0000000000000000000000000000001874d10f728e8c510452215c384d203300` 
```
  cb-sol-cli bridge \
    register-resource \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --bridge 0xAD45532Bee08642B97d6219755Ac41A14dFe94ae \
    --handler 0x03845C3597C2935C41E48f0E62527190993dcbAA \
    --targetContract 0x69F5d11dE7C7ed5c5b43Fa13916cbf524e082F43 \
    --resourceId "0x0000000000000000000000000000001874d10f728e8c510452215c384d203300"
```

Register the erc20 contract as mintable/burnable:
```
  cb-sol-cli bridge \
    set-burn \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --tokenContract "0x69F5d11dE7C7ed5c5b43Fa13916cbf524e082F43" \
    --bridge 0xAD45532Bee08642B97d6219755Ac41A14dFe94ae \
    --handler 0x03845C3597C2935C41E48f0E62527190993dcbAA
```

Register the associated handler as a minter:
```
  cb-sol-cli erc20 \
    add-minter \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --minter "0x03845C3597C2935C41E48f0E62527190993dcbAA" \
    --erc20Address 0x69F5d11dE7C7ed5c5b43Fa13916cbf524e082F43
```

Check relayer role of an address:
```
  cb-sol-cli admin \
    is-relayer \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --relayer 0xff93B45308FD417dF303D6515aB04D9e89a750Ca \
    --bridge 0xAD45532Bee08642B97d6219755Ac41A14dFe94ae
```

Approve bridge to assume custody of tokens:
```
  cb-sol-cli erc20 \
    approve \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --amount 1000 \
    --recipient "0x03845C3597C2935C41E48f0E62527190993dcbAA" \
    --erc20Address 0x69F5d11dE7C7ed5c5b43Fa13916cbf524e082F43
```

Transfer 1 token to account:
```
  cb-sol-cli erc20 \
    deposit \
    --url https://rinkeby-light.eth.linkpool.io/ \
    --privateKey 292ba317811cfe7ea399c7a8868520d1367b1baa17bc6f506d99c7f7490d71aa \
    --gasPrice 1000000000000 \
    --amount 1000 \
    --dest 1 \
    --recipient "0xd43593c715fdd31c61141abd04a99fd6822c8558854ccde39a5684e7a56da27d" \
    --resourceId "0x0000000000000000000000000000001874d10f728e8c510452215c384d203300" \
    --bridge "0xAD45532Bee08642B97d6219755Ac41A14dFe94ae"
```




