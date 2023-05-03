## Unutilized cache
In `StrategyManager._completeQueuedWithdrawal()`, a local variable, `strategiesLength`, has been declared but it has not been fully utilized.

Consider having the affected code block refactored as follows:

[File: StrategyManager.sol#L774-L780](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L774-L780)

```diff
        // store length for gas savings
        uint256 strategiesLength = queuedWithdrawal.strategies.length;
        // if the withdrawer has flagged to receive the funds as tokens, withdraw from strategies
        if (receiveAsTokens) {
-            require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
+            require(tokens.length == strategiesLength, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
            // actually withdraw the funds
            for (uint256 i = 0; i < strategiesLength;) {
```
## Unneeded convenient function
In DelayedWithdrawalRouter.sol, `claimDelayedWithdrawals(uint256 maxNumberOfDelayedWithdrawalsToClaim)` is unneeded since the caller can always have `msg.sender` inputted as `recipient`:

[File: DelayedWithdrawalRouter.sol#L79-L97](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L79-L97)

```diff
    function claimDelayedWithdrawals(address recipient, uint256 maxNumberOfDelayedWithdrawalsToClaim)
        external
        nonReentrant
        onlyWhenNotPaused(PAUSED_DELAYED_WITHDRAWAL_CLAIMS)
    {
        _claimDelayedWithdrawals(recipient, maxNumberOfDelayedWithdrawalsToClaim);
    }

-    function claimDelayedWithdrawals(uint256 maxNumberOfDelayedWithdrawalsToClaim)
-        external
-        nonReentrant
-        onlyWhenNotPaused(PAUSED_DELAYED_WITHDRAWAL_CLAIMS)
-    {
-        _claimDelayedWithdrawals(msg.sender, maxNumberOfDelayedWithdrawalsToClaim);
-
    }
```