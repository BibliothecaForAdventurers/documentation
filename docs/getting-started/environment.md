---
sidebar_position: 1
---

# Environment Setup

Clone this repo and use our docker shell to interact with starknet:

``` bash
# Clone
git clone git@github.com:BibliothecaForAdventurers/realms-contracts.git

cd realms-contracts
```
---

## Development Workflow
If you are using VSCode, we provide a development container with all required dependencies.  

:::note
This requires [Docker](https://docs.docker.com/get-docker/) and the [Remote - Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)).
:::

When opening VS Code, it should ask you to re-open the project in a container, if it finds
the .devcontainer folder. If not, you can open the Command Palette (`cmd + shift + p`),
and run “Remote-Containers: Rebuild and Reopen in Container”.

Then run

```bash

# installs python packages
pip3 install -r ./requirements.txt

# compiles contracts (artifacts are not version controlled)
nile compile

```


---
## If using development container
The development container loads settings and the repository information on your local computer but cannot read your GitHub login credentials from your local computer.

Instead, you can use the [Github CLI](https://cli.github.com/) to auth from your dev container:

1. Download the [Github CLI](https://github.com/cli/cli/blob/trunk/docs/install_linux.md#debian-ubuntu-linux-raspberry-pi-os-apt).
2. Visit the [Github Tokens page](https://github.com/settings/tokens) and click `Generate New Token` to create a new token that will be used in your dev container. Make sure to save it somewhere as the token is only visible upon creation.
3. With the container loaded, open the dev container terminal in vscode.
4. Run `gh auth login` and follow the steps, pasting in your new access token when asked.
---

## OSX ARM chips: Running without a container
Docker performance on ARM chips is pretty poor, so we recommend running without a container until these perf issues are resolved:
1. Pull down the repository
2. Install homebrew: https://brew.sh/
3. Install gmp: `brew install gmp`
4. Install dependencies: `pip3 install -r ./requirements.txt`
5. Install realms cli: `pip3 install ./realms_cli`


If you have further questions about the development workflow, please ask in [#builders-chat in the Realms Discord](https://discord.gg/yP4BCbRjUs).

---

## Common issues

```
ModuleNotFoundError: No module named 'starkware.starknet.business_logic.execution'
```

Solution: Dependencies are missing. Run `pip install requirements.txt` and report this issue in #builders as the container should take care of this automagically.

**Issue: Unable to run `git pull`**
```
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

Solution: Install the [Github CLI](https://github.com/cli/cli/blob/trunk/docs/install_linux.md#debian-ubuntu-linux-raspberry-pi-os-apt), run `gh auth login` and make sure you **re-authenticate.** 

When you see this prompt, select **Yes**: `? You're already logged into github.com. Do you want to re-authenticate?`

This will update the private key in your ~/.ssh file.