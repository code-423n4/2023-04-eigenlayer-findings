## Dummy returned values could break other function calls dependent on them
In StrategyBase.sol, `sharesToUnderlyingView()` returns `amountShares` when `totalShares == 0` which is erroneous. If you were to call `withdraw()`, it would first run into an underflow error on line 137:

[File: StrategyBase.sol#L121-L156](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L121-L156)

```solidity
    function withdraw(address depositor, IERC20 token, uint256 amountShares)
        external
        virtual
        override
        onlyWhenNotPaused(PAUSED_WITHDRAWALS)
        onlyStrategyManager
    {
        require(token == underlyingToken, "StrategyBase.withdraw: Can only withdraw the strategy token");
        // copy `totalShares` value to memory, prior to any decrease
        uint256 priorTotalShares = totalShares;
        require(
            amountShares <= priorTotalShares,
            "StrategyBase.withdraw: amountShares must be less than or equal to totalShares"
        );

        // Calculate the value that `totalShares` will decrease to as a result of the withdrawal
137:        uint256 updatedTotalShares = priorTotalShares - amountShares;
        // check to avoid edge case where share rate can be massively inflated as a 'griefing' sort of attack
        require(updatedTotalShares >= MIN_NONZERO_TOTAL_SHARES || updatedTotalShares == 0,
            "StrategyBase.withdraw: updated totalShares amount would be nonzero but below MIN_NONZERO_TOTAL_SHARES");
        // Actually decrease the `totalShares` value
        totalShares = updatedTotalShares;

        /**
         * @notice calculation of amountToSend *mirrors* `sharesToUnderlying(amountShares)`, but is different since the `totalShares` has already
         * been decremented. Specifically, notice how we use `priorTotalShares` here instead of `totalShares`.
         */
        uint256 amountToSend;
        if (priorTotalShares == amountShares) {
            amountToSend = _tokenBalance();
        } else {
            amountToSend = (_tokenBalance() * amountShares) / priorTotalShares;
        }

        underlyingToken.safeTransfer(depositor, amountToSend);
    }
```
It is deemed non-critical for now since `VoteWeigherBase.weightOfOperator()` externally calling `sharesToUnderlying()` has `sharesAmount > 0` check in the if statement. Elsewhere, it might return a wrong `weight` if cares have not been adequately put in place:   

[File: VoteWeigherBase.sol#L75-L82](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/middleware/VoteWeigherBase.sol#L75-L82)

```solidity
                if (sharesAmount > 0) {
                    weight += uint96(
                        (
                            (strategyAndMultiplier.strategy).sharesToUnderlying(sharesAmount)
                                * strategyAndMultiplier.multiplier
                        ) / WEIGHTING_DIVISOR
                    );
                }
```
## ETH restakers at a disadvantage when deciding to switch operator
In order to switch operator, undelegation requires `stakerStrategyList[depositor].length == 0`. Unlike all other liquid restaking where a staker can always make a full removal of shares via [`StrategyManager.queueWithdrawal()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L329-L429), the ETH restaker can only do so when exiting the Beacon Chain unless an over commitment has been made (deliberately or not) or calling `queueWithdrawal()` but not ignore [`completeQueuedWithdrawals()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L442-L449) which is kind of irrational.

For this reason, it is advisable to keep `beaconChainETHStrategy` separate from `stakerStrategyList` by using a different account.

## StrategyManager.slashShares() could easily run into DoS without first checking the contract balance of EigenPod
`StrategyManager.slashShares()` could easily run into DoS if the staker is still validating on Beacon Chain. Apparently, there is simply no ETH available in contract EigenPod to fulfill the sending of ETH to the recipient.

Consider adding the following check to the affected function:

[File: StrategyManager.sol#L507-L510](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L507-L510)

```diff
_            if (strategies[i] == beaconChainETHStrategy) {
+            if (strategies[i] == beaconChainETHStrategy && address(eigenPodManager.ownerToPod(slashedAddressr).balance) >= shareAmounts[i]) {
                 //withdraw the beaconChainETH to the recipient
                _withdrawBeaconChainETH(slashedAddress, recipient, shareAmounts[i]);
            }
```
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
[File: EigenPod.sol#L90](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L90)

```diff
-    /// @notice Emitted when an ETH validator is prove to have withdrawn from the beacon chain
+    /// @notice Emitted when an ETH validator is proven to have withdrawn from the beacon chain
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
## Limiting 32 strategies
Hardcoding the upper limit of `stakerStrategyList` is limiting. In the event the protocol has over grown the 'whitelist' of strategies, users would have to resort to multiple accounts dealing with over [32 strategies](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L28). 

[File: StrategyManager.sol#L635-L641](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L635-L641)

```solidity
        if (stakerStrategyShares[depositor][strategy] == 0) {
            require(
                stakerStrategyList[depositor].length < MAX_STAKER_STRATEGY_LIST_LENGTH,
                "StrategyManager._addShares: deposit would exceed MAX_STAKER_STRATEGY_LIST_LENGTH"
            );
            stakerStrategyList[depositor].push(strategy);
        }
```
## Incorrect input of number in private __gap
In EigenPod.sol, there are total of 6 instead of 4 state variables to cater for future versions to add new variables without shifting down storage in the inheritance chain.

    address public podOwner;
    uint64 public mostRecentWithdrawalBlockNumber;
    uint64 public restakedExecutionLayerGwei;
    bool public hasRestaked;
    mapping(uint40 => VALIDATOR_STATUS) public validatorStatus;
    mapping(uint40 => mapping(uint64 => bool)) public provenPartialWithdrawal;

Consider having the affected code line refactored as follows:

[File: EigenPod.sol#L473](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L473)

```diff
-    uint256[46] private __gap;
+    uint256[44] private __gap;
```
## Missing whistleblower method for other liquid restaking
A watcher can call [`EigenPod.verifyOvercommittedStake()`](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L241-L294) to help enhance pooled security. However, this method is missing for non-beaconChainETHStrategy. Consider looking into implementing it where possible. 

## Missing check that leaves.length is a power of 2
According to the function NatSpec of `Merkle.merkleizeSha256()`, it requires the leaves.length is a power of 2. Consider adding the needed check where possible.

[File: Merkle.sol#L123-L153](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L123-L153)

1. Implement `isPowerOfTwo()` that checks if a given number is a power of 2:

```solidity
    function isPowerOfTwo(uint256 x) internal pure returns (bool) {
        return x != 0 && (x & (x - 1)) == 0;
    }
```
2. Refactor `merkleizeSha256()` to call `isPowerOfTwo()` and require the length of leaves to be a power of 2 as follows:

```solidity
    function merkleizeSha256(
        bytes32[] memory leaves
    ) internal pure returns (bytes32) {
        require(
            isPowerOfTwo(leaves.length),
            "merkleizeSha256: The number of leaves must be a power of 2"
        );

        // ... rest of the function
    }
```
With this refactoring, `merkleizeSha256()` will revert the transaction if the number of leaves is not a power of 2.

## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For example, the import instances below could be refactored conforming to the suggested standards as follows:

[File: StrategyManager.sol#L11-L13](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L11-L13)

```diff
- import "../interfaces/IEigenPodManager.sol";
+ import {IEigenPodManager} from "../interfaces/IEigenPodManager.sol";
- import "../permissions/Pausable.sol";
+ import {Pausable} from "../permissions/Pausable.sol";
- import "./StrategyManagerStorage.sol";
+ import {StrategyManagerStorage} from "./StrategyManagerStorage.sol";
```