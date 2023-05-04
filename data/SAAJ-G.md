 
# Gas Optimizations Report

This report focuses on EigenLayer contest, in context of various improvements that can be made in terms of gas cost.

Some of the opportunities identified for improving gas efficiency throughout the codebase of EigenLayer are categorised into 11 main areas; with further multiple instances in each of the category.


# [G-01] Use solidity version 0.8.19 to gain some gas boost (24 Instances)

Use latest version of solidity i.e., 0.8.19 for getting additional gas saving for reference check this article.

Link to the code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManager.sol
2.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/core/StrategyManagerStorage.sol
3.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/strategies/StrategyBase.sol
4.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodManager.sol
5.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPod.sol
6.	https://github.com/code-423n4/2023-04-eigenlayer/tree/main/src/contracts/pods/EigenPodPausingConstants.sol
7.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol
8.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol
9.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol
10.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol
11.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol
12.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol
13.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/ISlasher.sol
14.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPausable.sol
15.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol
16.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategy.sol
17.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol
18.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IETHPOSDeposit.sol
19.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelayedWithdrawalRouter.sol
20.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol
21.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPodManager.sol
22.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IPauserRegistry.sol
23.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol
24.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IServiceManager.sol


# [G-02] Immutable has more gas efficiency than constant (15 Instances)

Using immutable instead of constant, save more gas due to avoiding storage access for state variables.

Variable values are set through constructor when using immutable, which also eliminates the need of assigning initial values to state variable making them more efficient in terms of gas cost.


Link to the Code:

1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L35
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L38
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L40
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L45
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L28
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManagerStorage.sol#L46
7.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L22
8.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L23
9.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L28
10.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L38
11.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L41
12.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L16
13.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L24
14.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L22
15.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L23


# [G-03] Use hardcode address instead address(this) (06 Instances)

Instead of using address(this), it is more gas-efficient to pre-calculate and use the hardcoded address.

Link to the code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L236
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L242
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L456
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L461
 
# [G 04] Declare Public library as internal library (03 Instances)

External call to a public library function is very expensive for reference checks this article. When library has only internal functions it can be used as internal library.

Link to the code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/BeaconChainProofs.sol
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Endian.sol


# [G-05] Use assembly to check for address(0) (10 Instances)
	
Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L343
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L631
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L684
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L114
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L196
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L211
7.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/DelayedWithdrawalRouter.sol#L45
8.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/Pausable.sol#L57
9.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L42
10.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/permissions/PauserRegistry.sol#L48


# [G-06] Wastage of deployed gas when return is not present for returns function (03 Instances)

Wastage of gas during deployment; when return is absent for named variable when function returns.

Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L227
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L97
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/strategies/StrategyBase.sol#L99


# [G 07] abi.encode() is less efficient than abi.encodepacked()(06 Instances)

Refer to this article.

Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L268
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L879
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L175
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L202


# [G 08] String literals passed to abi.encode()/abi.encodePacked() should not be split by commas (11 Instances)

String literals can be split into multiple parts and still be considered as a single string literal.
EACH new comma costs 21 gas due to stack operations and separate MSTOREs.
	
Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L150
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L268
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L276
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L277
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L279
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L879
7.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L175
8.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPodManager.sol#L202
9.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/pods/EigenPod.sol#L461
10.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L138
11.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L146


# [G-09] Cache array length outside of loop (01 Instances)

If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L466


# [G-10] Use calldata instead of memory for function parameters (13 Instances)

Using calldata in external function does not require data to be stored, which reduced the process time as compared to memory. This in return saves gas during calling the data.

Link to the Code:

1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L876
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L254
3.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L30
4.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L48
5.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L81
6.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L99
7.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/libraries/Merkle.sol#L130
8.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L46
9.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IBeaconChainOracle.sol#L53
10.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L88
11.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IStrategyManager.sol#L203
12.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IEigenPod.sol#L101
13.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/interfaces/IDelegationManager.sol#L38


# [G-11] >= costs less gas than > (02 Instances)

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas.

Link to the Code:
1.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L190
2.	https://github.com/code-423n4/2023-04-eigenlayer/blob/main/src/contracts/core/StrategyManager.sol#L824
