# Gas optimisation
## Emitting events within a loop is very expensive (costs a lot of gas)
The formula for how much gas an event costs is as follows [source](https://github.com/wolflo/evm-opcodes/blob/main/gas.md#a8-log-operations):
`375 + 375 * num_topics + 8 * data_size + mem_expansion cost`
This gets calculated within every iteration of the loop.
## Instances:
**3 instances** of this issue.
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L376
`emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);`
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L598
`emit StrategyAddedToDepositWhitelist(strategiesToWhitelist[i]);`
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L613
`emit StrategyRemovedFromDepositWhitelist(strategiesToRemoveFromWhitelist[i]);`
Recommendations:
`emit ShareWithdrawalQueued(msg.sender, nonce, strategies[i], shares[i]);`
Can be refactored into:
Within the function body:
```solidity
uint256 len = strategies.length;
IStrategy[] memory tmp_strat = new IStrategy[](len);
uint256[] memory tmp_shares = new uint256[](len);
```
Within the loop:
```solidity
tmp_strat.push(strategies[i]);
tmp_shares.push(shares[i]);
```
it can then be emitted once outside the for loop as:
`emit ShareWithdrawalQueued(msg.sender, nonce, tmp_strat, tmp_shares);`
this can be applied to the rest of the instance.