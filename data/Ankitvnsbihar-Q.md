# [L-1]Audit Report: [Bug in depositBeaconChainETH function]

StrategyManager.sol(https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol)
IStrategyManager.sol(https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol)
EigenPodManager.sol(https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol)

## Description:

In the smart contract EagenPodManager.sol, there is a function called restakeBeaconChainETH that calls a function named depositBeaconChainETH in the IStrategyManager interface. However, it seems that the IStrategyManager interface is not importing the StrategyManager.sol contract that contains the implementation of the depositBeaconChainETH function. This could potentially cause a bug where the function call in restakeBeaconChainETH fails to find the implementation of depositBeaconChainETH and may result in unexpected behavior.

## Impact:

This bug can lead to unexpected behavior or errors when the restakeBeaconChainETH function is called, which could result in loss of funds or other undesired outcomes.

## Recommended Mitigation Steps:

To fix this issue, the IStrategyManager interface should import the StrategyManager.sol contract. Once this is done, the restakeBeaconChainETH function should work as intended. Additionally, it is recommended to thoroughly test the updated contract to ensure that the bug has been fully resolved.