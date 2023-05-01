## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|N-01       |  No check on length of input arrays | 3 |
|N-02       |  Don't have tokens as an input parameter | 1 |
|N-03       |  `totalShares` is always 0 when `priorTokenBalance` is 0| 1 |

# Details
## Non critical
## [N-01] No check on length of input arrays
The function [`completeQueuedWithdrawals()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L456-L469) has 4 input arrays. However there is no check to see if they all have the same length. If there are arrays with a length lower than `queuedWithdrawals`. The function will fail with an index out of bound.

The same applies to the [`queueWithdrawal()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329-L429) and [`slashShares()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L482-L524) functions. It's a bit different here, only 2 out of the 3 or 4 input array lengths are checked.

## [N-02] Don't have tokens as an input parameter
When you withdraw tokens in the [`_completeQueuedWithdrawal`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L785-L790) function, the token is a input parameter. However, since the token can only be the underlying token, the token can be extracted from the queuedWithdrawal struct. This will eliminate one more input parameter and make the experience a bit easier.
```diff
        } else {
            // tell the strategy to send the appropriate amount of funds to the depositor
            queuedWithdrawal.strategies[i].withdraw(
-               msg.sender, tokens[i], queuedWithdrawal.shares[i]
+               msg.sender, queuedWithdrawal.strategies[i].underlyingToken(), queuedWithdrawal.shares[i]
            );
        }
```
## [N-03] `totalShares` is always 0 when `priorTokenBalance` is 0
The [`deposit`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L92-L101) has to many checks when calculating the shares of a new deposit. When `totalShares` is not 0, it calculates the `priorTokenBalance`. Afterwards it checks if this is equal to 0. However if totalShares is more than 0, there is no possibility of `priorTokenBalance` to be 0.

`priorTokenBalance` will only be 0 on the first deposit and then it won't go into the else statement. The last if statement can be removed.
```diff
        if (totalShares == 0) {
            newShares = amount;
        } else {
            uint256 priorTokenBalance = _tokenBalance() - amount;
-            if (priorTokenBalance == 0) {
-                newShares = amount;
-            } else {
                newShares = (amount * totalShares) / priorTokenBalance;
-            }
        }
```