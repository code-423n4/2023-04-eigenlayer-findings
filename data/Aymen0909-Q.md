# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1 | `claimableUserDelayedWithdrawals` does not take into account `withdrawalDelayBlocks` | Low | 1 |
| 2 | `sharesToUnderlying` and `underlyingToShares` functions marked as `view` but docs say they can make changes | Low | 2 |
| 3 | `slashQueuedWithdrawal` and `slashShares` functions should emit an event  | Low | 1 |
| 4 | Immutable state variables lack zero address checks | Low | 7 |
| 5 | Named return variables not used anywhere in the functions | NC | 2 |

## Findings

### 1- `claimableUserDelayedWithdrawals` does not take into account `withdrawalDelayBlocks` :

#### Risk : Low

The `claimableUserDelayedWithdrawals` function from the DelayedWithdrawalRouter contract is used to get all the delayedWithdrawals that are currently claimable by the `user`, but the function does not take into account that delayedWithdrawals can only be claimed after `withdrawalDelayBlocks` from their creation time (blockCreated), the result of this error is that the function will return all the users's unclaimed delayedWithdrawals even if some of them are not yet claimable at that time.

The condition for a delayedWithdrawal to be claimable is highlighted in the `canClaimDelayedWithdrawal` function below :

```solidity
 function canClaimDelayedWithdrawal(address user, uint256 index) external view returns (bool) {
    return ((index >= _userWithdrawals[user].delayedWithdrawalsCompleted) && (block.number >= _userWithdrawals[user].delayedWithdrawals[index].blockCreated + withdrawalDelayBlocks));
}
```

Basically there 2 condition : delayedWithdrawal index is greater than `delayedWithdrawalsCompleted` and `withdrawalDelayBlocks` has passed from the creation of the delayedWithdrawal.

As you can see in the `claimableUserDelayedWithdrawals` function below, the first condition is verified as the function finds only the users's unclaimed delayedWithdrawals (calculated with `claimableDelayedWithdrawalsLength`) but the second condition is not verified as there is no check against `withdrawalDelayBlocks` for each delayedWithdrawal.

```solidity
function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
    uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
    uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
    // @audit get all unclaimed delayedWithdrawals without 
    uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;
    DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);
    for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
        // @audit No check against `withdrawalDelayBlocks`
        claimableDelayedWithdrawals[i] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
    }
    // @audit will return all unclaimed delayedWithdrawals without checking against `withdrawalDelayBlocks`
    return claimableDelayedWithdrawals;
}
```

This issue does not currently have an impact on the protocol as the function is external and not used inside it, but it would if in the future other contracts rely on it, add to that this error will cause problems for users or other protocols when using this function in their logic.

#### Mitigation
I recommend to check if each DelayedWithdrawal is ready to be claimed in the `claimableUserDelayedWithdrawals` function, the function should be modified as follows : 

```
function claimableUserDelayedWithdrawals(address user) external view returns (DelayedWithdrawal[] memory) {
    uint256 delayedWithdrawalsCompleted = _userWithdrawals[user].delayedWithdrawalsCompleted;
    uint256 delayedWithdrawalsLength = _userWithdrawals[user].delayedWithdrawals.length;
    uint256 claimableDelayedWithdrawalsLength = delayedWithdrawalsLength - delayedWithdrawalsCompleted;
    DelayedWithdrawal[] memory claimableDelayedWithdrawals = new DelayedWithdrawal[](claimableDelayedWithdrawalsLength);

    uint256 index = 0; // counter for really claimable DelayedWithdrawals
    for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {
        // check if withdrawalDelayBlocks has passed for delayedWithdrawal creation
        if (block.number >= _userWithdrawals[user].delayedWithdrawals[i].blockCreated + withdrawalDelayBlocks){
            claimableDelayedWithdrawals[index] = _userWithdrawals[user].delayedWithdrawals[delayedWithdrawalsCompleted + i];
            index++;
        }
    }
    return claimableDelayedWithdrawals;
}
```

### 2- `sharesToUnderlying` and `underlyingToShares` functions marked as `view` but docs say they can make changes :

#### Risk : Low

The functions `sharesToUnderlying` and `underlyingToShares` are both marked as `view` but their respective comments say that they may make state modifications, because the functions are marked `view` in the StrategyBase contract any strategy that inherits from it will not be able to change the functions mutability and so those functions will never be able to make state modifications. 

If this is just an error in the comments then it must be corrected but if the functions are really supposed to be able to make state modifications then the code must be reviewed to enable that.

#### Proof of Concept
Instances include:

File: StrategyBase.sol [Line 180-188](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L180-L188)
```solidity
/**
* @notice Used to convert a number of shares to the equivalent amount of underlying tokens for this strategy.
* @notice In contrast to `sharesToUnderlyingView`, this function **may** make state modifications
* @param amountShares is the amount of shares to calculate its conversion into the underlying token
* @dev Implementation for these functions in particular may vary signifcantly for different strategies
*/
function sharesToUnderlying(uint256 amountShares) public view virtual override returns (uint256) {
    return sharesToUnderlyingView(amountShares);
}
```

File: StrategyBase.sol [Line 205-213](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L205-L213)
```solidity
/**
* @notice Used to convert an amount of underlying tokens to the equivalent amount of shares in this strategy.
* @notice In contrast to `underlyingToSharesView`, this function **may** make state modifications
* @param amountUnderlying is the amount of `underlyingToken` to calculate its conversion into strategy shares
* @dev Implementation for these functions in particular may vary signifcantly for different strategies
*/
function underlyingToShares(uint256 amountUnderlying) external view virtual returns (uint256) {
    return underlyingToSharesView(amountUnderlying);
}
```

### 3- `slashQueuedWithdrawal` and `slashShares` functions should emit an event :

#### Risk : Low

The function `slashShares` is used to slash a given operator/staker shares and the function `slashQueuedWithdrawal` is used to slash the shares being withdrawen in the QueuedWithdrawal, both functions should emit an event to acknowledge stakers that there shares where slashed and this event can also be used by external dapps to triggers changes in their states. 

#### Mitigation

Consider  emitting events in both `slashQueuedWithdrawal` and `slashShares` functions.

### 4- Immutable state variables lack zero address checks :

Constructors should check the values written in an immutable state variables(address) is not the address(0).

#### Risk : Low

#### Proof of Concept
Instances include:

File: StrategyManagerStorage.sol [Line 73-75](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L73-L75)
```
delegation = _delegation;
eigenPodManager = _eigenPodManager;
slasher = _slasher;
```

File: EigenPodManager.sol [Line 77-80](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L77-L80)
```
ethPOS = _ethPOS;
eigenPodBeacon = _eigenPodBeacon;
strategyManager = _strategyManager;
slasher = _slasher;
```

File: StrategyBase.sol [Line 47](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L47)
```
strategyManager = _strategyManager;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.


### 5- Adding a `return` statement when the function defines a named return variable, is redundant :

#### Risk : Non critical

#### Proof of Concept
Instances include:

File: StrategyManager.sol [Line 671](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L671)

File: StrategyBase.sol [Line 111](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L111)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.