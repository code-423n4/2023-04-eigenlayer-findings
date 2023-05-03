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


3. deposit/withdraw in IStrategy does not need to take token, if only onlyStrategyManager can call and only 1 rewardToken is expected per strategy.