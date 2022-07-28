---
sidebar_position: 3
---


# Liquidity

After initialisation, liquidity can be freely added or removed from the pools using the methods below.

Note, fees are recovered during swaps and so there is no reference to fees during liquidity pool interactions.

### Add Liquidity

Use this method to add subsequent liquidity to an existing pair.

This method is called with:

* The maximum amount of currency the caller is willing to spend when adding liquidity
* The token type id they are supplying liquidity for
* The exact amount of tokens the caller will spend when adding liquidity
* A maximum timestamp which the transaction must be accepted by

Liquidity is supplied at the current price point in the `x * y = k` curve.

Due to the fluctuations in price as swaps are made, the caller may not know the exact amount of currency that will be required to supply the liquidity pool until the transaction is accepted. 
The caller instead supplies the maximum amount of currency they are willing to spend. This acts as a measure of slippage.

The exchange issues liquidity pool tokens equivalent to the supplied currency.

### Remove Liquidity

Use this method to redeem tokens supplied to the Liquidity pool, by burning liquidity pool tokens.

This method is called with:

* The minimum amount of currency the caller is willing to receive when removing liquidity
* The token type id they are removing liquidity for
* The minimum amount of tokens the caller is willing to receive when removing liquidity
* The exact amount of liquidity pool tokens to spend
* A maximum timestamp which the transaction must be accepted by

Liquidity is remove at the current price point in the `x * y = k` curve.

Due to the fluctuations in price as swaps are made, the caller may not know the exact amount of currency or tokens that will be received when removing liquidity from the pool until the transaction is accepted.
The caller instead supplies the minimum amount of currency and tokens they are willing to receive. This acts as a measure of slippage.

The exchange burns liquidity pool tokens supplied in the call.