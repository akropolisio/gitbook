# remargin()

During operation of the vault it is possible for the leverage of the strategy's margin account to deviate away from 1x. If the strategy's leverage goes below 1x there is no financial risk, but the vault is not capital efficient as the non-profiting buffer represents a disproportionate amount of the strategy. More dangerously, however, is if the strategy's leverage goes above 1x, as then it is possible for the vault to fall below the margin ratio and for the margin account to get liquidated, which would result in a substantial loss.

The _remargin()_ function solves this problem.

If the position's leverage is greater than 1x then a certain amount of the short and long positions are sold and added to the buffer. This brings the Position Buffer back to its correct value and resets the leverage back to 1x.

If the position's leverage is less than 1x then a certain amount of the buffer position is used to open short and long positions. This brings the Position Buffer back to its correct value and resets the leverage back to 1x.

{% hint style="info" %}
Position sizes will also increase based on returns accumulated by the strategy. These returns are _compounded_ into positions during remargins.
{% endhint %}

{% hint style="success" %}
The _remargin()_ function ensures that the **Position Buffer** is consistently maintained and that derivative positions remain at 1x leverage.
{% endhint %}

A description of the _remargin()_ code and the maths behind it are shown below.

```
/**
     * @notice  remargin the strategy such that margin call risk is reduced
     * @dev     only callable by owner
     */
    function remargin() external onlyOwner {
        // harvest the funds so the positions are up to date
        harvest();
        // ratio of the short in the short and buffer
        int256 K = (((int256(MAX_BPS) - int256(buffer)) / 2) * 1e18) /
            (((int256(MAX_BPS) - int256(buffer)) / 2) + int256(buffer));
        // get the price of ETH
        (int256 price, ) = oracle.priceTWAPLong();
        // calculate amount to unwind
        int256 unwindAmount = (((price * -getMarginPositions()) -
            K *
            getMargin()) * 1e18) / ((1e18 + K) * price);
        require(unwindAmount != 0, "no changes to margin necessary");
        // check if leverage is to be reduced or increased then act accordingly
        if (unwindAmount > 0) {
            // swap unwindAmount long to want
            uint256 wantAmount = _swap(uint256(unwindAmount), long, want);
            // close unwindAmount short to margin account
            mcLiquidityPool.trade(
                perpetualIndex,
                address(this),
                unwindAmount,
                price + slippageTolerance,
                block.timestamp,
                referrer,
                tradeMode
            );
            // deposit long swapped collateral to margin account
            _depositToMarginAccount(wantAmount);
        } else if (unwindAmount < 0) {
            // the buffer is too high so reduce it to the correct size
            // open a perpetual short position using the unwindAmount
            mcLiquidityPool.trade(
                perpetualIndex,
                address(this),
                unwindAmount,
                price - slippageTolerance,
                block.timestamp,
                referrer,
                tradeMode
            );
            // withdraw funds from the margin account
            int256 withdrawAmount = (price * -unwindAmount) / 1e18;
            mcLiquidityPool.withdraw(
                perpetualIndex,
                address(this),
                withdrawAmount
            );
            // open a long position with the withdrawn funds
            _swap(uint256(withdrawAmount / DECIMAL_SHIFT), want, long);
        }
        positions.margin = getMargin();
        positions.unitAccumulativeFunding = getUnitAccumulativeFunding();
        positions.perpContracts = getMarginPositions();
        emit Remargined(unwindAmount);
    }
```

The _remargin()_ begins by harvesting the strategy - this updates all positions.

Next the ratio of expected short:buffer, K, is calculated. This is calculated using the following equation:

$$
K = \frac{(1 - buffer) / 2}{((1-buffer)/2) + buffer}
$$

Then the _unwindAmount_, **Z**, is calculated. After _remargin()_, the value of long and short should be equal, thus we have the following equation - where **P** is the index price, **X** is the long or short size and **Y** is the margin size:

$$
P(X-Z) = PZ + YK
$$

Rearranging for **Z**:

$$
Z = \frac{(PX - KY)}{2P}
$$

The proof that this resets leverage is at the bottom of the page.

If **Z** is greater than 0, the vault is over-leveraged. Z positions are then closed from the short position and Z positions are closed on the long. This combined amount is then deposited to the margin account.

If **Z** is less than 0,  the vault is under-leveraged. Z positions are then opened in the short position and Z positions are opened on the long - the funds to collateralise these positions are taken from the Position Buffer.

Proof that leverage, **L** is reset, after _remargin(),_ the leverage is as follows:

$$
L = \frac{P(X-Z)}{KY + PZ}
$$

Replace **Z** with the remargined **Z** value:

$$
L = \frac{P(X-\frac{PX-KY}{2P})}{KY + P\frac{PX-KY}{2P}}
$$

$$
L = \frac{PX-\frac{PX-KY}{2}}{KY + \frac{PX-KY}{2}}
$$

$$
L = \frac{PX + KY}{KY + PX}
$$

$$
L = 1
$$
