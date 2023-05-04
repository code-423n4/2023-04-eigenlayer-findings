| ID    | Title |
| -------- | ------- |
| 01 | Emit events before external calls
| 02 | Withdrawals can be completed when the system is in frozen mode
| 03 | `Address.isContract()` can fail
| 04 | Check for stale values on setter functions
| 05 | Change the state before external calls
| 06 | `__gap` could be declared on a single file to be reused across the project
| 07 | Add the private keyword for private variables
| 08 | Constant redefined elsewhere
| 09 | Add a limit for the maximum number of characters per line
| 10 | Interchangeable usage of uint and uint256
| 11 | Improvements on `for` loop
| 12 | Don't break a line for the `else` clause

# [01] Emit events before external calls

Wherever possible, consider emitting events before external calls. In case of reentrancy, funds are not at risk (for external call + event ordering), however emitting events after external calls can damage frontends and monitoring tools in case of reentrancy attacks.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L661-L669

# [02] Withdrawals can be completed when the caller is frozen

It's not possible to queue a withdrawal when the system is frozen for a particular address, but it's possible to conclude already queued withdrawals for the same address even if the system is frozen. This might cause problems related to slashed stakers being able to complete withdrawals unexpectedly. 

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L431-L449

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L451-L469

Note: this might be intentional due to the comment on [L463](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L463).

## Recommendation

Don't allow queue withdrawals to be completed when an address is frozen mode. Consider adding the `onlyNotFrozen` modifier into `StrategyManager.completeQueuedWithdrawal()` and `StrategyManager.completeQueuedWithdrawals()`.

# [03] `Address.isContract()` can fail

`Address.isContract()` will fail to check if the input is a contract in case the contract calls the function through the constructor.

For example, if a contract constructor calls `StrategyManager.depositIntoStrategyWithSignature()`, the function `Address.isContract()` will return false, and then `ECDSA.recover()` will be called instead, resulting in a contract call being validated as an EOA.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L283-L295

## Recommendation

One option could be to replace `Address.isContract()` and make the check through `tx.origin`, since `tx.origin` will always be an EOA. However, the usage of `tx.origin` should be well tested and further scrutinized since it can introduce new problems.

# [04] Check for stale values on setter functions

Add a check ensuring that the new value if different than the current value to avoid emitting unnecessary events.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L838-L849

# [05] Change the state before external calls

Consider changing the state prior to external transfers to follow the checks-effects-interactions pattern. For example, consider calling `_addShares()` before `token.safeTransferFrom()` in `StrategyManager._depositIntoStrategy()`.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L661-L667

# [06] `__gap` could be declared on a single file to be reused across the project

To improve code reusability, consider declaring gap on a single file to be reused.Please, refer to this [example](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol) for an implementation reference.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L115

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L177

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L226

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L250

# [07] Add the private keyword for private variables

Adding the private keyword will make it more explicit.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L42

# [08] Constant redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

A cheap way to store constants in a single location is to create an internal constant in a library. If the variable is a local cache of another contract’s value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don’t get out of sync.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L37-L40

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22-L23

# [09] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

Refer to this [example](https://github.com/ProjectOpenSea/seaport/blob/main/contracts/lib/Consideration.sol#L77-L89) for how to handle comments wrapping.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L133

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L73-L74

# [10] Interchangeable usage of uint and uint256

Consider using only one approach, e.g. using it only uint256.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L33

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L137

# [11] Improvements on `for` loop

Consider applying the following pattern on `for` loops:

- Avoid initializing `i` to zero
- Make increment prefixed instead of postfixed and also consider making it unchecked since it can't overflow.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L115

# [12] Don't break a line for the `else` clause

Consider refactoring from:

```
if {

}
else {

}
```

To:

```
if {

} else {

}
```

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L507-L514
