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
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: StrategyManager.sol#L679-L708](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L679-L708)

```diff
    function _removeShares(address depositor, uint256 strategyIndex, IStrategy strategy, uint256 shareAmount)
        internal
-        returns (bool)
+        returns (bool _status)
    {
        // sanity checks on inputs
        require(depositor != address(0), "StrategyManager._removeShares: depositor cannot be zero address");
        require(shareAmount != 0, "StrategyManager._removeShares: shareAmount should not be zero!");

        //check that the user has sufficient shares
        uint256 userShares = stakerStrategyShares[depositor][strategy];
        
        require(shareAmount <= userShares, "StrategyManager._removeShares: shareAmount too high");
        //unchecked arithmetic since we just checked this above
        unchecked {
            userShares = userShares - shareAmount;
        }

        // subtract the shares from the depositor's existing shares for this strategy
        stakerStrategyShares[depositor][strategy] = userShares;

        // if no existing shares, remove the strategy from the depositor's dynamic array of strategies
        if (userShares == 0) {
            _removeStrategyFromStakerStrategyList(depositor, strategyIndex, strategy);

            // return true in the event that the strategy was removed from stakerStrategyList[depositor]
-            return true;
+            _status = true;
        }
        // return false in the event that the strategy was *not* removed from stakerStrategyList[depositor]
-        return false;
+        _status = false;
    }
```
## Use smaller uint128 or smaller type and pack variables to use lesser amount of storage slots
As the solidity EVM works with 32 bytes, most variables work fine with less than 32 bytes and may be packed when sitting next to each other so that they can be stored in the same slot, this saves gas when writing to storage ~2000 gas.

For instance, the two state variables below may be refactored as follows:

[File: StrategyManagerStorage.sol#L44-L46](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L44-L46)

```diff
-    uint256 public withdrawalDelayBlocks;
+    uint128 public withdrawalDelayBlocks;
    // the number of 12-second blocks in one week (60 * 60 * 24 * 7 / 12 = 50,400)
-    uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
+    uint128 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
```
This alone will save ~2000 gas with 2 slot of storage reduced to 1 slot. Additionally, the respective max value of [`uint128`](https://velvetshark.com/articles/max-int-values-in-solidity), which is commonly adopted for token amounts in Uniswap V2 and V3, is more than sufficient catering to the limits of the unsigned integer variables as can be evidenced from the data table below:

```
uint            Digits          Max value
-----------------------------
uint128         39              340,282,366,920,938,463,463,374,607,431,768,211,455
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, consider replacing `>=` with the strict counterpart `>` in the following inequality instance:

[File: StrategyManager.sol#L263](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L263)

```diff
-            expiry >= block.timestamp,
            // Rationale for subtracting 1 on the right side of the inequality:
            // x >= 10; [10, 11, 12, ...]
            // x > 10 - 1 is the same as x > 9; [10, 11, 12 ...]
+            expiry > block.timestamp - 1,
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: StrategyManager.sol#L422](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L422)

```diff
-        if (undelegateIfPossible && stakerStrategyList[msg.sender].length == 0) {
+        if (!(!undelegateIfPossible || stakerStrategyList[msg.sender].length != 0)) {
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.12",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A `private` visibility that saves more gas on function calls than the `internal` visibility is adopted because the modifier will only be making this call inside the contract.

For example, the modifier instance below may be refactored as follows:

[File: StrategyManager.sol#L104-L107](StrategyManager.sol#L109-L112](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L104-L107)

```diff
+    function _onlyFrozen(address staker) private view {
+        require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");
+    }

    modifier onlyFrozen(address staker) {
-        require(slasher.isFrozen(staker), "StrategyManager.onlyFrozen: staker has not been frozen");
+        _onlyFrozen(staker);
        _;
    }
```
## Unneeded DelayedWithdrawalRouter.sol
There is no economic benefit and/or safety concern to the protocol by implementing this contract logic since all ETH funds reaching this stage already come clean of any slashes or penalties needed.

Consider [sending ETH](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L464-L466) directly from EigenPod.sol to cut down on unneeded function calls and contract deployment.
