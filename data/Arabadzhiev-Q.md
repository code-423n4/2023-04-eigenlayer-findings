# [L-01] Misleading owner-only function comments

The comments of the `addStrategiesToDepositWhitelist` and `removeStrategiesFromDepositWhitelist` functions are stating that they are owner-only,
while they actually are strategy-whitelister-only.

## Lines of code

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L591

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L606

# [N-01] Wrong order of NatSpec param tags

In some places, the param tags of the function/event NatSpec comments are not in the same order as the arguments they are describing.

## Lines of code 
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L48-L52

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L125-L127

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L141-L144

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L473-L476

# [N-02] Functions with multiple NatSpec param tags for a single argument

There are functions that have multiple param tags with different descriptions for a single argument.

## Lines of code
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L158-161

# [N-03] Functions without NatSpec param tags for all arguments

There are functions that have some arguments that are described by param tags, and some that are not. For the sake of consistency, it is generraly better to either describe all arguments, or none at all.

## Lines of code
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L473-L476

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L316-L319

# [N-04] Explicitly returning named `return` variables

Explicitly returning named return variables is confussing, since in most other places they are being returned implicitly.

## Lines of code

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L671
