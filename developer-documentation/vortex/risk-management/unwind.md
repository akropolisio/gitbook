# unwind()

In historic crypto-market conditions the funding rate has consistently been positive, which is an assumption that this strategy relies on. The Managers of Vortex are constantly monitoring the funding rate and ensuring that it remains positive; if it stays negative for a prolonged amount of time then the Manager can _unwind()_ the funds to prevent any further losses. This function can also be used to prevent a liquidation. The _unwind()_ function is described below:

```
    /**
     * @notice  unwind the position in adverse funding rate scenarios, settle short position
     *          and pull funds from the margin account. Then converts the long position back
     *          to want.
     * @dev     only callable by the owner
     */
    function unwind() public onlyAuthorised {
        require(!isUnwind, "unwound");
        isUnwind = true;
        mcLiquidityPool.forceToSyncState();
        // swap long asset back to want
        _swap(IERC20(long).balanceOf(address(this)), long, want);
        // check if the perpetual is in settlement, if it is then settle it
        // otherwise unwind the fund as normal.
        if (!_settle()) {
            // close the short position
            _closeAllPerpPositions();
            // withdraw all cash in the margin account
            mcLiquidityPool.withdraw(
                perpetualIndex,
                address(this),
                getMargin()
            );
        }
        // reset positions
        positions.perpContracts = 0;
        positions.margin = getMargin();
        positions.unitAccumulativeFunding = getUnitAccumulativeFunding();
        emit StrategyUnwind(IERC20(want).balanceOf(address(this)));
    }
```

_unwind()_ will close all long positions, then check if the MCDEX perpetual market has been settled. If it has been settled, then all funds will be withdrawn as all positions have already been closed by MCDEX. If the perpetual pool is not settled, then all short positions are closed and withdrawn from the margin account to the strategy contract.
