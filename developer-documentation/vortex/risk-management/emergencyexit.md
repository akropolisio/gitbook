# emergencyExit()

In a situation where an immediate exit is required - such as if positions were to be liquidated or in the event of an exploit that may affect the system - a Manager can perform an _emergencyExit()_. This is where all positions are unwound and sent to the Vortex Governance multisig.&#x20;

```
    /**
     * @notice  emergency exit the entire strategy in extreme circumstances
     *          unwind the strategy and send the funds to governance
     * @dev     only callable by governance
     */
    function emergencyExit() external onlyGovernance {
        // unwind strategy unless it is already unwound
        if (!isUnwind) {
            unwind();
        }
        uint256 wantBalance = IERC20(want).balanceOf(address(this));
        // send funds to governance
        IERC20(want).safeTransfer(governance, wantBalance);
        emit EmergencyExit(governance, wantBalance);
    }
```

{% hint style="warning" %}
This is a function of last resort, but is important to maintain security over funds.
{% endhint %}
