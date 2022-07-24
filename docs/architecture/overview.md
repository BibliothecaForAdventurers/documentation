---
sidebar_position: 1
---


# Technical overview

This is a high level overview of how the game fits together.

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