 ### [QA-01] Function `returns` declares a named variable, but actually returns something else

[src/contracts/libraries/Endian.sol#L4-L9](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol#L4-L9)
 ```
@@ -4,9 +4,9 @@ pragma solidity =0.8.12;
 library Endian {
     function fromLittleEndianUint64(
         bytes32 lenum
-    ) internal pure returns (uint64 n) {
+    ) internal pure returns (uint64) {
         // the number needs to be stored in little-endian encoding (ie in bytes 0-8)
-        n = uint64(uint256(lenum >> 192));
+        uint64 n = uint64(uint256(lenum >> 192));
         return
             (n >> 56) |
             ((0x00FF000000000000 & n) >> 40) |
```

### [QA-02] Variable could be used

In `_completeQueuedWithdrawal` the value of `queuedWithdrawal.strategies.length` is assigned to a variable and used directly again a few lines below.

[src/contracts/core/StrategyManager.sol#L774-L778](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L774-L778)
```
// store length for gas savings
uint256 strategiesLength = queuedWithdrawal.strategies.length;
// if the withdrawer has flagged to receive the funds as tokens, withdraw from strategies
if (receiveAsTokens) {
    require(tokens.length == queuedWithdrawal.strategies.length, "StrategyManager.completeQueuedWithdrawal: input length mismatch");
```


### [QA-03] Immutable fields in camelCase

In the codebase both camelCased and PASCAL_CASE are used.

[src/contracts/pods/EigenPod.sol#L49-L53](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L49-L53)
```
    /// @notice The single EigenPodManager for EigenLayer
    IEigenPodManager public immutable eigenPodManager;

    /// @notice The amount of eth, in gwei, that is restaked per validator
    uint64 public immutable REQUIRED_BALANCE_GWEI;
```

### [QA-04] Numeric values having to do with time should use time units for readability

Suffixes like `seconds`, `minutes`, `hours`, `days` and `weeks` after literal numbers can be used to specify units of time.  

[src/contracts/pods/DelayedWithdrawalRouter.sol#L24](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L24)
```
uint256 public constant MAX_WITHDRAWAL_DELAY_BLOCKS = 50400;
```
[src/contracts/pods/EigenPod.sol#L41](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L41)
[src/contracts/core/StrategyManagerStorage.sol#L46](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L46)

### [QA-05] The `nonReentrant` modifier should occur before all other modifiers

It is a best-practice to protect against reentrancy in other modifiers.  

[src/contracts/core/StrategyManager.sol#L164-L169](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L164-L169)
```
function depositBeaconChainETH(address staker, uint256 amount)
    external
    onlyEigenPodManager
    onlyWhenNotPaused(PAUSED_DEPOSITS)
    onlyNotFrozen(staker)
    nonReentrant
```
There 9 instances of this issue.

### [QA-06] Lines too long

Keep line width to max 120 characters for better readability where possible.  

[src/contracts/core/StrategyManager.sol#L81](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L81)  
```
* @param withdrawer Is the party specified by `staker` who will be able to complete the queued withdrawal and receive the withdrawn funds.
```
There are 353 instances of this issue.

### [QA-07] Use a more recent version of solidity


[src/contracts/core/StrategyManager.sol#L2](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L2)  
```
pragma solidity =0.8.12;
```
There are 24 instances of this issue.

### [QA-08] Constants should be all uppercase with words separated by underscores


[src/contracts/core/StrategyManagerStorage.sol#L70](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L70)  
```
IStrategy public constant beaconChainETHStrategy = IStrategy(0xbeaC0eeEeeeeEEeEeEEEEeeEEeEeeeEeeEEBEaC0);
```
[src/contracts/pods/EigenPodManager.sol#L37](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L37)  

### [QA-09] Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the `form import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation.  

[src/contracts/core/StrategyManager.sol#L4](https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L4)  
```
import "@openzeppelin/contracts/interfaces/IERC1271.sol";
```
There are 72 instances of this issue.