### underlyingToken and podOwner variables can be made immutable
The variables `underlyingToken` and `podOwner` can be immutable

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L57
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L59

### Pack bool hasRestaked with address podOwner(Saves 1 SLOT: 2k gas)
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L73

### Reorder the require statement to have cheaper one before the gas consuming one
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L343
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L766