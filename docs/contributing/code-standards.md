---
sidebar_position: 1
---

# Code standards for contributing

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

---

## Testing

#### Protostar

#### Pytest

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
