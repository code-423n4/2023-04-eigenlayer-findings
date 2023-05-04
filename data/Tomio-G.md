1.
Title: function `_tokenBalance()` called twice

Proof of Concept:
[StrategyBase.sol#L150-L152](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L150-L152)

Recommended Mitigation Steps:
`_tokenBalance()` cache it like [L197](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L197)

```
uint256 tokenBalance = _tokenBalance();
if (priorTotalShares == amountShares) {
            amountToSend = tokenBalance ;
        } else {
            amountToSend = (tokenBalance  * amountShares) / priorTotalShares;
        }
```
________________________________________________________________________

2.
Title: Use `uint32` instead of `uint256`

Proof of Concept:
[DelayedWithdrawalRouter.sol#L24](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L24)
the maximum value of `MAX_WITHDRAWAL_DELAY_BLOCKS` is only 50400, which is well within the range of a `uint32`. Using `uint32` instead of `uint256` for withdrawalDelayBlocks would reduce storage requirements and hence save gas.
________________________________________________________________________

