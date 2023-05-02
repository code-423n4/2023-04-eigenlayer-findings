# queueWithdrawal can be spammed.
https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329
Avoid spamming on `queueWithdrawal` anyone can call this function without actually staking in Eigenlayer.
```solidity
function test_spamQueueWithdrawal() public {
        address anyone = address(1337);
        //Junk data
        uint[] memory strategyIndexes_ = new uint[](1);
        strategyIndexes_[0] = 1;
        IStrategy[] memory strategyArray = new IStrategy[](0);
        uint[] memory shareAmounts = new uint[](0);
        
        //Anyone does not have any stake on Eigenlayer
        //Anyone tries to spam the system
        cheats.startPrank(anyone);
        for (uint256 i; i < 1e2; ++i) {
            strategyManager.queueWithdrawal(
            strategyIndexes_,
            strategyArray,
            shareAmounts,
            attacker,
            false
        );
        }
        cheats.stopPrank();
    }
```
Note: This wasn't caught by bot.
## Recommendation:
Add `require(strategies.length != 0)` to `queueWithdrawal`