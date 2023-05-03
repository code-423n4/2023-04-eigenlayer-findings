# Array length not checked

In the queueWithdrawal and slashShares functions of StrategyManager.sol, the length of the strategyIndexes parameter is not checked. This could cause user operations to not be fully executed.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L329-L382

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L482-L520