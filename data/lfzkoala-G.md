# Issue 1 - Optimizations for storage reads

## Description
The `_claimDelayedWithdrawals` function reads from storage multiple times with `_userWithdrawals[recipient].delayedWithdrawals`.

## Locations
src/contracts/pods/DelayedWithdrawalRouter.sol#137-163

## Suggestion
There are some minor improvements that can be made:

1. You can cache the `_userWithdrawals[recipient]` struct to avoid multiple storage reads:

```solidity
UserWithdrawal storage userWithdrawal = _userWithdrawals[recipient];
```

Then replace all occurrences of `_userWithdrawals[recipient]` with `userWithdrawal`.

2. Instead of using the `unchecked` block, you can initialize the `i` variable with `uint256 i = 0;` and replace the `++i` with `i++`. This change will not have a significant impact on gas usage, but it makes the code more readable and easier to understand.

With these changes, the function would look like this:

```solidity
function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) internal {
    uint256 amountToSend = 0;
    UserWithdrawal storage userWithdrawal = _userWithdrawals[recipient];
    uint256 delayedWithdrawalsCompletedBefore = userWithdrawal.delayedWithdrawalsCompleted;
    uint256 _userWithdrawalsLength = userWithdrawal.delayedWithdrawals.length;
    uint256 i = 0;
    while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
        DelayedWithdrawal memory delayedWithdrawal = userWithdrawal.delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
        if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
            break;
        }
        amountToSend += delayedWithdrawal.amount;
        i++;
    }
    userWithdrawal.delayedWithdrawalsCompleted = delayedWithdrawalsCompletedBefore + i;
    if (amountToSend != 0) {
        AddressUpgradeable.sendValue(payable(recipient), amountToSend);
    }
    emit DelayedWithdrawalsClaimed(recipient, amountToSend, delayedWithdrawalsCompletedBefore + i);
}
```

These optimizations are minor but can help reduce gas usage and improve readability. 