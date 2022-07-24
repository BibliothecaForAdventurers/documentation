---
sidebar_position: 1
---

# Cairo standards

This document describes the coding standards for the Realms Cairo contracts.

Cairo is a young language, tooling, best pracitces and conventions are still evolving. As is true for all blockchain programming (with the possible exception of Solana) writing secure, correct smart contrats is of highest importance. Yet code is read much more often than it is written, hence it should be written in an easy to understand and comprehend fashion. Conventions like the following lead to better security, less cognitive load on the reader and improve collaboration.

Please open a PR if you have anything that you'd like to add to this list.

---

## General

- 1 line between function / blocks
- file ends with a empty line
- indentation: 4 spaces

---

## Commenting within contracts

Cairo does not support in-function docstrings, so the docs are put above the function definition in the following format:

```python
# @dev Sets resource level
# @implicit syscall_ptr (felt*)
# @implicit pedersen_ptr (HashBuiltin*)
# @implicit range_check_ptr
# @param token_id (Uint256): A Uint256
# @param resource_id (felt): A felt
# @param level (felt): A felt
# @return success (felt): Success
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

---

## Functions, parameters and variables

These should all be formatted in snake case.

```python
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr : BitwiseBuiltin*
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

---

## Events

Only emit events from @external, @l1_handler or @constructor functions, never from @view or private functions.

---

# Naming

### Contract names, events, structs and namespaces

Events and namespaces also should be camel case.

```python
ContractAbility.cairo

# ...

@event
func Settled(owner : felt, token_id : Uint256):
end

# ...

struct ShieldGameRole:
    member man : felt
    member woman : felt
end

# ...

@contract_interface
namespace IXoroshiro:
    func next() -> (rnd : felt):
    end
end
```

---

## Error message formatting

Always wrap asserts in `with_attr` and use the module/contract name at the beginning of the message for easy debugging.

```python
with_attr error_message("RESOURCES: Cannot claim resources"):
    assert was_transfered = TRUE
end
```

---

## Address Variables

Add the `_address` suffix to any variable holding an address. Unlike Solidity, Cairo doesn't yet have an address type, and so adding this suffix makes it clearer to the reader what the variable is and does.

const usdc = 0x... becomes const `usdc_address` = 0x... and so on.

---

## Function architecture & libraries

### Libraries

After much trial and error we have landed on the very efficient pattern of writing stateless libraries for all our contracts. These namespaced libraries are pure functions that can be composed together and most importantly can be tested very quickly!

First you need to write your library which is simple:

```
# Note namespaces must be CamelCase!!
namespace Resources:
    # Turns IDS into an Array
    func _calculate_total_mintable_resources{
        syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr
    }(happiness : felt, realms_data : RealmData, days : felt, mint_percentage : felt) -> (
        resource_mint : Uint256*
    ):
        alloc_locals

        let (
            r_1_output, r_2_output, r_3_output, r_4_output, r_5_output, r_6_output, r_7_output
        ) = _calculate_all_resource_output(happiness, realms_data)

        # USER CLAIM
        let (r_1_user) = _calculate_resource_claimable(days, mint_percentage, r_1_output)
        let (r_2_user) = _calculate_resource_claimable(days, mint_percentage, r_2_output)
        let (r_3_user) = _calculate_resource_claimable(days, mint_percentage, r_3_output)
        let (r_4_user) = _calculate_resource_claimable(days, mint_percentage, r_4_output)
        let (r_5_user) = _calculate_resource_claimable(days, mint_percentage, r_5_output)
        let (r_6_user) = _calculate_resource_claimable(days, mint_percentage, r_6_output)
        let (r_7_user) = _calculate_resource_claimable(days, mint_percentage, r_7_output)

        let (_, resource_mint : Uint256*) = _calculate_mintable_resources(
            realms_data, r_1_user, r_2_user, r_3_user, r_4_user, r_5_user, r_6_user, r_7_user
        )

        return (resource_mint)
    end
end

```

Then you can use these libraries within your contracts by importing the namespace:

```
## import the namespace
from contracts.settling_game.library.library_resources import Resources

@view
func get_all_vault_raidable{syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr}(
    token_id : Uint256
) -> (user_mint_len : felt, user_mint : Uint256*):

    # HERE: importing the namespace and . selecting the function like so:
    let (resource_mint : Uint256*) = Resources._calculate_total_mintable_resources(
        100, realms_data, total_vault_days, PILLAGE_AMOUNT
    )

    return (realms_data.resource_number, resource_mint)
end

```

---

## Templates

### Default contract template

```python
# -----------------------------------
# <FILEDOC> & License
# -----------------------------------

%lang starknet

# Standard imports

# Custom imports

# -----------------------------------
# STRUCTS
# -----------------------------------

# -----------------------------------
# STORAGE
# -----------------------------------

# -----------------------------------
# EVENTS (events should be CamelCased)
# -----------------------------------

# -----------------------------------
# CONSTRUCTOR
# -----------------------------------

# -----------------------------------
# GETTERS
# -----------------------------------

# -----------------------------------
# SETTERS
# -----------------------------------

# -----------------------------------
# EXTERNAL
# -----------------------------------

# -----------------------------------
# ADMIN
# -----------------------------------

```

### Interface template

```python
%lang starknet
%builtins pedersen range_check [optional]

# -----------------------------------
# <FILEDOC>
# -----------------------------------

# Standard imports

# Custom imports

@contract_interface
namespace NameSpaceExample:
    func function_example(argument : felt) -> (answer : felt):
    end
end
```
