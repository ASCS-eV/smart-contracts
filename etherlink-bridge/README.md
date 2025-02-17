# Etherlink Bridge

## What is Etherlink?

Etherlink is an EVM-compatible, non-custodial Layer 2 blockchain powered by Tezos Smart Rollup technology.
It enables seamless integration with existing Ethereum tools, including wallets and indexers, and facilitates asset transfers to and from other EVM-compatible chains.

Built upon the secure foundation of Tezos layer 1, Etherlink delivers a fast, fair, and (nearly) free experience.
This permissionless and censorship-resistant environment empowers developers to actively create and participate in the next generation of decentralized applications.

More information can be found on the [Etherlink website](https://www.etherlink.com/) and [Etherlink docs](https://docs.etherlink.com/).

## Bridging tokens

You can move tokens in and out of Etherlink through a process called bridging.
See these related pages for more information:

- [Bridging XTZ between Tezos layer 1 and Etherlink](https://docs.etherlink.com/bridging/bridging-tezos)
- [Bridging tokens between Etherlink and other EVM networks](https://docs.etherlink.com/bridging/bridging-evm)
- [Bridging FA tokens between Tezos layer 1 and Etherlink](https://docs.etherlink.com/bridging/bridging-fa)

### Etherlink FA Token Bridge

To bridge assets from Tezos to Etherlink, we've used a helper stack provided by the Baking Bad team:

- [Etherlink Bridge](https://github.com/ASCS-eV/etherlink-bridge)

### Bridged token details

#### Tezos

- Token Contract: [KT1PCaD2kmgCHy15wQ1gpqZUy9RLxyBVJdTF](https://better-call.dev/ghostnet/KT1PCaD2kmgCHy15wQ1gpqZUy9RLxyBVJdTF)
- Token ID: 1
- Deposit hash: [oojtGLnHuS8og5WGf8jF8EoxTbegfrXvpxzvyPiW2GYZFGbFLaJ](https://ghostnet.tzkt.io/oojtGLnHuS8og5WGf8jF8EoxTbegfrXvpxzvyPiW2GYZFGbFLaJ/21781492)

#### Bridge

- Ticketer Contract: [KT1EATXWJv2dHPWrSxbxAkjMWb3KByAn4sKd](https://better-call.dev/ghostnet/KT1EATXWJv2dHPWrSxbxAkjMWb3KByAn4sKd)
- ERC 20 Proxy contract: [0x646B92C8f21e55DF67E766047E4bD7bEdF8DfA14](https://testnet.explorer.etherlink.com/address/0x646B92C8f21e55DF67E766047E4bD7bEdF8DfA14)
- Token Bridge: [KT1WXQLq8fC7cShE3Q8PkZqu5xMGqYgbETDd]([KT1EATXWJv2dHPWrSxbxAkjMWb3KByAn4sKd](https://better-call.dev/ghostnet/KT1WXQLq8fC7cShE3Q8PkZqu5xMGqYgbETDd))

#### Etherlink

- Deposit hash: [0xad0fa6b98b66bc19ab4936d1181697ac7f1e19755e1501e4e250434200a32cba](https://testnet.explorer.etherlink.com/tx/0xad0fa6b98b66bc19ab4936d1181697ac7f1e19755e1501e4e250434200a32cba)
- Token: [0x646B92C8f21e55DF67E766047E4bD7bEdF8DfA14](https://testnet.explorer.etherlink.com/token/0x646B92C8f21e55DF67E766047E4bD7bEdF8DfA14)

### Process

We've used docker to run the bridge.
But check the [Etherlink Bridge](https://github.com/baking-bad/etherlink-bridge) repo for more information on how to run the commands.

- Setting up the token bridge:

  ``` sh
  docker run --rm etherlink-bridge bridge_token \
    --token-address KT1PCaD2kmgCHy15wQ1gpqZUy9RLxyBVJdTF \
    --token-type FA2 \
    --token-id 1 \
    --token-decimals 0 \
    --token-symbol "TENVX" \
    --token-name "HD Map" \
    --tezos-private-key {INSERT TEZOS PRIVATE KEY} \
    --tezos-rpc-url "https://rpc.tzkt.io/ghostnet" \
    --etherlink-private-key {INSERT ETHERLINK PRIVATE KEY} \
    --etherlink-rpc-url "https://node.ghostnet.etherlink.com" \
    --skip-confirm
    ```
  
  This command should give you a set of contract that you can use to bridge the token.

- Depositing the token:

  ``` sh
  docker run --rm etherlink-bridge deposit \     
    --token-bridge-helper-address KT1WXQLq8fC7cShE3Q8PkZqu5xMGqYgbETDd \
    --amount 1 \      
    --tezos-private-key {INSERT TEZOS PRIVATE KEY} \
    --tezos-rpc-url "https://rpc.tzkt.io/ghostnet" \
    --receiver-address 0xE2cb6C628cb379BE40322BbB85eBF46718350B06 \
    --smart-rollup-address sr18wx6ezkeRjt1SZSeZ2UQzQN3Uc3YLMLqg
    ```

### Future outlook

The FA2.1 contract contains a ticket export functionality which can be used to directly issue tickets.
The payload for this must be at minimum an Ethereum address in [H160](https://docs.rs/ethereum-types/latest/ethereum_types/struct.H160.html) form.
This would improve the efficiency and user experience but does not affect the result of the token being bridged to L2 Etherlink.
