---
sidebar_position: 1
---

# Command line tool

---

# Setup

This is in heavy development and things might not work as expected... Bring a torch to slay some STARK dragons...

1. Upgrade pip: `/usr/local/bin/python -m pip install --upgrade pip` (Note: This will [break for OSX users who install via homebrew](https://github.com/Homebrew/legacy-homebrew/issues/26900). The workaround is to upgrade with homebrew: `brew install python3` or python3 @3.7)
2. Remove _all_ previous cairo nile packages: `$ pip uninstall cairo-nile` and check with `$ pip freeze` to make sure it's removed.
3. Install nile 0.6.1: `pip install cairo-nile`
4. Install the realms_cli: `$ pip install realms_cli/` (ensure you are in the realms-contracts dir)

You now should have the realms_cli commands available when you run `$ nile`.

---

# ENV Setup

1. Compile all the contracts with `$ nile compile` (The CLI calls these .json files, they are gitignored to avoid conflicts.)
2. Compile the account contract `$ nile compile openzeppelin/account/Account.cairo --account_contract`

Create an `.env.nile` in the realms_cli/ directory with the following entries:

```
export STARKNET_PRIVATE_KEY=<A PRIVATE KEY>  # admin private key - see below to generate
export STARKNET_NETWORK=alpha-goerli  # different from nile_network
```

⚠️ Never commit this file!

After your initial setup you will have to rerun the following commands on each new session:

```bash
$ source realms_cli/.env.nile
```

---

# Create Account

This assumes you have installed and created an Argent X account

1. Export your private key from Argent X
2. Save this PK as STARKNET_PRIVATE_KEY in your `.env.nile`
3. Run `source realms_cli/.env.nile`
4. The run `python scripts/script.py`
5. Save the printed public key in the goerli.accounts.json in address-1
6. Copy your address from Argent and save it in both goerli.accounts.json and in goerli.deployments.txt (replacing the account-1 address)
7. Rerun `pip install realms_cli/`


