---
sidebar_position: 1
---

# Token Emporium (AMM)

### The Emporium a traditional AMM with a slight variation. Users can trade up to `n` amount of ERC1155 tokens in one transaction with a ERC20 token!

---

## History of the Emporium

The contract is heavily based on the [NiftyswapExchange20.sol contract](https://github.com/0xsequence/niftyswap/blob/master/src/contracts/exchange/NiftyswapExchange20.sol) and its [specifications](https://github.com/0xsequence/niftyswap/blob/master/SPECIFICATIONS.md). 

One deployed contract will handle pairs between the ERC20 currency and all the tokens on an ERC1155.
Each pair has its price curve tracked individually.

Price changes on one token pair will not affect another.

The exchange contract itself is ERC1155 compliant and will issue LP tokens with a token type id corresponding to the token type id in the pair.
These tokens can then be freely traded or used in other DeFi applications!

---

## Understanding the code
Terminology and naming conventions

---
The ERC20 token is defined as the *currency*.

The ERC1155 token is defined as the *token*.

Some variables have a trailing underscore `_` to prevent collisions.

*Pair* is used to describe a price curve between the currency and a single token type on the token contract.

Functions named with `_loop` are used for recursive processing of lists of items.

### Contract Interactions
---

The contract can be broken into a number of sections:

* Initialisation
* Liquidity
* Swaps

---