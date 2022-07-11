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

Below is an example of a protostar test. You can see we have created a dummy struct which we simulate the state with. Then we simulate the state into the functions and assert the outcome. This test will take ~10s.

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

After you have written your unit tests, import the logic into the actual contract and write a final deploy test. These tests will take longer as protostar will have to deploy the contracts to the state. But hopefully if you have written the unit tests correctly then the final tests should all pass or at least only require minor changes!

#### Deploying contracts from tests

There may be circumstances where testing isolated functions is insufficient. Protostar enables a "deploy_contract" cheatcode to test interactions between contracts.

##### Example

Below is an example of a testing suite with several test cases that rely on prior contract deployments

-> tests/protostar/nft_marketplace/test_bibliotheca_marketplace.cairo

```

@view
func __setup__{
    syscall_ptr : felt*, range_check_ptr
}():
    alloc_locals

    local contract_address: felt
    local proxy_lords: felt
    local supply: Uint256 = Uint256(1000000000000000000000000,0)
    local Account: felt
    local realms: felt
    local proxy_realms: felt
    # %{ context.Account = deploy_contract("./lib/cairo_contracts/src/account/Account.cairo", [123456]).contract_address %}
    %{ context.lords = deploy_contract("./contracts/settling_game/tokens/Lords_ERC20_Mintable.cairo", []).contract_address %}
    %{ context.proxy_lords = deploy_contract("./contracts/settling_game/proxy/PROXY_Logic.cairo", [context.lords]).contract_address %}

    let (local caller: felt) = get_contract_address()
    %{ context.contract_address = deploy_contract("./contracts/nft_marketplace/bibliotheca_marketplace.cairo", 
    [context.proxy_lords, 
    ids.caller, 
    ids.caller]).contract_address %}

    %{ context.realms = deploy_contract("./contracts/settling_game/tokens/Realms_ERC721_Mintable.cairo", []).contract_address %}
    %{ context.proxy_realms = deploy_contract("./contracts/settling_game/proxy/PROXY_Logic.cairo", [context.realms]).contract_address %}
    %{ print("__setup__ complete") %}
end

```

__setup__ allows you to generate a required starknet state before any tests run. For each test case, protostar copies the starknet state and "context" objects declared within __setup__, avoiding deploying contracts repeatedly

-> tests/protostar/nft_marketplace/test_bibliotheca_marketplace.cairo

```
@external
func test_update_price{
    syscall_ptr : felt*, pedersen_ptr : HashBuiltin*, range_check_ptr
}():
    alloc_locals

    local contract_address: felt
    local proxy_lords: felt
    local supply: Uint256 = Uint256(1000000000000000000000000,0)
    local Account: felt
    local realms: felt
    let (local caller: felt) = get_contract_address()
    
    local trade_data: felt = 22599173018747098# 18014416252971264319588
    local price: felt = 1000000000000000000

    %{ ids.realms = context.realms %}
    %{ ids.contract_address = context.contract_address %}
    Realms.mint(realms, caller, Uint256(1,0))
    Realms.setApprovalForAll(realms, contract_address, 1)

    NFT_Marketplace.open_trade(
        contract_address=contract_address,
        _token_contract=realms,
        _token_id=Uint256(1,0),
        _price=price,
        _expiration = 604800 # 7 days expiry
    )
    
    NFT_Marketplace.update_price(contract_address=contract_address, _trade=1, _price=10)
    let (trade: Trade) = NFT_Marketplace.get_trade(contract_address=contract_address, idx=1)
    assert_eq(trade.price, 10)
    return ()
end
```

Then, as shown in the code snippet above, within each test case, you can simply assign the created "context" object to utilize the deployed contract
