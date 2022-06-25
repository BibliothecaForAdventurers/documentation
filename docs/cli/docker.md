---
sidebar_position: 2
---

# Docker Setup

(This only works if you have Docker installed)

---

# Create Account & Generate Keys

Install Argent X and create an account. Export your private key from Argent X. Run the following command to generate your keys (replace `<PRIVATE_KEY>` with your key from Argent X).

```
docker run --env STARKNET_NETWORK=alpha-goerli -it \
  ghcr.io/bibliothecaforadventurers/loot:latest /bin/zsh -c "\
  STARKNET_PRIVATE_KEY=<PRIVATE_KEY> python scripts/script.py"
```

This will result in output that looks like

```
private: 55054544859965845478538158955201704430
public: 3357758989248226918739234415483593308350104701645488245057268862229223785568
--> put the public key as a dict key in NETWORK.accounts.json
```

Take note of these values, as well as your account address from Argent X as these will be needed to build your image.

---

# Build Image

Save the following Dockerfile locally

```dockerfile
FROM ghcr.io/bibliothecaforadventurers/loot:latest

ARG STARKNET_PRIVATE_KEY
ENV STARKNET_PRIVATE_KEY=$STARKNET_PRIVATE_KEY
ARG STARKNET_PUBLIC_KEY
ENV STARKNET_PUBLIC_KEY=$STARKNET_PUBLIC_KEY
ARG STARKNET_ACCOUNT_ADDRESS
ENV STARKNET_ACCOUNT_ADDRESS=$STARKNET_ACCOUNT_ADDRESS
ARG STARKNET_NETWORK
ENV STARKNET_NETWORK=${STARKNET_NETWORK:-alpha-goerli}
ENV MAX_FEE=1000000000000000

WORKDIR /usr/local/lib/python3.7/site-packages/nile/core
RUN curl -O "https://raw.githubusercontent.com/BibliothecaForAdventurers/nile/b66940ef8f7465469ab33a5a582fef99253582a6/src/nile/core/call_or_invoke.py"

WORKDIR /loot/realms-contracts/
RUN nile compile
RUN nile compile openzeppelin/account/Account.cairo --account_contract

RUN perl -i -p0e "s/}\,.+?index/},\n    \"$STARKNET_PUBLIC_KEY\": {\n        \"address\": \"$STARKNET_ACCOUNT_ADDRESS\",\n        \"index/s" /loot/realms-contracts/goerli.accounts.json
RUN perl -i -pe "s/0x.+?account\-1/$STARKNET_ACCOUNT_ADDRESS:artifacts\/abis\/Account.json:account-1/" /loot/realms-contracts/goerli.deployments.txt

RUN pip3 install realms_cli/

ENTRYPOINT ["nile"]
```

In the directory you saved the Dockerfile, run the following command to build your Docker image. Replace the placeholders with the values from the previous section.

⚠️ Never expose this image you've built to the public since your keys can be seen in docker history!

```
docker build \
  --build-arg STARKNET_PRIVATE_KEY=<PRIVATE_KEY> \
  --build-arg STARKNET_PUBLIC_KEY=<PUBLIC_KEY> \
  --build-arg STARKNET_ACCOUNT_ADDRESS=<ACCOUNT_ADDRESS> \
  . -t realms_cli
```
---

# Run Actions


```bash
# list available actions
docker run -t realms_cli

# run check_resources action
docker run -t realms_cli check_resources

# get shell access
docker run -it --entrypoint /bin/zsh realms_cli

```
