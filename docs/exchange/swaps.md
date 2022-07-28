---
sidebar_position: 3
---


# Swaps

Swaps are performed as either buy or sell actions.

When making a swap, the exchange will calculate the price according to the `x * y = k` curve.
Fees are collected against the currency in both buy and sell actions.
Due to this, `k` will steadily increase as a measure to collect rewards for the liquidity providers.
When liquidity is removed from the pools, as `k` has increased, their proportional share in the pool will have increased as well.

### Buy Tokens

Use this method to purchase tokens with currency.

This method is called with:

* The maximum amount of currency the caller is willing to spend when swapping
* The token type id they are swapping
* The exact amount of tokens the caller will receive when swapping
* A maximum timestamp which the transaction must be accepted by

See the above Swap section for information about the pricing curve.

Due to the fluctuations in price as swaps are made, the caller may not know the exact amount of currency that will be required to swap until the transaction is accepted. 
The caller instead supplies the maximum amount of currency they are willing to spend. This acts as a measure of slippage.

### Sell Tokens

Use this method to sell tokens for currency.

This method is called with:

* The minimum amount of currency the caller is willing to receive when swapping
* The token type id they are swapping
* The exact amount of tokens the caller will spend when swapping
* A maximum timestamp which the transaction must be accepted by

See the above Swap section for information about the pricing curve.

Due to the fluctuations in price as swaps are made, the caller may not know the exact amount of currency that will be required to swap until the transaction is accepted. 
The caller instead supplies the minimum amount of currency they are willing to receive. This acts as a measure of slippage.

### Get Buy / Sell Price

The `get_all_buy_price` and `get_all_sell_price` functions are read only functions used to get the current price according to the `x * y = k` curve, and take into consideration the exchange fee.

These methods are separated from the buy and sell methods so that they can be used for price display on frontends.

The liquidity provider fee is stored in the thousandths. e.g. A value of `15` would equate to a 1.5% fee on trades. Thus `1000` is used as a static value in these calculations.

---


## Misc Getters

There are additional getters for the following stored values:

* Currency contract address
* Token contract address
* Currency reserves (for the given token type id)
* LP fee (in thousandths)

The contract does not store a value for the ERC1155 token reserves and instead relies on the `balanceOf` ERC1155 function.

### LP ERC 1155 Compliance

There are additional method to support the ERC1155 compliance of LP tokens provided by this contract.