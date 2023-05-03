## Inadequate check in the last if block of `StrategyManager.queueWithdrawal()`
The caller might be self delegated, making the function revert even more late in logic. Consider having the affected code line refactored as follows:

[File: StrategyManager.sol#L422-L424](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422-L424)

```diff
-        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
+        if (undelegateIfPossible && !delegation.isOperator(msg.sender) && stakerStrategyList[msg.sender].length == 0) {
            _undelegate(msg.sender);
        }
```

## Comment and code mismatch
The function comment on `StrategyManager.completeQueuedWithdrawal()` is missing the struct `withdrawerAndNonce` instance. Consider having it rewritten as follows:  

[File: StrategyManager.sol#L432](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L432) 

```diff
-     * @notice Used to complete the specified `queuedWithdrawal`. The function caller must match `queuedWithdrawal.withdrawer`
+     * @notice Used to complete the specified `queuedWithdrawal`. The function caller must match `queuedWithdrawal.withdrawerAndNonce.withdrawer`
```
## Typo mistakes
[File: StrategyManager.sol#L287](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L287)

```diff
-         * 2) if `staker` is a contract, then `signature` must will be checked according to EIP-1271
+         * 2) if `staker` is a contract, then `signature` must be checked according to EIP-1271
```
## Events associated with setter functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here is a specific instance entailed:

[File: EigenPodManager.sol#L186-L189](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L186-L189)

```solidity
    function _updateBeaconChainOracle(IBeaconChainOracle newBeaconChainOracle) internal {
        beaconChainOracle = newBeaconChainOracle;
        emit BeaconOracleUpdated(address(newBeaconChainOracle));
    }
```