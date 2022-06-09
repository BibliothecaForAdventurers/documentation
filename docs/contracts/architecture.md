---
sidebar_position: 2
---

# 🏰 | Grand Architecture

### The Realms Settling Game - based on the Realms derivative NFT of the Loot Project

A modular game engine architecture for the StarkNet L2 roll-up - Forked & heavily inspired by the pioneering work done by the Dope Ware RYO game [here](https://github.com/dopedao/RYO).

## 🤔 What is the game?

---

#### Picture a million players all asynchronously working the blockchain; harvesting resources, building alliances, & slaying foes.

Settling is an on-chain game of economics and Chivarly built on-top of ZK-STARKS. Earn resources by staking your Realms, raid rivals, steal loot & form on-chain alliances to crush your enemies.

Settling is entirely on-chain; the UI is purely just a client for a distributed backend. Feel free to create your own superior client if you wish.

The game is all open-source and we encourage people to build modules and contribute.

<!-- ## 📦 Contract hierarchy

### Player Account

- A player with a wallet

### Governance Account

- An admin who controls the Arbiter.
- The admin may be an L2 DAO to administer governance decisions
  voted through on L2, where voting will be cheap.
- Governance might enable a new module to have write-access to
  and important game variable. For example, to change the location
  that a player is currently in. All other modules that read and use location
  would be affected by this.

### Arbiter (most power in the system).

- Can update/add module mappings in ModuleController.

### ModuleController (mapping of deployments to module_ids).

- The game 'swichboard' that connects all modules.
- Is the reference point for all modules. Modules call this
  contract as the source of truth for the address of other modules.
- The controller stores where modules can be found, and which modules
  have write access to other modules.

### Modules (open ended set)

- Game mechanics (where a player would interact to play).
- Storage modules (game variables).
- L1 connectors (for integrating L1 state/ownership to L2)
- Other arbitrary contracts
- Module logic contained in L (e.g L_Settling.cairo) and state in S (S_Settling.cairo) -->
