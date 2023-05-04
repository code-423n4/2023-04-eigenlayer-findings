LOW/QA:

1)
https://github.com/code-423n4/2023-04-eigenlayer/blob/138cf7edb887f641ae48e33e963ab1be4ff474c1/src/contracts/core/StrategyManager.sol#L415
https://github.com/code-423n4/2023-04-eigenlayer/blob/138cf7edb887f641ae48e33e963ab1be4ff474c1/src/contracts/core/StrategyManager.sol#L771

// @audit Using uint32 for withdrawalStartBlock instead of uint256 may introduce potential risks due to integer overflow when comparing it with block.number. It is recommended to use uint256 for withdrawalStartBlock to ensure consistent and safe comparisons. The use of uint32 for withdrawalStartBlock poses a high-risk exploit due to potential integer overflow, allowing attackers to bypass the withdrawal delay mechanism. It is crucial to switch to uint256 to prevent this vulnerability. 
However, years until it overflows to zero: 1,613 years

2)
https://github.com/code-423n4/2023-04-eigenlayer/blob/138cf7edb887f641ae48e33e963ab1be4ff474c1/src/contracts/core/StrategyManager.sol#L777

// @audit shouldnt this check be much earlier in either this function and/or in the calling function in order to avoid gas wastage etc, as anyone can call the calling function?