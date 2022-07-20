---
sidebar_position: 2
---

# Deploying entire game state

The following scripts deploy all contracts necessary to test and play realms on localhost/goerli.

This might take a while to do...


```bash
# set your PK in ENV
source realms_cli/.env.nile

# FIRST TIME ONLY SETUP
# You will need to make an Admin address which is the owner of all of these by:
# This will create an admin account for you
# Note you need to have an ENV setup with a PK named STARKNET_ADMIN_PRIVATE_KEY
nile setup STARKNET_ADMIN_PRIVATE_KEY

# you need to compile on contract changes, otherwise you deploy old contracts!
nile compile

# Deploys all the contracts
nile run --network goerli realms_cli/deploy/token_contracts.py
nile run --network goerli realms_cli/deploy/game_contracts.py

# inits the game
nile run --network goerli realms_cli/deploy/init_game.py

# sets the costs of each item
nile run --network goerli realms_cli/deploy/set_costs.py

# Add new Modules 
# NOTE: You need to update the internal object to the new module names... Do not just run this...
nile run --network goerli realms_cli/deploy/new_module_deployer.py

# Update tokens 
nile run --network goerli realms_cli/deploy/update_token_helper.py
```

## After deployments

Due to the way proxies are deployed you need to set the abi of the implementation in the `goerli.deployments.txt` file. If you don't do this, you will not be able to call any view functions in the deployed contracts.

Like so:

```bash
# before
0x04b1bb18182f29c74d2c1ea1c34b40c8e57b34704cbdeee7bf9a3a6673b9d6fc:artifacts/abis/Food.json:Food
0x008119b900f3c08066197cbe648fcb14fa4f66a3d35a857f3b5526f56dbf0068:artifacts/abis/PROXY_Logic.json:proxy_Food

# after change
0x04b1bb18182f29c74d2c1ea1c34b40c8e57b34704cbdeee7bf9a3a6673b9d6fc:artifacts/abis/Food.json:Food
0x008119b900f3c08066197cbe648fcb14fa4f66a3d35a857f3b5526f56dbf0068:artifacts/abis/Food.json:proxy_Food
```


# Tips

If you want to check a tx hash, run either

```bash
nile debug --network NETWORK TXHASH

# local host
starknet get_transaction_receipt --hash TXHASH
```

# Errors

This happens when you have an extra line at the end of the deployments.txt file. There should only be 1 line after the last written line.
```
ValueError: not enough values to unpack (expected at least 2, got 1)
```


