---
sidebar_position: 1
---

## Code standards for contributing

---

When contributing it is very important to follow these standards.

### Commenting at beginning on contracts

At the beginning of every contract make sure it is clear exactly what the contract does.

```python
# --------------------------------------------------------------------------------------------------------
# This file contains default implementations for foreach, map, reduce and filter functions.
# --------------------------------------------------------------------------------------------------------
```

### Commenting within contracts

It is important that contracts are very readable. For all modules each function should have a specific purpose.

```python

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

### Function naming

Functions should all be snake case

```python
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr : BitwiseBuiltin*
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

### Parameter naming

#### Function parameters & return parameters should all be snake case

```python
func set_resource_level{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr, bitwise_ptr : BitwiseBuiltin*
}(token_id : Uint256, resource_id : felt, level : felt) -> (success: felt):
    resource_levels.write(token_id, resource_id, level)
    return (success)
end
```

### Contract naming

Contracts should be camel case.

```python
ContractAbility.cairo
```

### Function architecture & libraries

#### Libraries

### Unit tests

#### Protostar
