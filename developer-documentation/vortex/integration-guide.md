---
description: Guide for smart contract interactions with the vault
---

# 🧑💻 Integration guide

To interact with the system via smart contract there are only two functions you need to use, _deposit()_ and _withdraw()_:

### deposit(uint256 \_amount, address \_recipient)

```
    /**
     * @notice  deposit function - where users can join the vault and
     *          receive shares in the vault proportional to their ownership
     *          of the funds.
     * @param  _amount    amount of want to be deposited
     * @param  _recipient recipient of the shares as the recipient may not
     *                    be the sender
     * @return shares the amount of shares being minted to the recipient
     *                for their deposit
     */
    function deposit(uint256 _amount, address _recipient)
        external
        nonReentrant
        whenNotPaused
        returns (uint256 shares)
    {
        require(_amount > 0, "!_amount");
        require(_recipient != address(0), "!_recipient");
        require(totalAssets() + _amount <= depositLimit, "!depositLimit");

        shares = _issueShares(_amount, _recipient);
        // transfer want to the vault
        want.safeTransferFrom(msg.sender, address(this), _amount);

        emit Deposit(_recipient, _amount, shares);
    }
```

The recipient will receive vault shares that represent their share of funds in the vault at the time of deposit. Deposited funds are not deployed to the strategy immediately; they instead remain idle in the vault until a _harvest_ is called where they are deployed to the strategy.

This function will revert if:

* The amount to be deposited is 0;
* The recipient is not a valid address;
* The deposit limit is reached;
* The caller has not approved funds to be used by the vault;
* The caller does not have _\_amount_ of the asset in their wallet.

### withdraw(uint256 \_shares, address \_recipient)

```
/**
 * @notice  withdraw function - where users can exit their positions in a vault
 *          users provide an amount of shares that will be returned to a recipient.
 * @param  _shares    amount of shares to be redeemed
 * @param  _recipient recipient of the amount as the recipient may not
 *                    be the sender
 * @return amount the amount being withdrawn for the shares redeemed
 */
function withdraw(uint256 _shares, address _recipient)
    external
    nonReentrant
    whenNotPaused
    returns (uint256 amount)
{
    require(_shares > 0, "!_shares");
    require(_shares <= balanceOf(msg.sender), "insufficient balance");
    amount = _calcShareValue(_shares);
    uint256 vaultBalance = want.balanceOf(address(this));
    uint256 loss;

    // if the vault doesnt have free funds then funds should be taken from the strategy
    if (amount > vaultBalance) {
        uint256 needed = amount - vaultBalance;
        needed = Math.min(needed, totalLent);
        uint256 withdrawn;
        (loss, withdrawn) = IStrategy(strategy).withdraw(needed);
        vaultBalance = want.balanceOf(address(this));
        if (loss > 0) {
            amount = vaultBalance;
            totalLent -= loss;
            // all assets have been withdrawn so now the vault must deal with the loss in the share calculation
            // _shares = _sharesForAmount(amount);
        }
        // reduce the totallent by the amount withdrawn, if the amount withdrawn is greater than the totallent
        // then make it 0
        if (totalLent >= withdrawn) {
            totalLent -= withdrawn;
        } else {
            totalLent = 0;
        }
    }

    _burn(msg.sender, _shares);
    if (amount > vaultBalance) {
        amount = vaultBalance;
    }
    emit Withdraw(_recipient, amount, _shares);
    want.safeTransfer(_recipient, amount);
}
```

The withdraw function is slightly more complicated as funds are taken from the strategy if there are insufficient idle funds held in the vault contract.

The caller will have their share tokens burnt in exchange for the want value of their shares, this is determined by _calcShareValue(shares)._ If the vault doesn't have funds available immediately it will withdraw funds from the strategy. This involves closing the correct number of short positions, long positions and buffer positions which will output a withdrawal amount equivalent to the amount needed to fulfil the withdrawal. In the event of a loss, the amount returned will be reduced and this will be reflected in the share value and _totalLent_.
