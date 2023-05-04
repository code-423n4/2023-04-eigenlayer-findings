# Low Issues

• [L-1] `queueWithdrawal()` and `slashShares()` can revert with an out of bounds error if strategies are removed
• [L-2] `slashQueuedWithdrawal()` can leave stuck assets in strategies if passed an incorrect `indicesToSkip`
• [L-3] Allowing anyone to claim delayed withdrawals on behalf of the users can be detrimental to them
• [L-4] Missing events for important actions
• [L-5] `_claimDelayedWithdrawals` emits "empty" events when `maxNumberOfDelayedWithdrawalsToClaim == 0`

## [L-1] `queueWithdrawal()` and `slashShares()` can revert with an out of bounds error if strategies are removed

If an strategy is removed from the user strategies array, it will "change" the index of the following strategies, leading to the possibility of the function trying to get the data from an out of bounds index.

### Impact

`queueWithdrawal()` and `slashShares()` will revert depending on the `strategyIndexes` passed. The user will have to call the functions with one strategy at a time or calculate the correct final indexes.

### Proof of Concept

Both `queueWithdrawal()` and `slashShares()` call `_removeShares()` passing to it an `strategyIndexes` array, with the expected positions of the strategies.

If the strategy has no more shares, `_removeShares()` proceeds to remove the strategy from the list:

```solidity
    if (userShares == 0) {
        _removeStrategyFromStakerStrategyList(depositor, strategyIndex, strategy);

        // return true in the event that the strategy was removed from stakerStrategyList[depositor]
        return true;
    }
```

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L700-L705)

`_removeStrategyFromStakerStrategyList()` contains this line:

```solidity
    if (stakerStrategyList[depositor][strategyIndex] == strategy) {
        // ...
    }
    // ...
    stakerStrategyList[depositor].pop();
```

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L717)

Let's suppose that the users passed `[0, 1]` as the `strategyIndexes` array, as those are the indexes before calling the function.

If the strategy with `strategyIndex == 0` is removed, the array length will be now 1.

This will make the line `stakerStrategyList[depositor][strategyIndex]` revert with index out of bounds as the `strategyIndex` for the second strategy to remove was `1`, which is out of bounds for the new array length.

The user would have to mitigate the issue by calculating the actual indexes, and pass `strategyIndexes = [0, 0]`, for example.

### Coded POC

Add this file to `src/test/StrategyManagerUnit.t.sol` and run `forge test -m "testQueueWithdrawal_FailsOutOfBounds"`:

```solidity
    function testQueueWithdrawal_FailsOutOfBounds() external {
        StrategyWrapper dummyStrat2 = new StrategyWrapper(strategyManager, dummyToken);

        // Whitelist the strategy for deposit
        cheats.startPrank(strategyManager.owner());
        IStrategy[] memory strategies = new IStrategy[](1);
        strategies[0] = dummyStrat2;
        strategyManager.addStrategiesToDepositWhitelist(strategies);
        cheats.stopPrank();

        uint256 depositAmount = 1e18;

        // User deposits into both the conflicting strategy and another one
        strategyManager.depositIntoStrategy(dummyStrat, dummyToken, depositAmount);
        strategyManager.depositIntoStrategy(dummyStrat2, dummyToken, depositAmount);

        // Define some variables for queueing the withdrawal
        uint256 withdrawalAmount = 1e18;
        address staker = address(this);
        address withdrawer = address(this);

        uint256[] memory strategyIndexes = new uint256[](2);
        strategyIndexes[0] = 0;
        strategyIndexes[1] = 1; // @audit This will make the tx fail with out of bounds

        IStrategy[] memory strategyArray = new IStrategy[](2);
        strategyArray[0] = dummyStrat;
        strategyArray[1] = dummyStrat2;

        IERC20[] memory tokensArray = new IERC20[](2);
        tokensArray[0] = dummyToken;
        tokensArray[1] = dummyToken;

        uint256[] memory sharesArray = new uint256[](2);
        sharesArray[0] = withdrawalAmount;
        sharesArray[1] = withdrawalAmount;

        IStrategyManager.WithdrawerAndNonce memory withdrawerAndNonce = IStrategyManager.WithdrawerAndNonce({
            withdrawer: withdrawer,
            nonce: uint96(strategyManager.numWithdrawalsQueued(staker))
        });
        IStrategyManager.QueuedWithdrawal memory queuedWithdrawal = 
            IStrategyManager.QueuedWithdrawal({
                strategies: strategyArray,
                shares: sharesArray,
                depositor: staker,
                withdrawerAndNonce: withdrawerAndNonce,
                withdrawalStartBlock: uint32(block.number),
                delegatedAddress: strategyManager.delegation().delegatedTo(staker)
            }
        );

        // Reverts with out of bounds when trying to queue the whole amount of both strategies and remove them from the list
        cheats.expectRevert();
        strategyManager.queueWithdrawal(strategyIndexes, queuedWithdrawal.strategies, queuedWithdrawal.shares, withdrawer, false);
    }
```

### Tools Used

Manual Review

### Recommended Mitigation Steps

Check that the `strategyIndex` is less than the `stakerStrategyList` length in `_removeStrategyFromStakerStrategyList()` and loop through all of the strategies in case it is out of bounds.

## [L-2] `slashQueuedWithdrawal()` can leave stuck assets in strategies if passed an incorrect `indicesToSkip`

`indicesToSkip` is meant to be used to skip malicious strategies that may revert.

The problem is that once skipped those strategies cannot be retried to be slashed.

If the user calling the function skips a legit strategy, it will remain orphan, with their corresponding assets stuck in it.

## Impact

Assets from strategies in orphan queued withdrawals will be stuck on the corresponding strategy contract.

They won't be able to be slashed.

## Proof of Concept

`slashQueuedWithdrawal()` sets `withdrawalRootPending[withdrawalRoot] = false;`, so that `withdrawalRoot` will be unusable in the protocol in the future.

`slashShares` won't be effective, as the shares were previously removed in the `queueWithdrawal()` function, so they can't be slashed.

So, any skipped strategy in the queued withdrawal will become unslashable in the future.

```solidity
    function slashQueuedWithdrawal(address recipient, QueuedWithdrawal calldata queuedWithdrawal, IERC20[] calldata tokens, uint256[] calldata indicesToSkip)
        external
        onlyOwner
        onlyFrozen(queuedWithdrawal.delegatedAddress)
        nonReentrant
    {
        require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.slashQueuedWithdrawal: input length mismatch");

        // find the withdrawalRoot
        bytes32 withdrawalRoot = calculateWithdrawalRoot(queuedWithdrawal);

        // verify that the queued withdrawal is pending
        require(
            withdrawalRootPending[withdrawalRoot],
            "StrategyManager.slashQueuedWithdrawal: withdrawal is not pending"
        );

        // reset the storage slot in mapping of queued withdrawals
        withdrawalRootPending[withdrawalRoot] = false; // @audit makes `withdrawalRoot` unusable in the future

        // keeps track of the index in the `indicesToSkip` array
        uint256 indicesToSkipIndex = 0;

        uint256 strategiesLength = queuedWithdrawal.strategies.length;
        for (uint256 i = 0; i < strategiesLength;) {
            // check if the index i matches one of the indices specified in the `indicesToSkip` array
            if (indicesToSkipIndex < indicesToSkip.length && indicesToSkip[indicesToSkipIndex] == i) {
                unchecked {
                    ++indicesToSkipIndex;
                }
            } else {
                if (queuedWithdrawal.strategies[i] == beaconChainETHStrategy){
                     //withdraw the beaconChainETH to the recipient
                    _withdrawBeaconChainETH(queuedWithdrawal.depositor, recipient, queuedWithdrawal.shares[i]);
                } else {
                    // tell the strategy to send the appropriate amount of funds to the recipient
                    // @audit This is the only chance to slash the user for this strategy
                    queuedWithdrawal.strategies[i].withdraw(recipient, tokens[i], queuedWithdrawal.shares[i]);
                }
                unchecked {
                    ++i;
                }
            }
        }
    }
```

[Link to code](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L536-L579)

## Tools Used

Manual Review

## Recommended Mitigation Steps

Add skipped strategies from `slashQueuedWithdrawal()` to a map, so that they can be slashed later if they were skipped by mistake.

It can also help with strategies that may have a momentary revert on `withdraw()`, but can eventually make it work again.

## [L-3] Allowing anyone to claim delayed withdrawals on behalf of the users can be detrimental to them

For accounting purposes, taxes, or any other legal implication, user may decide to postpone their withdrawals for an upcoming date.

Allowing anyone to withdraw on their behalf would be in detriment of the mentioned reasons.

Consider removing the `DelayedWithdrawalRouter::claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim)` function:

```solidity
    /**
     * @notice Called in order to withdraw delayed withdrawals made to the `recipient` that have passed the `withdrawalDelayBlocks` period.
     * @param recipient The address to claim delayedWithdrawals for.
     * @param maxNumberOfDelayedWithdrawalsToClaim Used to limit the maximum number of delayedWithdrawals to loop through claiming.
     * @dev 
     *      WARNING: Note that the caller of this function cannot control where the funds are sent, but they can control when the 
     *              funds are sent once the withdrawal becomes claimable.
     */
    function claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim)
        external
        nonReentrant
        onlyWhenNotPaused(PAUSED_DELAYED_WITHDRAWAL_CLAIMS)
    {
        _claimDelayedWithdrawals(recipient, maxNumberOfDelayedWithdrawalsToClaim);
    }
```

## [L-4] Missing events for important actions

Some important functions in `StrategyManager.sol` do not trigger an event that allows to track them.

### Recommended Mitigation Steps

Consider adding relevant events to the functions: 

`slashShares()`, `slashQueuedWithdrawal()` to keep track of slashes.

`addStrategiesToDepositWhitelist()` and `removeStrategiesFromDepositWhitelist` for users an other protocols to keep track of changes in whitelisted strategies.

## [L-5] `_claimDelayedWithdrawals` emits "empty" events when `maxNumberOfDelayedWithdrawalsToClaim == 0`

The `_claimDelayedWithdrawals` allows`maxNumberOfDelayedWithdrawalsToClaim == 0`, which will bypass the `while` statement, and emit an "empty" event.

In order to keep events emission as clean as possible, consider checking that `maxNumberOfDelayedWithdrawalsToClaim != 0`

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

# Non-Critical Issues

## [NC-1] Use `i` as the increment variable

`j` is usually used when there is already a previous `i` variable being used.

Consider using `i` in `_removeStrategyFromStakerStrategyList()`, as it is not previously used:

```solidity
    function _removeStrategyFromStakerStrategyList(address depositor, uint256 strategyIndex, IStrategy strategy) internal {
        // if the strategy matches with the strategy index provided
        if (stakerStrategyList[depositor][strategyIndex] == strategy) {
            // replace the strategy with the last strategy in the list
            stakerStrategyList[depositor][strategyIndex] =
                stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
        } else {
            //loop through all of the strategies, find the right one, then replace
            uint256 stratsLength = stakerStrategyList[depositor].length;
            uint256 j = 0;
            for (; j < stratsLength;) {
                if (stakerStrategyList[depositor][j] == strategy) {
                    //replace the strategy with the last strategy in the list
                    stakerStrategyList[depositor][j] = stakerStrategyList[depositor][stakerStrategyList[depositor].length - 1];
                    break;
                }
                unchecked {
                    ++j;
                }
            }
            // if we didn't find the strategy, revert
            require(j != stratsLength, "StrategyManager._removeStrategyFromStakerStrategyList: strategy not found");
        }
        // pop off the last entry in the list of strategies
        stakerStrategyList[depositor].pop();
    }
```