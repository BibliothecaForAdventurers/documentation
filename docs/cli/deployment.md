---
sidebar_position: 2
---

# Deploying entire game state

The following scripts deploy all contracts necessary to test and play realms on localhost/goerli.

This might take a while to do...


```bash
# you need to compile on contract changes, otherwise you deploy old contracts!
nile compile

# Deploys all the contracts
nile run --network goerli realms_cli/deploy/token_contracts.py
nile run --network goerli realms_cli/deploy/game_contracts.py

# inits the game
nile run --network goerli realms_cli/deploy/init_game.py

# sets the costs of each item
nile run --network goerli realms_cli/deploy/set_costs.py
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


