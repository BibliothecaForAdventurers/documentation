---
sidebar_position: 3
---

# Initialisation

The exchange is initialised through the proxy pattern for now. You are welcome to just implement a set constructor if you prefer that.

## Setup

Each deployment of the contract will work with pairs between an ERC20 contract and all the tokens on an ERC1155 contract. This means one contract can manage multiple exchange pairs. 

The constructor takes the address for the ERC20 and ERC1155 token contracts, and the liquidity provider fee.

The liquidity provider fee is provided in the thousandths. e.g. A value of 15 would equate to a 1.5% fee on trades.

```
@external
func initializer{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(
    currency_address_ : felt,
    token_address_ : felt,
    lp_fee_thousands_ : Uint256,
    royalty_fee_thousands_ : Uint256,
    royalty_fee_address_ : felt,
    proxy_admin : felt,
):
    currency_address.write(currency_address_) # ERC20 address of currency token
    token_address.write(token_address_) # ERC1155 address of tokens
    lp_fee_thousands.write(lp_fee_thousands_) # LP Fees
    set_royalty_info(royalty_fee_thousands_, royalty_fee_address_) # Currency Royalty
    Proxy_initializer(proxy_admin)
    return ()
end
```

## Initial Liquidity

Use this method to provide the initial liquidity to a pair.

This method is only available for the first time liquidity is added to a pair. If you are creating pairs between multiple tokens on the ERC1155 contract, this method will need to be called for each pair.

When calling this method you provide the currency amount, ERC1155 token type id and the token amount.
This sets the initial price of the pair. We expect any large enough variation in pricing to be corrected via arbitrage.

The exchange issues liquidity pool tokens equivalent to the supplied currency.

```
@external
func initial_liquidity{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(
    currency_amounts_len : felt,
    currency_amounts : Uint256*,
    token_ids_len : felt,
    token_ids : Uint256*,
    token_amounts_len : felt,
    token_amounts : Uint256*,
):
    alloc_locals

    # Recursive break
    if currency_amounts_len == 0:
        return ()
    end

    assert currency_amounts_len = token_ids_len
    assert currency_amounts_len = token_amounts_len

    let (caller) = get_caller_address()
    let (contract) = get_contract_address()

    let (token_address_) = token_address.read()
    let (currency_address_) = currency_address.read()

    # Only valid for first liquidity add to LP
    let (currency_reserves_ : Uint256) = currency_reserves.read([token_ids])
    with_attr error_message("Only valid for initial liquidity add"):
        assert currency_reserves_ = Uint256(0, 0)
    end

    # Transfer currency and token to exchange
    IERC20.transferFrom(currency_address_, caller, contract, [currency_amounts])
    tempvar syscall_ptr : felt* = syscall_ptr
    IERC1155.safeTransferFrom(token_address_, caller, contract, [token_ids], [token_amounts])

    # Assert otherwise rounding error could end up being significant on second deposit
    let (ok) = uint256_le(Uint256(1000, 0), [currency_amounts])
    with_attr error_message("Must supply larger currency for initial deposit"):
        assert_not_zero(ok)
    end

    # Update currency reserve size for token id before transfer
    currency_reserves.write([token_ids], [currency_amounts])

    # Initial liquidity is currency amount deposited
    lp_reserves.write([token_ids], [currency_amounts])

    # Mint LP tokens
    ERC1155_mint(caller, [token_ids], [currency_amounts])

    # Emit event
    liquidity_added.emit(caller, [currency_amounts], [token_ids], [token_amounts])

    # Recurse
    return initial_liquidity(
        currency_amounts_len - 1,
        currency_amounts + Uint256.SIZE,
        token_ids_len - 1,
        token_ids + Uint256.SIZE,
        token_amounts_len - 1,
        token_amounts + Uint256.SIZE,
    )
end
```