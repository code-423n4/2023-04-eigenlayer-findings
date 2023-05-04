## _claimDelayedWithdrawals doesn't reuse/delete the paid DelayedWithdrawal slot in _userWithdrawals

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L142

In createDelayedWithdrawal(), new DelayedWithdrawal is pushes into the _userWithdrawals array:

```solidity
    function createDelayedWithdrawal(address podOwner, address recipient) external payable onlyEigenPod(podOwner) {
        uint224 withdrawalAmount = uint224(msg.value);
        if (withdrawalAmount != 0) {
            DelayedWithdrawal memory delayedWithdrawal = DelayedWithdrawal({
                amount: withdrawalAmount,
                blockCreated: uint32(block.number)
            });
            _userWithdrawals[recipient].delayedWithdrawals.push(delayedWithdrawal);
            emit DelayedWithdrawalCreated(podOwner, recipient, withdrawalAmount, _userWithdrawals[recipient].delayedWithdrawals.length - 1);
        }
    }
```

However, when these DelayedWithdrawals are paid/claimed, the useless slot is not reused.

```solidity
    function _claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim) internal {
        uint256 amountToSend = 0;
        uint256 delayedWithdrawalsCompletedBefore = _userWithdrawals[recipient].delayedWithdrawalsCompleted;
        uint256 _userWithdrawalsLength = _userWithdrawals[recipient].delayedWithdrawals.length;
        uint256 i = 0;
        while (i < maxNumberOfDelayedWithdrawalsToClaim && (delayedWithdrawalsCompletedBefore + i) < _userWithdrawalsLength) {
            // copy delayedWithdrawal from storage to memory
            DelayedWithdrawal memory delayedWithdrawal = _userWithdrawals[recipient].delayedWithdrawals[delayedWithdrawalsCompletedBefore + i];
            // check if delayedWithdrawal can be claimed. break the loop as soon as a delayedWithdrawal cannot be claimed
            if (block.number < delayedWithdrawal.blockCreated + withdrawalDelayBlocks) {
                break;
            }
            // otherwise, the delayedWithdrawal can be claimed, in which case we increase the amountToSend and increment i
            amountToSend += delayedWithdrawal.amount;
            // increment i to account for the delayedWithdrawal being claimed
            unchecked {
                ++i;
            }
        }
        // mark the i delayedWithdrawals as claimed
        _userWithdrawals[recipient].delayedWithdrawalsCompleted = delayedWithdrawalsCompletedBefore + i;
        // actually send the ETH
        if (amountToSend != 0) {
            AddressUpgradeable.sendValue(payable(recipient), amountToSend);
        }
        emit DelayedWithdrawalsClaimed(recipient, amountToSend, delayedWithdrawalsCompletedBefore + i);
    }
```

If we can reuse these slots (e.g. use a pre-allocated array as a circular buffer), **we can make the storage cost from O(n) to a const number**. This can be a big saving for a busy recipient.
