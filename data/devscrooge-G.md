# [L-01] No input address validation

## Lines of Code
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L129
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L587
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/core/StrategyManager.sol#L146
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/strategies/StrategyBase.sol#L46
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/strategies/StrategyBase.sol#L51
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/EigenPodManager.sol#L76
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/EigenPodManager.sol#L85-L87
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/EigenPod.sol#L137-L139
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/EigenPod.sol#L152
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/DelayedWithdrawalRouter.sol#L44
- https://github.com/code-423n4/2023-04-eigenlayer/blob/398cc428541b91948f717482ec973583c9e76232/src/contracts/pods/DelayedWithdrawalRouter.sol#L49


## Vulnerability Details

### Description
Address validation is missing in certain functions, such as `initialOwner`, `initialStrategyWhitelister`, `_pauserRegistry` and all the others included in the links, within the smart contracts. It should be checked that all input addresses are not equal to zero, especially those that may cause the contract to become unusable or result in the locking of tokens. This practice is widely regarded as essential for ensuring optimal functionality.

### Impact
Having some input addresses set as the zero address can result in invalid behavior of certain functions of the protocol.

### Tools Used
Manual review

### Recommended Mitigation Steps
A mechanism for checking the input should be implemented in all the mentioned functions. For example:

```bash=
function setStrategyWhitelister(address newStrategyWhitelister) external onlyOwner {
+    require(newStrategyWhitelister != address(0));
     _setStrategyWhitelister(newStrategyWhitelister);
}

```