---
sidebar_position: 1
---

## Code standards for contributing

---

This document describes the coding standards for the Realms Cairo contracts.

### General

- 1 line between function / blocks
- file ends with a empty line
- indentation: 4 spaces

### Commenting within contracts

Cairo does not support in-function docstrings, so the docs are put above the function definition in the following format:

```python

# General explanation goes here.
#
# Params:
#   - token_id: realm_id to make action on
#   - resource_id: resource id to upgrade
#   - level: level to upgrade resource to
# Returns:
#   - returns success
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr : BitwiseBuiltin*
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

### Functions, parameters and variables

These should all be formatted in snake case.

E.g.:
```python
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr : BitwiseBuiltin*
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

### Contract names, events, structs and namespaces

These should all be camel case.

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

Events and namespaces also should be camel case.

### Function architecture & libraries

#### Libraries

### Unit tests

#### Protostar

### Default contract template

```python
# --------------------------------------------------------------------------------------------------------
# <FILEDOC> & License
# --------------------------------------------------------------------------------------------------------

%lang starknet

# Standard imports

# Custom imports

#
# Structs
#

#
# Storage
#

#
# Events (events should be CamelCased)
#

#
# Constructor
#

#
# Getters
#

#
# Setters
#

#
# External
#

#
# Functions (you can rename this)
#
```

### Interface template

```python
%lang starknet
%builtins pedersen range_check [optional]

# --------------------------------------------------------------------------------------------------------
# <FILEDOC>
# --------------------------------------------------------------------------------------------------------

# Standard imports

# Custom imports

@contract_interface
namespace NameSpaceExample:
    func function_example(argument : felt) -> (answer : felt):
    end
end
```