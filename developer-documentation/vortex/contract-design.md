---
description: Overview of the vault and strategy implementation
---

# 🍭 Contract Design

![](<../../.gitbook/assets/Screenshot from 2021-11-01 18-57-32.png>)

1. **deposit** - A user deposits stablecoins into the vault and receives back tokenised shares of the vault representing their share of the vault.
2. **harvest** - _Only a Manager may call this function_. This is the function that puts funds to work, determines profit/loss of the strategy and distributes funds to the buffer position, short position and long position. It also handles vault updates and protocol fee collection.
3. **remargin** - _Only a Manager may call this function_. It first completes a _harvest_. Then runs some calculations to determine what assets must be swapped in order to return the buffer position to the correct amount and to return the leverage of the margin account to 1. This is a risk management function and keeps the strategy safe from liquidation risk.
4. **withdraw** - A user deposits their shares of the vault, which are then burnt and the user is returned stablecoins according to the value of the shares of the vault at the point of withdrawal. Withdrawals involve selling off strategy positions so that withdrawals from the vault can happen freely.
5. **unwind** - _Only a Manager may call this function_. It first completes a _harvest_. Then it will convert all longPositions to the stable asset, close all short positions and withdraw all funds from the margin account back to the strategy. This is a risk management function and keeps the strategy safe from prolonged negative funding rates and liquidation risk.
6. **setParameters (Manager)** - _Only a Manager may call these functions_. These functions set various parameters in the strategy.
7. **emergencyExit** - _Only a Manager may call this function_. It first completes an _unwind_. Then all funds are transferred to the governance multisig. This is a risk management function and protects funds from extreme conditions such as an exploit risk.
8. **setParameters(Governance)** - _Only Governance may call these functions_. These functions set various parameters in the vault and strategy.

