---
sidebar_position: 1
---

# Testing

Testing best practices are still emerging on StarkNet. We outline how we write our game contracts below.

---

# Protostar

We use protostar for unit tests due to it's speed and direct composability with .cairo.

Find the installation guide here: https://docs.swmansion.com/protostar/

### Testing patterns

Stateless libraries and unit tests for each function allow you to accelerate your development. This is the pattern we have taken in writing all our logic. By following this pattern you can efficiently write tests.

### Example

Below is an example of a protostar test. You can see we have created a dummy struct which we simulate the state with. Then we the state into the functions and assert the outcome. This test will take ~10s.

```
-> tests/settling_game/resources/test_resources.cairo

@external
func test_calculate_realm_resource_ids{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr
}():
    alloc_locals

    let realmData = RealmData(
        TEST_REALM_DATA.REGIONS,
        TEST_REALM_DATA.CITIES,
        TEST_REALM_DATA.HARBOURS,
        TEST_REALM_DATA.RIVERS,
        TEST_REALM_DATA.RESOURCE_NUMBER,
        TEST_REALM_DATA.RESOURCE_1,
        TEST_REALM_DATA.RESOURCE_2,
        TEST_REALM_DATA.RESOURCE_3,
        TEST_REALM_DATA.RESOURCE_4,
        TEST_REALM_DATA.RESOURCE_5,
        TEST_REALM_DATA.RESOURCE_6,
        TEST_REALM_DATA.RESOURCE_7,
        TEST_REALM_DATA.WONDER,
        TEST_REALM_DATA.ORDER,
    )

    let (resource_ids : Uint256*) = Resources._calculate_realm_resource_ids(realmData)

    assert resource_ids[0].low = TEST_REALM_DATA.RESOURCE_1
    assert resource_ids[1].low = TEST_REALM_DATA.RESOURCE_2
    assert resource_ids[2].low = TEST_REALM_DATA.RESOURCE_3
    assert resource_ids[3].low = TEST_REALM_DATA.RESOURCE_4
    assert resource_ids[4].low = TEST_REALM_DATA.RESOURCE_5
    assert resource_ids[5].low = TEST_REALM_DATA.RESOURCE_6
    assert resource_ids[6].low = TEST_REALM_DATA.RESOURCE_7

    return ()
end

```

And this is the function that it is calling:

```
-> contracts/settling_game/library/library_resources.cairo

func _calculate_realm_resource_ids{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr
}(realms_data : RealmData) -> (resource_ids : Uint256*):
    alloc_locals

    let (local resource_ids : Uint256*) = alloc()

    # ADD VALUES TO TEMP ARRAY FOR EACH AVAILABLE RESOURCE
    assert resource_ids[0] = Uint256(realms_data.resource_1, 0)

    if realms_data.resource_2 != 0:
        assert resource_ids[1] = Uint256(realms_data.resource_2, 0)
    end

    if realms_data.resource_3 != 0:
        assert resource_ids[2] = Uint256(realms_data.resource_3, 0)
    end

    if realms_data.resource_4 != 0:
        assert resource_ids[3] = Uint256(realms_data.resource_4, 0)
    end

    if realms_data.resource_5 != 0:
        assert resource_ids[4] = Uint256(realms_data.resource_5, 0)
    end

    if realms_data.resource_6 != 0:
        assert resource_ids[5] = Uint256(realms_data.resource_6, 0)
    end

    if realms_data.resource_7 != 0:
        assert resource_ids[6] = Uint256(realms_data.resource_7, 0)
    end

    return (resource_ids)
end

```

After you have written your unit tests, then import the logic into the actual contract and write a final deploy test. These tests will take longer as protostar will have to deploy the contracts to the state. But hopefully if you have written the unit tests correctly then the final tests should all pass or at least only require minor changes!
