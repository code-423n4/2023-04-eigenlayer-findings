### underlyingToken and podOwner variables can be made immutable
The variables `underlyingToken` and `podOwner` can be immutable

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L57
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L59

### Pack bool hasRestaked with address podOwner(Saves 1 SLOT: 2k gas)
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L73

### Redundant `token == underlyingToken` require check should be made modifier

https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L86

### copy `totalShares` value to memory for the deposit function as well
Just as done in the withdraw function: https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L130