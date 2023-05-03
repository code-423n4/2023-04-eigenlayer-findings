1. make input in StrategyManager::completeQueuedWithdrawals as a struct

right now the input is not guaranteed to be the same length, it's better to make them in a struct so length checks are not needed

```solidity
function completeQueuedWithdrawals(
        QueuedWithdrawal[] calldata queuedWithdrawals,
        IERC20[][] calldata tokens,
        uint256[] calldata middlewareTimesIndexes,
        bool[] calldata receiveAsTokens
    ) external
```

Recommendation

```solidity
struct Input {
    QueuedWithdrawal a;
    IERC20[] b;
    uint256 c;
    bool d;
}

function completeQueuedWithdrawals(Input[] calldata intputs) {
}
```

2. Same issue as 1.) for slashShares

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L482-L489


3. deposit/withdraw in IStrategy does not need to take token, if only onlyStrategyManager can call and only 1 rewardToken is expected per strategy.


4. Nothing is immediately withdrawable with the use of `DelayedWithdrawalRouter.sol`

However there are a few doc/comments that still use "immediately withdrawable", which is mis-leading.

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L374

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L389


5.