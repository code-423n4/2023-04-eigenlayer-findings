1. `decreaseDelegatedShares` is called when `shareAmounts` = 0. This may cause unintended side effects as this makes another external call within the `delegation` contract. If `shareAmounts`= 0 then no changes need to be made to the caller's shares so they will waste gas calling this function, instead it should probably just revert in this case.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L205

2. `queueWithdrawal` violates CEI and makes an external call to `msg.sender` which may be dangerous

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L346

3. `slashShares` is missing sanity check to ensure `shareAmounts.length == tokens.length`.

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L482-L524

4. `slashQueuedWithdrawal` doesn't enforce the check that if `shares` contains `beaconChainETH` then it must only contain `beaconChainETH`

https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L536-L579
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/core/StrategyManager.sol#L324
