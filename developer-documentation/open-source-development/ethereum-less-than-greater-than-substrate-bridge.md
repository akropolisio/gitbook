---
description: >-
  Ethereum <-> Parity Substrate Blockchain bridge for self transfers of ERC20
  representation.
---

# Ethereum <-> Substrate bridge

### Github repo:

{% embed url="https://github.com/akropolisio/erc20-substrate-bridge" %}

### You can try it out in our chain: <a href="#you-can-try-it-out-in-our-chain" id="you-can-try-it-out-in-our-chain"></a>

1. Make sure you have Ethereum and Substrate extensions. Typical choices would be:\
   a. `Metamask` (or any other Ethereum extension) and switch it to `Kovan`\
   b. `polkadot{.js}`
2. Go [here](https://polkadai-bridge.akropolis.io)
3. Connect with both extensions(two pop-up windows should appear)
4. You will see that your balances from extensions should appear on the page.
5. Transfer some Kovan test DAI to our Substrate-based chain.
6. Transfer some DAI from our chain to your Ethereum account.

```
    cd ./frontend <br/>
```

```
    npm i <br/>
    npm run codegen <br/>
    npm run dev <br/> 
```
