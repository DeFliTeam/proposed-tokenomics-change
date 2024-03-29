## To make comment or add suggestion to this proposal please fork the repo, make any changes to the Readme and then issue a merge request for review.


# Proposed-tokenomics-change

A Proposal to Change the Tokenomics of DeFli Network and Associated Tokens 

## The Current Tokenomics and Reward Structure 

The DeFli Network assigns an individual token to each project, namely baseDEFLI, baseDESKY, baseDACARS, bDecharge, DRODEC and DeLink. Each have their own reward tokenomics. 

The current token support structure is to assign real-world revenues against each project via "buy & burn". These revenues are determined by the revenues generated for each project 
element within our DeFli UTM. 

## Proposed Tokenomics 

To create a "master token" that can be acquired by exchanging the sub-tokens. 

The rate of exchange is determined by a "per-epoch" "network score". 

The network score is influenced by user revenue per token, network growth per token; weighted to include the difference in entry price points and staked tokens delgated to each network protocol. 

The master token will emit, per epoch, a fixed number of tokens. The allocation rate per network is determined by the network score or the current epoch. 

## Creation 

Contracts will be deployed on Optimism and then bridged to BASE to enable inclusion in the Optimism Superchain

## Changes to Current Tokenomics of Sub-Networks 

None

## Bringing Revenues On Chain 

Through the current buy and burn methodology operating solely on the master-token. This supports the price of the master token whilst simultaneously increasing the value of the tokens emitted to each sub-network. Per epoch DeFli will 
commit a percentage of net revenues to the "master-token". This percentage will start at around 83% and will be announced at the time of epoch closure, this will be validated through an API pull from our sales ledger acting as an oracle.

## Staking 

Staking of sub-network tokens in a "minimum lock-up period" protocol. The percentage of circulating supply that is staked is a determining factor in the network score. Staker earns "master token" as a reward.

## Network Score 

Determined by: 

70%- Network revenue in previous epoch (Nr)
10%- Device onboarding growth rate in previous epoch, weighted for onboarding costs (Do)
20%- Staked percentage (Sp)  

Network Score =  

(Nr * Do) * Sp

## Master Tokenomics 

Total Supply: 365,000,000  
Epoch's: 144 hours, reducing by 24 hours per 6 months to a final 24 hour epoch
86% Rewards | 7% Staking | 7% Team / Investors  

60,000,000 per annum release years 1-3 followed by a halving event every 3 years

## Security 

Implement Gnosis SAFE Multi-Sig to Mint Function- Proposed Contract 

```bash
// SPDX-License-Identifier: MIT
// Compatible with OpenZeppelin Contracts ^5.0.0
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Pausable.sol";
import "@openzeppelin/contracts/access/AccessControl.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Permit.sol";

/// @custom:security-contact team@defli.xyz
contract DACARS is ERC20, ERC20Burnable, ERC20Pausable, AccessControl, ERC20Permit {
    bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");

    constructor(address defaultAdmin, address pauser, address minter)
        ERC20("DACARS", "DACARS")
        ERC20Permit("DACARS")
    {
        _grantRole(DEFAULT_ADMIN_ROLE, defaultAdmin);
        _grantRole(PAUSER_ROLE, pauser);
        _mint(msg.sender, 18279248 * 10 ** decimals());
        _grantRole(MINTER_ROLE, minter);
    }

    function pause() public onlyRole(PAUSER_ROLE) {
        _pause();
    }

    function unpause() public onlyRole(PAUSER_ROLE) {
        _unpause();
    }

    function mint(address to, uint256 amount) public onlyRole(MINTER_ROLE) {
        _mint(to, amount);
    }

    // The following functions are overrides required by Solidity.

    function _update(address from, address to, uint256 value)
        internal
        override(ERC20, ERC20Pausable)
    {
        super._update(from, to, value);
    }
}
```

## Use of InfluxDB API 

Data acquired by DeFli Devices is currently pushed to user specific buckets on InfluxDB. 

DeFli processes 6-9 million messages per day making it unsuitable for pushing directly on to a pre-built chain. 

InfluxDB will act as the Oracle for rewards assignment via GET API 

## Future Integration 

To use message compression techniques in enabling the DeFli private blockchain to become the Oracle for the rewards chain (BASE).




