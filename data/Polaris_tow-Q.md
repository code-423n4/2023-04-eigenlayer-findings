## Missing check
 In the deposit function, it is necessary to check whether the newly issued shares exceed MAX_NONZERO_TOTAL_SHARES (which is 1e9 in this case).
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/strategies/StrategyBase.sol#L78

## denial-of-service attack
In the claimDelayedWithdrawals function, although there are conditional statements in the loop, if the maximum loop count is very large, it may cause the transaction to fail. If the loop count exceeds the block limit, a denial-of-service attack may occur.
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L91

## user may need to wait a long time to access their funds.
If a user calls createDelayedWithdrawal multiple times within a short period of time, they may become locked out and unable to withdraw funds from the smart contract. Because the withdrawalDelayBlocks variable can be modified by the owner, if the owner sets it for a long time, the user may need to wait a long time to access their funds.
https://github.com/code-423n4/2023-04-eigenlayer/blob/5e4872358cd2bda1936c29f460ece2308af4def6/src/contracts/pods/DelayedWithdrawalRouter.sol#L59



