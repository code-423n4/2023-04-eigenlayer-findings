## [GAS-1] ++i Costs Less Gas Than i++, Especially When It's Used In For-loops (--i/i-- Too)

i++ contains two extra instructions compared to ++i. These two instructions are DUP (3 gas) and POP (2 gas).

### Lines of code:

contract: https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol

`466: for(uint256 i = 0; i < queuedWithdrawals.length; i++) {`

contract: https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol

`115: for (uint256 i = 0; i < claimableDelayedWithdrawalsLength; i++) {`

contract: https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol

`137: for (uint i = 0; i < numNodesInLayer; i++) {`

`145: for (uint i = 0; i < numNodesInLayer; i++) {`