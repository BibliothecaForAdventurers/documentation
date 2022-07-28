---
sidebar_position: 2
---

# Deploy 

This deploys the `Exchange_ERC20_1155.cairo` contract

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
nile run --network goerli realms_cli/amm/deploy.py


# Upgrades
nile run --network goerli realms_cli/amm/upgrade.py
```