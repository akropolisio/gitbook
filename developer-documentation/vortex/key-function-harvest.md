---
description: In-depth descriptions of key functions
---

# 🌾 Key Function - harvest

## _harvest_

```
    /**
     * @notice  harvest the strategy. This involves accruing profits from the strategy and depositing
     *          user funds to the strategy. The funds are split into their constituents and then distributed
     *          to their appropriate location.
     *          For the shortPosition a perpetual position is opened, for the long position funds are swapped
     *          to the long asset. For the buffer position the funds are deposited to the margin account idle.
     * @dev     only callable by the owner
     */
    function harvest() public onlyOwner {
        uint256 shortPosition;
        uint256 longPosition;
        uint256 bufferPosition;
        isUnwind = false;

        mcLiquidityPool.forceToSyncState();
        // determine the profit since the last harvest and remove profits from the margin
        // account to be redistributed
        uint256 amount;
        bool loss;
        if (positions.unitAccumulativeFunding != 0) {
            (amount, loss) = _determineFee();
        }
        // update the vault with profits/losses accrued and receive deposits
        uint256 newFunds = vault.update(amount, loss);
        // combine the funds and check that they are larger than 0
        uint256 toActivate = IERC20(want).balanceOf(address(this));

        if (toActivate > 0) {
            // determine the split of the funds and trade for the spot position of long
            (shortPosition, longPosition, bufferPosition) = _calculateSplit(
                toActivate
            );
            // deposit the bufferPosition to the margin account
            _depositToMarginAccount(bufferPosition);
            // open a short perpetual position and store the number of perp contracts
            positions.perpContracts += _openPerpPosition(shortPosition, true);
        }
        // record incremented positions
        positions.margin = getMargin();
        positions.unitAccumulativeFunding = getUnitAccumulativeFunding();
        emit Harvest(
            positions.perpContracts,
            IERC20(long).balanceOf(address(this)),
            positions.margin
        );
    }
```

The harvest function is the strategy's "work" function. It is responsible for running the strategy.

```
    /**
     * @notice  determine the funding premiums that have been collected since the last epoch
     * @return  fee  the funding rate premium collected since the last epoch
     * @return  loss whether the funding rate was a loss or not
     */
    function _determineFee() internal returns (uint256 fee, bool loss) {
        int256 feeInt;

        // get the cash held in the margin cash, funding rates are saved as cash in the margin account
        int256 newAccFunding = getUnitAccumulativeFunding();
        int256 prevAccFunding = positions.unitAccumulativeFunding;
        int256 livePositions = getMarginPositions();
        if (prevAccFunding >= newAccFunding) {
            // if the margin cash held has gone down then record a loss
            loss = true;
            feeInt = ((prevAccFunding - newAccFunding) * -livePositions) / 1e18;
            fee = uint256(feeInt / DECIMAL_SHIFT);
        } else {
            // if the margin cash held has gone up then record a profit and withdraw the excess for redistribution
            feeInt = ((newAccFunding - prevAccFunding) * -livePositions) / 1e18;
            uint256 balanceBefore = IERC20(want).balanceOf(address(this));
            if (feeInt > 0) {
                mcLiquidityPool.withdraw(perpetualIndex, address(this), feeInt);
            }
            fee = IERC20(want).balanceOf(address(this)) - balanceBefore;
        }
    }
```

The harvest will begin by determining the profit/loss since the previous harvest. It will do this by getting the _unitAccumulativeFunding_ and getting the difference from the last harvest's _unitAccumulativeFunding_. If the difference is positive then the profits will be withdrawn from the margin account. If the difference is negative then the loss will be determined.

```
    /**
     * @notice function to update the state of the strategy in the vault and pull any funds to be redeposited
     * @param  _amount change in the vault amount sent by the strategy
     * @param  _loss   whether the change is negative or not
     *                 be the sender
     * @return toDeposit the amount to be deposited in to the strategy on this update
     */
    function update(uint256 _amount, bool _loss)
        external
        onlyStrategy
        returns (uint256 toDeposit)
    {
        // if a loss was recorded then decrease the totalLent by the amount, otherwise increase the totalLent
        if (_loss) {
            totalLent -= _amount;
        } else {
            _determineProtocolFees(_amount);
            totalLent += _amount;
        }
        // increase the totalLent by the amount of deposits that havent yet been sent to the vault
        toDeposit = want.balanceOf(address(this));
        totalLent += toDeposit;
        lastUpdate = block.timestamp;
        emit StrategyUpdate(_amount, _loss, toDeposit);
        if (toDeposit > 0) {
            want.approve(strategy, toDeposit);
            want.safeTransfer(msg.sender, toDeposit);
        }
    }
```

Next, the harvest will update the vault of the profit/loss. The vault will update its _totalLent_ which is the funds it has lent to the vault; if there is a profit then _totalLent_ will increase and the protocol fees will be determined. If there is a loss then _totalLent_ will decrease. Finally, funds that have been deposited after the previous harvest will be recorded and transferred to the strategy contract.

```
    /**
     * @notice  split an amount of assets into three:
     *          the short position which represents the short perpetual position
     *          the long position which represents the long spot position
     *          the buffer position which represents the funds to be left idle in the margin account
     * @param   _amount the amount to be split in want
     * @return  shortPosition  the size of the short perpetual position in want
     * @return  longPosition   the size of the long spot position in long
     * @return  bufferPosition the size of the buffer position in want
     */
    function _calculateSplit(uint256 _amount)
        internal
        returns (
            uint256 shortPosition,
            uint256 longPosition,
            uint256 bufferPosition
        )
    {
        require(_amount > 0, "_calculateSplit: _amount is 0");
        // remove the buffer from the amount
        bufferPosition = (_amount * buffer) / MAX_BPS;
        // decrement the amount by buffer position
        _amount -= bufferPosition;
        // determine the longPosition in want then convert it to long
        uint256 longPositionWant = _amount / 2;
        longPosition = _swap(longPositionWant, want, long);
        // determine the short position
        shortPosition = _amount - longPositionWant;
    }
```

The harvest will then calculate the split of the gains (deposits and/or profits) and split them first into the buffer position, then the long position, then the short position.  The long position is swapped from the deposit asset to the long asset using a Uniswap v2 or Uniswap v3 interfaced AMM.

```
    /**
     * @notice  deposit to the margin account without opening a perpetual position
     * @param   _amount the amount to deposit into the margin account
     */
    function _depositToMarginAccount(uint256 _amount) internal {
        IERC20(want).approve(address(mcLiquidityPool), _amount);
        mcLiquidityPool.deposit(
            perpetualIndex,
            address(this),
            int256(_amount) * DECIMAL_SHIFT
        );
        emit DepositToMarginAccount(_amount, perpetualIndex);
    }
```

The harvest will then deposit the short position and buffer position deposit asset into the strategy's MCDEX margin account.

```
    /**
     * @notice  open the perpetual short position on MCDEX
     * @param   _amount the collateral used to purchase the perpetual short position
     * @return  tradeAmount the amount of perpetual contracts opened
     */
    function _openPerpPosition(uint256 _amount, bool deposit)
        internal
        returns (int256 tradeAmount)
    {
        if (deposit) {
            // deposit funds to the margin account to enable trading
            _depositToMarginAccount(_amount);
        }

        // get the long asset mark price from the MCDEX oracle
        (int256 price, ) = oracle.priceTWAPLong();
        // calculate the number of contracts (*1e12 because USDC is 6 decimals)
        int256 contracts = ((int256(_amount) * DECIMAL_SHIFT) * 1e18) / price;
        int256 longBalInt = -int256(IERC20(long).balanceOf(address(this)));
        // check that the long and short positions will be equal after the deposit
        if (-contracts + getMarginPositions() >= longBalInt) {
            // open short position
            tradeAmount = mcLiquidityPool.trade(
                perpetualIndex,
                address(this),
                -contracts,
                price - slippageTolerance,
                block.timestamp,
                referrer,
                tradeMode
            );
        } else {
            tradeAmount = mcLiquidityPool.trade(
                perpetualIndex,
                address(this),
                -(getMarginPositions() - longBalInt),
                price - slippageTolerance,
                block.timestamp,
                referrer,
                tradeMode
            );
        }
        emit PerpPositionOpened(tradeAmount, perpetualIndex, _amount);
    }
```

The harvest will then open a short perpetual position on MCDEX using the short position collateral. The MCDEX internal oracle is used to determine the number of short contracts to open.

Finally, a few parameters are updated and a _harvest_ event is emitted. _Fin._
